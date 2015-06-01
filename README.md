# Mustache naming conventions
A set of naming conventions when working with [Mustache](https://mustache.github.io/) templates. It will make your tag names clear, and model inheritance more maintainable.

## Naming
Name your model properties by this formula...

```
{
  NameOfTemplate_nameOfProperty: "This is the content!"
}
```
...and use it in your templates:
```
{{NameOfTemplate_nameOfProperty}}
```

## Example
Let's look at the sample "post"-template below. In the post we are rendering the post title (`{{title}}`) and content (`{{content}}`). We are also using a partial for the meta information (`{{>meta}}`):

`post.mustache`:
```
<article>
  <h1>
    {{title}}
  </h1>
  <p>
    {{content}}
  </p>
  {{#meta}}
    {{>meta}}
  {{/meta}}
</article>
```

`meta.mustache`:
```
<div>
  <p>
    Posted at: {{content}}
  </p>
  <p>
    Author: {{author}}
  </p>
</div>
```
When rendering the post, we are using this model:

```
{
  title: "This is my first post!",
  content: "The content of my first post is great."
  meta: {
    content: "2015-06-01",
    author: "Jane Doe"
  }
}
```

Everything works fine right now, but what if we are missing the date for the meta? Then the model would look like this:

```
{
  title: "This is my first post!",
  content: "The content of my first post is great."
  meta: {
    author: "Jane Doe"
  }
}
```

Due to the inheritance in Mustache, this would cause the rendered result to use the `content`-entry, one level up from the `meta`-entry, when rendering the meta partial, giving us this result:

```
<article>
  <h1>
    This is my first post!
  </h1>
  <p>
    The content of my first post is great.
  </p>
  <div>
    <p>
      Posted at: The content of my first post is great.
    </p>
    <p>
      Author: Jane Doe
    </p>
  </div>
</article>
```

By using this set of naming conventions, we solve the problem in the above scenario, and we make our templates more maintainable:

`Post.mustache`:
```
<article>
  <h1>
    {{Post_title}}
  </h1>
  <p>
    {{Post_content}}
  </p>
  {{#Post_meta}}
    {{>Meta}}
  {{/Post_meta}}
</article>
```

`Meta.mustache`:
```
<div>
  <p>
    Posted at: {{Meta_content}}
  </p>
  <p>
    Author: {{Meta_author}}
  </p>
</div>
```
```
{
  Post_title: "This is my first post!",
  Post_content: "The content of my first post is great."
  Post_meta: {
    Meta_content: "2015-06-01",
    Meta_author: "Jane Doe"
  }
}
```
