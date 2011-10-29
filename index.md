---
layout: default
version: 0.1.0alpha
headers:
  - text: Introduction
    type: h1
  - text: Why not use Rails and Backbone?
    type: h2
  - text: Features
    type: h2
  - text: Flexibility without the glue code
    type: h2
  - text: Getting started
    type: h1
  - text: Create an app
    type: h2
  - text: File structure
    type: h2
  - text: Apps and static pages
    type: h1
  - text: Creating apps
    type: h2
  - text: Connecting servers to apps
    type: h2
  - text: Static pages
    type: h2
  - text: Views
    type: h1
  - text: Creating templates
    type: h2
  - text: Pre-defined templates
    type: h3
  - text: Template syntax
    type: h2
  - text: Whitespace and HTML conformance
    type: h3
  - text: Variables
    type: h3
  - text: Sections
    type: h3
  - text: Partials
    type: h3
  - text: Bindings
    type: h3
  - text: Relative model paths and aliases
    type: h3
  - text: Performance
    type: h2
  - text: Stylesheets
    type: h2
  - text: Rendering
    type: h2
  - text: app.view
    type: h2
  - text: Controllers
    type: h1
---

# Derby

<p class="promo">MVC framework making it easy to write realtime, collaborative applications that run in both Node.js and browsers.</p>

<div class="javascript">
<h3>hello.js</h3>
{% highlight javascript %}
var hello = require('derby').createApp(module),
    view = hello.view,
    get = hello.get;

// Templates define both HTML and model <- -> view bindings
view.make('Body',
  'Holler: <input value="((message))"><h1>((message))</h1>'
);

// Routes render on client as well as server
get('/', function (page, model) {
  // Subscribe specifies the data to sync
  model.subscribe('message', function () {
    page.render();
  });
});
{% endhighlight %}

<h3>server.js</h3>
{% highlight javascript %}
var express = require('express'),
    hello = require('./hello'),
    server = express.createServer()
      .use(express.static(__dirname + '/public'))
      // Apps create an Express middleware
      .use(hello.router()),

    // Apps also provide a server-side store for syncing data
    store = hello.createStore({ listen: server });

server.listen(3000);
{% endhighlight %}
</div>

<div class="coffeescript">
<h3>hello.coffee</h3>
{% highlight coffeescript %}
{view, get} = require('derby').createApp module

# Templates define both HTML and model <- -> view bindings
view.make 'Body',
  'Holler: <input value="((message))"><h1>((message))</h1>'

# Routes render on client as well as server
get '/', (page, model) ->
  # Subscribe specifies the data to sync
  model.subscribe 'message', ->
    page.render()
{% endhighlight %}

<h3>server.coffee</h3>
{% highlight coffeescript %}
express = require 'express'
hello = require './hello'
server = express.createServer()
  .use(express.static __dirname + '/public')
  # Apps create an Express middleware
  .use(hello.router())

# Apps also provide a server-side store for syncing data
store = hello.createStore listen: server

server.listen 3000
{% endhighlight %}
</div>

# Introduction

