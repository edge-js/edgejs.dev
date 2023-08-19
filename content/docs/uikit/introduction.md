# UIKit

The Edge UIKit is a headless components library that aims to extract repetitive parts of your templating layer without making any design choices for you. Following are some of the reason for using the Edge UIKit.

- **Unstyled**: All components are unstyled and you can design them as per your application design system.

- **Alpine.js for interactivity**: The frontend interactivity is implemented using Alpine.js. Therefore, your applications must install and use Alpine.js

- **Accessible**: All components are fully accessible as per [WAI-ARIA](https://www.w3.org/WAI/ARIA/apg/#aria_ex) guidelines.

## Installation
The UIKit packages includes primitives for both the frontend and the backend. The Alpine.js components are distributed as plugins and hence you can install and use the one's you need.

```sh
npm i edge-uikit@next
```

### Edge setup
Register the UIKit plugin with Edge.

```ts
import edge from 'edge.js'
import uiKit from 'edge-uikit'

edge.use(uiKit)
```

### Alpine.js setup
In order to use interactive components, you will have to install [Alpine.js](https://alpinejs.dev/) and cherry-pick the UIKit plugins you want to use.

:::note

Make sure you are using a bundler like Vite to compile your frontend assets.

:::

```sh
npm i -D alpinejs
```

```js
import Alpine from 'alpinejs'
import { tabs } from 'edge-uikit/tabs'

/**
 * Using the tabs plugin. It is required by the
 * tabs component.
 */
Alpine.plugin(tabs)

Alpine.start()
```

## Basic example
