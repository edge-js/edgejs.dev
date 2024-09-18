---
summary: Learn how to define markup using slots
---

# Slots

Slots are named outlets you can define within the component opening and closing tag using the `@slot` tag. The component can access slots as functions via the `$slots` object.

Let's create a card component and use slots to render different card sections.

```edge
// title: views/components/card.edge
@let(attributes = $props
  .merge({
    class: ['card']
  })
  .toAttrs()
)

<div {{ attributes }}>
  <div class="card_header">
    {{{ await $slots.header() }}}
  </div>

  <div class="card_contents">
    {{{ await $slots.content() }}}
  </div>
</div>
```

Now, let's use the `card` component and define its contents using slots.

```edge
@card({ class: ['card-lg', 'card-shadow'] })
  @slot('header')
    <strong> Quick start </strong>
  @end
  
  @slot('content')
    <p> Start building your next project in minutes </p>
  @end
@end
```

## Main and named slots

In the previous example, we used named slots to define contents for multiple outlets. However, you can use the `main` slot if a component needs just one slot.

The main slot refers to all the contents inside the component's opening and closing tag. In the following example, we accept the card title as a prop and its content as a `main` slot.

```edge
<div {{ attributes }}>
  <div class="card_header">
    {{ title }}
  </div>

  <div class="card_contents">
    {{{ await $slots.main() }}}
  </div>
</div>
```

```edge
@card({ title: 'Quick start' })
  <p> Start building your next project in minutes </p>
@end
```

## Slots scopes

Slots defined using the `@slot` tag can access the state of the current template. They do not have access to the component state.

In the following example, we define the `cardSize` and the `sizes` variables. These variables are available only to the component, not the parent template slots.

```edge
// title: views/components/card.edge
// highlight-start
@let(cardSize = 'medium')

@let(sizes = {
  medium: '350px',
  small: '200px',
  large: '450px'
})
// highlight-end

<div class="{{ sizes[cardSize] }}">
  <div class="card_header">
    {{{ await $slots.header() }}}
  </div>

  <div class="card_contents">
    {{{ await $slots.content() }}}
  </div>
</div>
```

```edge
@card()
  @slot('header')
    <strong> Quick start </strong>
  @end
  
  // highlight-start
  @slot('content')
    {{-- The value of cardSize will be undefined --}}
    <p> I am a {{ cardSize }} card </p>
  @end
  // highlight-end
@end
```

However, a component can pass data to the slot when rendering it. Let's look at the following example:

```edge
@let(cardSize = 'medium')

@let(sizes = {
  medium: '350px',
  small: '200px',
  large: '450px'
})

<div class="{{ sizes[cardSize] }}">
  <div class="card_header">
    // highlight-start
    {{{ await $slots.header({ sizes, cardSize }) }}}
    // highlight-end
  </div>

  <div class="card_contents">
    // highlight-start
    {{{ await $slots.content({ sizes, cardSize }) }}}
    // highlight-end
  </div>
</div>
```

Now, you can access the shared state inside the `@slot` component as follows.

```edge
@card()
  @slot('header')
    <strong> Quick start </strong>
  @end
  
  // highlight-start
  @slot('content', componentState)
    <p> I am a {{ componentState.cardSize }} card </p>
  @end
  // highlight-end
@end
```