Derby includes a powerful data synchronization engine called [Racer](http://racerjs.com/) that automatically syncs data between browsers, servers, and a database. Models subscribe to changes on specific objects, enabling granular control of data propagation without defining channels. Racer supports offline usage and conflict resolution out of the box, which greatly simplifies writing multi-user applications.

Derby applications load immediately and can be indexed by search engines, because the same templates render on both server and client. In addition, templates define bindings, which instantly update the view when the model changes and vice versa. Derby makes it simple to write applications that load as fast as a search engine, are as interactive as a document editor, and work offline.

## Why not use Rails and Backbone?

Derby represents a new breed of application frameworks, which we believe will replace currently popular libraries like [Rails](http://rubyonrails.org/) and [Backbone](http://documentcloud.github.com/backbone/).

Adding dynamic features to apps written with [Rails](http://rubyonrails.org/), [Django](https://www.djangoproject.com/), and other server-side frameworks tends to produce a tangled mess. Server code renders various initial states while jQuery selectors and callbacks desperately attempt to make sense of the DOM and user events. Adding new features typically involves changing both server and client code, often in different languages.

Many developers now include a client MVC framework like [Backbone](http://documentcloud.github.com/backbone/) to better structure client code. A few have started to use declarative model-view binding libraries, such as [Knockout](http://knockoutjs.com/) and [Angular](http://angularjs.org/), to reduce boilerplate DOM manipulation and event bindings. These are great concepts, and adding some structure certainly improves client code. However, they still lead to duplicating rendering code and manually synchronizing changes in increasingly complex server and client code bases. Not only that, each of these components must be manually wired together and packaged for the client.

Derby radically simplifies this process of adding dynamic interactions. It runs the same code in servers and browsers, and it syncs data automatically. Derby takes care of template rendering, packaging, and model-view bindings out of the box. Since all features are designed to work together, no code duplication and glue code are needed. Derby equips developers for a future when all data in all apps are realtime.

## Features

* **HTML templates:** [Mustache](http://mustache.github.com/mustache.5.html)-like templates are rendered into HTML on both the server and client. Because they render on the server, pages display immediately---even before any scripts are downloaded. Templates are mostly just HTML, so designers can understand and modify them.

* **View bindings:** In addition to HTML rendering, templates specify live bindings between the view and model. When model data change, the view updates the properties, text, or HTML neccessary to reflect the new data. When the user interacts with the page---such as editing the value of a text input---the model data are updated.

* **Client and server routing:** The same routes produce a single-page browser app and an [Express](http://expressjs.com/) server app. Links render instantly with push/pop state changes in modern browsers, while server rendering provides access to search engines and browsers without JavaScript.

* **Model syncing:** Model changes are automatically sychronized with the server and all clients subscribed to the same data over [Socket.IO](http://socket.io/).

* **Conflict resolution:** The server detects conflicts, enabling clients to respond instantly and work offline. Multiple powerful techniques for conflict resolution are included.

* **Customizable persistence:** Apps function fully with in-memory, dynamic models. After the design crystallizes and the logic is written, schemas can be added to provide validation and automatic persistence of data to one or more databases.

## Flexibility without the glue code

Derby eliminates the tedium of wiring together a server, server templating engine, CSS compiler, script packager, minifier, client MVC framework, client JavaScript library, client templating and/or bindings engine, client history library, realtime transport, ORM, and database. It elminates the complexity of keeping state synchronized among models and views, clients and servers, multiple windows, multiple users, and models and databases.

At the same time, it plays well with others. Derby is built on top of popular components, including [Node.js](http://nodejs.org/), [Express](http://expressjs.com/), [Socket.IO](http://socket.io/), [Browserify](https://github.com/substack/node-browserify), [Stylus](http://learnboost.github.com/stylus/docs/iteration.html), [UglifyJS](https://github.com/mishoo/UglifyJS), [MongoDB](http://www.mongodb.org/), and soon other popular databases and datastores. These components can also be used directly. The data synchronization layer, [Racer](http://racerjs.com/), can be used separately. Other libraries, such as jQuery, work just as well along with Derby.

When following the default file structure, templates, styles, and scripts are automatically packaged and included in the appropriate pages. In addition, Derby can be used via a dynamic API, as seen in the simple example above.

# Getting started

As with all Node.js modules, first install [Node](https://github.com/joyent/node/wiki/Installation) and [npm](http://npmjs.org/).

Instal Derby with:

    $ npm install -g derby

Derby requires [Redis 2.2-scripting](https://github.com/antirez/redis/tree/2.2-scripting). Derby's models are powered by [Racer](http://racerjs.com/), which uses Redis to store data transactions and manage PubSub. Racer uses Redis Lua scripting, which [will be included](http://antirez.com/post/everything-about-redis-24) in the next stable release, Redis 2.6.

Download, extract, and compile Redis 2.2-scripting:

    $ curl -O http://redis.googlecode.com/files/redis-2.2.111-scripting.tar.gz
    $ tar xzf redis-2.2.111-scripting.tar.gz
    $ cd redis-2.2.111-scripting
    $ make

Then start the Redis server:

    $ src/redis-server

## Create an app

Derby includes a simple project generator:

    $ cd ~
    $ derby new first-project
    $ cd first-project

or, for [CoffeeScript](http://jashkenas.github.com/coffee-script/):

    $ cd ~
    $ derby new --coffee first-project
    $ cd first-project
    $ make

`make` will execute the coffee compiler with the watch option, so leave it running in a separate terminal.

Make sure Redis is running, and fire up Node:

    $ node server.js

## File structure

The default file structure is:

    /lib
      /app
        index.js
      /server
        index.js
    /public
      /img
      /gen
    /styles
      /app
        index.styl
      404.styl
      base.styl
      reset.styl
    /views
      /app
        index.html
      404.html
    .gitignore
    package.json
    README.md
    server.js

In [CoffeeScript](http://jashkenas.github.com/coffee-script/) projects, the `lib` directory is generated by the compiler, and script files should be edited in the `src` directory instead. The project generator will create a `Makefile` for compiling CoffeeScript projects.

Derby uses a filename based convention similar to Node.js modules. A file named `demo.js` and a directory `demo` containing a file `index.js` both define an app with the name "demo." The same applies for styles and views, which can either be `demo.styl` or `demo\index.styl` and `demo.html` or `demo\index.html`.

Apps are associated with their respective styles and views by filename only. Derby automatically includes them when rendering. Both support importing, so shared styles and templates may be defined in separate files.

Static files can be placed in the public folder. The default Express server created by the Derby project generator sets a cache time of one year for all static files. Therefore, new file versions must be given new filenames. Derby compiles scripts for the browser into the `public\gen` folder by default. Each script's filename is generated from a hash, so that it can be cached long term.

# Apps and static pages

Derby projects support one or more single-page apps as well as static pages. Apps have a full MVC structure, including a model provided by [Racer](http://racerjs.com/), a template and styles based view, and controller code with application logic and routes (which map URLs to actions). Static pages consist of only templates and styles.

On the server, apps provide a router middleware for Express. One or more app routers as well as server only routes can be included in the same Express server. 

Derby packages up all of an app's templates, routes, and application code when rendering. Regardless of which app URL the browser requests initially, the app is able to render any other state within the same application client-side. If the app cannot handle a URL, it will fall through and request from the server. Errors thrown during route handling also cause requests to fall through to the server.

Derby works great with only a single app, though developers may wish to create separate apps if only certain sets of pages are likely to be used together. For example, a project might have a separate desktop web app and mobile web app. Or a project might have an internal administration panel app and a public content app.

## Creating apps

Apps are created in the file that defines the app's controller code. They are then associated with a server by requiring the app within the server file.

> ### `app = `derby.createApp` ( module )`
> 
> **module:** Derby uses the module object to create an app. The app's name is taken from its filename, and Derby exports a number of methods on the app.
> 
> **app:** Returns an app object, which is equivalent to `module.exports`.

The app's filename is used to determine the name of the app. App names are used to automatically associate an app with template and styles files of the same name.

The app name is also used as the name of the global variable that the application exposes in the browser. Therefore, app names should be valid JavaScript variable names, starting with a letter and containing only alphanumeric characters and underscores.

The `createApp` method adds a number of methods to the app. On both the client and the server, these are `view`, `render`, `ready`, `get`, `post`, `put`, `del`, and `hook`. On the server only, Derby also adds `router`, `createStore`, and `session`.

## Connecting servers to apps

The Derby project generator outputs an Express server for a typical setup. Because Derby shares most code between server and client, Derby server files can be very minimal.

The server includes an app with a standard Node.js require statement. It can then use the `app.router()` method to create a router middleware for Express that handles all of the app's routes.

The server also needs to create a `store` object, which is what Racer uses to setup Socket.IO, create models, coordinate data syncing, and interface with databases. Typically, a project will have only one store, even if it has multiple apps. It is possible to have multiple stores, though an app can only be associated with one store. A store associated with one app can be created using that app's `app.createStore()` method. If a store is shared among multiple apps, it should be created using the `derby.createStore()` method, which is passed each of the apps as aruguments.

## Static pages

Derby can also render static pages from templates and styles not associated with an app. This is useful for error pages and other pages that don't need dynamic content.

> ### `staticPages = `derby.createStatic` ( root )`
> 
> **root:** The root path that contains the "views" and "styles" directories.
> 
> **staticPages:** Returns a staticPages object, which has a render method. (While unused, static is a [reserved JavaScript keyword](https://developer.mozilla.org/en/JavaScript/Reference/Reserved_Words), and it cannot be a variable name.)

The staticPages object keeps a reference to the directory root and provides a `staticPages.render()` method. It is intended for use in server-only Express routes. See [Rendering](#rendering).

# Views

Typically, writing Derby apps begins with HTML templates. These templates define the rendered HTML as well as model-view bindings.

## Creating templates

Derby compiles a collection of HTML-based templates into a page based on a number of pre-defined names. Pages usually define at least a `Title` and `Body` template. Templates may be created programatically via the `view.make()` method:

{% highlight javascript %}
var view = require('derby').createApp(module).view;

view.make('Body', '<h1>Howdy!</h1>');
{% endhighlight %}

{% highlight coffeescript %}
{view} = require('derby').createApp module

view.make 'Body', '<h1>Howdy!</h1>'
{% endhighlight %}

However, they are generally placed in template files within the `views` directory. Each app automatically looks for a template file that shares the same name and calls view.make for each template. Templates placed in a template file are also automatically bundled with the application scripts so that they can be rendered on the client.

Template files are also HTML, but each template is wrapped in a tag that names the template. This name must end in a colon to differentiate it from a normal HTML tag. These tags need not be closed. For example:

{% highlight html %}
<Title:>
  Silly example

<Body:>
  <h1>Howdy!</h1>
{% endhighlight %}

Templates can be imported from another file for sharing among multiple pages. File paths are expessed relatively, similar to how Node.js modules are loaded. A single template may be imported, or all templates in the file may be imported with the reserved template name `All`.

#### shared.html
{% highlight html %}
<sharedTitle:>
  Too awesome not to share
{% endhighlight %}

#### app.html
{% highlight html %}
<!-- import template keeping the same name -->
<sharedTitle: from="./shared">

<!-- import template and change its name -->
<Title: from="./shared" import="sharedTitle">

<!-- import all templates in a file keeping the same names -->
<All: from="./shared">
{% endhighlight %}

### Pre-defined templates

By default, Derby includes templates with the names `Doctype`, `Title`, `Head`, `Header`, `Body`, `Script`, and `Tail` when it renders a page.

Some of these templates have names that also are the names of HTML tags, but only `Title` wraps the template in a tag. Derby does *not* include any non-required HTML elements, such as `<html>`, `<head>`, and `<body>` by default. Browsers don't need them, and pages will validate as proper HTML5 without them.

By convention, Pre-defined template names are capitalized to indicate that the page renderer will include them automatically. However, since HTML tags are case-insensitive, Derby template names are also case insensitive. Thus, `Body`, `BODY`, and `body` all represent the same template.

Note that template files don't contain boilerplate HTML, such as doctype definitions, stylesheets, and script includes. By default, Derby includes these items in an order optimized for fast load times. Also to optimize load time, it sends pages a number of chunks:

#### First chunk

1. **`Doctype:`** Standard HTML5 doctype and character set definition---`<!DOCTYPE html><meta charset=utf-8>`---unless overridden 
2. **`Title:`** "Derby app" unless overridden
3. **`Head:`** Optional location for meta tags, scripts that must be placed in the HTML `<head>`, and manually included stylesheets
4. CSS is compiled and inserted after the Head template automatically.
5. **`Header:`** Optional location for a page header that will be sent with the initial response chunk. Note that this is actually part of the HTML `<body>`, but it should render correctly by itself. It is separated out so that it can be displayed to the user before the rest of the page if the remainder of the page takes a while to download. Typically this includes fixed content, such as a logo and a top navigation bar.

#### Second chunk

6. **`Body:`** The page's content.

#### Third chunk

7. Inline scripts placed in a file named `inline.js` or added via the `view.inline()` method. Scripts are typically included this way if they are needed to properly render the page, such as resizing an element based on the window size.
8. **`Script:`** Optional location for external scripts loaded before the client scripts. For example, this is where a script tag that includes jQuery would be placed. Note that this template is just a location within the page, and it is not wrapped in a script tag.
9. Client scripts are automatically included via an asynchronously loaded external script. The name of the script is a hash of its content so that it can be cached by the browser long term.

#### Fourth chunk

10. JSON bundle of the model data, event bindings, and other data resulting from rendering the page. This bundle initializes the application once the external client script loads.
11. **`Tail:`** Optional location for additional scripts to be included at the very end of the page.

<style>
ol{counter-reset: item}
ol>li{display: block}
ol>li:before{content: counter(item) ". "; counter-increment: item}
#second_chunk+ol{counter-reset: item 5}
#third_chunk+ol{counter-reset: item 6}
#fourth_chunk+ol{counter-reset: item 9}
</style>

## Template syntax

Derby's template syntax is largely based on [Mustache](http://mustache.github.com/mustache.5.html), a popular logic-less templating language. Following is an example borrowed from the Mustache site.

A typical Mustache template:

    Hello {{"{{"}}name}}
    You have just won ${{"{{"}}value}}!
    {{"{{"}}#in_ca}}
    Well, ${{"{{"}}taxed_value}}, after taxes.
    {{"{{"}}/in_ca}}

Given the following data context:

    {
      name: "Chris",
      value: 10000,
      taxed_value: 10000 - (10000 * 0.4),
      in_ca: true
    }

Will produce the following:

    Hello Chris
    You have just won $10000!
    Well, $6000.0, after taxes.

Logic-less templates better enforce separation of logic from presentation by making it impossible to embed logic within views. Instead of conditional statements and loops, logic-less templates use placeholders that are replaced with data passed in when the template is rendered. This data is often referred to as the "context."

With Mustache, application code generates a context object before rendering the view. It then passes that object along with the template at render time. Derby templates can be used this way as well. However, in addition to looking for objects in a context object, Derby assumes that the model is part of the context. Even better, Derby is able to automatically establish live bindings between the view and objects in the model. Derby slightly extends the Mustache syntax in order to support these featueres.

The other major difference between Mustache and Derby templates is that Derby templates must be valid HTML first. Mustache is completely language agnostic---it can be used to compile anything from HTML to source code to a document. However, Derby templates are first parsed as HTML so that the parser can understand how to bind data to the surrounding DOM objects. Template tags are only allowed within text, within attribute values, and surrounding elements. In addition, template tags may *not* be used within the value of an `id` attribute.

#### Invalid template tag placements
{% highlight html %}
<!-- INVALID: Within element names -->
<{{"{{"}}tagName}}>Bad boy!</{{"{{"}}tagName}}>

<!-- INVALID: Within attribute names -->
<b {{"{{"}}attrName}}="confused" {{"{{"}}booleanAttr}}>Bad boy!</b>

