# Svg Icons (via Iconify)

Edge has an official integration for rendering icons using [Iconify](https://icon-sets.iconify.design/). Iconify has over 100 open-source icon sets you can reference using a unified API.

## Installation
The Edge and Iconify integration is a plugin you must install from the npm packages registry.

```sh
npm i edge-iconify
```

The next step is to register the plugin with Edge.

```ts
import { Edge } from 'edge.js'
import { edgeIconify } from 'edge-iconify'

const edge = Edge.create()

/**
 * Register the plugin
 */
edge.use(edgeIconify)
```

### Installing Iconify bundles

Before you can render icons, you will have to install the Iconify icon bundles. Iconify provides various distribution bundles. However, the Edge integration works only with JSON collections.

Let's install and use the [HeroIcons](https://icon-sets.iconify.design/heroicons/) icon set.

```sh
npm i @iconify-json/heroicons
```

```ts
import { Edge } from 'edge.js'
// highlight-start
import { edgeIconify, addCollection } from 'edge-iconify'
import { icons as heroIcons } from '@iconify-json/heroicons'

/**
 * Add heroIcons collection
 */
addCollection(heroIcons)
// highlight-end

const edge = Edge.create()

/**
 * Register the plugin
 */
edge.use(edgeIconify)
```

## Usage
Once the setup is completed, you can render icons using the `@svg` tag. The `svg` tag accepts the icon identifier and an optional set of attributes to define on the output SVG element.

```edge
@svg('heroicons:arrow-left-solid')
```

Output

```html
<svg width="1em" height="1em" viewBox="0 0 24 24"><path fill="currentColor" fill-rule="evenodd" d="M11.03 3.97a.75.75 0 0 1 0 1.06l-6.22 6.22H21a.75.75 0 0 1 0 1.5H4.81l6.22 6.22a.75.75 0 1 1-1.06 1.06l-7.5-7.5a.75.75 0 0 1 0-1.06l7.5-7.5a.75.75 0 0 1 1.06 0Z" clip-rule="evenodd"></path></svg>
```

In the following example, we define custom width, height, and color for the icon.

```edge
@svg('heroicons:arrow-left-solid', {
  width: 40,
  height: 40,
  color: 'purple'
})
```

Output

```html
<svg width="40" height="40" viewBox="0 0 24 24" color="purple"><path fill="currentColor" fill-rule="evenodd" d="M11.03 3.97a.75.75 0 0 1 0 1.06l-6.22 6.22H21a.75.75 0 0 1 0 1.5H4.81l6.22 6.22a.75.75 0 1 1-1.06 1.06l-7.5-7.5a.75.75 0 0 1 0-1.06l7.5-7.5a.75.75 0 0 1 1.06 0Z" clip-rule="evenodd"></path></svg>
```

### Inline rendering within markup
You can use the `svg` global helper to render the SVG icons alongside the raw text markup (without a line break).

```edge
<button> {{ svg('heroicons:archive-box') }} Delete post <button>
```

## Finding the icon identifier
You can view the available icons from the [Iconify icons set browser](https://icon-sets.iconify.design). 

- Start by selecting the icon set you are using.
- Search for the icon you want to use.
- Click and copy its identifier displayed next to the Icon preview.

::video{url="https://res.cloudinary.com/adonis-js/video/upload/v1692263692/icon-search_hi608m.mp4"  controls="true"}

## Finding and installing icon bundles
Unfortunately, we have not seen a detailed installation guide on the Iconify website for their JSON collections. You can use the following table to reference the collections and their npm package name.

:::note

The following table is aggregated from the [Iconify collections.json](https://github.com/iconify/icon-sets/blob/master/collections.json) file.
:::

::include{template="partials/iconify-icons-set-ref-table"}
