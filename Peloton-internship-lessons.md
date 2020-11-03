# Here's what I learned planning and building an MVP as a frontend dev @Peloton

Writing this, I just finished my summer internship at [Peloton Interactive](https://onepeloton.com) 🥳 As I hang up my biker gang helmet once and for all, I wanted to talk about my biggest learning experience: working on a large-scale React project from concept to MVP presentation.

Before you click away hearing the word "internship," note this is a 100% real project that is deployed to production [right now](https://onepeloton.com/careers)! 

***Note:** This post touches both on the product planning process and some of my learnings with git and design. Since I don't know my audience's background writing this, I hope you find some takeaways on any front that speak to you!*

## So, what was the project?

Simply put, to fix... this.

![](old-careers-page-207bdd44-c3cf-444d-92dd-24f81fbfe4f0.gif)

It's nice to see a lot of open positions. Well, except when you get lost finding the right section header 😕 There was also a major problem with company presentation. I don't know about you, but a big list of jobs alone doesn't get me excited about the company I'm applying to!

So, my team was tasked with ripping out our infinite list of open positions and putting something much friendlier in its place. We also needed to communicate the company's story better to get people to **smash** that apply button.

## Lesson 1: The discovery phase is important!

Like any starry-eyed CS student, I expect to start hacking on the frontend right away while the design mockups poured in. But slow down there! There's a lot of research we need to do first. I'll call this the "discovery phase."

At Peloton, analysis of existing solutions (other career sites in this case) is done by the UX designers. Though I didn't play a part in the end experience from a UI perspective, I needed to weight in on options for implementation. In other words, how can we load all of our job listings as fast as possible without too much technical overhead?

For some background, Peloton's existing careers page uses the Greenhouse API to retrieve job postings and display by department header. This is done through a single, extra-large API call to get **all** the job listings with **all** of the departments and **all** of the job descriptions attached. With hundreds of listings and thorough job descriptions on each, that call gets... chunky.

![](chunky-jobs-2fb92734-15a4-4915-b4e7-3238ad164df5.png)

If we try to get that on a poor cell connection, it can take up to 10 seconds before we can start scrolling!

### So, what are some better options?

With this in mind, I started checking the network activity for existing career pages using Greenhouse. From dev tools alone, I could already piece together some smarter approaches to try:

- Paginated loading using a custom endpoint. Since the Greenhouse API doesn't offer pagination, I'd need to add an endpoint to Peloton's backend to divvy up the Greenhouse response into pages. This would turn our page into a full-stack endeavor, though it wouldn't be too difficult.
- Using PHP with [Greenhouse's API plugin.](https://github.com/grnhse/greenhouse-tools-php) Though this adds some helper methods for each endpoint, it adds little functionality over just making the calls myself. Plus, I'd have to learn PHP 😬
- Building a 100% static site. In other words, no network calls at runtime for **instant** loading. However, popping a static site generator like [Gatsby](https://www.gatsbyjs.org) into the repo was almost out of the question due to technical overhead. So, I'd need a custom solution to poll Greenhouse at build-time and keep that list of jobs up to date, which is also fairly complex.
- A hybrid of pagination and static content. [Spotify Jobs](https://www.spotifyjobs.com/search-jobs/) uses this to show the first page of listings instantly and poll the API for additional content. Though slick, this means similar overhead to building a 100% static page.
- Loading just the departments on the homepage for a much smaller API call. When choosing a department, I could retrieve the listings for that department alone.

I noticed another easy win looking closer at the API: each job listing includes a URL linking to the job description and application form. This means we can avoid loading the job descriptions entirely and link to the applications directly, saving a **ton** on response size.

### How did this inform the design?

After researching the Greenhouse API and weighing out these options, I had enough info to come back to product and design. With design observing the UX of existing solutions and myself observing the tech behind them, we converged on a solid approach:

- Display a list of departments on the homepage, along with the number of openings for each. This cuts down on API response size while making the homepage more inviting.
- Create landing pages for each department. Each should include their respective job listings and a location filter.
- Add some extra sections on company mission for some added 🌶️. For the MVP, this meant using the existing company perks section and adding a video on company culture.

This obviously isn't the perfect solution in the end. What if a user wants to browse by location? How will someone interested for a retail role browse compared to an engineer scoping out the HQ?

These questions are worth asking as the team fleshes out the best experience going forward. However, we couldn't lose scope of the problem at hand: making the existing experience snappier and more inviting. If our MVP succeeds in that, it's still a great starting point!

## Lesson 2: Epics are super helpful for task breakdown

After researching ideas and deciding on a solution, we needed to start building actionable tasks. I've done my fair share of semi-thought-out GitHub issues, but this added a whole world of product terminology to the fold.

The first was the "KPI," or a key performance indicator. To put it less fancily, we needed to measure the value a feature would have for the project and the company as a whole. This guided the MVP design process to see what the minimum set of features would be to make a kicka** careers page. By extension, this guided the user stories our tasks would focus on.

...Which brings me to user stories. Though this was covered in my college curriculum, I had little perspective on how useful they were in project planning. For those unfamiliar, the format goes [a little something like this](https://www.youtube.com/watch?v=TLGWQfK-6DY):

As a [stakeholder], I want to do [something] so that [reasons].

Based on this, we can figure out all the tasks necessary to make that goal happen. This often leads to an "epic" each actionable task branches off of. I'd think of the epic as an overarching feature we're developing to address a specific user story. From this, we can work out the major tasks the developers and designers should work on.

For us, this started with a few user stories:

- As a visitor, I want to find the career I'm looking for easily so I can apply.
- As a visitor, I want to view all the jobs available for my preferred location so I can view my opportunities there.
- As a visitor, I want to learn more about the company so I decide if it's a good fit for me.

From these, we worked out some actionable epics:

- Display job listings by department
- Allow filtering of job listings
- Showcase a video about Peloton company mission

With these MVP goals mapped out and mockups in place, it's time to start developing!

## Lesson 3: Smart subtasks lead to manageable PRs

This was a hard lesson to learn as the king of adding unrelated fixes to my branches 🙃 Though I had improved working in team projects at college, I rarely had to plan out a month of tasks myself so PRs could smartly build off of each other. This was difficult at times without getting my hands dirty first, since I may not know the technical implementation of features before starting on them. Even so, instead of diving in head first like usual, I had to sit down and plan a semi-realistic roadmap.

In the end, I worked with the project manager to get a list of steps I'd complete:

- Get the careers page displaying at the new URL (we were using `[onepeloton.com/careers](http://onepeloton.com/careers)` instead of the old `/company/careers` for better discoverability)
- Port over the existing "Perks" section to the new page
- Get the list of departments pulled in from greenhouse, properly formatted for frontend use
- Display the list of departments on the page
- Add a banner image with a "call to action" that will scroll to the list of departments
- Add independent department pages that can be linked to by name (ex. the "Apparel" department can be reached at `onepeloton.com/careers/apparel`)
- Pull in job listings from Greenhouse to display on each department page
- Add a location filter
- Add a department filter that redirects to the different department pages

Making this holistic overview was really help in judging the scale of each feature. It also helped me figure out which tasks were dependent on others, like setting up URL routing before building out the visuals.

### So how did you make sure each PR was "manageable?"

Though this task list guided our issue board, some issues were easily large enough for multiple PRs.

Filing down the task list into PR-able subtasks led to some hiccups along the way. Namely, I ended up restructuring my components multiple times as I started noticing similar functionality. For example, I noticed both the department page and landing page needed access to our API dispatch functions, so I restructured these layouts to use a shared wrapper component. An extra PR needed to pop up in for these situations to keep the purpose of each push hyper-focused. Though this meant more PRs overall that the team needed to review, the scope of them was much clearer! I learned this pointer from a fellow dev who made sure PRs touched about five files or less, excluding one-line edits for imports and the like.

[Test-driven development](https://dev.to/kylegalbraith/how-to-get-started-with-test-driven-development-today-401j) also helped gauge the size of each PR. This was a lesson best learned the hard way... by putting out an [absolute unit](https://www.dailydot.com/unclick/absolute-unit-meme/) of a PR no one had time to review.

This happened by taking on a task and judging its scale at a surface level. Here, I was  setting up [URL slugs](https://prettylinks.com/2018/03/url-slugs/) for each department, so each could have their own link-able landing pages. At first, this seemed like a simple mapping from the names of each department to a slug and setting up routing to display the right page. This had some minor caveats, like waiting for the departments to come in from Greenhouse before generating the slugs, but this easily built on the code I already had. So, I made a new git branch, started hacking it out, got the pages working...

and realized I wasn't handling redirects for bad slugs.

This check for redirects ended up being a little more than met the eye. A coworker showed me a much simpler way to handle redirects using redux state management, but not until waiting a week for my first PR review!

This is a classic example of how thinking in terms of tests could show me all cases I needed to consider, making it easier to break everything down. In my experience, the size of the test file often reflects the size of the final PR. So, if those unit tests are easy to predict, try writing them out early to understand the scope of a feature. If getting 100% test coverage isn't possible, try writing down all the possible use cases for the feature before diving in 😁

## Lesson 4: Designer communication is key

I will confess, this wasn't my first time working with designers on a project. However, they were usually less experienced or working on smaller scale projects with a lot of flexibility around the end product's design. Working with trained UX... xperts on a new project was a new experience, but an exciting one!

First off, getting regular feedback from designers was very helpful for hacking out the CSS. This meant UAT reviews, or giving feedback on a test version of the site, and desk-side checks for more visual PRs. At Peloton, designers work on a pretty strict desktop - tablet - mobile mockup system with specific pixel breakpoints for each. This led to some detailed feedback on all of the layouts I created, down to the smallest 10px padding adjustment. 

As you may expect from such detailed guidelines, Peloton has a comprehensive design system to manage the scalability of elements. In fact, they have a small team of "UI engineers" communicating closely with designers to keep the system up to date. This means using [Storybook](https://www.notion.so/What-I-learned-taking-an-industry-React-JS-project-from-zero-to-MVP-0bec902ca8d64d4ab127483b3351e541) to keep track of all the buttons, headings, banners, etc. for both designers and developers to reference. This made my job a lot easier working out sizing and spacing for text and navigational components, with just a handful of custom layouts left to style by hand. Just paying attention to the breakpoints and using flexbox was all it took to handle these effectively!

![](peloton-careers-SCALABLE-8acd5dc2-b2a6-4894-9cd2-0660eb2e83b9.gif)

*The final landing page for careers. All buttons, headers, and image sizing are from Storybook components, while the arrow icons and 2-column layout use custom CSS.*

There was also close communication on imagery used for each page of the MVP. Namely, we needed to work out breakpoints for where the image would need to be cropped. For example, if a figure at the right side of the image on desktop needs to appear in the center on mobile, two differently cropped versions of that image would be necessary. I could use some positioning magic to crop using pure CSS, but since our project uses [Cloudinary](https://cloudinary.com) to serve differently sized images based on screen width, there's no reason to get that hacky.

## Wrapping up

My time at Peloton this summer was an amazing and rewarding experience. It's rare that a lowly intern can be the main developer on a project team, given the same responsibilities and expectations as a regular employee. It's even rarer that an intern gets to present that feature to company stakeholders, and have that project get deployed to production unchanged! If you're interested, you can [view my slide deck](https://careers-page-demo.surge.sh) from the MVP presentation (yes, it's using [mdx-deck](https://github.com/jxnblk/mdx-deck)!).

Overall, I am extremely grateful for my time at the company, and I'm excited to see how the careers project progresses. I hope these lessons I learned along the way help you on your MVP development journey 🚀

## Thanks for reading!

I'm a frontend web dev and designer always tinkering with something. I just snagged a shiny "dot dev" URL for my portfolio, so head over there for all my socials and a little more about my dev journey!

👉 [https://bholmes.dev](https://bholmes.dev/)

Outline

- What was the project
- Lesson 1: What's a discovery phase? (what is it, how did it affect meeting schedule)
- Lesson 2: Epics to task delegation
- Lesson 3: Smart subtasks for manageable PRs
- Lesson 4: Finding React component hierarchy everyone understands
- Lesson 5: Communicating with designers