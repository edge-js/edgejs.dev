---
summary: Learn how to use layouts using slots
---

# Layouts

You can use slots to render templates. Let's create a `index.edge` file inside the `views/components/layout` directory.

```edge
// title: views/components/layout/app.edge
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>{{ title || "Your default title" }}</title>
  @if ($slots.meta)
    {{{ await $slots.meta() }}}
  @endif
</head>
<body>
  {{{ await $slots.main() }}}
</body>
</html>
```

Edit a `welcome.edge` file inside the `views` directory.  
In this exemple we want to render the main content of or welcome page using our `$slots.main` function, add a meta markup with `$slots.meta`, and change the default title. 

```edge
// title: views/welcome.edge
@layout.app({ title: "Welcome page title" })
  @slot('meta')
    <meta name="description" content="A welcome page made with EdgeJS">
  @endslot

  <h1>Hello world</h1>
@end
```

As you can see, we can use **slots** to replace section of our layout. This is what we have done with the **meta section** and the **main section**.
