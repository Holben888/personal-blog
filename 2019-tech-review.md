# The web dev tools that helped me get s*** done in 2019. Plus, a thank you!

Now that we're turning over a new year (a new decade even!), I wanted to take a minute to remember all the tools that made my life easier in 2019. Most "best of" lists for web dev tend to summarize the technologies in general terms. So for this, I want to focus more on the projects I worked on this year, and how these technologies helped me do them. Onwards!

# Netlify

I'm probably not alone in absolutely *gushing* over [Netlify](https://www.netlify.com) all year. It's just so freaking easy to get from zero to deployed! In brief, Netlify is for deploying a website frontend, along with a ton of extra goodies: continuous deployment, support for hooks, custom domains with automatic SSL certificates... all wrapped in a gorgeous dashboard that's easy to navigate.

I deployed two pretty big projects over there: my portfolio website at https://bholmes.dev, and the landing page for our Bits of Good organization over on https://bitsofgood.org. I hadn't managed custom domains in my life before this, but Netlify walked me through every step of copying DNS configs and other network-y things. I also highly recommend allowing Netlify to manage your domain over your domain provider, since they make setting up subdomains and SSL certificates so much easier 😁

## Rapid testing on mobile Safari

Bet you've heard this one before: the website looks great on the "mobile view" in Chrome dev tools, but the layout completely breaks on the actual phone 🤦‍♂️

Well, Netlify actually has a solution for this: [manual deployment from the command line](). Instead of pushing up a new git commit every time you want to move that `margin` 5 pixels to the right, you can just push up your working directory at a URL of your choosing! This is perfect for testing a frontend before making a PR, and Netlify will make sure all your serverless functions, API proxies, and extra production setup get deployed as well.

