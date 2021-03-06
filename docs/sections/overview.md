**Heads up!**
{%= docs("sections/cli-notice") %}

**Features**
{%= docs("sections/features") %}

## Install verb-cli
{%= docs("sections/install-verb-cli") %}

## .verb.md
{%= docs("sections/verb-md") %}

## verbfile.js
{%= docs("sections/verbfile") %}

***

# Template API

> See [Template](https://github.com/jonschlinkert/template) for all available methods.

### .data

> Load data to pass to templates.

Any of these work:

```js
verb.data({foo: 'bar'});
verb.data('package.json');
verb.data(['foo/*.{json,yml}']);
```

### .helper

> Add helpers to be used in templates.

```js
verb.helper('read', function(filepath) {
  return fs.readFileSync(filepath, 'utf8');
});

//=> {%%= read("foo.txt") %}
```

### .partial

> Add partials to be used in other templates.

```js
verb.partial('notice', { content: '<strong>...</strong>' });
verb.partial('banner', { content: '/*! Copyright (c) 2014 Jon Schlinkert, Brian Woodward... */' });
// or load a glob of partials
verb.partials('partials/*.md');

// optionally pass locals, all template types support this
verb.partials('partials/*.md', {site: {title: 'Code Project'}});
```

**Usage**

Use the `partial` helper to inject into other templates:

```js
{%%= partial("banner") %}
```

Get a cached partial:

```js
var banner = verb.cache.partials['banner'];
```

### .page

> Add pages that might be rendered (really, any template is renderable, pages fit the part though)

```js
verb.page('toc.md', { content: 'Table of Contents...'});
// or load a glob of pages
verb.pages('pages/*.md', {site: {title: 'Code Project'}});
```

Use the `page` helper to inject pages into other templates:

```js
{%%= page("toc") %}
```

Get a cached page:

```js
var toc = verb.cache.pages['toc'];
```

Pages are `renderable` templates, so they also have a `.render()` method:

```js
var toc = verb.cache.pages['toc'];
// async
toc.render({}, function(err, content) {
  console.log(content);
});

// or sync
var res = toc.render();
```

**Params**

 - `locals` **{Object}**: Optionally pass locals as the first arg
 - `callback` **{Function}**: If a callback is passed, the template will be rendered async, otherwise sync.


### .layout

> Add layouts, which are used to "wrap" other templates:

```js
verb.layout('default', {content: [
  '<!DOCTYPE html>',
  '  <html lang="en">',
  '  <head>',
  '    <meta charset="UTF-8">',
  '    <title>{%%= title %}</title>',
  '  </head>',
  '  <body>',
  '    <<% body %>>', // `body` is the insertion point for another template
  '  </body>',
  '</html>'
].join('\n')});

// or load a glob of layouts
verb.layouts('layouts/*.md', {site: {title: 'Code Project'}});
```

Layouts may be use with any other template, including other layouts. Any level of nesting is also possible.

**Body tags**

Layouts use a `body` as the insertion point for other templates. The syntax verb uses for the `body` tag is:

```js
<<% body %>>
```

Admittedly, it's a strange syntax, but that's why we're using it. Verb shouldn't collide with templates that you might be using in your documentation.


**Usage**

Layouts can be defined in template locals:

```js
// either of these work (one object or two)
verb.page('toc.md', { content: 'Table of Contents...'}, { layout: 'default' });
verb.partial('foo.md', { content: 'partial stuff', layout: 'block' });
```

Or in the front matter of a template. For example, here is how another layout would use our layout example from earlier:

```js
// using this 'inline' template format to make it easy to see what's happening
// this could be loaded from a file too
verb.layout('sidebar', {content: [
  '---',
  'layout: default',
  '---',
  '<div>',
  ' <<% body %>>',
  '</div>'
].join('\n')});
```

# Task API

{%= apidocs("index.js") %}


## Why Verb instead of X?

I created Verb to help me maintain my own projects. Any time that is spent maintaining a project that could be automated instead, is time that is being taken away from real productivity.

**Verb does exactly what I needed**

To that end, I wanted a documentation generator that would work in the following scenarios:

- **simple, no config**: most projects don't need complicated docs. e.g. "just build the readme and don't ask me questions."
- **handle the boilerplate stuff**: APIs change from project to project, but my name doesn't, my github URL doesn't, and my choice of licence doesn't. Verb should know those things.
- **don't ask me questions**: I just want to run `verb`, and it should work. No setup or config. There is more than enough data in package.json to handle the boilerplate part of a readme.
- **generate API docs**: When I want [API docs](#api-docs), I should have to jump through hoops, or add `.json` files to directories. I should be able to add the docs wherever I want, to the README, separate docs, or use templates to generate a gh-pages site.
- **render markdown, not HTML**: this one was important to me. There are hundreds of [great libs](https://github.com/jonschlinkert/remarkable) that can render markdown to HTML. Once you have well-formatted markdown documentation, it's easy to convert to HTML.