<!-- INVALID: Splitting an html tag -->
<b{{"{{"}}#maybe}}>Bad boy!</b{{"{{"}}/maybe}}>

<!-- INVALID: Splitting an element -->
{{"{{"}}#maybe}}<b>{{"{{"}}/maybe}}Bad boy!</b>

<!-- INVALID: Within an id attribute value -->
<b id="{{"{{"}}id}}">Bad boy!</b>
{% endhighlight %}

#### Valid placements
{% highlight html %}
<!-- Within text -->
<b>Let's go {{"{{"}}activity}}!</b>

<!-- Within attribute values (other than id) -->
<b style="color:{{"{{"}}displayColor}}">Let's go running!</b>

<!-- Surrounding an element -->
{{"{{"}}#maybe}}<b>Let's go dancing!</b>{{"{{"}}/maybe}}
{% endhighlight %}

### Whitespace and HTML conformance

Before parsing, all HTML comments, leading whitespace, and new lines are removed from templates. Whitespace at the end of lines is maintained, in case a space is desired in the HTML output. The contents of `<script>` and `<style>` tags are passed through literally.

Derby's HTML parser should be able to parse any valid HTML, including elements that don't require closing tags and unquoted attributes. HTML attribute values only need to be quoted if they are the empty string or if they contain a space, equals sign, or greater than sign. Note that since Derby templates are parsed as HTML first, any of these characters within a template tag require an attribute to be escaped. When in doubt, add quotes around attributes---Derby will remove them from the output HTML when they are not required.

Because it understands the HTML context, Derby's HTML escaping is much more minimal than that of most templating libraries. You may be surprised to see unescaped `>` and `&` characters. These only need to be escaped in certain contexts, and Derby only escapes them when needed. If you are skeptical, an [HTML5 validator](http://html5.validator.nu/) will detect most escaping bugs.

Throughout these docs, the output of templates is shown indented and on multiple lines for the sake of readability. However, Derby's renderer would not output any indentation or line breaks. In addition, output attribute values are quoted, but Derby only includes quotes around attribute values if they are needed.

### Variables

Variables insert a value from the context or model with a given name. If the name isn't found, nothing will be inserted. Values are HTML escaped by default. Triple braces may be used to insert a value without escaping.

#### Template

{% highlight html %}
<Body:>
  <p>{{"{{"}}name}}
  <p>{{"{{"}}age}}
  <p>{{"{{"}}location}}
  <p>{{"{{"}}{location}}}
{% endhighlight %}

