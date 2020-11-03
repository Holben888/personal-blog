# HTML5 tags - how do they work, and which ones should I use?

**Let's get down to the fundamentals.** Whether you've seen HTML or not, it may feel unclear when to use each element, or even what elements are available these days. The following should be a helpful cheatsheet for web dev beginners and markup professionals alike!

_This article is a snippet from **Bits of Good's web dev bootcamp.** If you like what you see, [head over here](https://www.notion.so/gtbitsofgood/Curriculum-ca431096426b4fd1968ac49121ff2fdb) to view the full course (with written resources, video walkthroughs, and project activities) for free!_

# Starting with definitions

HTML, or _Hypertext Markup Language,_ is a language used to define the "skeleton" of a webpage. It isn't meant to include any bells and whistles, and it definitely won't look pretty on its own! It's simply meant to define _what_ gets shown on the page, from images to text and everything in between.

All modern browsers support the latest HTML standard: **HTML5**. This adds a whole host of features to improve the structuring of webpages. Namely, it adds special elements to denote the "layout" of the page, like where the navigation bar is, where the footer is, and so on. This is super useful for visually impaired individuals that rely on screen readers to browse the Internet, since it's now easier than ever to explain all the landmarks on the page right from the HTML!

## Getting into syntax

When writing HTML, you will be working with something known as a **tag.** Basically, this is used to define some block of content you want to display on your website. There are many different tags you can use for different types of content, like blocks of text, images, headings, and so on.

For example, we can use the paragraph tag `<p>` to display a block of text like so:

```html
<p>
  Hard work and no play makes Jack a dull paragraph.
</p>
```

Note that we need to right the tag twice: once to define where its content starts and again to define where it ends. In this case, it starts before our block of text (`<p>`) and ends just after our text block (`</p>`). Note you need an added `/` to denote a closing tag versus an opening tag. This whole block start to finish is called an **element.**

### Attributes

Along with declaring a tag, you can also add a set of **attributes** to that tag for extra information. These are written using the format `attribute="value"` inside the opening tag.

The `class` and `id` attributes are the most common, which will be explained better [once we start using CSS](https://www.notion.so/gtbitsofgood/CSS-Overview-and-specificity-519611a09c684d85910f40a3e00fcb06). Another example is declaring the language used by your page with `lang` as shown below.

### The main wrapper

```html
<html lang="en-US">
  <body>
    <!-- More to come in here! -->
  </body>
</html>
```

This is a basic wrapper you would always want to put around your HTML file. First, we create the base `html` element and specify our page's language using `lang="en-US"`. Since anyone from Canada to Singapore may access the websites you build, it's best to specify the language so translation tools know what to expect!

Next, we add our body for all of our website's content to live. This is denoted by the `body` element. Also notice the `<!-- … -->` inside. This is how comments work in HTML, so whatever's inside those brackets won't be shown on the page.

_**Note:** If you use CodePen, each Pen autmoatically adds these `html` and `body` wrappers for you around whatever code you put inside the "HTML" box, complete with the language specification. So, no need to use it on every CodePen you create, but know it's an important concept when building website outside of this tool!_

# Important tags

There are **many** HTML tags worth knowing. You can find the full list [here](https://www.tutorialrepublic.com/html-reference/html5-tags.php), but these are some highlights you should know for getting started 🏃‍♂️

## Tags for content

- `<p>` A paragraph block; the most general block when you want some text on the page
- `<h1>` ... `<h6>` A header block, with `h1` being the highest level header, `h2` being the second highest, and so on. A newspaper article is a solid example of this, where the article title would be an `h1`, each section heading would be an `h2`, and each subheading would be an `h3`
- `ul` / `ol` A block for creating a list of content. Use a `ul` when you want a bulleted (*u*nordered) list and an `ol` for a numbered (*o*rdered) list
  - `li` For each element inside of the list
- `dl` A block for creating a more complex list of content. This works a lot like a `ul` / `ol`, but now each item can have a title _and_ a description as two separate blocks. Perfect for lists that have a header / body structure
  - `dt` The "definition title," or the title of a list item
  - `dd` The "definition description," or the content of that list item
    _**Note:** No need for a wrapper around these two elements. Just place the `dd` element below its associated title `dt`, and the list item should look right! If you absolutely need to, it's safe to wrap these in a `div` for styling purposes._
- `<span>` A wrapper around text within a paragraph or header. This is great for targeting certain lines or words in a text block for custom styling
- `<a href="https://penguin.club">` A weblink to some other website or a page within your own website. The link is specified by the `href` attribute

  _**Note:** You can force links to open in a new tab instead of the current page by including the attribute `target='_blank'`_

- `<img src="https://cute_cat.gif" alt="gif of a cat eating ice cream" />` An image, with the actual image file coming from the `src` link and the `alt` describing the image. You can read the `alt` when hovering over an image or when using a screen reader
- `<button>` A button you can click. You'll use JavaScript to get buttons to actually do something, but we'll leave that as a mystery for now 😁

## Tags for the layout of that content

- `<header>` A page header, often with the site banner and navigation bar
- `<footer>` A page footer, often with important links that couldn't go anywhere else
- `<main>` The main content area of a page. Helpful to separate from the header and footer
- `<nav>` The navigation bar of a webpage (aka that ribbon at the top with all the links to other pages on the site). Usually within the `<header>`
- `<section>` A sub-portion of the main content. Good for dividing up, well, sections of the page. Yes, nesting sections within sections works as well!
- `<div>` A general way to define a container. You'll end up reaching for these when working on page layouts, trying to position content in certain places on the page

### `<div>` versus `<section>`

Generally, you'll use a `<section>` to group elements under common themes, like in a book report outline you'd write in high school. A `<div>`, meanwhile, is not used to convey this sort of separation. Take a news article as an example. We may say the article's content and the comments are two separate `<section>` elements. However, each comment, along with the container for like and dislike buttons, are just `<div>`s.

## Try these elements yourself!

Hop over to [codepen.io](http://codepen.io) and make a new Pen. Start pasting some of the tags above into the HTML section to see what they display in the preview box!

## See these tags in action

Now, let's take a look at a completed website to see how the HTML is structured. We'll be using your browser's "inspector" for this, and you can follow along by visiting the live site at https://gsg.surge.sh. I encourage you to open this tool on some of your favorite website as well to see how they structure their own markup!

[![Screenshot of the Golden Swarm Games club splash page](http://img.youtube.com/vi/I4FK_7pZJV8/0.jpg)](http://www.youtube.com/watch?v=I4FK_7pZJV8 'Intro to HTML - inspecting how a real website is structured!')
**Intro to HTML - inspecting how a real website is structured!**
[https://www.youtube.com/watch?v=I4FK_7pZJV8](https://www.youtube.com/watch?v=I4FK_7pZJV8)

## Found this helpful?

Great! This article is part of a _huge_ suite of resources used by the Bits of Good organization to teach newbies the ropes. If you want to...
💅 **learn how to write better CSS with your bare hands**
⚙️ **get the hang of array functions in JS**
📶 **figure out how to talk to APIs**
👩‍💻 **build feature-rich frontends and backends using JS frameworks**
🏃‍♂️ **_and most anything else in the world of web dev_,**
Check out the Bits of Good bootcamp! All activities, written resources, and projects are free for you to explore and share with others 😁

## Access the curriculum [here](https://www.notion.so/gtbitsofgood/Curriculum-ca431096426b4fd1968ac49121ff2fdb)!

If you have any feedback, criticisms, or content suggestions, message us at hello@bitsofgood.org.
