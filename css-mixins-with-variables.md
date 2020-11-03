# How you can (sort of) write SASS @mixins in plain CSS

_**Trigger warning:** I may use SASS and SCSS interchangeably throughout this post. You've been warned_

You probably clicked on this post thinking "wait, you can't write mixins in plain CSS. What kind of clickbait is this?"

Let me admit something right off the bat: this post _does not_ show a one-to-one solution to replicate everything amazing about mixins. It's not like there's some magical corner of CSS to **ENGAGE SASS MODE.** 

![Plankton (from Spongebob) whipping a krabby patty into maximum overdrive](https://media1.tenor.com/images/faf7ef24e333a644891ae19a07da3529/tenor.gif?itemid=5564441)



So yes, the pattern I'm about to detail isn't a _direct_ translation of SCSS / Stylus / LESS mixins. Still, it will help you overcome a problem that makes you reach for mixins in the first place:

1. You have some CSS you want to reuse in a bunch of places
2. You want to pass some **parameters** to that reusable CSS to adjust its behavior

# Alright, expectations lowered. What do ya got?

Well, it all starts with our friend, [CSS variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties).

If you haven't used them before, CSS variables are a lot like SCSS variables; you can store anything you want in them, and you can use them in any ruleset you define. However, these variables are _extra powerful_. Rather than being a value you define **once** and use everywhere, CSS variables can be reassigned at **any level of the cascade**. SCSS details this distinction [on their own documentation](https://sass-lang.com/documentation/variables).

That kind of power let's you pull off something like this:

```html
<div style="--theme-color: red">
  <p>I'm a red paragraph!</p>
</div>
<div style="--theme-color: blue">
  <p>I'm a blue paragraph!</p>
</div>
```

```css
p {
  color: var(--theme-color);
}
```

Now, each paragraph will have a different color, depending on the variable value assigned by the parent `div`. Nice!

# A concrete example

Say you have some social links on your site. You want all of these links to have a uniform layout, but you want to adjust the coloring to match the site you're linking to. In this case, we have two links to consider:

![Demo of two links with different colors (Twitter and GitHub)](/Users/benholmes/Creative Cloud Files/Documents/Blog/css-mixins-with-variables/mixins-concrete-example.gif)

There's a bit more to this example than meets the eye. Notice each link has a different color in not one, but _four places_:

- The text `color`
- The SVG icon's `fill`
- The link `border`
- The `background-color` when you hover

Without variables, this would be extremely annoying / not-DRY. We'd have to write four rulesets (including nested styling for the icon) for every new color we add 💀

If you're cool enough to use a preprocessor, you could truncate your styles using a handy mixin. It [goes a little something like this](https://www.youtube.com/watch?v=TLGWQfK-6DY):

```css
@mixin color-link($color) {
  color: $color;
  border: 1px solid $color;
  
  /* color the nested icon */
  svg {
    fill: $color;
  }
  
  /* change the background color on hover */
  &:hover {
    background-color: $color;
  }
}
```

Now, if we ever have a new link color to add, we can write a nice one-liner in a new selector:

```css
.twitter-link {
  @include color-link(#1fa0f2);
}
```

**Here's a Pen to show this solution in action:**

<p class="codepen" data-height="265" data-theme-id="light" data-default-tab="html,result" data-user="bholmesdev" data-slug-hash="Yzyavop" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Mixin demo - SCSS">
  <span>See the Pen <a href="https://codepen.io/bholmesdev/pen/Yzyavop">
  Mixin demo - SCSS</a> by Benjamin Holmes (<a href="https://codepen.io/bholmesdev">@bholmesdev</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Alright, but plain CSS can't do that...

That's where you're _wrong!_ Though CSS lacks our nifty `@include` syntax, we can still pass variables to a `color-link` ruleset in a similar way.

Let's start with a raw example, no variables applied:

```css
/* change our mixin to a CSS class */
a.color-link {
  /* replace each $color reference with a hardcoded val for now */
  color: #1fa0f2;
  border: 1px solid #1fa0f2;
}

/* CSS can't do nested rulesets, so we gotta separate these out */
a.color-link > svg {
  fill: #1fa0f2;
}

a.color-link:hover {
  background-color: #1fa0f2;
  color: white;
}

a.color-link:hover > svg {
  fill: white;
}
```

We did a couple things here:

1. We turned our `color-link` mixin into a plain ole' CSS class
2. We got rid of our nesting syntax since CSS still can't do that ([but it could be coming soon!](https://tabatkins.github.io/specs/css-nesting/))

**Now, we're ready to introduce some variable magic.** ✨

```css
a.color-link {
  /* replace our hardcoded vals with a variable reference */
  color: var(--color);
  border: 1px solid var(--color);
}

a.color-link > svg {
  fill: var(--color);
}

a.color-link:hover {
  background-color: var(--color);
  color: white;
}

a.color-link:hover > svg {
  fill: white;
}
```

And finally, we'll rewrite our `twitter` and `github` classes from before:

```css
.twitter-link {
  --color: #1fa0f2;
}

.github-link {
  --color: #24292D;
}
```

_Boom._ With CSS variables, we can just assign a value to our `color` variable in whatever CSS selector we choose. As long as we apply either of these guys alongside our `color-link` class...

```html
<a class="color-link twitter-link">...</a>
```

Our `color-link` "mixin" will apply the appropriate colors where we need them!

**Here's another CodePen to see this working:**

<p class="codepen" data-height="265" data-theme-id="light" data-default-tab="html,result" data-user="bholmesdev" data-slug-hash="LYpdMyr" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Mixin demo - plain CSS">
  <span>See the Pen <a href="https://codepen.io/bholmesdev/pen/LYpdMyr">
  Mixin demo - plain CSS</a> by Benjamin Holmes (<a href="https://codepen.io/bholmesdev">@bholmesdev</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

# Yes, there are still limitations

This definitely makes your plain CSS stylesheets more DRY, but it fails to address some funkier use cases.

For example, SCSS can pull off conditionals and looping inside its mixins. This can let you, say, pass true / false booleans as parameters to switch between styles.

```css
/* arbitrary example, applying different layout mixins depending on browser support */
@mixin grid-if-supported($grid) {
    @if $grid {
        @include crazy-grid-layout;
    } @else {
        @include crazier-flexbox-layout;
    }
}
```

_Inspired by a rundown of magical mixin features [found here](https://scotch.io/tutorials/how-to-use-sass-mixins)_

We also have to modify our HTML by applying more classes. Depending on who you ask, this defeats part of a mixin's sexiness. Mixins can be dropped into any existing ruleset you already have, without needing to create new classes or rulesets.

I agree with this somewhat, but this post should at least show off how powerful CSS variables really are. They can even store complicated styles with spacing and commas, like `--crazy-padding: 12px 12px 0 0` or `--dope-box-shadow: 1px 2px 3px #abcabc`. The same can't be said for SCSS mixin parameters!

# Thanks for reading!

This post is part of my hard-headed goal to ditch CSS preprocessors. I definitely miss nested styles... but the power of CSS variables, combined with literal _microseconds_ of build time whenever I save, make me happy enough with raw CSS these days 😁

In case you're interested, I have another post taking a deeper dive into CSS variables, exploring how you can down on JS style manipulation. [Check it out here!](https://dev.to/bholmesdev/how-using-css-variables-cut-down-on-my-javascript-dc4) 

Also, expect some more posts over the next few months. I finally graduated college, which I have... [mixed feelings about to say the least](https://twitter.com/BHolmesDev/status/1258771501647646722?s=20). This month should be nothing but pumping out quarantine-ridden content before I start my first _adult job._ 

**So, drop a follow if this helped you!** More to come soon.