#### Context

{% highlight javascript %}
page.render({ name: 'Parker', location: '<b>500 ft</b> away' });
{% endhighlight %}
{% highlight coffeescript %}
page.render name: 'Parker', location: '<b>500 ft</b> away'
{% endhighlight %}

#### Output

{% highlight html %}
<p>Parker
<p>
<p>&lt;b>500 ft&lt;/b> away
<p><b>500 ft</b> away
{% endhighlight %}

### Sections

Sections cause their contents to be conditionally rendered. They also set the scope of the context for their contents. In Mustatche, sections must begin and end with the same name, but Derby requires only an end tag without the name.

Sections of the form `{{"{{"}}#shown}}Example{{"{{"}}/}}` render their contents when the name matches a truthy value. If the section matches an array, it will render the contents once for each item in the array.

Inverted sections take the form `{{"{{"}}^shown}}Counter example{{"{{"}}/}}`. Their contents render when the name matches a falsey value (false, null, undefined, 0, '', or NaN) or an empty array. Derby also provides a shorthand syntax for defining a section and inverted section together: `{{"{{"}}#shown}}Example{{"{{"}}^}}Counter example{{"{{"}}/}}`

#### Template

{% highlight html %}
<Body:>
  <h1>
    {{"{{"}}#visited}}
      Welcome back!
    {{"{{"}}^}}
      Welcome to the party!
    {{"{{"}}/}}
  </h1>
  <ul>
    {{"{{"}}#users}}
      <li>{{"{{"}}name}}: {{"{{"}}motto}}
    {{"{{"}}/}}
  </ul>
  {{"{{"}}^hideFooter}}
    <small>Copyright &copy; 1999 Party Like It's.</small>
  {{"{{"}}/}}
{% endhighlight %}

