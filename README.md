# Mustache naming conventions
A set of naming conventions when working with [Mustache](https://mustache.github.io/). Helping you write clear and maintainable templates, safe from data inheritance problems.

## Naming
Name your model properties by this formula...

```js
{
  TemplateName_valueName: "string"
  TemplateName_partialName: {
    PartialName_valueName: "string"
  }
}
```
...and use it in your templates:
```html
{{TemplateName_valueName}}

{{#TemplateName_partialName}}
  {{>PartialName}}
{{/TemplateName_partialName}}
```

## Example
Let's look at the sample "post"-template below. In the post we are rendering the post title (`{{title}}`), post content (`{{{content}}}`) and we're using a partial for the author information (`{{>author}}`):

`post.mustache`:
```html
<article>
  <h1>
    {{title}}
  </h1>
  
  {{{content}}}
    
  {{#author}}
    {{>author}}
  {{/author}}
</article>
```

`author.mustache`:
```html
<div>
  <p>Posted by {{name}}, {{title}}</p>
</div>
```
When rendering the post, we are using this model:

```js
{
  title: "An interesting article!",
  content: "<p>The content of my article is great.</p>"
  author: {
    name: "Jane Doe",
    title: "anthropology professor"
  }
}
```

Everything works fine right now, but what if the author `title` was somehow missing?

Due to the **inheritance** in Mustache, it would cause the rendered result to use the `title`-entry, one level up from the `author`-entry, when rendering the author partial. Giving us this incorrect result:

```html
<article>
  <h1>
    An interesting article!
  </h1>
  
  <p>The content of my article is great.</p>
  
  <div>
    <p>Posted by Jane Doe, An interesting article!</p>
  </div>
</article>
```

By using this set of naming conventions, we solve the problem in the above scenario, and make our templates more maintainable:

`Post.mustache`:
```
<article>
  <h1>
    {{Post_title}}
  </h1>
  
  {{{Post_content}}}
  
  {{#Post_author}}
    {{>Author}}
  {{/Post_author}}
</article>
```

`Meta.mustache`:
```
<div>
  <p>A post by {{Author_name}}, {{Author_title}}</p>
</div>
```

Model:
```js
{
  Post_title: "An interesting article!",
  Post_content: "<p>The content of my article is great.</p>"
  Post_author: {
    Author_name: "Jane Doe",
    Author_title: "anthropology professor"
  }
}
```
