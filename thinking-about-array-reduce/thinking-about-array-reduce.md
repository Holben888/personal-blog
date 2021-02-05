---
title: Another way to understand array.reduce
description: I struggled with understanding array.reduce for a while. So I found my own way of explaining it that clicks 💡
tags: javascript, beginners
cover_image: https://raw.githubusercontent.com/Holben888/personal-blog/main/thinking-about-array-reduce/thumbnail.png
---

If you've run the guantlet of [array methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#instance_methods) in JavaScript, you've probably hit a conceptual roadblock:

_Wait, how do I use the `reduce` function again?_

I actually led a JS bootcamp on this topic at Georgia Tech ([material 100% free-to-use here!](http://hack4impact.org/dev-bootcamp)). Questions on `reduce` have come up so many times, and I think I've finally found an explanation that clicks 😁 Hope it works for you too!

## 🎥 Video walkthrough

If you prefer to learn by video tutorial, this one's for you! You can [fork this CodePen](https://codepen.io/bholmesdev/pen/rXJpmr?editors=0010) for the source material to follow along 🏃‍♂️

{% YouTube P9qlPEg_MKc %}

## 📝 Step-by-step cheatsheet

Let's walk our way to `reduce` by using what we know: good ole' for loops.

Here's an example. Say we have our favorite album on a CD (remember those? 💿), and our stereo tells us the length of each track in minutes. Now, we want to figure out how long the _entire album_ is.

Here's a simplified approach for what we want to do:

```js
// make a variable to keep track of the length, starting at 0
let albumLength = 0
// walk through the songs on the album...
album.songs.forEach(song => {
  // and add the length of each song to our running total
  albumLength += song.minutesLong
})
```

No too bad! Just loop over the songs, and **accumulate** the album runtime while we walk through the songs. This is basically the process you'd use in real life, tallying up the album length as you skip through the tracks on your stereo.

That word "accumulate" is pretty significant here though. In essence, we're taking this list of track lengths, and **reducing** them to a single **accumulated** number: the `albumLength`. This process of **reducing** to an **accumulator** should set off a light bulb in your head: 💡 _we can use `array.reduce`!_

### Going from `forEach` to `reduce`

Let's try reduce-ifying our function from earlier. This is a simple, 4 step process:

1. Change `forEach` to `reduce`:

```js
let albumLength = 0
album.songs.reduce((song) => {
  albumLength = albumLength + song.minutesLong
})
```

2. Move `albumLength` to the **first parameter of the loop function**, and the initial value (0) to the **second parameter of `reduce`**

```js
// accumulator up here 👇
album.songs.reduce((albumLength, song) => {
  albumLength = albumLength + song.minutesLong
}, 0) // 👈 initial value here
```

3. Change `albumLength = ` to a **return statement.** This isn't too different conceptually, since we're still adding our song length onto our "accumulated" album length:

```js
album.songs.reduce((albumLength, song) => {
  // 👇 Use "return" instead of our = assignment
  return albumLength + song.minutesLong
}, 0)
```

4. Retrieve the result of our `reduce` loop (aka our total album length). This is just the value returned:

```js
const totalAlbumLength = album.songs.reduce((albumLength, song) => {
  return albumLength + song.minutesLong
}, 0)
```

**And that's it!** 🎉

### So wait, why do I even need `reduce`?

After all that work, `reduce` might feel like a slightly harder way of writing a `for` loop. In a way... it kind of is 😆

It offers one key benefit though: **since `reduce` returns our total, function chaining is a lot easier.** This may not be a benefit you appreciate right away, but consider this more complex scenario:

```js
// Say we have this array of arrays,
// and we want to "flatten" everything to one big array of songs
const songsByAlbum = [
  ['15 step', 'Nude', 'All I need'],
  ['Burn the witch', 'Daydreaming', 'Glass eyes'],
  ['Paranoid android', 'No surprises', 'Karma police']
]
let songs = []
songsByAlbum.forEach(albumSongs => {
  // "spread" the contents of each array into our big array using "..."
  songs = [...songs, ...albumSongs]
})
```

This isn't too hard to understand. But what if we want to do some _more_ fancy array functions on that list of `songs`? 

```js
// Ex. Make these MF DOOM songs titles all caps
let songs = []
songsByAlbum.forEach(albumSongs => {
  songs = [...songs, ...albumSongs]
})
songs.map(song => song.toUppercase())
```

![MF DOOM giving finger guns](https://raw.githubusercontent.com/Holben888/personal-blog/main/thinking-about-array-reduce/mf-doom-finger-guns.gif)

_All caps when you spell the man name. Rest in piece [MF DOOM](https://open.spotify.com/artist/2pAWfrd7WFF3XhVt9GooDL?si=McgDfNnbTJK5yIdKB_EHGQ)_

This is fine, but what if we could "chain" these 2 modifications _together_?

```js
// grab our *final* result all the way at the start
const uppercaseSongs = [
  ['Rap Snitches Knishes', 'Beef Rap', 'Gumbo'],
  ['Accordion', 'Meat Grinder', 'Figaro'],
  ['Fazers', 'Anti-Matter', 'Krazy World']
]
// rewriting our loop to a "reduce," same way as before
.reduce((songs, albumSongs) => {
  return [...songs, ...albumSongs]
}, [])
// then, map our songs right away!
.map(song => song.toUppercase())
```

_Woah!_ Throwing in a `reduce`, e just removed our standalone variables for `songsByAlbum` and `songs` entirely 🤯

Take this example with a grain of salt though. **This approach _can_ hurt the readability of your code** when you're still new to these array functions. So, just keep this `reduce` function in your back pocket, and pull it out when you could really see it improving the quality of your code.

## Thanks for reading! If this post helped you...

I love writing about this sort of stuff. With this, I'm officially 5 posts into my goal to **post once a week in 2021!** Go ahead and [**drop a follow**](https://dev.to/bholmesdev) to hold me accountable 😁

You can also 🐦 [find me on Twitter](https://twitter.com/BHolmesDev) for tens of tweets of month, and learn more about ❤️ [Hack4Impact here](https://hack4impact.org)!

