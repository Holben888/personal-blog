# The case for using Pug in a component-driven world

![cover_image](/Users/benholmes/Creative Cloud Files/Documents/Blog/CSS-grid-concrete-examples/cover_image.png)

⚠️ _**Trigger warning:** Potentially controversial opinions ahead! Turn back now if Gatsby is your lord and savior, and you serve to protect the React dogma._

So I just finished building my [shiny new personal site](https://bholmes.dev) ✨ I thought about using fancy, component-driven frameworks like [Gatsby](https://www.gatsbyjs.org) to stay on the *bleeding edge* of web development, but after some reflection, I thought to myself...

*do I really need all this tooling to write some static HTML?*

This ended up being a **huge** learning experience on how to build a statically generated SPA from scratch (check out [the README](https://github.com/Holben888/bholmesdev) if you're curious how I approached it!). But it also taught me some valuable lessons on how far you can go while ditching the component libraries we all know and love.

## A little background: going from dynamic to static sites

Okay, let's be real for a moment: component-level thinking _rules_ modern web development. Even as [new frontend libraries](https://svelte.dev) and [Ruby-esque frameworks](https://redwoodjs.com) hit the scene, they still rely on the same basic formula: write your markup and JS logic inside a bite-sized component, and compose those components with imports and exports. Whether those components are class-based, functional, or even at the DOM level (hello web components 👋), they're all focused on these ideas of **logic isolation** and **code reusability**.

Popular libraries like React and Vue have become such a one-size-fits-all solution that we don't even question them anymore. New to building literally anything on the Internet? Just run `npx create-react-app` and get going!

...right?

I used to agree. But over the past couple years of Gatsby and JAMStack preaching, I've also come to realize that _damn, we're making some **fat** JS bundles_. With all these JS-based components, we're shipping entire rendering libraries to the browser, even for a company's plane ole' static splash page!

Before getting cynical, it's worth remembering why these libraries were made in the first place. React wasn't created because Facebook should be a better static site; it was created because Facebook is a **super dynamic, ultra complex web _app_** with logins, trackers, home feeds, settings menus, etc etc etc. That involves a _ton_ of data / state management, which means, well, a whole lot of JavaScript to construct the web page. For this use case, it makes perfect sense to build and use a UI rendering library that's **state driven**, rather than **markup driven.**

This is why Gatsby (a popular static site generator)  came years after, say Redux state management. Devs were mostly fascinated with building dynamic, JS-driven experiences that state, props, and data objects could solve. It wasn't until later that devs started wondering how they could bend these JS-heavy libaries to their will for static site creation.

If you ask me, it's pretty ironic that it takes a 500 MB directory called `node_modules` to generate a website with... as little JavaScript as possible.

Still, I can't say I'm surprised either. When you're taking a library like React, which _needs_ JavaScript to render anything to the page, you'll obviously need even more JavaScript to process all that rendering logic to begin with. Fire really does fight fire.

### So... why use React on a static site? 

At first, it kinda feels like using a chainsaw to slice a loaf of bread. Why use a rendering library like React when you have zero-to-little rerendering to worry about?

In short, **hydration.**

If you haven't heard of this before, it basically lets us write a dynamic, state-driven webpage, but also render as much of the page ahead-of-time as possible using static HTML. [The Gatsby blog](https://www.gatsbyjs.org/docs/react-hydration/) does a great job explaining this process, but here's a quick step-by-step:

1. Your app exists as a big bundle of components, much like a `create-react-app`.
2. The static site generator comes along and renders out this bundle at build time. Now, instead of shipping a blank HTML file to the user, you can send the entire page's markup for a speedy page load.
3. Now, we want to do some stateful component magic _alongside_ the static HTML we just built. To pull this off, we can look at the HTML page that's already been generated and compare it against our tree of components. When we find a component that _does_ some state management craziness, we'll slot it into our existing HTML _without rerendering the entire page._ In other words, we **hydrate** our markup with some stateful components.

Seems slick! But as you might have guessed, we'll need to ship the entire component library to the client (React in this case) in order to compare against the HTML. So it's still a fat bundle... but at least the user sees something on the first page load 🤷‍♀️

### And what if you don't need state management?

Now, React doesn't make as much sense. If we just need to handle some button clicks, we should probably just write a couple lines of vanilla JS instead of, you know, shipping the entire React library 😬

For some perspective, here's some common dev requests when building a static site:

1. **We want to break down our static site into reuseable UI components** that can accept some JS objects as parameters (aka "props"). This lets us, say, turn a list of blog post links into a bunch of clickable cards on our homepage.
2. **We need to fetch some information at build time** to slap into our production site. For example, we can go get some Twitter posts at build time to slide into our site's homepage, without shipping any API calls or exposing secret keys.
3. **We need to generate a bunch of URL routes** from either a directory of files or a fat JSON object of content. For instance, we have a folder of markdown files that we want to turn into a personal blog, making each file its own URL on the interwebs.

These are all great reasons to use static site generators. But looking at this list, only the *first* requirement actually involves a component library. And even then, we may not need to worry about rerenders or componentized state management; it's mostly done at build time! If only there was a way to make our markup reuseable and template-able, without shipping a bunch of unused JS...

## (Re)enter: Pug

That's right, good ole' Pug (formerly Jade). You know, that cute little templating library you used on your last CodePen, or maybe the weird looking HTML you found on an Express server template. It's a mighty little library from a pre-React era, before component-driven state management was even a thing.

It also uses a [simplified syntax](https://pugjs.org/language/tags.html) for HTML that makes it a bit easier to type / look at, which I'm personally a fan of 😁

So why am I bringing up this jaded (pun intended) templating library? Well, let's run through some of Pug's defining features to see what it can do. I'm hungry so we'll use a donut shop for examples 🍩:

1. **You can take in some JavaScript data and turn it into HTML elements.** This opens the door for all kinds of craziness, like looping, conditional "if" blocks, defining text content... you name it:

   ```jade
   main.krispy-kreme-menu
   	if menuItems.length === 0
   		p.alert Sorry! All sold out of gooey deliciousness :(
   	else
   		dl
       each item in menuItems
         dt #{item.name}
         dd #{item.price}
   ```

   And at the JavaScript level:

   ```js
   const stringOfRenderedHTML = pug.render('/filename.pug', { menuItems: [...donuts] })
   // spit out this string of HTML into a .html file at build time
   ```

   

2. **You can compose multiple HTML files (now `.pug` files) into a single page layout.** For example, you can create a navigation bar in one file...

   ```jade
   // nav.pug
   nav.krispy-kreme-navigation
   	a(href="/") Home
   	a(href="/donuts") Buy some donuts
   	a(href="/contact") Somehow complain about your donuts
   ```

   ... and import into another file:

   ```jade
   // index.pug
   html
   	body
   		include nav.pug
   		main.donut-content
   			ul.list-of-tastiness
   			...
   ```

   We can go even deeper by passing parameters / "props" between these files. Check out this `mixin` syntax:

   ```jade
   // nav-mixins.pug
   mixin NavBar(links)
   	each link in links
   		a(href=link.href) link.text
   ```

   ```jade
   // index.pug
   include nav-mixins.pug
   html
   	body
   		+NavBar(donutLinksPassedDownByJS)
   		main.donut-content
   			ul.list-of-tastiness
   ```

   Here, we can consider each mixin an `export` statement from `nav-mixins.pug`. Then, when we `include` this file somewhere else, those mixins become usable via the `+` decorator (aka our "import" statement).

### So in summary...

✅ We can turn JSON objects into _**static**_ HTML with a one-line script (`pug.render(filename, someJSON)`)

✅ We can break up our layout into multiple files using imports

✅ We can define component-like "mixins" for reusability and data passing

...in other words, **we get to make our UIs with components, but without sending a bunch of libraries to the client!**

##But wait, this idea is nothing new!

I know! Backend servers have been doing this for decades.

Let's consider the server-driven model you would use for, say, an [ExpressJS](https://expressjs.com) app. Everytime you hit an API endpoint, the server may go look up some info from a database, roll that data into an HTML template (probably using Pug), and send it back to the user. Same goes for PHP, C#, GoLang, or whatever exotic server you've seen before.

But guess what? A static site generator *does the exact same thing!* The only difference is that now, instead of doing all the data fetching + templating in an *API endpoint*, we're doing it in a *build script* that we call when the website actually gets deployed. For those familiar, this is that fancy script you tell Netlify to run when you first deploy your site.

Servers used this templating model long before we were making crazy, ultra-dynamic web apps. So my question is this: when your website just has some static landing pages, a few lines of JS, and *maybe* a blog to generate... why throw away this idea of templating for component libraries?

## Call to action 👉 check out 11ty

I just found out about [**11ty**](https://www.11ty.dev) (pronounced "eleven-tee"), a static site generator built with this *exact* philosophy in mind. You can choose the HTML templating language of your choice (Markdown, Haml, Pug, Nunjucks, and a bunch more), and let the framework handle all the complicated routing and page generation for you. If you're trying to build a portfolio site with a blog, a promotional splash page for a company, or anything super static-y, this is honestly the best solution I can think of. 

You can also [fork the Pug-based framework](https://github.com/Holben888/bholmesdev) my personal site uses if you're curious. That said, it's missing some pretty major capabilities at the moment (nested routes and CMS integration to name a few), so proceed with caution there.

That said, I'm *definitely* not suggesting you give up your beautiful Gatsby site! There are some serious benefits to [their hydration model](https://www.gatsbyjs.org/docs/react-hydration/) in case you want any state management stuff. Plus, if your super comfortable in React-land and don't have time to pick up new tools, it's a pretty convenient choice with a huge community of support. Same goes for [Gridsome](https://gridsome.org), a Vue-based static site generator that works in a similar way.

Anyhow, whatever you end up using, I hope this article got you thinking a bit more about what static site generators *really* do. With that, I'll leave you with this cute pug photo to stick in your mind 🐶

![cute-pug](/Users/benholmes/Creative Cloud Files/Documents/Blog/pug-in-a-component-world/cute-pug.jpg)