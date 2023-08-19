# Components

Components allows you to split the application UI into reusable pieces with isolated state. You can use components to define layouts or create a collection of different UI elements.

Before we talk more about components, let's understand how they differ from components in the frontend ecosystem.

- **No reactivity**: Since Edge is a backend template engine, there is no concept of reactivity in Edge or its components layer.

- **No support CSS or frontend JavaScript**: Edge templates are not processed using build tools. Therefore we do not process inline CSS or create frontend bundles.

## Creating components

Components are regular Edge templates created with the purpose of reuse. Components can access additional runtime properties like `$props` and `$slots`, which are unavailable to other Edge templates.

We recommend you create components inside the `components` directory of your template's root path. This helps create a visual boundary between the components and the rest of the templates used by your application.

Let's start by creating a button component. We will store it inside the `components/button.edge` file. 

```edge
// title: views/components/button.edge
<button type="{{ type || 'submit' }}"> {{ text }} </button>
```

## Using components

You must use the `@component` tag to render a component inside your templates. The component tag accepts the template path as the first parameter and `props` as the second parameter.

```edge
<form>
  @!component('components/button', { text: 'Login' })
  @!component('components/button', { text: 'Cancel', type: 'reset' })
</form>
```

Output

```html
<form>
  <button type="submit"> Login </button>
  <button type="reset"> Cancel </button>
</form>
```

Components from [named disks](../getting_started.md#mounting-disks) can be referenced by prefixing the disk name.

```edge
@!component('uikit::components/button', { text: 'Login' })
```

## Passing props

The component props are passed as the second parameter using the `@component` tag. Props are always represented as an object, and you can access them inside the component's template using the object property name.

See also: [Props reference](./props.md)

```edge
@component('components/button', {
  type: 'submit',
  class: 'btn btn-large',
  text: 'Login'
})
```

```edge
// title: Within the component
{{ type }}
{{ class }}
{{ text }}
```

Another way to access props is using the `$props` property. For example:

```edge
{{ $props.get('type') }}
{{ $props.get('class') }}
{{ $props.get('text') }}
```

## Using Slots

Slots are named outlets with markup inside them. Since writing HTML markup within props can quickly become messy, the slots provide a better authoring experience.

In the following example, we use the `@renderSlot` tag within the component to render the contents of the `main` slot.

See also: [Slots reference](./slots.md)

```edge
<button {{ $props.toAttrs() }}>
  @!renderSlot('main')
</button>
```

The contents of the `main` slot are place between the opening and the closing tags.

```edge
@component('components/button', {
  class: ['flex', 'align-center', 'space-x-4']
})
  <i class="fa-lock"></i>
  <span> Login </span>
@end
```

## Components as tags

Edge allows you to reference components as tags. So, instead of using the `@component` tag, you can use the component filename as the tag name to render it.


:::note

Components as tags only work for components stored inside the `components` directory.


:::


```edge
// title: views/component/modal.edge
<div class="modal">
  <header>
    @!renderSlot('header')
  </header>

  <main>
    @!renderSlot('content')
  </main>

  <footer>
    @!renderSlot('footer')
  </footer>
</div>
```

Given the above template is stored inside the `modal.edge` file, you can render it as follows.

```edge
@modal()
  @slot('header')
    <h2> Delete post </h2>
  @end

  @slot('content')
    <p> You are about to delete the post permanently </p>
  @end

  @slot('footer')
    <button> Yes, delete it </button>
    <button> Cancel </button>
  @end
@end
```

You can reference components as tags from named disks by prefixing the disk name with a dot separator.

```edge
{{-- Component as a tag from the uikit disk --}}
@!uikit.input()

{{-- Via component tag --}}
@!component('uikit::input')
```

### Filename to tagName conversion

You can reference the files stored within the `components` directory as tags inside your edge templates. The following transformation rules are applied when creating a tag from the file name.

| Template path | Tag name |
|---------------|------------|
| `form/input.edge` | `@form.input` |
| `tool_tip.edge` | `@toolTip` |
| `checkout_form/input.edge` | `@checkoutForm.input` |
| `modal/index.edge` | `@modal` |
