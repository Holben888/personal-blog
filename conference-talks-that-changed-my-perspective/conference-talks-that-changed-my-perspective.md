---
title: Conference talks that changed by perspective as a web dev
description: Let's explore architecture complexity, when abstractions are bad, how React Hooks work, and the importance of SvelteJS
tags: webdev, programming, react, svelte 
cover_image: https://raw.githubusercontent.com/Holben888/personal-blog/main/conference-talks-that-changed-my-perspective/thumbnail.png
---

## Rich Hickey - Simple Made Easy 🧶

[**🎥 View the talk + transcript here**](https://www.infoq.com/presentations/Simple-Made-Easy/)

If you've ever heard the someone say that something is "easy, but not simple," this is probably the talk they're referencing. This is an easy recommend to programmers in general (not just web devs). Still, I think this talk is *especially* relevant to frontend-ers nowadays with all the tools at our disposal. 

It feels like web frameworks and "best practices" are moving towards some powerful new opinions:

1. **Everything is a component**
2. **Functional programming is king**
3. **State management is complex** and deserves a rethink ([hello state machines](https://xstate.js.org/docs/about/goals.html#api-goals) 👋)

☝️ These are the points Rich was getting at with this tallk, _over a decade ago!_ This is why I highly recommend revisiting this talk *multiple* times throughout your web dev journey. As a junior getting comfortable with enterprise-grade React apps, it's helped me understand the _why_ behind my team's architectural decisions.

### Personal notes

- **Simple** is an objective measure, no matter the person, reflecting how many **interwoven pieces** (complexity) there are in a given system
- **Easy** is relative to every individual, reflecting how "familiar" or "near at hand" something feels
- Agile programming encourages us to move fast without taking a step back
  - Complexity and tech debt pile up when we ignore signs of bad abstraction
  - Favorite quote of the talk: _"But programmers fire the starting pistol every 100 yards and call it a (new) sprint!"_

- Replace **complecting** (knotted-up code with lots of interdependent pieces) with **composing** (modularity a la modern frontend frameworks)
  - Think building a castle from Legos instead of a "knitted castle" from interweaving
  - Separate horizontally, stratify vertically
- Concrete improvements to make
  - **"Stateful" variables complect _values_ with _change overtime_**; make transition from one state to another predictable (see [state machines](https://xstate.js.org/docs/about/goals.html#choosing-xstate))
  - **Stay close to what the system does;** consider the _behavior_ over _implementation details_
    - Example: [Declarative over imperative programming](https://www.freecodecamp.org/news/imperative-vs-declarative-programming-difference/)
    - Example: test-driven-development done right ([incredible talk by Ian Cooper here](https://www.youtube.com/watch?v=EZ05e7EMOLM))
  - **Work with _rules_ over conditional / switch case spaghetti**
    - Given some data X, here's some rules to make it become Y
    - Lean on [pure functions](https://www.freecodecamp.org/news/what-is-a-pure-function-in-javascript-acb887375dfe/), which give you a consistent output for a given input

## Dan Abramov - The Wet Codebase 🌊

[**🎥 View the talk + transcript here**](https://www.deconstructconf.com/2019/dan-abramov-the-wet-codebase)

Here's another architecture-y talk that also reaches far beyond web development. If you're unfamiliar with [Dan Abramov](https://overreacted.io), he's one of the most prolific members of the React core team from a teaching standpoint alone. So if you want advice on architecting your web apps, this is your guy 😁

This talk goes hand-in-hand with [Kent C Dodd's post on "AHA programming"](https://kentcdodds.com/blog/aha-programming). Generally, they're both addressing the biggest pitfall of component-based thinking: copy / paste feels like bad practice, so you abstract _every_ piece of logic to its own little file.

Sure there's a place for abstraction, but there's _also_ a place for duplication! This talk has a lot of examples and funny quotables to keep things light; definitely worth the watch.

### Personal notes

- If left unchecked, abstractions can become _Frankenstein_ code overtime
  - An abstraction _almost_ fits are use case, but not quite 👉 we widdle away that round hole to fit our square peg
  - When bugs arise for _one_ use case, we introduce fixes affecting _tons_ of other use cases
- 👍 When abstraction is good
  - **Makes your code more declarative** / focus on a specific intent (see that Rich Hickey talk above 😉)
  - **Avoids missed bug fixes** 👉 fix it once, fix everywhere
- 👎 When abstraction is bad
  - **Creates coupling** - when it doesn't _quite_ fit, you can create a monster of refactors
  - **Adds indirection** - creates layers and layers overtime; _"We avoid spaghetti code, but we create lasagna code"_ 🇮🇹
- Ways to improve going forward
  - **Test code that _uses_ an abstraction**, not the abstraction itself
    - If you remove that abstraction later, your tests explode!
    - Abstractions are just another implementation detail (again, [TDD is king](https://www.youtube.com/watch?v=EZ05e7EMOLM))
  - **Don't commit to abstraction layers until you need them;** _"If a girl is into the same obscure bands as you are... that doesn't mean you're meant to be together"_
  - **Be ready to remove abstractions later;** Be the one asking _"Please inline this abstraction!"_ in a PR review!

## Rich Harris - Rethinking Reactivity

{% youtube AdNJ3fydeao %}

In my opinion, this is **the biggest bombshell to drop** since React was first revealed 💣

A trigger warning is probably in order here: if you're a diehard React follower, this talk questions _a lot_ of practice React holds dear (including the virtual DOM itself!).

But even if you disagree with Rich's points, this talk is a _serious_ landmark in the web framework canon. It also exposes what "bundlers," "compilers," and "reacting to change" all _really_ mean under the hood. If you aren't convert to a Svelte fan after this, you'll at least understand where the web has been, and where it might be heading!

### Personal notes

- **What is reactive programming?**
  - It all started with spreadsheets
    - I change a value in one cell, and other cells "react" to those changes with formulas
    - Earliest example of **only re-rendering values that change**
  - It's 1) about tracking values and 2) updating _dependents_ on that value
- **Problem with React's model**
  - When state changes in a component, that *whole component* re-evaluates itself from the top
  - Treats your HTML like a black box; apply the change, then diff against the previous chunk
  - Really, React doesn't know about your "state values" or how they affect the DOM!
  - **Bad signs for efficiency:** I shouldn't need `useMemo`, `useCallback`, `shouldComponentUpdate`, etc
- Instead of opting _out_ of reevaluating state (a la `useMemo`), we could opt _in_ by flagging state variables that depend on other state variables
  - Much like a spreadsheet; write formulas that flag which variables affect a given value
  - Svelte uses a custom `$:` operator to "flag" state that is computed from _other_ state [(example here)](https://svelte.dev/tutorial/reactive-declarations)
- **Svelte is a compiler, not a runtime**
  - In other words, a "Svelte" component compiles to JS your browser understand
  - No "runtime" (like React's virtual DOM) needs to get imported
  - Also means Svelte can **bend the JS language to its will** 
    - Evan You, creator of VueJS - Svelte is too magical, since it lets you write JavaScript that isn't totally valid 
    - Rich Harris' response - this opinion is like believing HTML, CSS and JS should live in separate files. CSS-in-JS is weird too, so what's wrong with this?
- Some other cool demos of Svelte
  - [**CSS scoping by component**](https://css-tricks.com/what-i-like-about-writing-styles-with-svelte/) just by using a `<style>` tag
  - [**Transition directives**](https://svelte.dev/tutorial/transition) with sensible out-of-the-box options

## Shawn "swyx" Wang - Getting Closure on React Hooks 🎣

{% youtube KJP1E-Y-xyo %}

This is definitely a fast-paced, code-heavy talk, so you'll probably want 1x speed on this one. 

That said... this is _the most_ enlightening talk I've seen on React. Period. It's only 30 minutes long, but it gave me holistic understanding on how React hooks, state management, and re-rendering all work together. It also shows some huge use cases for "closure" in JS. If you have a web dev interview coming up, point to this! 😉

### Personal notes

Hard to write a succinct, bulleted list for this one. So, I just annotated the finished product to explain how everything works. Fair warning: it's a _lot_ to take in!

**[🚀 Functioning codepen to see it in action](https://codepen.io/bholmesdev/pen/YzpWGaz?editors=0011)**

```js
const React = (function () {
  // create an array for all the state variables in our "React app"
  let stateValues = [];
  // start our state index at 0. We'll use this
  // to throw state into that array ☝️ everytime someone calls "useState"
  let index = 0;
  function useState(initValue) {
    // state should be set to either:
    // 1. the initial value (aka React.useState(initValue))
    // if this is the first time our component rendered
    // 2. the value from the *last* render
    // if we're re-rendering our component (aka stateValues[index])
    const state = stateValues[index] || initValue;
    // "freeze" our index to this particular useState call with _index.
    // prevents the index from changing before we try to call setState later!
    const _index = index;
    const setState = (newValue) => {
      stateValues[_index] = newValue;
    };
    // increment index so our next useState call doesn't override the state
    // we just stored above
    index++;
    return [state, setState];
  }
  function render(Component) {
    // every time we re-render our app,
    // update all our state variables starting from the top
    index = 0;
    const C = Component();
    // "render" the component (which calls the useState function)
    C.render();
    return C;
  }
  // return all our functions from this foe React "module"
  return { useState, render };
})();

function Component() {
  const [count, setCount] = React.useState(2);
  return {
    // whenever we "render" this component with React.render, 
    // just log the value of our state variable
    render: () => console.log({ count }),
    click: () => setCount(count + 1)
  };
}

let App = React.render(Component) // logs { count: 2 }
App.click() // sets the state at stateValues[0] to 3
App.click() // sets the state at stateValues[0] to 4
React.render(Component) // logs { count: 4 }
```

## Thanks for reading! If this post helped you...

I love writing about this sort of stuff. With this, I'm officially 6 posts into my goal to **post once a week in 2021!** Go ahead and [**drop a follow**](https://dev.to/bholmesdev) to hold me accountable 😁

You can also 🐦 [find me on Twitter](https://twitter.com/BHolmesDev) for tens of tweets of month.
