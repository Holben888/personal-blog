# Building a *sexy* mobile navbar dropdown in any framework

I've been building a lot more static sites recently, and every one of them needs the same thing:

- **A nice and *responsive* navigation bar** with logo on the left, links on the right 💪
- For mobile screens, collapse those links on the right into a **hamburger menu** 🍔
- **Hit all the marks for accessibility**: semantic HTML, keyboard navigation, and more ♿️
- **Add some polished animations** for that *sleek, modern feel* ✨

Oh, and implement it using whatever framework the team is using. This may sound daunting... but after bouncing between [React](https://reactjs.org/), [Svelte](https://svelte.dev/), and plain-ole JS, and I think I've found a solid solution you can take with you wherever you go.

**Onwards!**

## First, what's the end goal?

Here's a screen-grab from my most recent project: redesigning the [Hack4Impact](https://hack4impact.org) nonprofit site.

![Demoing responsive mobile nav-bar with screen resize](/Users/benholmes/Creative Cloud Files/Documents/Blog/navbar-dropdown/end-goal.gif)

*Ignore the cats. We needed some purrfect placeholders while we waited on content* 😼

This has some fancy bells and whistles like that background blur effect, but it covers the general "formula" we're after!

##Lay down some HTML

Let's define the general structure of our navbar first.

```html
<nav>
	<a class="logo" href="/">
    <img src="dope-logo.svg" alt="Our professional logo (ideally an svg!)" />
  </a>
  <button class="mobile-dropdown-toggle" aria-hidden="true">
    <!-- Cool hamburger icon -->
  </button>
  <div class="dropdown-link-container">
    <a href="/about">About Us</a>
    <a href="/work">Our Work</a>
    ...
  </div>
</nav>
```

**A few things to note here:**

1. We're *not* using an unordered list (`ul`) for our links. You might see this recommendations floating around the web, but [this article from Chris Coyier](https://css-tricks.com/navigation-in-lists-to-be-or-not-to-be/) really solidified things for me. In short: **adding a list doesn't mean a better screenreader / accessibility experience!** Plus, it makes things harder from a developer perspective (mainly bc you shouldn't nest `divs` inside of lists. Who knew 🤷‍♀️)

2. You'll notice our `dropdown-link-container` element, which wraps around all our links *except* the logo. This `div` won't do anything fancy for desktop users. But once we hit our mobile breakpoint, we'll hide these elements in a big dropdown triggered by our `mobile-dropdown-toggle` button.
3. We're slapping an `aria-hidden` attribute on our dropdown toggle. For a simple nav like this, there's no reason for a screenreader to pick up on this button; it can always pick up on all our links even when they're "visually hidden", so there's no toggling going on 🤷‍♀️ Still, if you really want to mimic the "toggle" effect for these users (which you should for super busy navbars), you can [look into adding `aria-expanded` to your markup](https://www.w3.org/WAI/GL/wiki/Using_aria-expanded_to_indicate_the_state_of_a_collapsible_element). This is getting a bit in the weeds for this article though, so you can use my easy-out for now.

For those following along at home, you should have something like this:

https://codepen.io/bholmesdev/pen/zYqQvxj

## Now, some CSS

Before worrying about all that mobile functionality, let's spiff up the *widescreen* experience.

### Our base styles

To start, we'll set up the alignment and width for our navbar.

```css
nav {
  max-width: 1200px; /* should match the width of your website content */
  display: flex;
  align-items: center; /* center each of our links vertically */
  margin: auto; /* center all our content horizontally when we exceed that max-width */
}

.logo {
  margin-right: auto; /* push all our links to the right side, leaving the logo on the left */
}

.dropdown-link-container > a {
  margin-left: 20px; /* space out all our links */
}

.mobile-dropdown-toggle {
  display: none; /* hide our hamburger button until we're on mobile */
}
```

The `max-width` property is an important piece here. Without it, our nav links will get pushed *wayyyy* to the right (and our logo *wayyyy* to the left) for larger screens. Here's a little before-and-after to show you what I mean.

![h4i-nav-max-width](/Users/benholmes/Creative Cloud Files/Documents/Blog/navbar-dropdown/h4i-nav-max-width.gif)

**Before: ** Our nav elements stick to the edges of the screen. This doesn't match up with our page content very well, and makes navigation awkward on larger devices.

![h4i-nav-good-max-width](/Users/benholmes/Creative Cloud Files/Documents/Blog/navbar-dropdown/h4i-nav-good-max-width.gif)

**After: ** Everything is *beautifully* aligned, making our website a lot more "scan-able."

Of course, you can add padding, margins, and background colors to-taste 👨‍🍳 But as long as you have a `max-width` and `margin: auto` for centering the nav on the page, you're 90% done already! Here's another pen to see it in action:

https://codepen.io/bholmesdev/pen/eYZapdM

### Adding the dropdown

Alright, now let's tackle our dropdown experience. First, we'll just focus on re-styling our links into a vertical column that takes up the height of the page:

```css
@media (max-width: 768px) { /* arbitrary breakpoint, around the size of a tablet */
  .dropdown-link-container {
    /* first, make our dropdown cover the screen */
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 100vh;
    /* fix nav height on mobile safari, where 100vh is a little off */
    height: -webkit-fill-available;
    
    /* then, arrange our links top to bottom */
    display: flex;
    flex-direction: column;
    /* center links vertically, push to the right horizontally.
       this means our links will line up with the rightward hamburger button */
    justify-content: center;
    align-items: flex-end;

    /* add margins and padding to taste */
    margin: 0;
    padding-left: 7vw;
    padding-right: 7vw;

    background: lightblue;
  }
}
```

This is pretty standard for the most part. Just a few things of note here:

**First, we use `position: fixed` to align our dropdown to the top of our *viewport*.** This is distinct from `position: absolute`, which would shift the nav's position depending on our scroll position 😬

**Then, we use the `-webkit-fill-available` property to fix our nav height in mobile Safari.** I'm sure you're thinking *"what, how is 100vh not 100% of the user's screen size? What did Apple do this time?"* Well, the problem comes from the iOS' disappearing URL bar. When you scroll, a bunch of UI elements slide out of the way to give you more screen real estate. That's great and all, but it means anything that *used* to take up 100% of the screen now needs to be resized! We have this problem on our [Bits of Good nonprofit homepage](https://bitsofgood.org):

![safari-nav-height-problem](/Users/benholmes/Creative Cloud Files/Documents/Blog/navbar-dropdown/safari-nav-height-problem.gif)

Notice the links aren't *quite* vertically centered until we swipe away all the Safari buttons. If you have a bunch of links, this could lead to cut-off text and images too!

In the end, all you need is the override `height: -webkit-fill-available` to specifically target this issue. Yes, feature flags like `-webkit` are usually frowned upon. But since this issue only pops up in mobile Safari (a webkit browser), there really isn't a problem with this approach in my opinion 🤷‍♀️ Worst case, the browser falls back to `100vh`, which is still a totally usable experience.

Finally, let's make sure our logo and dropdown buttons actually appear *on top of* our dropdown. Because of `position:fixed`, the dropdown will naturally hide everything underneath it until we add some `z-index`ing:

```css
@media (max-width: 768px) {
  .logo, .mobile-dropdown-toggle {
    z-index: 1;
  }
  
  .mobile-dropdown-toggle {
    display: initial; /* override that display: none attribute from before */
  }
  
  .dropdown-link-container {
    ...
    z-index: 0; /* we're gonna avoid using -1 here, since it could position our navbar below other content on the page as well! */
  }
}
```

Squoosh this CodePen to our breakpoint size to see these styles at work:
https://codepen.io/bholmesdev/pen/KKzLdbR

## Let's animate that dropdown

Alright, we have most of our markup and styles finished up. Now, let's make that hamburger button do something!

We'll start with handling the menu button clicks. To show you how simple this setup is, I'll just use vanilla JS:

```js
// get a ref to our navbar (assuming it has this id)
const navElement = document.getElementById("main-nav");

document.addEventListener("click", (event) => {
  if (event.target.classList.contains("mobile-dropdown-toggle")) {
    // when we click our button, toggle a CSS class!
    navElement.classList.toggle("dropdown-opened");
  }
});
```

Now, we'll animate our dropdown into view whenever that `dropdown-opened` class gets applied:

```css
/* inside the same media query from before */
@media (max-width: 768px) {
  ...
  .dropdown-link-container {
    ...
    /* our initial state */
    opacity: 0; /* fade out */
    transform: translateY(-100%); /* move out of view */
    transition: transform 0.2s, opacity 0.2s; /* transition these smoothly */
  }
  
  nav.dropdown-opened > .dropdown-link-container {
    opacity: 1; /* fade in */
    transform: translateY(0); /* move into view */
  }
}
  
```

Nice! With just a few lines of CSS, we just defined a little fade + slide in effect whenever we click our dropdown. You can mess around with it here. Modify the transitions however you wish!

https://codepen.io/bholmesdev/pen/jOqoQRZ

### Adapting for *big boy* components

Alright, I know some of you want to slide this in your framework of choice at this point. Well, it shouldn't be too difficult! You can **keep all the CSS the same,** but here's a component snippet you can plop into React:

```jsx
export const BigBoyNav = () => {
	const [mobileNavOpened, setMobileNavOpened] = useState(false);
	const toggleMobileNav = () => setMobileNavOpened(!mobileNavOpened);

  return (
    <nav className={mobileNavOpened ? 'dropdown-opened' : ''}>
      ...
      <button class="mobile-dropdown-toggle" onClick={mobileNavOpened} aria-hidden="true">
    </nav>
	)
}
```

And one for Svelte:

```html

<!-- ...might've included this to show how simple Svelte is :) -->
<script>
	let mobileNavOpened = false
  const toggleMobileNav = () => mobileNavOpened = !mobileNavOpened;
</script>

<nav className:mobileNavOpened="dropdown-opened">
	...
  <button class="mobile-dropdown-toggle" on:click={toggleMobileNav} aria-hidden="true">
</nav>
```

...You get the point. It's a toggle :laughing:

## The little things

We have a pretty neat MVP at this point! I just left a couple accessiblity pieces for the end to get you to the finish line 🏁

### Collapse that dropdown when you click a link

_**Note:** You can skip this if you're using a vanilla solution like Jekyll, Hugo or some plain HTML. In those cases, the entire page will reload when you click a link, so there's no need to hide the dropdown!_

If we're gonna cover the users entire screen, we should probably hide that dropdown again once they choose the link they want. We could just *any* click events in our dropdown like so:

```js
document.addEventListener('click', event => {
  // if we clicked on something inside our dropdown...
  if (ourDropdownElement.contains(event.target)) {
    navElement.classList.remove('dropdown-opened')
  }
})
```

...but this wouldn't be super accessible :sweat:. Sure, it handles mouse clicks, but how will it fare against keyboard navigation with the "tab" key? In that case, the user will tab to the link they want, hit "enter," and stay stuck in `dropdown-opened` without any feedback!

Luckily, there's a more "declarative" way to get around this problem. Instead of listening for user clicks, we can just listen for whenever the route changes! This way, we don't need to consider how the user navigates through our dropdown links; Just listen for the result.

Of course, this solution varies depending on your router of choice. Let's see how [NextJS](http://nextjs.org) handles this problem:

```jsx
export const BigBoyNav = () => {
  const router = useRouter(); // grab the current route with a React hook
  const activeRoute = router.pathname;

  ...
  // whenever "activeRoute" changes, hide our dropdown
  useEffect(() => {
    setMobileNavOpened(false);
  }, [activeRoute]);
}
```

Vanilla [React Router](https://reactrouter.com) should handle this problem the same way. Regardless of your framework, just make sure you trigger your state change whenever the active route changes :thumbsup:

### Handle the "escape" key

For *even better* keyboard accessiblity, we should also toggle the dropdown whenever the "escape" key is pressed. This is bound to a very specific user interaction, so we're free to add an event listener for this one:

```js
// vanilla JS
const escapeKeyListener = (event: KeyboardEvent) =>
	event.key === 'Escape' && navElement.classList.remove('dropdown-opened')

document.addEventListener('keypress', escapeKeyListener);
```

...and for component frameworks, make sure you remove that event listener whenever the component is destroyed:

```jsx
// React
useEffect(() => {
  const escapeKeyListener = (event: KeyboardEvent) =>
  event.key === 'Escape' && setMobileNavOpened(false);

  // add the listener "on mount"
  document.addEventListener('keypress', escapeKeyListener);
  // remove the listener "on destroy"
  return () => document.removeEventListener('keypress', escapeKeyListener);
}, []);
```

## See a fully-functional React example 🚀

If you're curious how this could all fit together in a React app, our entire [Hack4Impact](https://hack4impact.org) website is accessible on CodeSandbox!

**To check out the Nav component, [head over here](https://codesandbox.io/s/hack4impact-website-0kn5m?file=/components/shared/Nav/index.tsx).** 

## Thanks for reading! If this article was helpful...

I love writing about this sort of stuff 👨‍💻
🐦 [**Follow my Twitter for random web dev tips and articles I find cool**](https://twitter.com/bholmesdev)
📗 [**Follow my blog for new posts every 2-3 weeks**](https://dev.to/bholmesdev)