#### Context

{% highlight javascript %}
page.render({
  visited: true,
  users: [
    { name: 'Billy', motto: "Shufflin', shufflin'" },
    { name: 'Ringo', motto: "Make haste slowly." }
  ]
});
{% endhighlight %}
{% highlight coffeescript %}
page.render
  visited: true
  users: [
    { name: 'Billy', motto: "Shufflin', shufflin'" }
    { name: 'Ringo', motto: "Make haste slowly." }
  ]
{% endhighlight %}

#### Output

{% highlight html %}
<h1>Welcome back!</h1>
<ul>
  <li>Billy: Shufflin', shufflin'
  <li>Ringo: Make haste slowly
</ul>
<small>Copyright &copy; 1999 Party Like It's.</small>
{% endhighlight %}

Note how in the above example, the context becomes each array item inside of the `#users` section. Similarly, sections set scope when reffering to the name of an object. In addition to the local scope, template tags may refer to anything in the parent scope.

#### Template

{% highlight html %}
<Body:>
  {{"{{"}}#users}}
    {{"{{"}}#jill}}I like <a href="{{"{{"}}link}}">{{"{{"}}favorite}}</a>.{{"{{"}}/}}
  {{"{{"}}/}}
{% endhighlight %}

#### Context

{% highlight javascript %}
page.render({
  users: {
    jill: {
      favorite: 'turtles'
    }
  },
  link: 'http://derbyjs.com/'
});
{% endhighlight %}
{% highlight coffeescript %}
page.render
  users:
    jill:
      favorite: 'turtles'
  link: 'http://derbyjs.com/'
{% endhighlight %}

#### Output

{% highlight html %}
I like <a href="http://derbyjs.com/">turtles</a>.
{% endhighlight %}

### Partials

Partials are used to include one template inside of another. The scope of the parent context is inherited inside of the partial. Both for code readability and for more efficient template compilation, it is best to keep individual templates relatively simple and use partials for each significant unit.

