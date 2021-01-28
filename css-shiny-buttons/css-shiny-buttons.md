---
title: A shiny-on-hover effect that follows your mouse (CSS) ✨
published: false
description: Using CSS variables + any frontend framework to add a metallic shine to your UI
tags: css, javascript, webdev, showdev
cover_image: https://raw.githubusercontent.com/Holben888/personal-blog/main/css-shiny-buttons/thumbnail.png
---

> Using CSS variables + any frontend framework to add a metallic shine to your UI

Hover states are probably the most fun a developer can have when a designer isn't looking. You've seen the basics at this point; fade-ins, growing and shrinking, color shifts, [animated rainbow gradients](https://www.joshwcomeau.com/react/rainbow-button/), etc etc etc.

But there was one animation that inspired me recently (props to Keyframers for shouting it out)

{% twitter 1278384065087893505 %}

This one's unique since it isn't some "static" hover state that always looks the same. It actually *tracks your mouse moment* to make the page even more interactive! This seemed like such a cool idea... that we threw it all over our [Hack4Impact](https://hack4impact.org) site 😁

So let's explore

-  🎈 Why CSS variables can help us
- ✨ How we style our button
- 🪤 How we map mouse movements to a metallic shine
- 🔨 How to adapt this animation to any UI framework

***Onwards!***

## Our end goal

![Demo of buttons shining on hover on hack4impact.org website](https://raw.githubusercontent.com/Holben888/personal-blog/main/css-shiny-buttons/shiny-button-demo.gif)

The effect is pretty simple on the surface. Just shift the color a little bit whenever you hover over the button, plus a little circular gradient for a "metallic" sheen. 

But there's a bit of added spice that CSS can't pull off on its own: **We need to track your cursor position** to make this interactive! Luckily, this has gotten a lot easier over the years; You won't even need a UI framework or state management to pull it off 👀

## 🎈 Brief primer on CSS variables

In case you haven't heard, CSS variables are kind of taking web development by storm right now. They're a bit like those `$` variables preprocessors like [SASS](https://sass-lang.com) and [LESS](http://lesscss.org) let you pull off, but with one huge benefit: **you can change the value of these variables at runtime** using JavaScript 😱

Let's see a simple example. Say we want to make a balloon pump, where you hit a button as fast as you can to "inflate" an HTML-style balloon.

If we didn't know anything about CSS variables, we'd probably do some style manipulation straight from JavaScript. Here's how we'd pump up a balloon using the `transform` property:

```js
const balloon = document.querySelector('.balloon');
// make the balloon bigger by 50%
balloon.style.transform = 'scale(1.5)';
```

Or, to make the balloon just a little bit bigger on every button click:

```js
...
const pump = document.querySelector('.pump')
// keep track of the balloon's size in a JS variable
let size = 1;
pump.addEventListener('click', () => {
  size += 0.1;
	balloon.style.transform = `scale(${size})`;
})
```

There's nothing wrong with this so far. But it has some growing pains:

1. We need to keep track of a CSS property (the balloon's `scale` size) **using a JS variable.** This could _ahem_ **balloon** into a suite of state variables overtime as we animate more elements throughout our app.
2. **We're writing our CSS using strings.** This leaves a sour taste in my mouth personally, since we loose all our syntax highlighting + editor suggestions. It can also get nasty to maintain when we want that `size` variable in other parts of our styles. For example, what if we wanted to change the `background-position` as the balloon inflates? Or the `height` and `width`? Or some `linear-gradient` with multiple color positions?

### CSS variables to the rescue

As you may have guessed, we can store this `size` from our code as a CSS variable!

We can use the same `.style` attribute as before, this time using the `setProperty` function to assign a value:

```js
let size = 1;
pump.addEventListener('click', () => {
  size += 0.1;
	balloon.style.setProperty('--size', size);
})
```

Then, slide that variable into our `transform` property from the CSS:

```css
.balloon {
  /* set a default / starting value if JS doesn't supply anything */
  --size: 1;
  ...
  /* use var(...) to apply the value */
  transform: scale(var(--size));
}
```

Heck, you can ditch that `size` variable entirely and make CSS the source of truth! Just read the value from CSS directly whenever you try to increment it:

```js
pump.addEventListener('click', () => {
  // Note: you *can't* use balloon.style here!
  // This won't give you the up-to-date value of your variable.
  // For that, you'll need getComputedStyle(...)
	const size = getComputedStyle(balloon).getPropertyValue('--size');
  // size is a string at this stage, so we'll need to cast it to a number
  balloon.style.setProperty('--size', parseFloat(size) + 0.1)
})
```

There's some caveats to this of course. Namely, CSS variables **are always strings** when you retrieve them, so you'll need to cast to an `int` or a `float` (for decimals) as necessary. The whole `.style` vs. `getComputedStyle` is a little weird to remember as well, so do whatever makes sense for you!

Here's a fully working example to *pump* up your confidence 🎈

<iframe height="265" style="width: 100%;" scrolling="no" title="Balloon pump with CSS variables 🎈" src="https://codepen.io/bholmesdev/embed/vYXogza?height=265&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/bholmesdev/pen/vYXogza'>Balloon pump with CSS variables 🎈</a> by Benjamin Holmes
  (<a href='https://codepen.io/bholmesdev'>@bholmesdev</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## ✨ Let's get rolling on our shiny button

Before putting our newfound CSS variable knowledge to the test, let's jump into the styles we'll need for this button.

Remember that we want a smooth gradient of color to follow our mouse cursor, like a light shining on a piece of metal. As you can imagine, we'll want a `radial-gradient` on our `button` that we can easily move around.

We could add a gradient as a secondary background on our button (yes, you can [overlay multiple backgrounds](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Backgrounds_and_Borders/Using_multiple_backgrounds) on the same element!). But for the sake of simplicity, let's just add another element *inside* our button representing our "shiny" effect. We'll do this using a [pseudo-element](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements) to be fancy 😁

```css
.shiny-button {
  /* add this property to our button, */
  /* so we can position our shiny gradient *relative* to the button itself */
  position: relative;
  /* then, make sure our shiny effect */
  /* doesn't "overflow" outside of our button */
  overflow: hidden;
  background: #3984ff; /* blue */
  ...
}

.shiny-button::after {
  /* all pseudo-elements need "content" to work. We'll make it empty here */
  content: '';
  position: absolute;
  width: 40px;
  height: 40px;
  /* make sure the gradient isn't too bright */
	opacity: 0.6;
  /* add a circular gradient that fades out on the edges */
	background: radial-gradient(white, #3984ff00 80%);
}
```

_**Side note:** You may have noticed our 8-digit hex code on the gradient background. This is a neat feature that lets you add transparency to your hex codes! [More on that here.](https://css-tricks.com/8-digit-hex-codes/)_

Great! With this in place, we should see a subtle, stationary gradient covering our button.

<iframe height="265" style="width: 100%;" scrolling="no" title="Shiny button 1 - Getting our button styles" src="https://codepen.io/bholmesdev/embed/LYbPjNQ?height=265&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/bholmesdev/pen/LYbPjNQ'>Shiny button 1 - Getting our button styles</a> by Benjamin Holmes
  (<a href='https://codepen.io/bholmesdev'>@bholmesdev</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>



##🪤 Now, lets track some mouse cursors

We'll need to dig into some native browser APIs for this. You probably just listen for `click` 99% of the time, so it's easy to forget the dozens of other event listeners at our disposal! We'll need to use the [`mousemove` event](https://developer.mozilla.org/en-US/docs/Web/API/Element/mousemove_event) for our purposes:

```js
const button = document.querySelector('.shiny-button')
button.addEventListener('mousemove', (e) => {
	...
})
```

If we log out or `event` object, we'll find some useful values in here. The main one's we're focusing on are `clientX` and `clientY`, which tell you the mouse position **relative to the entire screen.** Hover over this button to see what those values look like:

<iframe height="265" style="width: 100%;" scrolling="no" title="Shiny button 1 - Basic button hover" src="https://codepen.io/bholmesdev/embed/yLVBNzE?height=265&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/bholmesdev/pen/yLVBNzE'>Shiny button 1 - Basic button hover</a> by Benjamin Holmes
  (<a href='https://codepen.io/bholmesdev'>@bholmesdev</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

This is pretty useful, but it's not _quite_ the info we're looking for. Remember that our shiny effect is positioned _relative_ to the button surrounding it. For instance, to position the effect at the top-left corner of the button, we'd need to set `top: 0; left: 0;` So, we'd expect a reading of `x: 0 y: 0` when we hover in our example above... But this definitely _isn't_ the values that `clientX` and `clientY` give us 😕

There isn't a magical `event` property for this, so we'll need to get a little creative. Remember that `clientX` and `clientY` give us the **cursor position** relative to the window we're in. There's also this neat function called [`getBoundingClientRect()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect), which gets the x and y position of our **button** relative to the window. So if we subtract our button's position from our cursor's position... we should get our position relative to the button!

This is probably best explored with visuals. Hover your mouse around to see how our `mouse` values, `boundingClientRect` values, and subtracted values all interact:

<iframe height="265" style="width: 100%;" scrolling="no" title="Shiny button 3 - All our values" src="https://codepen.io/bholmesdev/embed/YzpKGbo?height=265&theme-id=light&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/bholmesdev/pen/YzpKGbo'>Shiny button 3 - All our values</a> by Benjamin Holmes
  (<a href='https://codepen.io/bholmesdev'>@bholmesdev</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 💅 Pipe those coordinates into CSS 

Alright, let's put two and two together here! We'll pass our values from the `mousemove` listener:

```js
button.addEventListener("mousemove", (e) => {
  const { x, y } = button.getBoundingClientRect();
  button.style.setProperty("--x", e.clientX - x);
  button.style.setProperty("--y", e.clientY - y);
})
```

Then, we'll add some CSS variables to that shiny pseudo-element from before:

```css
.shiny-button::after {
  ...
  width: 100px;
  height: 100px;
  top: calc(var(--y, 0) * 1px - 50px);
  left: calc(var(--x, 0) * 1px - 50px);
}
```

 **A couple notes here:**

1. We can set a default value for our variables using the second argument to `var`. In this case, we'll use 0 for both.

2. CSS variables have a weird concept of "types." Here, we're assuming we'll pass our `x` and `y` as integers.  This makes sense from our JavaScript, but CSS has a hard time figuring out that something like `10` _really_ means `10px`. To fix this, **just multiply by the unit you want using `calc`** (aka `* 1px`).
3. We subtract half the `width` and `height` from our positioning. This ensures that our shiny circle is centered up with our cursor, instead of following with the top left corner.

### Fade into our effect on entry

We're pretty much done here! Just one small tweak: if we leave this animation as-is, our shiny effect will _always_ show in some corner of our button (even when we aren't hovering).

We could fix this from JavaScript to show and hide the effect. But why do that when CSS lets you style-on-hover already?

```css
/* to explain this selector, we're */
/* selecting our ::after element when the .shiny-button is :hover-ed over */
.shiny-button:hover::after {
  /* show a faded shiny effect on hover */
  opacity: 0.4;
}
.shiny-button::after {
  ...
  opacity: 0;
  /* ease into view when "transitioning" to a non-zero opacity */
  transition: opacity 0.2s;
}
```

Boom! Just add a one-line transition effect, and we get a nice fade-in. Here's our finished product ✨

<iframe height="265" style="width: 100%;" scrolling="no" title="Shiny button 4 - Putting it all together" src="https://codepen.io/bholmesdev/embed/RwobPJG?height=265&theme-id=light&default-tab=css,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/bholmesdev/pen/RwobPJG'>Shiny button 4 - Putting it all together</a> by Benjamin Holmes
  (<a href='https://codepen.io/bholmesdev'>@bholmesdev</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 🔨 Adapt to your framework of choice

I get it, you might be dismissing this article with all the `eventListeners` thinking _well, I'm sure that JS looks much different in framework X._ Luckily, the transition is pretty smooth!

First, you'll need to grab a **reference** to the button you're shine-ifying. In React, we can use a `useRef` hook to retrieve this:

```jsx
const ShinyButton = () => {
  // null to start
  const buttonRef = React.useRef(null)
  React.useEffect(() => {
    // add a useEffect to check that our buttonRef has a value
    if (buttonRef) {
      ...
    }
  }, [buttonRef])
  
  return <button ref={buttonRef}>✨✨✨</button>
}
```

Or in Svelte, we can `bind` our element to a variable:

```html
<script>
  import { onMount } from 'svelte'
  let buttonRef
  // our ref always has a value onMount!
  onMount(() => {
    ...
  })
</script>

<button bind:this={buttonRef}>✨✨✨</button>
```

_Aside: I always like including Svelte examples, since they're usually easier to understand_ 😁

Once we have this reference, it's business-as-usual for our property setting:

### React example

```jsx
const ShinyButton = () => {
  const buttonRef = React.useRef(null)
  // throw your mousemove callback up here to "add" and "remove" later
  // might be worth a useCallback based on the containerRef as well!
  function mouseMoveEvent(e) {
    const { x, y } = containerRef.current.getBoundingClientRect();
    containerRef.current.style.setProperty('--x', e.clientX - x);
    containerRef.current.style.setProperty('--y', e.clientY - y);
  }

  React.useEffect(() => {
    if (buttonRef) {
      buttonRef.current.addEventListener('mousemove', mouseMoveEvent)
    }
    // don't forget to *remove* the eventListener
    // when your component unmounts!
    return () => buttonRef.current.removeEventListener('mousemove', mouseMoveEvent)
  }, [buttonRef])
  ...
```

### Svelte example

```html
<script>
  import { onMount, onDestroy } from 'svelte'
  let buttonRef
  // again, declare your mousemove callback up top
  function mouseMoveEvent(e) {
    const { x, y } = buttonRef.getBoundingClientRect();
    buttonRef.style.setProperty('--x', e.clientX - x);
    buttonRef.style.setProperty('--y', e.clientY - y);
  }
  onMount(() => {
		buttonRef.addEventListener('mousemove', mouseMoveEvent)
  })
  onDestroy(() => {
    buttonRef.removeEventListener('mousemove', mouseMoveEvent)
  })
</script>
```

The main takeaway: 💡 **don't forget to remove event listeners when your component unmounts!**

## Check out our live example on Hack4Impact

If you want to see how this works in-context, check out this CodeSandbox for our Hack4Impact site. We also added some CSS fanciness to make this effect usable on _any_ element, not just buttons ✨

**To check out the component, [head over here](https://codesandbox.io/s/hack4impact-website-0kn5m?file=/components/shared/Nav/index.tsx).** 

## Thanks for reading! If this article was helpful...

I love writing about this sort of stuff. With this, I'm officially 4 posts into my goal to **post once a week in 2021!** Go ahead and [**drop a follow**](https://dev.to/bholmesdev) to hold me accountable 😁

You can also 🐦 [find me on Twitter](https://twitter.com/BHolmesDev) for tens of tweets of month, and learn more about ❤️ [Hack4Impact here](https://hack4impact.org)!

