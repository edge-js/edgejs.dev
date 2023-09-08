---
summary: Partials are reusable templates with access to the state of the parent template
---

# Partials

Partials are markup fragments created to reuse them across different templates. Partials have access to all the data available to the parent template, including [inline variables](./templates_state.md#inline-variables).

You may include a partial inside a template using the `@include` tag. The tag accepts the relative path to the template file.

```edge
@include('partials/header')

<main>
  The main content goes here
</main>

@include('partials/footer')
```

You may include partials from other disks by prefixing the disk name before the template path.

```ts
@include('shared::partials/header')
@include('shared::partials/footer')
```

## Include conditionally

You may use the `@includeIf` method to conditionally include partials when a given statement returns `true`. For example:

```edge
@includeIf(post.comments.length, 'partials/comments')
```
