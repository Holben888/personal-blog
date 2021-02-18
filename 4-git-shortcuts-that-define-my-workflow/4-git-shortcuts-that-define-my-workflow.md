---
title: 4 Git shortcuts that define my workflow
description: Here's 4 bash snippets that let me push, pop, and pull my way to victory as a web dev!
tags: programming, git, bash
cover_image: https://raw.githubusercontent.com/Holben888/personal-blog/main/4-git-shortcuts-that-define-my-workflow/thumbnail.png
---

I started my first full-time job as a programmer this year, which means I've been picking up _a lot_ of new tricks in a pretty quick timeframe. Naturally, I started automating the workflows I use day-in and day-out 😁

Stop me if you've seen this workflow before:

1. I picked up a ticket on JIRA
2. I need to pull the latest main branch
3. I need to checkout a new branch
4. I need to push that branch to origin before opening my PR

I'll probably do this 5+ times in one day if we're in a bug-squashing flow. But when I'm hurrying, it's so easy to either **a)** work off an old "main" branch, or **b)** do the **copy-paste of shame** before your PR:

```
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin crap-not-again
```

_You know you cringe a little every time this pops up_ 😬

So let's discuss:

🍾 The importance of pushing and popping your stash

🙈 The joys of `upstream HEAD`

🧶 Writing aliases for your terminal to tie it all together

_Onwards!_

## The fantastic 4 aliases

Alright, let's do the big reveal. Here's my 4 shortcuts for slamming through daily tasks 💪

```bash
# Stash what I'm working on and checkout the latest master
alias gimme="git stash push -u && git checkout main && git pull -r"

# Grab the latest master and come back to the branch I was on (with stashing!)
alias yoink="gimme && git checkout - && git stash pop"

# Checkout a new branch and push it to origin (so I don't forget that set-upstream)
woosh() {
  git checkout -b $1 && git push -u origin HEAD
}

# ALL TOGETHER NOW
# Stash my current WIP, checkout a new branch off the latest master, and push it to origin
alias boop="gimme && woosh"
```

![1-fantastic-4-snippets](https://raw.githubusercontent.com/Holben888/personal-blog/main/get-s***-done-2020/1-fantastic-4-snippets.gif)

These commands are written in `bash`. So if you want to use these in your own terminal, go find your `bashrc` and paste away! This is usually found in your user directory: `~/.bashrc`. And for all you windows users out there: you'll probably need to [install the Git Bash terminal](https://git-scm.com/downloads) (included with Git) and create the file [like so](https://stackoverflow.com/questions/6883760/git-for-windows-bashrc-or-equivalent-configuration-files-for-git-bash-shell).

**✋ But wait!** Before you blindly copy these, let's unpack each of them a little.

## `gimme` ✊

This is my "return to home base" command. It assumes you might _not_ be on the main branch just yet, so it'll stash everything you're working on using the `stash` command. It also saves "untracked" / new files with the `-u` flag.

Next, it checks out your `main` branch and pulls the latest contents. That `-r` flag will be sure to "rebase" onto the latest, preventing unecessary merge conflicts.

This is the simplest command of the bunch, but it's also my most-used! On my team, we run a lot of automated scripts on `pull` for installing dependencies and pulling content from our [CMS](https://www.optimizely.com/optimization-glossary/content-management-system/). So, it's super useful to return to `main` and grab all this new content in one go.

## `yoink` 🎣

This phrase is certainly a close relative to `gimme`. For some etymology, I'll include the [urban dictionary entry](https://www.urbandictionary.com/define.php?term=Yoink) on what it means to _yoink:_

> An exclamation that, when uttered in conjunction with taking an object, immediately transfers ownership from the original owner to the person using the word regardless of previous property rights.

...In other words, we are grabbing the main branch and bringing it back to the branch we're on 😁 We pull this off using the `git checkout -` command after running `gimme`. This is super convenient for grabbing the latest changes to [force rebase the branch](https://egghead.io/lessons/git-push-a-rebased-local-branch-by-using-force-with-lease) we're working on.

**💡 Note:** is easy to achieve with a `fetch` command as well:

```bash
git fetch origin master:master
```

...but if your team runs automated scripts whenever you `pull`, you'll probably want the original command I described.

### Clarification on using `stash` vs `stash push`

You also may be new to using `push` and `pop` on your stash, instead of `apply` and `clear` (as I often used to do). Here's the distinction:

- Whenever you run "vanilla" `git stash`, **you're _merging_ your current changes** with all the other changes in the stash. Think of this like throwing all your belongings in a bag, and `git stash apply` is like dumping everything out onto the floor.
- Whenever you run `git stash push`, you're **putting your current changes in a _separate compartment_** from all the other changes in the stash. Think of this like choosing a different bag pocket every time you go to "stash" something. Then, when you `pop`, you pull from the last pocket you just used.

This is the reason we use `push` inside the **`gimme` command,** and `pop` inside the **`yoink` command.** It guarantees that we grab the same thing we just stashed, without getting mixed up with anything _already in_ your stash. If you've ever dug around your bag trying to find one tiny thing stuck at the bottom, you know what I'm talking about here 😁

## `woosh` 💨

This is the solution to your `--set-upstream` woes. Instead of pushing to origin later, this lets you check out a new branch and push _right away_ so you don't forget.

Yes, there's some cases when you don't want your local branch on the remote just yet, but this is pretty rare in my experience. I often create a branch corresponding to a given task on our Kanban board that I'm ready to push up right away.

And if you've never seen the `HEAD` parameter before... **remember that one!** It's a super slick way to auto-fill the name of your current branch instead of typing it out by hand 🔥

## `boop` 👇

_Shout out to "lord of boops" [Jason Lengstorf](https://www.jason.af) for the inspiration_

This command goes full-circle. If you're somehow too lazy to check out the latest `main` branch before starting anew, this will let you `gimme` and `woosh` all in one go!

Not much to explain with this one. Just go forth into perfect boop-dom.

## Thanks for reading! If this post helped you...

I love writing about this sort of stuff. With this, I'm officially 7 posts into my goal to **post once a week in 2021!** Go ahead and [**drop a follow**](https://dev.to/bholmesdev) to hold me accountable 😁

You can also 🐦 [find me on Twitter](https://twitter.com/BHolmesDev) for tens of tweets of month, and [visit my site](https://bholmes.dev) to learn more about my web dev journey.

