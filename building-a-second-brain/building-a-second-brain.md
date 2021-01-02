# Giving up on Notion to build a second brain

Okay, I'll be honest: I have a serious problem with notetaking apps. I've whipped through every service imaginable for a few months each... but I've never truly felt "at home."

I think this is my problem above all: **I need to write something down, but I don't know what magical folder / page / service / tag to stuff it into.** So, I just choose the first note app I find on whatever device I'm using. I swear to myself I'll move it to the right place *someday,* but the thought of organizing everything is too overwhelming to take action 😬

So, let's talk about:

1. Why Notion isn't cutting it for me
2. What the second brain / Zettelkasten method is
3. How Foam + VS Code helped me find a groove with personal notetaking 

Onwards!

## Why not Notion?

This is the main reason I just _can't_ get into using something like [Notion](https://notion.so) for personal notes. Yes, there's a lot to fall in love with from a developer's perspective:

- Easy to whip up code snippets with syntax highlighting
- Easy export to markdown
- Tons of integration options for *data tables*, if you're into that kind of thing
- Keyboard shortcuts galore

etc etc etc. But in the end, getting into Notion is a lot like getting into Vim. **You spend 30% of your time in efficiency heaven, and another 70% setting up the tool itself!** As a UI designer and developer myself, I just can't deal with all these knobs and switches. It's so tempting to tweak everything to perfection... and once the dust has settled, I still can't find that code snippet I made a week ago 😔

I also don't see any updates helping me from this perspective. My problem is really the fundamental approach of Notion: let the user build *their own* UI with as much flexibility as possible. I don't really need all this flexibility if I'm just journaling my thoughts and todos.

### Ignoring the bells and whistles doesn't help much either

Yes of course, I can just cast away my temptations and stick to basic note pages. But this still doesn't address other elephants in the room.

**First, fuzzy finding notes isn't the easiest.** Notion did add a "quick find" option that helps, but dang, the load times are still pretty slow 😢 Just trying to find a page _by its name,_ I've waited a few beats before finding the result I'm looking for (or no results at all). I expect this to improve overtime of course. But since the data is owned by _Notion_ instead of your _personal machine,_ there's always going to be loading spinners.

**I also can't shake the niceties of VS Code.** Right now, Notion doesn't offer options to globally find using regex, search by "directory" (or parent pages in Notion I suppose), or other useful tools developers take for granted. And let's not forget the shortcuts while you're editing! Find-and-replace, or multi-cursors, quickly bumping lines higher or lower? Truly a text editor's greatest hits.

## My dream workflow

With all that aside, let's get to the heart of what I _really_ want:

1. **Go from zero to writing a note with no effort.** Ideally, I just whip up a new file without worrying _where_ it goes
2. **Easily find my notes later.** Fuzzy find, tags, file names... whatever it takes.
3. **Keep the UI simple!** A vast majority of the time, markdown syntax is all I need to get my ideas down.

Turns out, there's an entire notetaking strategy that addresses these concerns!

### Enter the second brain 🧠

I'd been hearing about this concept for a while now, but never realized it's an actual _strategy_ to notetaking. As you'd expect, it's all about writing as much as possible so you can, well, have a second brain in **note form.** 