As in Mustache, partials are included by name with the syntax `{{"{{"}}> profile}}`. Because it is common to use a partial to render each item in a list or otherwise use a section to set the context for a partial, Derby supports the additional `{{"{{"}}users > profile}}` syntax. This is similar to `{{"{{"}}#users}}{{"{{"}}> profile}}{{"{{"}}/}}`, except that it only sets the scope and will always render whether or not `users` is defined. If the partial should always be rendered, the shorter syntax is preferred; it is more readable and more efficient to render.

#### Template

{% highlight html %}
<Body:>
  {{"{{"}}> nav}}

<nav:>
  <ul>{{"{{"}}navItems > navItem}}</ul>

<navItem:>
  <li><a href="{{"{{"}}link}}">{{"{{"}}title}}</a>
{% endhighlight %}

#### Context

{% highlight javascript %}
page.render({
  navItems: [
    { title: 'Home', link '/' },
    { title: 'About', link '/about' },
    { title: 'Contact us', link '/contact' }
  ]
});
{% endhighlight %}
{% highlight coffeescript %}
page.render
  navItems: [
    { title: 'Home', link '/' }
    { title: 'About', link '/about' }
    { title: 'Contact us', link '/contact' }
  ]
{% endhighlight %}

#### Output

{% highlight html %}
<ul>
  <li><a href="/">Home</a>
  <li><a href="/about">About</a>
  <li><a href="/contact">Contact us</a>
</ul>
{% endhighlight %}

### Bindings

Model-view binding is a relatively recent approach to adding dyanmic interaction to a page. Its use of declarative syntax dramatically lowers the amount of repetative, error-prone DOM manipulation code in an application. With Derby's bindings system, it should rarely be neccessary to write any DOM code at all.

Derby templates declare bindings by using double or triple parentheses instead of curly braces. Bound template tags output their values in the initally rendered HTML just like unbound tags. In addition, they create bindings that update the view immediately whenever the model changes. If bindings are used for elements that change upon user interaction---such as form inputs---Derby will update the model automatically as their values change.

Any template tag may be live bound, but bindings only work for data in the model. Context data is passed in at render time, and it doesn't change dynamically. If a binding tag uses a name not in the context object or the model at render time, it is still bound to the model, since the path may be defined later.

#### Template

{% highlight html %}
<Body:>
  Holler: <input value="((message))"><h1>((message))</h1>
{% endhighlight %}

#### Context
  
{% highlight javascript %}
model.set('message', 'Yo, dude.');
page.render();
{% endhighlight %}
{% highlight coffeescript %}
model.set 'message', 'Yo, dude.'
page.render()
{% endhighlight %}

#### Output

{% highlight html %}
Holler: <input value="Yo, dude." id="$0"><h1 id="$1">Yo, dude.</h1>
{% endhighlight %}

Note that the value in the model at render time is inserted into the HTML, as with a non-bound template tag. In addition, Derby establishes an event listener for the input element that sets the value of `message` whenever the user modifies the text of the input element. It also sets up a listeners for both the input and the h1 element to update their displayed values whenever `message` changes.

Rather than re-rendering the entire template when a value changes, only the individual elements are updated. In the case of the input, its `value` property is set; in the case of the h1, its `innerHTML` is set. Since neither of these elements have an `id` attribute specified in the template, Derby automatically creates ids for them. All DOM ids created by Derby begin with a dollar sign ($). If an element already has an id, Derby will use that instead.

Derby associates all DOM event listeners with an `id`, because getting objects by id is a fast DOM operation, it makes dealing with DOM events more efficient, event listeners continue working even if other scripts modify the DOM unexpectedly, and event listeners can be established at render time on the server and passed to the client via a JSON literal. Derby internally tracks events via literal objects instead of function callbacks, allowing it to render pages on the server and then re-establish the same event listeners on the client efficiently.

If a bound template tag is not fully contained by an HTML tag, Derby will wrap the template in an `<ins>` element. Most browsers will display `<ins>` elements with an underline by default. For Derby apps, this should be removed by adding `ins{text-decoration:none}` to the page's stylesheet.

#### Template

{% highlight html %}
<Body:>
  Welcome to our ((adjective)) city!
{% endhighlight %}

#### Context
  
{% highlight javascript %}
model.set('adjective', 'funny');
page.render();
{% endhighlight %}
{% highlight coffeescript %}
model.set 'adjective', 'funny'
page.render()
{% endhighlight %}

#### Output

{% highlight html %}
Welcome to our <ins id="$0">funny</ins> city!
{% endhighlight %}

