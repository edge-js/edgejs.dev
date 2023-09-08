---
summary: Learn how to loop over Arrays and Objects in Edge
---

# Loops

You can loop over `Objects` and `Arrays` using the `@each` tag. The `each` tag's inner workings are similar to the JavaScript `for of` loop.

```edge
@each(user in users)
  <li> {{ user.username }} </li>
@end
```

```edge
@each((user, index) in users)
  <li> {{ index + 1 }} {{ user.username }} </li>
@end
```

## Looping over objects

You can loop over JavaScript objects using the same `@each` tag. For example:

```ts
await edge.render('recipes', {
  food: {
    ketchup: '5 tbsp',
    mustard: '1 tbsp',
    pickle: '0 tbsp'
  }
})
```

```edge
@each((amount, ingredient) in food)
  <li> Use {{ amount }} of {{ ingredient }} </li>
@end
```

## Defining fallback content

The `@each` tag can be used with the `@else` tag to define fallback content in cases where the value is an empty array/object or undefined.

```edge
@each(comment in post.comments)
  @include('partials/comment')
@else
  <p> This post has not received any comments </p>
@end
```
