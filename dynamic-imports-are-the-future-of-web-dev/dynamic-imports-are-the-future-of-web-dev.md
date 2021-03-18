# Dynamic imports have redefined web development

Okay I'll be honest, I'm not going into this post with a "clickbait-y" agenda, and I don't have a defined narrative around this. I'm just a humble engineer that discovered this feature one day:

```js
const js = await import('script.js')
```

... and had to shout about it from the rooftops!

## Foreward: Personal delusions in a webpack world

What I'm sharing here is probably common knowledge to some. Heck, import-able JavaScript modules have lurked in the [ECMAScript](https://stackoverflow.com/a/33748435) standard [since 2017](https://medium.com/@giltayar/native-es-modules-ready-for-prime-time-87c64d294d3c)! But if you've been using "traditional" project configs like `create-react-app` for a long time, you might think that old-school bundling is how the world works.

So let me _ahem_ unpack the traditional definition of "bundling." In short, it's the concept of taking a chain of JS files like this:

```js
// toppings.js
export {
  blueberries: async () => await fetch('fresh-from-the-farm'),
  syrup = "maple",
}

// ingredients.js
export { flour: 'white', eggs: 'free-range', milk: '2%', butter: 'dairy-free' }

// pancake.js
import { blueberries, syrup } from './toppings'
import { flour, eggs, milk, butter } from './ingredients'

const pancake = new Pancake()

pancake.mixItUp([ flour, eggs, milk, butter ])
pancake.cook()
pancake.applyToppings([blueberries, syrup])
```

And "flattening" the import / export chains into a big bundle pancake 🥞

```js
// bundler-output-alksdfjfsadlf.js
const toppings__chunk = {
  blueberries: async () => await fetch('fresh-from-the-farm'),
  syrup = "maple",
}

const ingredients__chunk = { flour: 'white', eggs: 'free-range', milk: '2%', butter: 'dairy-free' }

const { blueberries, syrup } = toppings__chunk
const { flour, eggs, milk, butter } = ingredients__chunk
const pancake = new Pancake()

pancake.mixItUp([ flour, eggs, milk, butter ])
pancake.cook()
pancake.applyToppings([blueberries, syrup])
```

So we're compressing all the JavaScript files we're developing into a _single_ file for the browser to consume. Back in 2015-era web development, this really was the only way to pull off "importing" one JS file into another. `import` wasn't even valid JavaScript! It was just some neat trickery that build tools like webpack could pick up and understand.

But silently, in the depths of the ES spec, `import` and `export` syntax _did_ become valid JavaScript. Almost overnight, it became feasible to leave all your `import` and `export` statements in your code or even _gasp_ ditch your JS bundler entirely 😨

This innovation became what we call **modules.**

## ES Modules

There's an [in-depth article from MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#other_differences_between_modules_and_standard_scripts) on this topic that's _well_ worth the read. But in short, "ES Modules" (sometimes denoted by [`.mjs` files](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#aside_—_.mjs_versus_.js)) are JavaScript files with some exported values for others to import and use. As long as you load one of these files with the `type="module"` attribute:

```html
<script type="module" src="pancake.js"></script>
```

That script is ready to `import` all the other scripts it wants! Well, as long as those other scripts exist in your project's build of course (we'll ignore CORS issues for now 😁).

This concept of importing what's needed over "flattening all the things" has some nice benefits:

1. **You don't need to load and parse _everything_ up front.** By default, anything `import`ed is ["deferred" for loading as needed](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#other_differences_between_modules_and_standard_scripts). In other words, your computer won't turn into a fighter jet when you first visit your website.
2. **The need for tooling like webpack can (one day) disappear ✨** It's clear we don't want to write all our JS in a single file, so bringing browsers closure to how humans write their code is a huge win 🏆

### So wait... why does all my tooling look the same?

This was certainly my first question when I read up on this! I recently graduated from [`create-react-app`](https://create-react-app.dev) to [NextJS](https://nextjs.org) as my React boilerplate go-to, but there's still that same webpack configuration and a bundle process to think about 🤷‍♀️

A lot of this is just the curse of abstraction. Looking under the hood, these tools have made _great_ strides since ES modules hit the scene. Namely, tools like NextJS can magically "split" your React app into bite-sized chunks that get loaded as-needed. This means:

- only load the JS for a page **when you actually visit that page**
- only load React components **when they actually need to display**
- (bonus) pre-fetch JS **when someone is _likely_ to need it.** This is a more advanced feature ([documented here](https://nextjs.org/docs/api-reference/next/link#if-the-route-has-dynamic-segments)), but it lets you do all sorts of craziness; say, grabbing resources for a page when you hover over a link

There's also the benefit of **backwards compatibility** when using a bundler. For instance, Internet Explorer has no concept of "modules" or "import" statements, so any attempt to code split will blow up in your face 😬 But with a meta-framework like NextJS by your side, you can polyfill such use cases without having to thing about it.

That said, there was a major announcement that sent ripples through the web dev community: **[Microsoft will officially drop IE 11 support in August 2021](https://techcommunity.microsoft.com/t5/microsoft-365-blog/microsoft-365-apps-say-farewell-to-internet-explorer-11-and/ba-p/1591666)**!

## Wait, what is a dynamic import?

As [this article from the V8 team](https://v8.dev/features/dynamic-import) describes (creators of Google Chrome's rendering engine), a **dynamic import** is an asynchronous fetch for some JavaScript whenever you need it.

It's very similar to the [`fetch` API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) in a way! But instread of grabbing some JSON or plain text, we're grabbing some real, _executable_ code that we want to run.

All you need is a humble one-liner:

```js
const lookAtTheTime = await import('./fashionably-late.js')
```

...and you just grabbed all the `export`s from that file! If you've 
