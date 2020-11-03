# How mob programming made our remote team stronger than ever

Let's set the scene for those reading this in the future: we're in the middle of a worldwide pandemic, everyone on our team is working remotely, and we're all trying to find motivation to wake up before noon. As a typical software engineer, I certainly could have fallen into the introverted rhythm other workers have:

1. Peruse the team's Kanban board for the best issue to tackle
2. Hunker down at our desk, closing the door to shut out family / kids / construction noise / other distractions
3. Chat with the team for a few minutes to ask questions
4. Go to meetings with the mic off, contributing a sentence or two before the hour's up
5. End the day early waiting for the last PR review to go through

Given everything going on in the world, this is an option a lot of entry-to-intermediate level SWEs are taking. But in the end, this just leads to[ **burn out**](https://www.helpguide.org/articles/stress/burnout-prevention-and-recovery.htm#:~:text=Burnout is a state of,unable to meet constant demands.)**, less productivity, and way too little human interaction.**

Luckily, Peloton has always been a hub of collaborative development. Before going fully remote, the team practiced regular[ pair programming](https://martinfowler.com/articles/on-pair-programming.html) for in-the-moment code reviews and active discussion. But now that we can't share the same keyboard and mouse (or complain about each other's key bindings), how do we keep this collaborative energy alive?

## Enter: mob programming

This age-old philosophy may sound familiar to some. But for those new to the concept: it's a close relative to *pair* programming, just with, well, more people!

Woody Zuill (the father of the "mob" concept) swears by the technique. In short, he gathers all every team member across disciplines, even including product managers, to look at the same monitor and work towards the same goal. This uses employs the same [driver & navigator](https://martinfowler.com/articles/on-pair-programming.html) approach commonly practiced in pair programming, where only one person has the keyboard at a  given time (the driver) and they must be told *exactly* what to type by others on the team (the navigators). 

[His recent conference talk](https://www.youtube.com/watch?v=SHOVVnRB4h0) covers all the merits of this system, and it's the main source of inspiration for our own practices. If you don't have an hour to spare, here's a few of his key points:

1. Constant communication between developers leads to a **"highly amplified learning environment."** This is especially helpful for newer team members and junior SWEs that are still getting familiar with the team's tech stack; since no question is considered an "interruption," there's always room for learning and mentorship.

2. Working as a team is super helpful for discovering and resolving "code smell," which often crops up when a single developer makes decisions that other developers would disagree with. This extends to PR reviews as well, since the team is **agreeing on the code being written in real time.**

3. By involving team members across the stack and across disciplines, the team has a **constant feedback loop** throughout the day. This slowly eliminates the need for one-off meetings to discuss product decisions, since the team can always have an open-door policy.

## 1. Teaching by default

As a new hire myself, this was easily the biggest benefit. Working / interning at other companies, I always felt intimidated to questions when I hit a roadblock (which was *way* more often than I care to admit!). I devoted myself to 30-60 minutes of head-banging before asking for help, hoping that enough code-crawling would bring me to the right approach.

This is definitely a good practice when you're already familiar with the codebase. But for incoming junior SWEs, it gets difficult to even find the right questions to ask in order to understand the bigger picture. What's more, I might find a totally valid approach to a problem after some intense Googling, only to be shot down in a PR review since I "wasn't consistent" with the existing code 😞

Mob programming completely eliminates this friction. Since new hires are constantly working alongside seasoned developers, they can naturally pick up existing patterns and recurring approaches they wouldn't have picked up on otherwise. I've definitely spun my wheels for days on a PR without asking questions, only to be told "oh, that code was already written in `@ecomm/packageYouOverlooked/abstractionYouWanted.ts`! Just import that instead" 😕

What's more, junior developers can be encouraged to "drive" more often than they "navigate." Since the driver is meant to type for the rest of the team and nothing more, juniors can pause and wait for feedback without fear that they'll "look stupid" in front of their peers. This was especially helpful for myself, since I needed to learn a new programming language entirely to start contributing to backend tasks. Working with others, I immediately discovered patterns and external resources to get me up-to-speed in a couple weeks, instead of a couple *months.*

Working with developers across the stack is also a great way to pick up new patterns and even collaborate on new patterns worth implementing. This follows [Swyx's popular "Learn in Public" mantra](https://www.swyx.io/writing/learn-in-public/): 

Those who actively teach tend to **learn more themselves and even question long-held practices.** 

This leads to my next point...

## 2. Real-time code reviews

I've already alluded to the troubles of making PRs without solid communication. However, this issue isn't unique to new hires! Since best practices at Peloton are constantly evolving, developers will always have opinions over code that's about to be merged. There's also the age-old question of [DRY vs. WET programming](https://kentcdodds.com/blog/aha-programming): should I use this random module to solve my problem, or should we rethink this entirely and break it up into smaller chunks?

By forcing everyone into the same room (or the same video call in our case), we can resolve these disputes without all the GitHub back-and-forth. 

An example popped up just recently, as our team was working to add an email interest form to an area of the site. Soon enough, we discovered another component that was pretty similar to the form we were trying to create. However, looking through the Git log, the component hadn't been touched for over a year and used some *very* old practices 😬 Refactoring this piece could cause a wave of issues throughout the site depending on how we approached it. So, we decided to **mob** through the entire task.

In the end, this was the perfect way to get that feature to the finish line. I noticed that each person in the call had something unique to contribute to the refactor. For instance, Kavita (senior SWE) suggested an approach to pull our actual input box from our logic for analytics, which decoupled our stylish email form for future use on the site. Duncan (senior SWE) suggested a pattern for passing React props called a [render function](https://frontarm.com/james-k-nelson/passing-data-props-children/) that we were completely new to, which *dramatically* simplified code we'd been massaging for hours. 

We probably wouldn't have made these impactful decisions by working individually. Instead, a single developer may have grinded through the task, submitted a PR, and gotten a nice "LGTM!" from busy teammates. "Good enough" code is tiring to correct when a developer *already* went down the wrong path to get it done. **So why not communicate and get it right the first time?**

In the end, we've found fewer blockers, less time hastily reviewing code, and, crucially, much easier division of labor. Instead of dividng tickets into the *perfect* subtasks so devs can work independently, we can instead work on single features as a single unit. Now, subtasks that are dependant on each other don't seem so scary anymore!  Without the blockers of meetings and code reviews, we can seemlessly move from one step to the next 👍

## 3. From meetings to around-the-clock conversation

This is the most interesting aspect of mob programming that our team is  pushing to achieve. As of now, we've only tried to mob with fellow developers, without bringing product managers and designers into the fold. However, as we've started sharing our learnings with other departments, PMs have gotten interested in our process.

To start, our team's PM has started joining the call to ask about our [content management system](https://www.optimizely.com/optimization-glossary/content-management-system/), just to make sure the models he set up would show up correctly on our end. This led to...

- Newer developers (like myself) learning about how content is transferred from product + design to our backend
- The PM discovering a few toggles he forgot to set, as our team's senior developers walked him through the process
- A quick 15 minute turnaround on our PM's issue, instead of scheduling and holding a meeting for the same issue

Since our team keeps a video call running throughout the day with the same link to join, quick resolutions like this are super easy to pull off. In fact, this always-on policy brings us a little closer to our old open-concept office before the "new norm" hit us.

Now, our PM is interested in getting involved in more cross-department CMS integration in the future; he's even open to being a "driver" to contribute small changes to the codebase. Since all questions are valid, and the team is focused on a common goal, such a setup doesn't seem so strange anymore.

## Yes, there are some growing pains

As nice as this system has been, we've noticed some cracks in its foundation over the past month. Notably, we've found mobbing to be a little more difficult for smaller tasks like one-off bug fixes. In these situations, a lot of opinions get muddled together for what *could* be a quick 30 minute turnaround from a single dev. We've also found it hard to coordinate large "mobbing" sessions with more than 2 devs at a time given everyone's unique schedules. 

After discussing at our last [sprint retro](https://www.scrum.org/resources/what-is-a-sprint-retrospective), we decided to identify which tasks for a given week are best for mobbing upfront so we know what to focus on as a team. Then, for times where we can't all join forces, we'll work on smaller bug fixes that don't need quite as much collaboration.

In any case, Peloton is still new to the concept of mobbing, and we're trying to find the best balance that works for us. In the words of Woody Zuill: mobbing probably isn't the right answer for everyone, but it's worth trying no matter your team's structure or misconceptions 😁

