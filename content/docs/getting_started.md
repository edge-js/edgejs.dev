# Getting started

You may install Edge inside an existing Node.js project from the npm packages registry.


:::warning

Edge is an ESM-only package, and therefore your applications must use ES modules to import the package.

:::


```sh
npm i edge.js@next
```

You can start using Edge as soon as you install it. You do not need any compiler or build tools to compile the templates.

Let's start with the following project structure. We will store templates within the `views` directory and render them during an HTTP request.

```ts
.
├── views
│  └── home.edge
├── index.js
└── package.json
```

```ts
// title: index.js
import { Edge } from 'edge.js'
import { createServer } from 'node:http'

// highlight-start
const edge = Edge.create()
edge.mount(new URL('./views', import.meta.url))
// highlight-end

const server = createServer(async (req, res) => {
	// highlight-start
  const data = { username: 'virk' }
  const html = await edge.render('home', data)
	// highlight-end

  res.setHeader('content-type', 'text/html')
  res.end(html)
})

server.listen(3000)
```

```edge
// title: views/home.edge
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
</head>
<body>
  <h1>
    Hello {{ username }}
  </h1>
</body>
</html>
```

Let's go through the source code line by line.

- We start by importing the `edge.js` package and create a simple Node.js HTTP server.

- We use the `edge.mount`  method to register the `views` directory as the root URL for our templates. Template files end with the `.edge` extension.

- Finally, we use the `edge.render` method to render a template during an HTTP request. The render method accepts the template path (without the extension) and the data object to share with the template.

## Caching templates

The templates are re-compiled whenever you call the `edge.render` method. You can verify this by editing the `home.edge` file and refreshing the browser to see the modified output.

```edge
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
</head>
<body>
  <h1>
    // highlight-start
    Hello {{ username.toUpperCase() }}
    // highlight-end    
  </h1>
</body>
</html>
```

You must enable the cache mode in production to avoid re-compiling the templates. The compiled output will be saved within the memory.

```js
const edge = Edge.create({
  cache: process.env.NODE_ENV === 'production'
})
```

## Mounting disks

EdgeJS uses the concept of disks to find and render templates from the local filesystem. You can register one default disk and multiple named disks to lookup templates.

In the following example, we register the `views` directory as the default disk for finding templates.

```ts
const BASE_URL = new URL('./', import.meta.url)

edge.mount(new URL('views', BASE_URL))

/**
 * Render home.edge file from 
 * {BASE_URL/views} directory
 */
await edge.render('home')

/**
 * Render pages/posts/index.edge file from 
 * {BASE_URL/views} directory
 */
await edge.render('pages/posts/index')
```

You can also mount multiple named disks, which can be helpful if you have a theming system and want to switch between the themes.

```ts
const BASE_URL = new URL('./', import.meta.url)

edge.mount(
  'elegant',
  new URL('themes/elegant', BASE_URL)
)

edge.mount(
  'classic',
  new URL('themes/classic', BASE_URL)
)

edge.mount(
  'mono',
  new URL('themes/mono', BASE_URL)
)
```

Now you can render templates by prefixing the disk name before the template path. For example:

```ts
await edge.render('classic::home')
await edge.render('mono::pages/posts/index')
```

## In-memory templates

You can register templates that are kept within memory using the `.registerTemplate()` method. It accepts a unique name for the template as the first argument and at the template contents via the options parameter.

```ts
edge.registerTemplate('uikit.button', {
  template: `<button {{
    $props.except(['text']).toAttrs()
  }}>
    {{ text }}
  </button>`
})
```

Now, you can reference the above template as a component inside any other template. 

```edge
@!component('uikit.button', {
  text: 'Submit',
  type: 'submit',
  class: ['btn', 'btn-primary'],
  disabled: false,
})
```

## Rendering API

You can render EdgeJS templates using one of the following methods.

### render

The `render` method accepts the template path relative to the disk root and an optional data object to share with the template.

The return value is a string output of the rendered template.

```ts
const html = await edge.render('home')
console.log(html)
```

### renderSync

The `renderSync` method is similar to the `render`. However, it uses synchronous APIs under the hood to read and render the template files.

We recommend using the `render` method over the `renderSync` method.

```ts
const html = edge.renderSync('home')
console.log(html)
```

### renderRaw

The `renderRaw` method allows you to render raw text as a template.

```ts
const template = `
 <h1> Hello {{ username || 'Guest' }}! </h1>
`

await edge.renderRaw(template, { username: 'virk' })
```

### renderRawSync

The `renderRawSync` method is the same as `renderRaw` but uses synchronous APIs under the hood.

```ts
const template = `
 <h1> Hello {{ username || 'Guest' }}! </h1>
`

edge.renderRawSync(template, { username: 'virk' })
```

## Reserved keywords

The internals of compiled template relies on the following variables, and you must not define them as template state.

- `template`
- `$context`
- `state`
- `$filename`