This was pivotal for our [Bits of Good homepage](https://bitsofgood.org), where we had some magical CSS to reorganize our swooping vector graphics on mobile views. Also helped us [catch a screen sizing issue](https://stackoverflow.com/questions/37112218/css3-100vh-not-constant-in-mobile-browser) well before pushing a PR!

## Using Netlify Forms

I just dipped my toes into this, but [Netlify forms](https://docs.netlify.com/forms/setup/) proved a super quick and easy solution to storing HTML form responses without a backend. This came in clutch for our new Bits of Good "Contact Us" page. Originally, we planned to make an API request once the form's submitted to `POST` to a serverless function, which could then forward messages to our email. Not terrible, but sounds like a good 6-7 hours of work (we're bad at backends, so hold the I-could-do-this-in-negative-3-hours 😆).

Now with Netlify, here's all we have to do (for **free** mind you!)

1. Add `data-netlify` to our `form` element
2. Make sure each element in the form as a `name` attribute (which it already should for accessibility reasons!)
3. Add a `name` attribute to the form itself so we can see which form gets piped into Netlify
4. Profit 💰

Boom! All form responses now get stored on Netlify, complete with pretty solid spam detection. We can also deploy our serverless function for email forwarding directly to Netlify, which already has hooks for detecting form submissions. Nice!

# Svelte v3

I stumbled across this library at the end of last year, when it was the #1 write-in for the [State of JS 2018](https://2018.stateofjs.com/introduction/) survey. It was running on v2 at the time, but I was still blown away by it's mission statement: the magical disappearing framework ✨

Now, I've built more projects with it than I can count! Here's the gist of why it's so awesome:

- It's a library that gets out of your way at runtime. Instead of having some "virtual DOM" to manage the page or a needless JQuery dependency, it just slaps the code you wrote onto the browser with zero added dependencies! It'll generate helper functions as needed of course, but [the difference in production bundle size is staggering](https://twitter.com/swyx/status/1172690454363672577).
- You manage state without even realizing it. Instead of having a `setState` function or something similar, you just change your JS variables directly... and Svelte knows how to update the page. There's some extra tooling to handle edge cases, but this is definitely the biggest time saver for me!
- The templating is *super* minimal. All you need to know is how to write a `for` loop for lists of elements and how to insert JS variable values. There's a bunch of added goodies for binding and event handlers once you find the need.

**For a quick rundown on how easy Svelte is to learn and use, [check out my blog post](https://dev.to/bholmesdev/why-sveltejs-may-be-the-best-framework-for-new-web-devs-205i) exploring the tool from a teacher's perspective!**

## Svelte for quick projects

Svelte has become my new go-to for getting small projects up and running fast. I've been to a few hackathons at my university ([go Yellow Jackets!](https://gfycat.com/thornyoblonganemone)), and I use this Svelte template to start working on a web UI. I starting reaching for Svelte over, say, React because of how easy it is to make new components. Each component is basically "sandboxed" in its own file, where you write vanilla HTML, CSS, and JS while having all your styles and scripts scoped to that component alone. It's almost like whipping up a CodePen, if you could compose CodePens to create a mega-Pen for your entire website 😁

One of my favorite highlights was for a hardware hackathon called [BuildGT](https://build.hack.gt). The premise: play a frame of bowling with a Roomba as the ball! The end product is something that never should have existed, but it was super fun to get everything talking to each other. We handled communication between the Roomba and the Kinect sensor (for fallen pin detection) via web sockets, but the icing on the cake was the instructional UI built on Svelte in a matter of hours. 

### The hack

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">■■■■■▣□□□□ 59% <a href="https://twitter.com/hashtag/100DaysOfCode?src=hash&ref_src=twsrc%5Etfw">#100DaysOfCode</a><br><br>Joined a team for the 12 hour hardware+software hackathon <a href="https://twitter.com/hashtag/BuildGT?src=hash&ref_src=twsrc%5Etfw">#BuildGT</a>. 41 projects total... And we managed to take 1st 🏆 ! Here&#39;s a demo of our hack in action: Roomba bowling feat. a raspberry pi, Kinect sensor, a web UI, and web sockets 🔥 <a href="https://t.co/XIvsz3D2pN">pic.twitter.com/XIvsz3D2pN</a></p>— Ben Holmes (@BHolmesDev) <a href="https://twitter.com/BHolmesDev/status/1102251893110333440?ref_src=twsrc%5Etfw">March 3, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

### The UI

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">■■■■■▣□□□□ 59% <a href="https://twitter.com/hashtag/100DaysOfCode?src=hash&ref_src=twsrc%5Etfw">#100DaysOfCode</a> (part 2)<br><br>Spent a lot of my time on the web UI. Kept it super simple with raw HTML and CSS, and used <a href="https://twitter.com/sveltejs?ref_src=twsrc%5Etfw">@sveltejs</a> v3 to build it super quickly and change state based on web socket messages. Also grabbed those classic bowling alley anims 😆 <a href="https://t.co/fA8gpNxgzk">pic.twitter.com/fA8gpNxgzk</a></p>— Ben Holmes (@BHolmesDev) <a href="https://twitter.com/BHolmesDev/status/1102254956973826048?ref_src=twsrc%5Etfw">March 3, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Svelte for static sites

Aside from getting out ideas quickly, Svelte has some tricks up its sleeve for static site generation too. We use this for our Bits of Good org website ([full repo here](https://github.com/GTBitsOfGood/bog-web)), built on Svelte and the [Contentful CMS](https://www.contentful.com). To help with routing and prerendering the actual static site, we're using the magical [Sapper](https://sapper.svelte.dev) framework to manage it all for us. This acts a lot like [NextJS](https://nextjs.org), but offers some really nice benefits:

- Management of server-side rendering to make that initial page load *fast.* Similar to NextJS, you can specify all the API calls you want to fire before the page loads [like so](https://sapper.svelte.dev/docs#Preloading).
- Management of routing behind the scenes. It even lets you use a regular old `a` tag to link to other routes on the site, rather than importing some special `Link` component!

### Sapper also uses a build tool you can actually understand: Rollup!

> _**If you're wondering what a CMS is,** it's an external tool for storing all the text and images used by your website. This comes in handy working with non-techy team members who want to edit the content of the site, but don't know how to code. Instead of asking a developer, they can just open a web portal and edit the content themselves! [Wordpress](https://wordpress.org) is one of the most popular examples of this._

This lightweight build tool came in handy for managing our Contentful CMS setup. Rather than wrestling with webpack for days on end, Rollup was easy enough that we could set up a plugin from scratch! The docs walked us through every step of the process and led us to a pretty slick solution. All we do is import our text and image content from the CMS with an API call, convert markdown to HTML as necessary, and slap everything in a fat JSON at build time. 

Now, whenever a component needs to include some content from the CMS, we can just import by that content's ID. For example, here's how we would fetch all of our nonprofit projects for our [project highlights page](https://bitsofgood.org/projects):

```javascript
import projects from '@contentful-entries/project'
```

Bam! Now `projects` is an array of JavaScript objects with all the text and image URLs we need. As you can see, Rollup also helped us add an alias called `@contentful-entries` to make content retrieval a little easier. For those curious about the nitty-gritty details, here's our [final RollupJS plugin](https://github.com/GTBitsOfGood/bog-web/blob/master/rollup-plugin-content-loader.js) ✨

# CSS variables / custom properties

This was a big win for CSS when it hit browsers in 2018. In 2019, the feature's matured a ton and scored some pretty great browser adoption. Yes, IE is still the problem child... but there's [some good polyfills](https://stackoverflow.com/questions/46429937/ie11-does-a-polyfill-script-exist-for-css-variables) at this point if you need them 🙃

![Browser support for CSS variables](/Users/benholmes/Creative Cloud Files/Documents/Blog/images-2019_tech_review/CSS variables caniuse.png)

*Source: caniuse.com as of January 3, 2020*

I've started using these across the board to *finally* ditch SCSS for managing colors, font sizes, margins, etc. These are also much more versatile than the variables you'll find in SCSS preprocessors. Here's the main benefit: **instead of creating static variables at the global level, you can now create variables that only apply to the element getting styled.** In other words, these variables can exist at any level of the cascade!

This was super useful when I was trying to move some complex style calculations from JavaScript over to CSS with `calc()` magic. Crucially, CSS variables let me make JavaScript variables visible in CSS using inline styles.

**To see CSS variables in action, [check out my blog post](https://dev.to/bholmesdev/how-using-css-variables-cut-down-on-my-javascript-dc4) on a practical application!**

# Contraste

*Disclaimer: This is an app only available for Macs at the moment!*

This is more of a "design" tool than a pure "web development" tool, but I use it so often that I can't help but mention it here.

Contraste is a dead simple tool for one thing only: checking the text contrast on a page to get an accessibility rating. This is measured by "[ADA compliance](https://www.werkbot.com/blog/ada-compliance-a-vs-aa-vs-aaa)," which applies a score of A, AA, or AAA depending on your site's accessibility to the visually impaired. This all comes down to text contrast, which isn't as easy to eyeball as I thought! 

I freaking love this tool just for how simple it is, and the fact that it's a global tool rather than a browser extension. It's actually exposed some accessibility concerns when going form designer mockup to live website, where colors on the web don't *quite* match the beautiful colors in the mockup tool. 

![Contraste color picker in action, with color adjustent to fix accessibility compliance](/Users/benholmes/Creative Cloud Files/Documents/Blog/images-2019_tech_review/contraste.png)

# Before I go, thanks for all the support this year 😁

I started this blog back in March with a quick little post: [Why I'm using Surge and not GitHub pages](https://dev.to/bholmesdev/why-im-using-surge-and-not-github-pages-4lf5). I threw it out there not really knowing what I was doing, (even managed to write some technical inaccuracies on a 3 minute read 😆) and expected maybe a couple likes by the morning.

Then, I opened my profile to find over 100 followers and close to 1000 views. *Excuse me!? For this?*

![Is Anti-Racism As Bad As Racism? Twitter Answers A Website ...](/Users/benholmes/Creative Cloud Files/Documents/Blog/images-2019_tech_review/blinking-guy-meme.png)

To be honest, it was a major stroke of luck from sending the [right tweet at the right time](https://twitter.com/BHolmesDev/status/1103437685140660224?s=20) (thanks for the like [Emma Bostian!](https://dev.to/emmabostian)). I had started blasting Twitter with content months before to build some kind of audience, which may have helped me get noticed 🤷‍♀️ If your stuck trying to find readers on your own blog, all I can say is this: **never stop writing. Never stop advertising yourself. And please, never stop learning.**

So, after this freak accident and many posts later, I want to thank everyone so much for supporting me. It really gave me the motivation to see my blog through and genuinely love teaching others about web development. I'm apparently ending the year as one of the Top 500 authors on dev.to which is... a ridiculous milestone I'll never understand. 

So, here's to many more posts and learning opportunities in 2020! Cheers 🥂