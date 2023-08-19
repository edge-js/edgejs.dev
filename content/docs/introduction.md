# Introduction

Edge is a **simple**, **modern**, and **batteries included** template engine for Node.js. Edge is similar to writing JavaScript. If you know JavaScript, you know Edge.

<div class="feature_highlight">

```edge
Hello {{ user.username }}!
```

::include{template="partials/features/hello_world"}

</div>


<div class="feature_highlight">

```edge
{{ user?.subscription.plan || 'Not subscribed' }}
```

::include{template="partials/features/js_expressions"}

</div>

<div class="feature_highlight">

```edge
@let(payments = await user.getPayments())
You have made {{ payments.length }} payments so far.
```

::include{template="partials/features/async_await"}

</div>

<div class="feature_highlight">

```edge
@if(user.hasSubscription)
  Hurray! You have access to over 280 videos.
@else
  Videos are available only to subscribers.
@end
```

::include{template="partials/features/conditionals"}

</div>

<div class="feature_highlight">

```edge
@each(comment in post.comments)
  @include('partials/comment')
@end
```

::include{template="partials/features/loops"}

</div>

<div class="feature_highlight">

```edge
@accordion()
  @accordion.item({ title: 'What is Edge?' })
    Edge is a template engine for Node.js
  @end

  @accordion.item({ title: 'Why should I use Edge?' })
    Because you need a template engine 🤷🏻‍♂️
  @end

  @accordion.item({ title: 'How can I support Edge?' })
    By becoming a sponsor on Github
  @end
@end
```

::include{template="partials/features/components"}

</div>

## Why another template engine?

Many will not ask this question because all the popular template engines in the Node.js ecosystem are on life support or have barely innovated in the past few years.

On the other hand, we have been using Edge inside [AdonisJS](https://adonisjs.com) for almost seven years and have implemented some great features like:

- Components and Slots.
- Provide/Inject API you might be familiar with from Vue.js.
- Icons integration with [Iconify](https://iconify.design/).

If we ignore all the above features. Edge still provides a great DX in comparison to many other template engines.

- Edge is not restrictive. You can write any JavaScript expression inside it.
- Accurate error stacks
- Simple mental model and no custom dialect
- Extensible API. 80% of Edge features are implemented using the public API.

## Edge vs Vue.js / React / Svelte?

Edge is a backend template engine completely different from frontend libraries like Vue.js or React.

- There is no Reactivity in Edge.
- It is not tied to any DOM implementation. Edge is not even tied to HTML.
- You do not have to compile Edge templates. They are compiled at runtime.

A better comparison will be Nunjucks vs. Edge or Pug vs. Edge.

## Edge vs JSX

While JSX is excellent, it is technically not a template engine. JSX is a JavaScript syntax extension for XML, which most of the web uses to generate HTML.

On the other hand, Edge is a template engine that you can embed inside any markup language. Be it Markdown, JSON, YAML, and, of course, HTML.

I like JSX for many reasons, including type safety (when used with TypeScript). Edge can be an excellent fit for individuals who enjoy the template engine's terse syntax and expressive nature. JSX can get verbose at times.

<!-- EXAMPLES GOES HERE -->

## Why should I use Edge?

If you like the simplicity of using backend template engines and have been looking for one that feels modern, refreshing, and does not force you to learn a new language, then Edge is for you.

If you swim in the `<your-frontend-framework>` ecosystem, you do not need Edge. Imagine, you never opened this website 😉

## Next steps

- Start with the [Getting Started](./getting_started.md) guide.
- Read the [Syntax specification](./syntax_specification.md) guide to understand the Edge templating syntax better.
<!-- - Browse [components examples](). -->

## Call for Contributors

We are seeking contributors to help us create syntax highlighting extensions for all mainstream code editors.

- **Sublime Text** - [Officially supported](https://github.com/edge-js/edge-sublime)
- **VSCode** - [Community contribution](https://marketplace.visualstudio.com/items?itemName=luongnd.edge). Haven't been updated in a long time and can be further improved.
- **Vim** - We need contributors to help us highlight Edge on Vim.
- **WebStorm** - Need contributors. The JetBrains issues tracker has an [open issue](https://youtrack.jetbrains.com/issue/WEB-41625/AdonisJS-.edge-template-engine-support) requesting a syntax highlighter.

## Sponsors

::include{template="partials/sponsors"}
