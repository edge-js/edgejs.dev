# Upgrading to version 6

The latest release of Edge contains a handful of breaking changes, some performance improvements and most importantly works only with ES modules. This guide covers the steps you must follow to upgrade to `edge.js@6`.

## Upgrading packages
You can install the latest release from the npm packages registry. For now, the version 6 is tagged with `@next` tag.

```sh
npm i edge.js@next
```

The functionality of [`edge-supercharged`](https://github.com/edge-js/edge-supercharged) plugin is now baked into Edge directly, hence you can uninstall this package and remove its usage.

```sh
npm uninstall edge-supercharged
```

If you were using [`edge-stacks`](https://github.com/edge-js/stacks), and [`edge-iconify`](https://github.com/edge-js/edge-iconify), make sure to update these packages as well.

```sh
npm i edge-stacks@next
npm i edge-iconify@next
```

## Compatibility plugin
If your projects are not ready for the breaking changes (except ESM migration), you can configure the `migrate` plugin that provides compatibility between version 5 and 6.

```ts
import edge from 'edge.js'
import { migrate } from 'edge.js/plugins/migrate'

edge.use(migrate)
```

## Breaking changes

- All of the packages are now ESM only. 
- Requires `Node.js >= 18.16.0`.
- Layouts and sections have been removed. We recommend using components instead. Configure [compatibility plugin](#compatibility-plugin) to continue using layouts.
- Changes to the [Props API](#changes-to-props-api).
- Removed `@set` tag in favor of `@let` and `@assign` tags. [Learn more](#removing-set-tag-in-favor-of-let-and-assign-tags).
- Changes to global helpers.

## Changes to Props API
The components props have been changed completely, and methods like `serialize` and `serializeExcept` no longer exist. Instead, you have to use the `toAttrs` method.

:::note
You can get back the old Props API using the [compatibility plugin](#compatibility-plugin)
:::

```ts
/**
 * Serialize all attributes
 */
// delete-start
$props.serialize()
// delete-end
// insert-start
$props.toAttrs()
// insert-end

/**
 * Serialize all except the mentioned attributes
 */
// delete-start
$props.serializeExcept(['text'])
// delete-end
// insert-start
$props.except(['text']).toAttrs()
// insert-end

/**
 * Serialize only the mentioned attributes
 */
// delete-start
$props.serializeOnly(['class'])
// delete-end
// insert-start
$props.only(['class']).toAttrs()
// insert-end

/**
 * Merge custom attributes
 */
// delete-start
$props.serializeOnly(['class'], { type: 'text' })
// delete-end
// insert-start
$props.only(['class']).merge({ type: 'text' }).toAttrs()
// insert-end
```

## Removing `@set` tag in favor of `@let` and `@assign` tags
The `@set` tag has been removed in the favor of new `@let` and the `@assign` tags. Their inner workings and syntax are both different from the set tag.

:::note
You can add back support for the `@set` tag using the [compatibility plugin](#compatibility-plugin)
:::

```edge
/**
 * Define new variable
 */
// delete-start
@set('username', 'virk')
// delete-end
// insert-start
@let(username = 'virk')
// insert-end

/**
 * Update existing variable
 */
// delete-start
@set('username', 'romain')
// delete-end
// insert-start
@assign(username = 'romain')
// insert-end

/**
 * Mutate object properties
 */
// delete-start
@set(user, 'username', 'romain')
// delete-end
// insert-start
@assign(user.username = 'romain')
// insert-end
```

## Global helpers changes

- Remove `e` method in favor of `html.escape`.
- Remove `stringify` method in favor of `js.stringify`.
- Remove `safe` method in favor of `html.safe`.
- Remove `raise` method. It was never documented.

```edge
// delete-start
{{ e(post.content) }}
// delete-end
// insert-start
{{ html.escape(post.content) }}
// insert-end

// delete-start
{{ stringify(someJSONObject) }}
// delete-end
// insert-start
{{ js.stringify(someJSONObject) }}
// insert-end

// delete-start
{{ safe(post.content) }}
// delete-end
// insert-start
{{ html.safe(post.content) }}
// insert-end
```
