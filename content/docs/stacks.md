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
      // highlight-start
      @stack('world')
      // highlight-end
      @!dialog()
    </main>
  </body>
</html>
```
You can interact with Stacks by using one of four tags: `@pushTo`, `@pushOnceTo`, `@pushToTop` and `@pushOnceToTop`.

Both `@pushTo` and `@pushToTop` can be used as many times as you want, and will push whatever's within the tag to the stack every time.

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

Now let's fill out the `world` stack with some content:

```edge
// title: compontents/hello-world.edge

// highlight-start
@pushTo('world')
  <p>hello!</p>
@end
// highlight-end
```

Now, our output will look like this:

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
      <p>hello!</p>
    </main>
  </body>
</html>
```

If we wanted to add something above `<p>`, we could have used `@pushToTop` instead of `@pushTo`.

```edge
// title: compontents/hello-world.edge

@pushTo('world')
  <p>hello!</p>
@end

// highlight-start
@pushToTop('world')
  <h1>hey!</h1>
@end
// highlight-end
```

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
      <p>hey!</p>
      <p>hello!</p>
    </main>
  </body>
</html>
```

Combining both `pushToTop` and `pushOnceTo`, `pushToTopOnce` will push the content to the top of the stack only once, even if you use it multiple times.
