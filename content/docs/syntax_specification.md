# Syntax specification

This document outlines the Edge templating syntax specification. You can reference this doc to understand the Edge internals better or create a syntax highlighter for your favorite code editor.

## Goals

Edge's primary goal is not to introduce any new dialect to the templating layer. Instead, use JavaScript everywhere.

1. Keep the syntax closer to JavaScript.
2. It should be easier to type and understand.
3. Edge should work with any markup language and not just HTML.
4. Generate error stacks pointing to the original source file and the line number.

## Primitives

Edge is built on two core primitives, i.e

- **Curly braces**: The familiar curly braces `{{ }}` are used to evaluate a JavaScript expression.
- **Edge tags**: Edge tags starts with an `@` symbol followed by the tag name. Tags can receive properties and have children elements surrounded by an opening and a closing statement.

## Curly braces

Edge uses the familiar curly braces `{{ }}` for evaluating JavaScript expressions. The output of the expression is concatenated to the output string. For example:

```edge
Hello {{ username }}!
```

Given the username is `Virk`. The output will be

```
Hello Virk
```

### Multi-line expressions
Expressions can span across multiple lines. For example:

```edge
Hello {{
  users.map((user) => {
    return user.username
  })
}}
```

Since the output of a template is always a `string`, the output of a JavaScript expression is also converted to a string. 

In the above example, the JavaScript expression returns an array and will be converted to a comma-separated string. The output is the same as writing the following JavaScript code.

```ts
String(users.map((user) => {
  return user.username
}))
```

You can self-convert the array to string using the JavaScript `Array.join` method.

```edge
Hello {{
  users.map((user) => {
    return user.username
  }).join(', ')
}}
```

### Escaped HTML output

Edge performs HTML escaping on JavaScript expressions output rendered using the mustache braces.

Given the following HTML string

```edge
{{
 '<span style="color: red">This should be red.</span>'
}}
```

The output will be 

```
&lt;span style="color: red"&gt;This should be red.&lt;/span&gt;
```

If you want to render HTML as it is, you can either wrap it inside triple curly braces or use the `html.safe` global method.

```edge
{{{
 '<span style="color: red">This should be red.</span>'
}}}
```

```edge
{{
  html.safe(
    '<span style="color: red">This should be red.</span>'
  )
}}
```

### Escaping curly braces

Suppose you are using Edge in combination with a frontend template engine that also uses curly braces for interpolation. In that case, you may use the `@` symbol to inform Edge to skip a given statement. For example:

```edge
// title: Input
Edge should not parse @{{ username }}
```

```edge
// title: Output
Edge should not parse {{ username }}
```

## Edge tags

Edge tags are used to add rich features to the templating layer. Features like conditionals, loops, components, and partials are implemented using the [tags API](./digging_deeper/creating-custom-tags.md). 


:::note


A tag must be written in its line with no contents around it. This is a conscious decision to keep the tags API easy to scan at the compiler level. You can always mix content + JavaScript code on the same line using curly braces.


:::

Tags are further categorized into the following sub-categories.

### Block-level tags

Block-level tags have an opening and a closing statement with content inside it. The `@if` tag is a block-level tag.

```edge
@if(someCondition)
  If block content
@end
```

You can auto-close block-level tags by prefixing the `!` operator before the tag name.

```edge
// title: Auto close
@!component('button', { size: 'large' })
```

```edge
// title: Explicitly close
@component('button', { size: 'large' })
@end
```

### Inline tags

Inline tags do not accept the body, so you do not have to close them explicitly. The `@include` tag is an inline tag.

```edge
@include('partials/some-file')
```

### Tags without arguments

Tags can be specified without arguments as well. For example, the `@debugger` tag. However, if a tag accepts arguments, then you must always call it like a function.

```edge
@debugger
```

### Swallow newlines

Tags should always be used with block-level content because they create a newline separator between two blocks of text. 

However, if you are using tags where newlines can change the output meaning or make it invalid, you must append the `~` symbol to the tag.

:::caption{for="info"}

**Without tilde**

:::

```edge
Hello
@let(username = 'virk')
 {{ username }}
```

```
Hello
 virk
```


:::caption{for="info"}

**With tilde**

:::

```edge
Hello
@let(username = 'virk')~
 {{ username }}
```

```
Hello virk
```

## Comments

You can write comments in Edge by wrapping the text inside the `{{-- --}}` block.

```edge
{{-- Inline before --}} Hello {{-- Inline after --}}
```

```edge
{{--
  This is a multi-line comment.
--}}
```

## Examples

<table>
<thead>
<tr>
<th> Invalid ❌ </th>
<th> Valid ✅ </th>
</tr>
</thead>
<tbody>
<tr>
<td>

```edge
@if
(
username
)
```

</td>
<td>

```edge
@if(username)
```

</td>
</tr>
<tr>
<td>

```edge
@if(username) Hello @endif
```

</td>
<td>

```edge
@if(username)
  Hello
@end
```

</td>
</tr>
<tr>
<td>

```edge
@if(
  username
) <p> Hello </p>
@endif
```

</td>
<td>

```edge
@if(
  username
)
  <p> Hello </p>
@end
```

</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr>
<th> Invalid ❌ </th>
<th> Valid ✅ </th>
</tr>
</thead>
<tbody>
<tr>
<td>

```edge
@! component('button')
```

</td>
<td>

```edge
@!component('button')
```

</td>
</tr>
<tr>
<td>

```edge
@!component
('button', {
  type: 'primary'
})
```

</td>
<td>

```edge
@!component(
  'button',
  {
    type: 'primary'
  }
)
```

</td>
</tr>
</tbody>
</table>