Semantically, it would be more correct to use a `<div>` or `<span>`, depending on context. However, this is difficult to do automatcially. For example, `<div>` elements cannot be contained within a `<p>`, and `<span>` elements may not be wrapped around a `<div>`. Attemting to detect each of these situations and choose the proper wrapper element would significantly complicate the Derby renderer and make it more brittle to changes in HTML. In contrast, `<ins>` and `<del>` elements have a [transparent content model](http://www.w3.org/TR/html5/content-models.html#transparent-content-models), enabling them to be included inside or around most other HTML elements. Note that there are still some restrictions on where these elements may be used. For example, an `<option>` element may only contain text and no other elements. MDN has [good documentation](https://developer.mozilla.org/Special:Tags?tag=HTML:Element+Reference) of the usage context for various HTML elements. When in doubt, use an [HTML5 validator](http://html5.validator.nu/).

### Relative model paths and aliases

For items in the context object, objects from the parent scope can still be referred to directly from within sections. However, bindings are set up when templates are initially compiled, and objects defined in the model may change. Thus, model paths must refer to the full path regardless of location within the template.

Yet, a template might need to define how each item in an array should be rendered as well as bind to those items. In this case, relative model paths may be used. Paths relative to the current scope begin with a dot (`.`).

#### Template

{% highlight html %}
<Body:>
  <ul>((items > item))</ul>

<item:>
  <li><a href="{{"{{"}}url}}">((.name))</a>: $((.price))
{% endhighlight %}

#### Context

{% highlight javascript %}
model.set('items', [
  { name: 'Cool can', price: 5.99, url: '/p/0' },
  { name: 'Fun fin', price: 10.99, url: '/p/1' },
  { name: 'Bam bot', price: 24.95, url: '/p/2' }
]);
page.render();
{% endhighlight %}
{% highlight coffeescript %}
model.set 'items', [
  { name: 'Cool can', price: 5.99, url: '/p/0' }
  { name: 'Fun fin', price: 10.99, url: '/p/1' }
  { name: 'Bam bot', price: 24.95, url: '/p/2' }
]
page.render()
{% endhighlight %}

#### Output

{% highlight html %}
<ul id="$0">
  <li><a href="/p/0" id="$1">Cool can</a>: $<ins id="$2">5.99</ins>
  <li><a href="/p/1" id="$3">Fun fin</a>: $<ins id="$4">10.99</ins>
  <li><a href="/p/2" id="$5">Bam bot</a>: $<ins id="$6">24.95</ins>
</ul>
{% endhighlight %}

In the above example, note that the `url` is not bound, and it does not start with a dot. Since the context of the partial will be set to the array item at render time, this will render the value correctly, but it will not update if the value changes. `.name` and `.price` start with a dot, because they are bound to paths in the model relative to the item being rendered. Whenever the name or the price of an item changes, the appropriate fields will be updated in realtime. In addition, the entire list is bound. If a new item is added, an item is removed, or the items are reordered, the list will be updated in realtime.

Aliases to a specific scope may be defined, enabling relative model path references within nested sections. Aliases begin with a colon (`:`), and can be defined within a section tag or a partial tag that sets the scope.

#### Template

{% highlight html %}
<Body:>
  <h2>Toys in use:</h2>
  ((#toys :toy))
    ((#:toy.inUse))
      {{"{{"}}> toyStatus}}
    ((/))
  ((/))
  <h2>All toys:</h2>
  ((toys :toy > toyStatus))

<toyStatus:>
  <p>{{"{{"}}name}} on the ((:toy.location))
{% endhighlight %}

#### Context

{% highlight javascript %}
model.set('toys', [
  { name: 'Ball', location: 'floor', inUse: true },
  { name: 'Blocks', location: 'shelf' },
  { name: 'Truck', location: 'shelf' }
]);
page.render();
{% endhighlight %}
{% highlight coffeescript %}
model.set 'toys', [
  { name: 'Ball', location: 'floor', inUse: true }
  { name: 'Blocks', location: 'shelf' }
  { name: 'Truck', location: 'shelf' }
]
page.render()
{% endhighlight %}

#### Output

{% highlight html %}
<h2>Toys in use:</h2>
<ins id="$0">
  <ins id="$1"><p>Ball on the <ins id="$2">floor</ins></ins>
  <ins id="$3"></ins>
  <ins id="$4"></ins>
</ins>
<h2>All toys:</h2>
<ins id="$5">
  <p>Ball on the <ins id="$6">floor</ins></ins>
  <p>Blocks on the <ins id="$7">shelf</ins></ins>
  <p>Truck on the <ins id="$8">shelf</ins></ins>
</ins>
{% endhighlight %}

## Performance

While Derby's rendering performance has yet to be benchmarked and optimized, its architecture will ultimately enable it to outperform most current web application rendering approaches in real usage.

When large chunks of a page require updating, rendering HTML and then updating the innerHTML of an element is the fastest approach. However, when small changes to one item in a template occur, rerendering the entire template and replacing an entire section of the DOM is much slower than simply updating a single property or single element's innerHTML.

In addition, only rendering certain sections or an entire page client-side dramatically slows page loads. Even an extremely fast client-only renderer causes the browser to wait for the page to load a script (most likely via an additional request), interpret the script, render the template, and update the DOM before it has a chance to start performing layout of the HTML content.

Derby's architecture optimizes time to load the page initially, to re-render sections or the entire page client-side, and to update individual elements in realtime. It makes it easy for designers and developers to create application views with HTML-based templates, and it provides instant responsiveness with model-view bindings.

## Stylesheets

Derby uses **[Stylus](http://learnboost.github.com/stylus/)** to automatically compile and includes styles for each page. Stylus extends CSS with variables, mixins, functions, and other awesome features. It supports CSS style syntax interchangeably with a minimal whitespace based syntax. 

Derby also includes **[Nib](http://visionmedia.github.com/nib/)**, which adds a number of convenient CSS3 mixins to Stylus. Nib takes care of adding vendor prefixes, makes CSS gradients *much* easier, and has bunch of other useful features.

Stylus requires that files end in a `.styl` extension. It supports [importing other files](http://learnboost.github.com/stylus/docs/import.html), including support for `index.styl` files. Since Node.js, Derby templates, and Stylus all support similar file importing conventions, it is easy to use the same directory structure for analogous files in the `lib`/`src`, `views`, and `styles` directories.

Derby includes compiled CSS at the top of each page. Inlining CSS almost always decreases load time, and Stylus importing makes it easy to break up shared styles into files included in the appropriate pages. Note, however, that it is not optimial to include a very large amount of CSS, such as large data URI encoded images, at the top of the page. Large images are best loaded as separate files or inline at the bottom of the page, so that the rest of the page may be displayed first.

## Rendering

Views are rendered in response to [routes](#routes). Most routes should be defined inside of an app so that they can be handled both on the client and the server. Views can also be rendered in response to server only routes.

In each render method, `model`, `context`, and `status` arguments may be in any order or omitted.

> ### page.render` ( [context], [status] )`
> 
> **context:** *(optional)* Object specifying additional context objects to use in rendering templates.
> 
> **status:** *(optional)* Number specifying the HTTP status code. 200 by default. Has no effect when rendering on the client.

App routes supply a page object, which provides a consistent interface for rendering an entire page on both server and client. On the server, the page is rendered by calling Node.js response object methods like `res.write`. On the client, Derby renders the page locally. It then replaces the `document.title` and `document.body.innerHTML`, and updates the URL with `history.pushState`.

The page's render function implicitly renders in the context of the app's model. An additional context object may be supplied for items that are only needed at render time.

> ### app.render` ( res, model, [context], [status] )`
> 
> **res:** Response object passed to the Express routing callback
> 
> **model:** A Derby model object used for rendering. The contents of the model will be serialized and initialized into the same state in the browser once the page loads.
> 
> **context:** *(optional)* Additional context objects to use in rendering templates.
> 
> **status:** *(optional)* Number specifying the HTTP status code. 200 by default.

Apps may also be rendered within server only Express routes. In this case, it is neccessary to provide the renderer with a response object and model. On the server, Models are created with the `store.createModel()` method. If the Derby session middleware is used, it will create models automatically and set a reference to them on `req.model`.

> ### staticPages.render` ( name, res, [model], [context], [status] )`
> 
> **name:** Name of the view and style files to render
> 
> **res:** Response object passed to the Express routing callback
> 
> **model:** *(optional)* A Derby model object. A model object may be used for rendering, but it will not be serialized and included with a static page. Static pages don't have an associated app, and they don't include scripts by default.
> 
> **context:** *(optional)* Additional context objects to use in rendering templates.
> 
> **status:** *(optional)* Number specifying the HTTP status code. 200 by default.

For creating error pages and other static pages, Derby provides a `staticPages` object that renders a template and script file specified by name. Typically, this is used without a model object, but it is possible to supply a model object that is used for rendering only. See [Static pages](#static_pages).

## app.view

Derby adds an `app.view` object for creating and rendering views.

> ### view.after` ( name, callback )`
> 
> **name:** Name of the template
> 
> **callback:** Function called after the template is rendered on the client each time. The callback is passed the context object used to render the template.

> ### view.before` ( name, callback )`
> 
> **name:** Name of the template
> 
> **callback:** Function called before the template is rendered on the client each time. The callback is passed the context object used to render the template.

The `view.after()` and `view.before()` methods are used to attach a function to a view that is called each time the view is rendered. For example, this might be useful for creating animations in JavaScript, or for scrolling the page when new messages are pushed to a chat window.

Calling these methods on the server has no effect, and their callbacks will not be invoked on the server.

> ### view.make` ( name, template )`
> 
> **name:** Name of the template
> 
> **template:** A string containing the Derby template. Note that this should be only the content of the template, and it should not have a template name element, such as `<Body:>` at the start.

Apps should typically place all templates in a template file in the `views` folder instead of calling `view.make()` directly. However, templates may be added to an app this way as well.

Note that calling `view.make()` only renders the template; it does not include the template in the external script file separately. Thus, it must be called again on the client when the app loads.

> ### view.inline` ( fn )`
> 
> **fn:** Function to be inlined in the page and called immediately when the page loads.

This method is intended solely for server use and has no effect when called in the browser.

Scripts should be included inline in the page if needed to properly render the page. For example, a script might adjust the layout based on the window size, or it might autofocus a sign in box in browsers that don't support the HTML5 autofocus attribute.

Usually, it is preferable to place such scripts in a separate file called `inline.js` in the same directory as the app. This file will be automatically inlined when the app is created. Calling `view.inline()` directly does the same thing, but it is redundant to send the script inline and also include it in the app's external script file.

# Controllers
