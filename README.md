# Webpack and React

## Overview

In this lesson, we'll unpack what **Webpack** brings to the table when
developing React applications.

## Objectives

1. Learn what Webpack is
2. Learn how Webpack integrates with React
3. Frame Webpack's relative importance at this stage in learning React

# Webpack

Welcome back! We are picking up where we left off in the
[previous lesson][previous-lesson]. If you didn't take a break, shame on you. In
this lesson, we will explore Webpack and its place in the React development
process.

## The Problem

To best describe Webpack, we will begin by describing the problem that it was
created to solve.

Picture having a server that sends some JavaScript using webpage to browsers.
Let's imagine we have some `animateDiv.js` script we want browsers to receive
that itself makes use of `jquery`. The first file we send to a requesting
client, `index.html`, may look like this:

```html
<!-- index.html -->
<html>
  <head>
    <meta charset="utf-8" />
    <script src="./vendors/jquery.js"></script>
    <script src="./lib/animateDiv.js"></script>
    <title>Discotek</title>
  </head>
  <body>
    <div class="animat" onclick="animateDiv.js">
      I'm going to animate if you click me!
    </div>
  </body>
</html>
```

With this approach, we are actually making three http requests to the server for
the application:

- We hit the base url and are returned the `index.html` file
- `index.html` tells the browser to request `jquery.js` from the server
- `index.html` tells the browser to request `animateDiv.js` from the server

A quick and dirty way around this would be to combine our JavaScript files into
one file on the server (bringing this to two requests):

```html
<!-- index.html -->
...
<script src="./lib/combinedJqueryAnimateDiv.js"></script>
...
```

We could go one step further and even in-line the JavaScript directly into our
HTML in a `<script>` tag (sending everything at once in `index.html`):

```html
<!-- index.html -->
...
<script>
  // all the contents of jquery.js and animateDiv.js written directly here!
</script>
...
```

Unfortunately, this is not very practical. We need to, by 'hand', combine
JavaScript code from multiple files into one. Well...we're programmers! We
automate the boring tasks like this! Introducing **Webpack**!

[Webpack][webpack] lets us combine different files automatically. This means
that we can freely import external JS code in our JavaScript files (both local
files as well as `node_modules` installed with `npm`). We trust that Webpack,
before we send clients our JS code over the internet, intelligently packages it
up for us. In a simplified example:

- File `siliconOverlord.js` has space-age AI code in it
- File `enslaveHumanity.js` wants to make use of this other file and send it to
  browsers all over the internet.
- Instead of always sending both `enslaveHumanity.js` and `siliconOverlord.js`
  to browsers, one after the other, **Webpack** pre-bundles them together into a
  single file that can be sent instead: `singularity.js`

If you have been working with dependencies already (`gem`s in rails, `require`
in Ruby, `import/export` in vanilla JS, etc.) you may have noticed we did not
need any tool like Webpack to work with code written in other files. While this
is true, and we don't _need_ Webpack to do this, let's highlight the problem
Webpack solves before trying to understand it:.

When compiling a React application with Webpack, it'll check every file for
dependencies that it needs to import, and also include that code. In more
technical terms, it's traversing the dependency tree and inlining those
dependencies in our application. What we'll end up with is one big JS file that
includes _all_ of our code, including any dependencies (like `react`, your
components, your `npm` modules, etc.) in that file too. The convenience of this
is not to be underestimated: one file, with _all_ of our code, means we only
need to transfer a single thing to our clients when they ask for our React
applications!

Enough theory, let's take a look at a rudimentary example of how Webpack does
this. Let's assume we have the following application on our server that we want
to share with the world:

### Simplified Webpack example

The files we want our client to have, which constitute one whole dank web
application:

```js
// reveal.js (pre Webpack bundling)
function reveal(person, realIdentity) {
  person.identity = realIdentity;
}

export default reveal;
```

