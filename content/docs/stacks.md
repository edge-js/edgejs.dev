---
summary: Stacks allows creating inline placeholders in which other templates can push content
---

# Stacks

Stacks allow you to create inline placeholders for other templates to push content. For example, you can create a stack for inline JavaScript, and components/partials can push script tags inside it.

:::note

Stacks are available from `edge.js@6.1.0`. Before you start using them, make sure to update the package version.

:::

The `@stack` tag will create a named stack (it should be unique) in which other templates can push contents.

```edge
// title: components/layouts/main.edge
<!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    // highlight-start
    @stack('js')
    // highlight-end
  </head>
  <body>
    <main>
      @!dialog()
    </main>
  </body>
</html>
```

The `@pushOnceTo` tag will push the contents inside the earlier created stack. Since you may use the following component multiple times on a single page, we use the `pushOnceTo` tag to push the `script` tag only once. Otherwise, you will end up with multiple script tags.

```edge
// title: compontents/modal.edge
<dialog x-data="alpineModal">
</dialog>

// highlight-start
@pushOnceTo('js')
  <script>
    Alpine.data('alpineModal', function () {
      return {
        show() {},
        hide() {},
      }
    })
  </script>
@end
// highlight-end
```

Following will be the output HTML.

```html
<!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script>
      Alpine.data('alpineModal', function () {
        return {
          show() {},
          hide() {},
        }
      })
    </script>
  </head>
  <body>
    <main>
      <dialog x-data="alpineModal">
      </dialog>
    </main>
  </body>
</html>
```

Similar to the `@pushToOnce` tag, there is a `@pushTo` tag as well, which will push contents inside a named stack as many times as the component or the partial gets imported.
