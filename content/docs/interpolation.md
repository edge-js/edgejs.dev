# Interpolation

Interpolation refers to embedding the output of JavaScript expressions alongside the raw text markup. EdgeJS uses double curly braces `{{ }}` as delimiters. 

```edge
Hello {{ username }}!
```

Given the username is `Virk`. The output will be

```
Hello Virk!
```

You can use any valid [JavaScript expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#expressions) inside the curly braces, and Edge will evaluate it for you.

```edge
{{ user.username }}
{{ user.username.toUpperCase() }}
{{ (2 + 2) * 3 }}
{{ (await getUser()).username }}
```

## Multiline expressions

Expressions can also span across multiple lines. For example:

```edge
Hello {{
  users.map((user) => {
    return user.username
  })
}}
```

When writing multiline expressions, ensure the double curly braces are in the same line.

<table>

<thead>
<tr>
<th>
Invalid ❌
</th>

<th>
Valid ✅
</th>
</tr>
</thead>

<tbody>

<tr>
<td>

```edge
{
{
  users.map((user) => {
    return user.username
  })
}}
```

```edge
{{
  users.map((user) => {
    return user.username
  })
}
}
```

</td>
<td>

```edge
{{
  users.map((user) => {
    return user.username
  })
}}
```

</td>
</tr>

</tbody>

</table>


## Stringified output

Since the output of a template is always a `string`, the output of a JavaScript expression is also converted to a string by wrapping the output inside the [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/String#return_value) function.

Given the following expression

```edge
// title: Expression
{{
  users.map((user) => {
    return user.username
  })
}}
```

The JavaScript output will be

```ts
// title: JavaScript output code
String(users.map((user) => {
  return user.username
}))
```

The stringified output will be

```txt
// title: Output
virk,romain,julien,michael
```

If you do not want to rely on the default behavior, you can self-convert the array to a string using the JavaScript `Array.join` method.

```edge
Hello {{
  users.map((user) => {
    return user.username
  }).join(', ')
}}
```

## Escaped HTML output

The output of a JavaScript expression is HTML escaped to prevent your applications from XSS attacks.

Given the following HTML string

```edge
{{
 '<span style="color: red">This should be red.</span>'
}}
```

The output will be 

```
&lt;span style="color: red"&gt;This should be red.&lt;/span&gt;
```

If you want to render HTML as it is, you can either wrap it inside triple curly braces or use the `html.safe` global method.

```edge
{{{
 '<span style="color: red">This should be red.</span>'
}}}
```

```edge
{{
  html.safe(
    '<span style="color: red">This should be red.</span>'
  )
}}
```

## Skipping curly braces from evaluation 

If you are using Edge in combination with a frontend framework that also uses double curly braces, you can instruct Edge to skip certain statements by prefixing the braces with the `@` symbol.

```edge
{{-- Input -- }}
Edge should not parse @{{ username }}

{{-- Output -- }}
Edge should not parse {{ username }}
```
