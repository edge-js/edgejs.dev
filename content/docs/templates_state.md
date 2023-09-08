---
summary: Learn about different APIs to define template state
---

# Templates state

Templates state refers to the data the templates can access during their rendering phase. Edge provides four layers to pass or define the template state.


:::note

Since Edge templates are rendered on the server, the data is never shared with the client-side runtime.

:::

## Globals

The globals refer to the state defined using the `edge.global` method. Globals are available to all the templates, including components.

For example, you can use globals to share the website config with all the templates.

```ts
edge.global('config', {
  colorSheme: 'dark',
  menu: [],
  socialLinks: [],
})
```

```edge
<html class="{{ config.colorSchema }}">
  <head>
  <head>

  <body>
    <header>
      @each(item in config.menu)
      @end
    </header>

    <footer>
      @each(link in config.socialLinks)
      @end
    </footer>
  </body>
</html>
```

You can also share classes, functions, and almost every JavaScript data-type as global properties.

```ts
edge.global('findUser', async function (id) {
  return User.findById(id)
})
```

```edge
@let(user = await findUser(1))
{{ user.username }}
```

## Locals

The `locals` are similar to the global state but isolated between multiple render calls.

In the following example, we use the `edge.createRenderer` method to create multiple children instances of edge and share separate data objects with them.

The data will be available globally to all the templates included as partials or components, but not with the isolated children instances.

```ts
const templ1 = edge.createRenderer()
const templ2 = edge.createRenderer()

templ1.share({
  url: '/posts',
})

templ2.share({
  url: '/posts/1',
})

await templ1.renderRaw('{{ url }}') // /posts
await templ2.renderRaw('{{ url }}') // /posts/1
```

### Why use locals?

You might be thinking, why create a new isolated instance and use the `.share()` method to share locals with a template when you can pass the data during the `.render()` method call?


:::caption{for="info"}

**Why this?**


:::

```ts
const view = edge.createRenderer().share({
  url: req.url
})

await view.render('template-path')
```



:::caption{for="info"}

**And not this?**


:::

```ts
await edge.render('template-path', {
  url: req.url
})
```

Let's use a concrete example and understand when locals can be helpful.

Imagine you use Express.js and Edge together and want to share data with a template using middleware. Also, the shared data should be isolated between concurrent requests handled by your application.

You can create a new instance of the Edge renderer for each request and use the `share` method to share global data isolated between multiple HTTP requests.

```ts
app.use(function (req, res) {
  res.view = edge.createRenderer()
})

app.use(function (req, res) {
  res.view.share({
    url: req.url
  })
})

app.use(function (req, res) {
  res.view.share({
    user: req.auth.user
  })
})

// Finally render a template
app.get('/posts', async (req, res) => {
  const html = await res.view.render('posts')
  res.send(html)
})
```

## Rendering data object

The rendering data refers to the data object passed when calling the `edge.render` method. The rendering data is not shared with the components used by a template.

```ts
const renderingData = {}
await edge.render('template-path', renderingData)
```

## Inline variables

Inline variables are defined within the template as let variables. You can define inline variables using the `@let` tag and re-assign them new values using the `@assign` tag.

```edge
@let(config = await loadConfig())

{{ config.someKey }}
```

The scope of inline variables is similar to a let variable in JavaScript. Let's consider the following example, in which we mutate an inline variable inside an `each` loop.

```edge
// highlight-start
{{-- Define variable --}}
@let(total = 0)
// highlight-end

<ul>
  @each(item in items)
    // highlight-start
    {{-- Re-assign it a new value --}}
    @assign(total = total + item.price)
    // highlight-end
    <li> {{ item.name }} = {{ item.price }} </li>
  @end

  <li> Gross total = {{ total }} </li>
<ul>
```

## Data layers and their scope

The final template state is a merged copy of all the layers created using `Object.assign`. Therefore, the layer with top most priority will overwrite the values from the previous layers.

```ts
// title: Pseudocode
const finalState = Object.assign(
  {},
  globals,
  locals,
  renderingData
)
```

| Name | Shared with components? | Isolated? |
|----------|------------------------|----------|
| Globals | ✅ | ❌ |
| Locals | ✅ | ✅ |
| Rendering data object | ❌ | ✅ |
| Inline variables | ❌ | ✅ |
