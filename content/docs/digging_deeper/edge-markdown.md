---
summary: Learn how to use Edge Markdown to transform Markdown documents into HTML with support for MDC components, GitHub Flavored Markdown, and enhanced code blocks.
---

# Edge Markdown
Edge Markdown builds on top of [Remark](https://unifiedjs.com/explore/package/remark/) and [MDC](https://github.com/nuxtlabs/remark-mdc) to transform Markdown documents into HTML.

**MDC (Markdown Components)** is an extended Markdown format that allows you to use components directly inside Markdown. This makes it easy to combine prose and interactive elements in the same document.

The following are some of the key features of Edge Markdown.

- **GitHub Flavored Markdown (GFM)**: Support for GFM features such as tables, task lists, autolinks, and strikethrough.

- **Embedding Edge components**: Use Edge components inline within your Markdown files using MDC syntax.

- **Enhanced code blocks**: Code samples are rendered with **Shiki**, offering syntax highlighting, code block titles, line highlighting, and inline diffs.

- **Custom prose rendering**: Standard Markdown elements (images, headings, lists, paragraphs, etc.) can be rendered using your own custom components.

- **AST inspection utilities**: Since Markdown is compiled into an AST, you can analyze nodes to detect broken links, validate references, or rewrite image sources.

## Installation
Install the `edge-markdown` plugin from the npm packages registry.

```sh
npm i edge-markdown
```

The next step is to register the plugin with Edge.

```ts
import edge from 'edge.js'
import { edgeMarkdown } from 'edge-markdown'

edge.use(edgeMarkdown())
```

## Basic usage
Once the plugin has been configured, you may render Markdown content and files using the `@markdown` tag. You can either specify an absolute path to the markdown file or specify the raw content using the `content` property.

```edge
@markdown({
  file: absolutePathToMdFile,
})

@markdown({
  content: contentAsString,
})
```

The `@markdown` tag writes the generated HTML to the output. If you want to access additional properties like the document's frontmatter and table of contents, consider using the `$markdown.render` method.

```edge
@let(doc = await $markdown.render({
  file: absolutePathToMdFile,
}))

<h1>{{{ doc.frontmatter.title }}}</h1>
<div>{{{ doc.content }}}</div>

<div>{{{ doc.toc }}}</div>
```

## Configuration options
The `edgeMarkdown` function accepts various configuration options to customize the Markdown processing behavior. You can control component loading, syntax highlighting, HTML handling, table of contents generation, and plugin integration.

```ts
edge.use(edgeMarkdown({
  prefix: 'markdown',
  highlight: true,
  allowHTML: true,
  toc: {
    enabled: true,
    maxDepth: 2,
  },
  remarkPlugins: [],
  rehypePlugins: [],
  components: {},
  allowedTags: [],
  hooks: [
    (node) => {},
  ]
}))
```

<dl>
  <dt> prefix </dt>
  <dd>

  The `prefix` option defines the prefix to use for finding custom components within the `components` directory. For example, the `note` component by default will be loaded from the `components/markdown/note.edge` file.

  </dd>

  <dt>highlight</dt>
  <dd>

  Enable/disable the default Shiki setup for highlighting the code blocks. If you decide to disable it, then use a custom rehype plugin for processing code blocks.

  </dd>

  <dt>allowHTML</dt>
  <dd>

  Enable/disable the usage of HTML tags within Markdown. You should disable it when rendering user-authored content.

  </dd>

  <dt>toc</dt>
  <dd>

  Enable/disable the creation of a table of contents. The `toc` option accepts the same set of options accepted by the [`mdast-util-toc`](https://github.com/syntax-tree/mdast-util-toc?tab=readme-ov-file#options) package.

  </dd>

  <dt>allowedTags</dt>
  <dd>

  You may specify an array of HTML tags to render, and once defined, all other tags will be skipped. This setting may be helpful if you are converting Markdown content to an email and want to restrict the usage to specific HTML tags.

  </dd>

  <dt>remarkPlugins</dt>
  <dd>

  Register an array of remark plugins.

  </dd>

  <dt>rehypePlugins</dt>
  <dd>

  Register an array of rehype plugins.

  </dd>

  <dt>hooks</dt>
  <dd>

  The `hooks` property is an array of functions that are executed for every AST node. The AST nodes will be an `Element`, `Text`, or `Comment` from the [HAST](https://github.com/syntax-tree/hast) syntax tree.

  </dd>
</dl>

## Generating TOC
The HTML for the table of contents can be accessed from the return value of the `$markdown.render` method.

By default, headings up to level 2 are used to generate the TOC. However, you may configure TOC generation settings via the `toc` configuration option.

```edge
@let(doc = await $markdown.render({
  file: absolutePathToMdFile,
  toc: {
    maxDepth: 2
  }
}))

<div>
  {{{ doc.content }}}
</div>

<div>
  {{{ doc.toc }}}
</div>
```

## Configuring Shiki
By default, Edge Markdown uses Shiki for syntax highlighting with sensible defaults. You can customize the Shiki configuration by passing a custom theme and languages.

```ts
import { edgeMarkdown } from 'edge-markdown'

edge.use(edgeMarkdown({
  shiki: {
    theme: 'github-dark',
    langs: ['javascript', 'typescript', 'json']
  }
}))
```

You can also disable Shiki entirely and use your own rehype plugin for code highlighting.

```ts
edge.use(edgeMarkdown({
  highlight: false,
  rehypePlugins: [yourCustomCodePlugin]
}))
```

### Enabled transformers
The following [transformers](https://shiki.style/packages/transformers) from Shiki are enabled by default.

| Transformer | Usage |
|--------------|--------|
| `transformerNotationDiff` | Display diff markers using the `[!code --]` and `[!code ++]` inline comments. |
| `transformerNotationHighlight` | Highlight the current line using the `[!code highlight]` inline comment. |
| `transformerNotationWordHighlight` | Highlight a given word using the `[!code word:Hello]` comment before the line on which the word should be highlighted. |

Along with that, the metadata from the codeblock backticks is made available via the pre tag `node.properties` property.

````md
```ts title=start/routes.ts
console.log('Highlighted') // [!code highlight]
console.log('Not highlighted')

console.log('hewwo') // [!code --]
console.log('hello') // [!code ++]

// [!code word:Hello]
const message = 'Hello World'
```
````

## Displaying errors and warnings
The errors and warnings generated during the Markdown rendering process can be accessed using the `messages` property returned by the `$markdown.render` method.

These may include default and custom error messages reported using remark and rehype plugins. Every message is an instance of the [VFileMessage](https://unifiedjs.com/explore/package/vfile-message/) class.

```edge
@let(doc = await $markdown.render({
  file: absolutePathToMdFile
}))

@each(message in doc.messages)
  {{ message.line }}:{{ message.column }} {{ message.reason }}
@end
```

## Parsing front-matter
The front-matter from all the Markdown files is auto-extracted, and you may access it using the `frontmatter` property returned by the `$markdown.render` method.

```edge
@let(doc = await $markdown.render({
  file: absolutePathToMdFile
}))

{{ doc.frontmatter.title }}
{{ doc.frontmatter.summary }}
```

If you only need the frontmatter without rendering the Markdown content, use the `$markdown.frontmatter` method. This is a lightweight operation that parses only the YAML frontmatter and skips the entire remark/rehype pipeline.

```edge
@let(meta = await $markdown.frontmatter({
  file: absolutePathToMdFile
}))

{{ meta.title }}
{{ meta.summary }}
```

The `$markdown.frontmatter` method accepts a `file` or `content` property, just like `$markdown.render`.

```edge
@let(meta = await $markdown.frontmatter({
  content: rawMarkdownString
}))
```

## Rendering a preview
You can render a preview of a Markdown document using the `$markdown.preview` method. This method renders only the content before the first `h2` heading, which is useful for generating article summaries or content previews on listing pages.

```edge
@let(doc = await $markdown.preview({
  file: absolutePathToMdFile
}))

<article>
  <div>{{{ doc.content }}}</div>
</article>
```

The `$markdown.preview` method accepts the same options as `$markdown.render` and returns `content`, `frontmatter`, and `messages` properties. Table of contents generation is automatically disabled for previews.

If the document has no `h2` heading, the full content is rendered.

## Authoring components
You can create reusable components to enhance your Markdown content with interactive elements and custom styling. Edge Markdown uses MDC (Markdown Components) syntax to embed Edge components directly within Markdown files.

Components are automatically loaded from the `components/[prefix]` directory, where the prefix defaults to `markdown`. This allows you to organize your Markdown-specific components separately from your regular Edge components.

See also: [MDC components syntax reference](https://github.com/nuxtlabs/remark-mdc?tab=readme-ov-file#syntax)

Create a component file at `components/markdown/alert.edge`:

```edge
// title: components/markdown/alert.edge
<div class="alert alert-{{ type ?? 'note' }}">
  <h3>{{ title }}</h3>
  @markdownSlot()
</div>
```

Then use it in your Markdown files:

```mdc
::alert{title="Important" type="warning"}
This is a warning alert with **markdown content** inside.
::

::alert{title="Tip"}
You can also use components with slots.
::
```

### Using slots
MDC [allows using slots](https://github.com/nuxtlabs/remark-mdc?tab=readme-ov-file#-slots) within Markdown components. The slots are defined using the `#` tag followed by the slot name, and you can render their content using the `@markdownSlot` tag.

```mdc
::hero
Default slot text

#description
This will be rendered inside the `description` slot.
::
```

```edge
// title: components/markdown/hero.edge
<div>
  <h1>
    @markdownSlot()
  </h1>

  <div>
    @markdownSlot('description')
  </div>
</div>
```

## Rendering prose elements as components
You can override how standard Markdown elements are rendered by creating components with specific names. This allows you to customize the output of headings, paragraphs, images, and other elements.

In the following example, we create custom components inside the `components/markdown` directory for rendering the `img` and the `h2` tags.

```edge
// title: components/markdown/img.edge
<figure>
  <img src="{{ src }}" alt="{{ alt }}" />
  @if(title)
    <figcaption>{{ title }}</figcaption>
  @end
</figure>
```

```edge
// title: components/markdown/h2.edge
<h2 id="{{ id }}" class="section-heading">
  @markdownSlot()
</h2>
```
