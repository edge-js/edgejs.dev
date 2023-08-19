# Props

Props are the primary way to share data with a component when rendering it. A component can accept any props without maintaining a list of allowed props.

```edge
// title: views/components/input.edge
<input 
  type="{{ type || 'text' }}"
  placeholder="{{ placeholder || '' }}"
  name="{{ name }}"
  id="{{ name }}"
  value="{{ value: '' }}"
/>
```

Let's render the `input` component and pass it some props.

```edge
@!input({
  name: 'title',
  placeholder: 'Enter post title'
})

@!input({
  name: 'slug',
  placeholder: 'Enter post slug'
})
```

## Serializing props to HTML attributes

Right now, we are manually binding props to HTML attributes on the `input` element. This approach cannot scale because we must explicitly add support for every single HTML attribute.

You may use the `$props` object to serialize all the props to HTML attributes using the `toAttrs` method. All props will be serialized to a string and applied to the `input` element in the following example.

```edge
<input {{ $props.toAttrs() }} />
```

Let's take a step further and assign default styling classes to our input element using the `.merge` method. If you pass any additional classes when using the component, they will be merged with the default classes.

```edge
<input {{
  $props.merge({ class: ['input'] }).toAttrs()
}} />
```


<table style="table-layout: fixed;">

<thead>
  <tr>
    <th width="40%"> Input </th>
    <th width="60%"> Output </th>
  </tr>
</thead>

<tr>

<td>

```edge
@input({
  type: 'text',
  name: 'title',
  id: 'title',
})
```

</td>

<td>

```html
<input
  type="text"
  name="title"
  id="title"
  class="input"
/>
```

</td>
</tr>

<tr>

<td>

```edge
@input({
  type: 'text',
  name: 'title',
  id: 'title',
  class: [
    'input-medium',
    'input-rounded'
  ]
})
```

</td>

<td>

```html
<input
  type="text"
  name="title"
  id="title"
  class="input input-medium input-rounded"
/>
```

</td>
</tr>

</table>

### Removing existing classes

Props serialization offers no inbuilt APIs to remove existing classes using the `merge` method. However, you can use an additional prop someone can pass to ignore existing classes.

```edge
<input {{
  $props
    .mergeUnless(removeExistingStyles, { class: ['input'] })
    .except(['removeExistingStyles'])
    .toAttrs()
}} />
```

The default classes will be applied unless you pass the `removeExistingStyles` prop when rendering the component.

```edge
@!input({
  removeExistingStyles: true,
  class: ['flex', 'mt-2', 'mb-4', 'border']
})
```

## Props API

Following is the list of methods available on the `$props` object.

### has

Find if a given prop exists.

```edge
{{ $props.has('text') }}
```

### get

Get value for a given prop.

```edge
{{ $props.get('text') }}
```

### only

Get a new props object with only the mentioned keys.

```edge
{{ $props.only(['text', 'class']).get('text') }}
```

### except

Get a new props object except for the mentioned keys.

```edge
{{ $props.except(['text', 'size']) }}
```

### merge/mergeIf/mergeUnless

Merge custom properties with the props values. The props values have priority over custom properties.

In the following example, the value of the `type` property will be `text` unless an explicit value is provided at the time of rendering the component.

```edge
<button {{ $props.merge({ type: 'text' }).toAttrs() }}>
</button>
```

```edge
@!component('components/button', { type: 'reset' })
```

Output HTML

```html
<button type="reset">
</button>
```
