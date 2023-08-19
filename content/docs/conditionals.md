# Conditionals

You can write conditional blocks within Edge templates using the `@if`, `@elseif`, and the `@else` tags. The inner working of these tags is similar to the JavaScript `if/else` statements.

```edge
@if(user)
  <p> {{ user.username }} </p>
@end
```

```edge
@if(user.fullName)
  <p> Hello {{ user.fullName }}! </p>
@elseif(user.firstName)
  <p> Hello {{ user.firstName }}! </p>
@else
  <p> Hello Guest! </p>
@end
```

## The unless tag

Alongside the `@if` tag, you can also use the `@unless` tag to express **not if** statements. For example:

```edge
// title: With @if tag
@if(!account.isActive)
  <p>Please verify your email address to activate the account </p>
@end
```

```edge
// title: With @unless tag
@unless(account.isActive)
  <p>Please verify your email address to activate the account </p>
@end
```

## Using the ternary operator

You can write inline conditionals using the [JavaScript ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator).

```edge
<input
  class="input {{ hasError ? 'error' : '' }}"
/>
```