```js
// main.js (pre Webpack bundling)
import reveal from "./reveal.js";

const gutMensch = {
  name: "Andrew Cohn",
  identity: "Friendly Neighborhood Academy Xi Instructor",
};

reveal(gutMensch, "Chrome Boi");
```

Without Webpack, we would need to find some way to send both files to our client
and ensure they are playing nicely together. We couldn't just send the `main.js`
file wizzing over the internet, through a [series of tubes][tubes], to our
client expecting it to make use of the `reveal` function: the client hasn't even
received the `reveal.js` file in this case! While we have several ways we could
make this work, most of them are headaches and someone else has already made an
excellent solution: Webpack.

Instead of writing our own bespoke, artisanal, Etsy&trade; sell-able dependency
solution, we can just use Webpack!

**The result after we unleash Webpack on these files:**

```js
// bundle.js (post Webpack bundling)
function reveal(person, realIdentity) {
  person.identity = realIdentity;
}

const gutMensch = {
  name: "Andrew Cohn",
  identity: "Friendly Neighborhood Academy Xi Instructor",
};

reveal(gutMensch, "Chrome Boi");
```

If we were to first bundling our files with Webpack, we would instead have a
single, all-encompassing, file that ensures our dependencies are right where
they belong.

## More than Just JavaScript

In addition to bundling up our JavaScript code, Webpack also allows us to manage
all kinds of other assets in our React projects. You may have noticed that we're
able to `import` CSS files and images in addition to importing JavaScript files
&mdash; this is something we can _only_ do because of Webpack.

You can read more about how Webpack is configured to work in our React projects
in the [Create React App docs][create react app].

## Summary

You have just read a lot of information about a tool you likely have not worked
directly with before. Luckily, its straightforward to summarize:

In our React applications, **Webpack** manages pesky dependency loading for us
by **bundling** our many files' code and outputting a single file, which
contains all of our code, with dependencies properly placed, in one file.

## Looking Forward

After reading the previous lesson on Babel and now this one on Webpack, you may, understandably, be asking yourself:

- "How important is this Webpack/Babel jargon?"
- "How much do I need to learn about the different tools that improve React
  development experience vs. actual React programming?"

Because `create-react-app` is so opaque with configuration files, allow us to be
transparent with you:

At Academy Xi, we are constantly balancing an explanation of the fundamentals
against practice on the real skills that will get you producing valuable
applications the quickest. We believe that, while learning React basics, it's
important to know how these tools (Webpack + Babel) work on a _high level_.
Let's justify both in turn:

Most React code nowadays is being compiled one way or another — be it using
**Webpack**, an alternative such as [Browserify][browserify], or something else.
We want to use it, but we don't want to create unnecessary busywork for
ourselves or distract with peripherals.

Additionally, there are a lot of juicy nectarines (read: low hanging fruit) that
aren't present in the ECMAScript version browsers implement, such as
[upcoming proposed JS language features][babel-stage-2], which we can pluck with
**Babel**. Don't you want to sink your teeth into those
[syntactic sugary][syntactic-sugar] stone fruits?

For the most part, Babel and Webpack will be abstracted from you so you can
focus on learning the primary React competencies. This will streamline the
development process. In layperson terms, if React development skills were
muscles, we want to focus on getting you [swol][swol] before having you worry
about learning to assemble weight machines.

## Resources

- [Webpack][webpack]
- [Babel][babel]

[babel]: http://babeljs.io/
[previous-lesson]: https://learn.co/lessons/babel-and-react
[babel-stage-2]: https://babeljs.io/docs/plugins/preset-stage-2/
[webpack]: https://webpack.js.org/
[tubes]: https://en.wikipedia.org/wiki/Series_of_tubes
[browserify]: http://browserify.org/
[syntactic-sugar]: https://en.wikipedia.org/wiki/Syntactic_sugar
[swol]: https://i.imgur.com/RAegPMp.jpg
[create react app]: https://create-react-app.dev/docs/adding-a-stylesheet