This concept starts with the [Zettelkasten Method](https://zettelkasten.de/introduction/) used back in the pen-and-paper ages. It's built on some pretty basic principles:

1. Every note is treated as a _unique_ collection of thoughts, tagged by a unique ID of some sort
2. Notes should form an **ever-sprawling tree** of connected ideas / thoughts. This is pulled off with "links" between notes (references to those unique IDs), much like hyperlinks on the web!
3. You may index multiple "trees" of notes with tags or tables of contents, assuming your Zettelkasten grows pretty large

There's countless nuggets of advice on how to do a Zettelkasten *right*. But overall, it's clear that a physical Zettelkasten **maps perfectly to how the web works.** So, why not use a bunch of HTML files to create one? Or better yet, markdown files?

## Let's get back to basics with VS Code + Foam

If we're gonna simplify our notetaking process, there's nothing simpler than a text editor 😁

I discovered a project called [Foam](https://foambubble.github.io/foam/) recently that's... not really a stand-alone project; it's a collection of extensions that work well together, with some [helpful guides](https://foambubble.github.io/foam/recipes/recipes) on how to make the most of them.

All you need to do is [clone a repository](https://github.com/foambubble/foam-template) and watch the magic happen! It'll recommend all the extensions you should need to edit, link, and view your notes. But at the end of the day, you're really just writing a bunch of markdown files on your computer. Let's understand how powerful this can really be:

### Getting a birds-eye view 🗺

It's worth discussing a crucial part of the Foam style of notetaking: **you never need to group notes by directory.** We're so used to using file systems to organize everything, but let's be honest, that's not how our brain works!

Foam thrives on connecting notes with _links_, rather than folder hierarchies. This makes it much easier to define notes that might be referenced in a ton of places. Instead of finding the _exact_ directory where a note lives, you just need to reference the file itself.

Foam will help you find any patterns that naturally emerge from links with a [graph visualization extension](https://foambubble.github.io/foam/features/graph-visualisation). It's basically one big [map of your head](https://www.youtube.com/watch?v=jPCuQMKVh0Y) that you can click into and explore!

![Connected web of notes on the Rust Lang book](https://raw.githubusercontent.com/Holben888/personal-blog/main/building-a-second-brain/1-foam-graph.png)

This is the graph generated by my [recent challenge to learn Rust lang](https://twitter.com/BHolmesDev/status/1329265837828677632). Notice how this doesn't _quite_ match up to a parent-child relationship that directory trees demand. For instance, "Intro to structs" on the far left gets referenced by "Enums" _and_ "Rust ownership." But you couldn't have the same file sitting in multiple directories at once! That's the beauty of using free-form links; **anything can reference anything else**, so it's less of a _tree_ and more of a purposeful, tangled-up birds nest 😁
![birds nest with blue eggs in the center](https://www.birdwatchersdigest.com/bwdsite/wp-content/uploads/2015/01/robin-e1431602899326.jpeg)

_Metaphor for my brain_

### Solving the "I need to write this ASAP" problem

This beautiful graph solves our organization paralysis from earlier! Now that we've ditched the whole directory paradigm, there's a much lower barrier to entry when writing down something new.

And if we happen to write a note that doesn't reference any other notes, no worries! Our graph visualization will help us find those stragglers later on. This helped me consolidate some stray notes on books and podcasts into a table of contents like so:

```md
# The Logs

This is where I'll record everything I consume that _taught me something._ Topic? Doesn't matter. Length of the summary? Could be a couple words. Just need to record everything I possibly can!

## Podcasts
- **11-28-20** [[writing-for-software-developers-with-Philip-Kiely]]

## Blog posts
- **12-20-20** [[Kent-C-Dodds-Avoiding-re-renders a-useDebounce-example.md]]

## Books
- **11-26-20** [[the-dark-forest-Cixin-Liu]]
- **12-29-20** [[the-case-against-education-bryan-caplan]]

...
```

_**Side note:** If you haven't read the [Three Body Problem](https://www.amazon.com/Three-Body-Problem-Cixin-Liu/dp/0765382032/ref=sr_1_1?crid=3TMIORQZ73Q6E&dchild=1&keywords=three+body+problem&qid=1609545547&sprefix=three+body+%2Caps%2C202&sr=8-1), do it. It's unlike anything I've seen before._

This fixes a painpoint I found using Notion as well. Though it's great for quickly linking pages from other pages, you can never get a high-level overview of how everything fits together. Now, all your notes can fit together like puzzle pieces 🧩 

### Other niceties

Foam seems to have a solid community of contributors adding new features. For example, there's an easy way to discover all the pages referencing the current page you're own using **backlinks.**

![VS code extension that lists all pages referencing my current page](https://raw.githubusercontent.com/Holben888/personal-blog/main/building-a-second-brain/2-backlinks.png)

This shows all the references for that "Intro to Structs" note I mentioned earlier, right from the VS Code toolbar. It's a nice lay-of-the-land without scanning the entire graph visualization yourself.

There's also a way to add **tags** to your notes to group common topics. All you need is a little metadata at the top of your note:

```md
---
tags: [learning-rust]
---
```

And boom! It's globally discoverable in VS Code as well 💪

![VS toolbar readout of all notes using a given tag](https://raw.githubusercontent.com/Holben888/personal-blog/main/building-a-second-brain/3-tag-explorer.png)

## So is this the silver bullet to notetaking?

To be honest... I don't think so. At least not for _all_ of my notetaking across devices. This is due to some pretty fundamental limitations:

- **There's no practical way to view these notes on a phone**, let alone edit them
- **I can't add handwritten notes and drawing from my tablet**, unless I want to upload screenshots everytime

Notion had these issues too, so it's definitely not a step back for me (yes I know Notion has a mobile app, but it's full of loading spinners and far too many buttons for my tastes 😬).

Still, I'm seriously impressed with how much I can accomplish with just a text editor and a couple extensions. It's also comforting to actually _own_ my notes instead of using a cloud-based system.

So, I'll definitely be sticking with Foam as I enter 2021 🎉 If you have any tips on getting more out of a "second brain," please leave them in the comments!