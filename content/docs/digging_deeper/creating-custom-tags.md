---
summary: In-depth guide on creating custom Edge tags
---

# Creating custom tags

Most of the Edge features are implemented using its public tags API, which you can use to extend the Edge capabilities further.

When implementing a custom tag, you will be dealing with **Abstract syntax trees (AST)**, so having prior knowledge of the same will help. 

However, if you are starting with ASTs, we recommend visiting [astexplorer.net](https://astexplorer.net/), select `acorn` as the parser, and experimenting with the tool. You do not have to do Ph.D. in Abstract syntax trees. Most of the time, you will learn about its usage on the go. 

In this guide, we will cover the following topics.

- Create and register a custom edge tag.
- A Hello World introduction to parsing and re-printing ASTs.
- Implementing a conditional Edge tag.
- Creating locally scoped variables within the conditional block.

## Registering an Edge tag
A tag must implement the `TagContract` interface, and you can register it using the `edge.registerTag` method.

In the following example, we create a tag named `reverse` which writes a "Hello greeting" to the output.

```ts
import edge from 'edge.js'
import { TagContract } from 'edge.js/types'

/**
 * Defining a tag
 */
const reverse: TagContract = {
  block: false,
  seekable: true,
  tagName: 'reverse',
  compile(parser, buffer, token) {
    buffer.outputRaw('Hello from reverse tag')
  }
}

/**
 * Registering it with Edge
 */
edge.registerTag(reverse)

/**
 * Using the tag
 */
const output = await edge.renderRaw('@reverse()')

console.log(output) // I am the reverse tag
```

- `block`: The `block` flag creates a block-level Edge tag which must be used with an opening and a closing statement. For example: The `@if` and `@each` tags are block-level.
- `seekable`: A `seekable` tag accepts one or more arguments. If you create a tag that will never accept arguments, set this flag to `false`.
- `tagName`: A unique name for the tag.
- `compile`: The compile method converts the tag to JavaScript output. This is where you will be spending most of your time.

### Generating AST
Let's make the `reverse` tag accept a string value and create an AST of the arguments the tag accepts.

```ts
const reverse: TagContract = {
  block: false,
  seekable: true,
  tagName: 'reverse',
  // highlight-start
  compile(parser, buffer, token) {
    const expression = parser.utils.transformAst(
      parser.utils.generateAST(token.properties.jsArg, token.loc, token.filename),
      token.filename,
      parser
    )

    console.log(JSON.stringify(expression, null, 2))
  }
  // highlight-end
}
```

Now, let's try passing different values to the `@reverse` tag and monitor the console output of the `expression` variable.

---

#### Raw string

```edge
@reverse('hello world')
```

```json
{
  "type": "Literal",
  "start": 0,
  "end": 13,
  "loc": {
    "start": {
      "line": 1,
      "column": 9
    },
    "end": {
      "line": 1,
      "column": 13
    }
  },
  "value": "hello world",
  "raw": "'hello world'"
}
```

---

#### Variable reference

```edge
@reverse(username)
```

```json
{
  "type": "MemberExpression",
  "object": {
    "type": "Identifier",
    "name": "state"
  },
  "computed": false,
  "property": {
    "type": "Identifier",
    "start": 0,
    "end": 8,
    "loc": {
      "start": {
        "line": 1,
        "column": 9
      },
      "end": {
        "line": 1,
        "column": 8
      }
    },
    "name": "username"
  }
}
```

#### Function call

```edge
@reverse(getUserName())
```

```json
{
  "type": "CallExpression",
  "start": 0,
  "end": 13,
  "loc": {
    "start": {
      "line": 1,
      "column": 9
    },
    "end": {
      "line": 1,
      "column": 13
    }
  },
  "callee": {
    "type": "MemberExpression",
    "object": {
      "type": "Identifier",
      "name": "state"
    },
    "computed": false,
    "property": {
      "type": "Identifier",
      "start": 0,
      "end": 11,
      "loc": {
        "start": {
          "line": 1,
          "column": 9
        },
        "end": {
          "line": 1,
          "column": 11
        }
      },
      "name": "getUserName"
    }
  },
  "arguments": [],
  "optional": false
}
```

As you can notice, the AST differs based on the input value of the `@reverse` tag. You can use the `expression.type` property to disallow certain and also feel free to mutate the AST.

### Converting AST to string
Once you mutate the AST, you can convert it to a valid JavaScript expression using the `.stringify()` method.

```ts
const reverse: TagContract = {
  block: false,
  seekable: true,
  tagName: 'reverse',
  compile(parser, buffer, token) {
    const expression = parser.utils.transformAst(
      parser.utils.generateAST(token.properties.jsArg, token.loc, token.filename),
      token.filename,
      parser
    )

    // highlight-start
    console.log(parser.utils.stringify(expression)))
    // highlight-end
  }
}
```

### Finishing the reverse tag implementation
Let's wrap up the `reverse` tag implementation. We will take the arguments provided to the tag, reverse its output value and write it to the output.

```ts
const reverse: TagContract = {
  block: false,
  seekable: true,
  tagName: 'reverse',
  compile(parser, buffer, token) {
    const expression = parser.utils.transformAst(
      parser.utils.generateAST(token.properties.jsArg, token.loc, token.filename),
      token.filename,
      parser
    )

    // highlight-start
    const outputExpression = `${parser.utils.stringify(expression)}.split("").reverse().join("")`
    buffer.outputExpression(outputExpression, token.filename, token.loc.start.line, false)
    // highlight-end
  }
}
```

Let's test our implementation.

```ts
// With a raw string
assert.equal(await edge.renderRaw(`@reverse('virk')`), 'kriv')

// With variable reference
assert.equal(await edge.renderRaw(`@reverse(username)`, {
  username: 'virk'
}), 'kriv')

// With function call
assert.equal(await edge.renderRaw(`@reverse(getUserName())`, {
  getUserName() { return 'virk' }
}), 'kriv')
```

## Creating a conditional tag
Let's tune up the difficulty level and implement a custom conditional tag to display notifications. 

The `@notification` tag will accept the notification type and render the children's content (within the opening and closing blocks) if a notification for the given type exists. For example:

```edge
@notification('success')
  <div class="alert alert-{{ notification.type }}">
    <p> {{ notification.message }} </p>
  </div>
@end
```

Under the hood, the tag will look for the `notifications` object within the [template state](../templates_state.md).

```ts
import edge from 'edge.js'
import { TagContract } from 'edge.js/types'

const notification: TagContract = {
  /**
   * Tag accepts content within the opening and
   * closing tags
   */
  block: true,

  /**
   * Tag accepts parameters
   */
  seekable: true,

  /**
   * The tag name
   */
  tagName: 'notification',

  /**
   * Compiling tag to JavaScript output
   */
  compile(parser, buffer, token) {
    const expression = parser.utils.transformAst(
      parser.utils.generateAST(token.properties.jsArg, token.loc, token.filename),
      token.filename,
      parser
    )

    const key = parser.utils.stringify(expression)

    /**
     * Write an if statement
     */
    buffer.writeStatement(
      `if (state.notifications && state.notifications[${key}]) {`,
      token.filename,
      token.loc.start.line
    )

    /**
     * Define a local variable
     */
    buffer.writeExpression(`let notification = {
      type: ${key},
      message: state.notifications[${key}],
    }`, token.filename, token.loc.start.line)

    /**
     * Create a local variables scope and tell the parser about
     * the existence of the "notification" variable
     */
    parser.stack.defineScope()
    parser.stack.defineVariable('notification')

    /**
     * Process component children using the parser
     */
    token.children.forEach((child) => {
      parser.processToken(child, buffer)
    })

    /**
     * Clear the scope of the local variables before we
     * close the if statement
     */
    parser.stack.clearScope()

    /**
     * Close if statement
     */
    buffer.writeStatement(
      `}`,
      token.filename,
      token.loc.start.line
    )
  }
}

edge.registerTag(notification)
```

Let's put the `notification` tag into action.

```ts
const notifications = {
  success: 'Settings saved successfully'
}

const output = await edge.renderRaw(`
  @notification('success')
    <div class="alert alert-{{ notification.type }}">
      <p> {{ notification.message }} </p>
    </div>
  @end
`, { notifications })
```
