# I wrote my own frontend build script and you can too

As a web developer, you've almost *definitely* encountered a build script at some point in your journey. Looking at today's landscape, you'll probably...

1. Run `npm install`
2. Spin up a server with `npm start` or `npm run dev`
3. Profit 💰

But wait, how did all of that just... *work?* All you know is the `package.json` has some funny-looking scripts referencing other scripts from who-knows-where. But with everyone telling you how scary webpack is, you probably haven't looked much deeper into it. Why mess with the `create-react-app` gods?

Well, this was me for the past few years of using build-heavy frameworks. But after finally taking a deeper look [on my own project](https://github.com/Holben888/bholmesdev), I'm here to show you what a simplified build script might look like (regardless of the framework you're using). Yes, your dream setup may look a lot different than this demo; this is just meant to get your gears turning on some cool concepts 😁

## Let's set the scene

To learn through example, let's say we want to build a static site using our favorite frontend tools. We could totally use a heavy hitter like [Jekyll](https://jekyllrb.com/) or [11ty](https://www.11ty.dev/) for this, but this is about having fun with build tools!

Before the code snippets, here's the array of tools we want to use:

1. Some HTML using a templating language (like Pug) we want to compile at build time
2. Some JavaScript we want to slide through a bundler with Babel. For example, we might be compiling our JS to an older version for IE support, or leaning on some fancy modules `import`s we want to roll into our code
3. Some SCSS that should get converted into plain CSS for browsers to understand

## First, a little setup

⚠️ *Skimming this article on your phone? Been there! Feel free to skip this section and get to the good stuff*

We're gonna go *dead* simple with our dev setup. To get going, we can just make a directory for our project (named whatever you want), and add the 2 following items:

```
/super-cool-project
	/public
	build.js
```

So we'll be writing all our build code in `build.js`, and we'll spit out the production-ready result in the `/public` folder.

Now, let's make sure we can install some JS packages to help us. If you're unacquainted, you can download the JS package manager npm [just by installing NodeJS](https://nodejs.org/en/download/) 😁

From your terminal, [head over to your project directory](https://www.macworld.com/article/2042378/master-the-command-line-navigating-files-and-folders.html) and initialize npm like so:

```bash
npm init -y
```

That `-y` flag is totally optional, but it'll let you skip over the package setup process for some convenience!

Finally, we can install all the packages we'll be using. Don't worry, we'll cover each of these when we start using them.

```npm install pug rollup sass
npm install -D pug rollup sass livereload serve
```

Notice the `-D` flag here. This is because we're using all of these packages to *build* the website before the user sees it. We don't want to ship these packages to the user when they visit the site, so we'll consider these "developer" / build-time dependencies. [More info on that here.](https://flaviocopes.com/npm-dependencies-devdependencies/#:~:text=Development%20dependencies%20are%20intended%20as,this%20is%20a%20development%20deploy.)

When you're done, go ahead and open the `package.json` file that appeared in your project directory (assuming something didn't explode!). It should look something like this:

```json
{
  "name": "super-cool-project",
  "version": "1.0.0",
  "description": "",
  "main": "build.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": { // version numbers may vary of course!
    "livereload": "^0.9.1",
    "pug": "^3.0.0",
    "rollup": "^2.26.2",
    "sass": "^1.26.10",
    "serve": "^11.3.2"
  }
}
```

## Time to build some templates

With that out of the way, let's get our hands dirty!







