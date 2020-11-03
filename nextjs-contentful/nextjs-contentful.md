# NextJS, Contentful, GraphQL, oh my!

We built the new [Hack4Impact.org](https://hack4impact.org) in a month-long sprint once we had designs in hand. To move this fast, we needed to make sure that we used tools that played to our strengths, while setting us up for success when designers and product managers want to update our copy. As the title _excitedly_ alludes to, NextJS + Contentful + GraphQL was the match for us!

No, this post won't help you answer _what tools should I use to build our site's landing page?_ But it should get your gears turning on:

- How to access Contentful's GraphQL endpoints (yes, they're free-to-use now!) 📝
- How to talk to GraphQL server + debug with GraphiQL 📶
- How we can roll query results into a static NextJS site with `getStaticProps` 🗞
- Going further with TypeScript 🚀

Onwards!

## Wait, why use these tools?

Some readers might be scoping whether to adopt these tools at all. Our walkthrough should be helpful, but for a TLDR:

1. [**NextJS**](https://nextjs.org) was a perfect match for our team, since we were already comfortable with a React-based workflow and wanted to play to our strengths. What's more, NextJS is flexible enough to build some parts of your website *statically*, and other parts *dynamically* (i.e. with [serverside rendering](https://medium.com/walmartglobaltech/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8)). This is pretty promising as [our landing site](https://hack4impact.org) expands, where we might add experiences that vary by user going forward (admin portals, nonprofit dashboards, etc).
2. [**Contentful**](https://www.contentful.com) is one of the more popular "headless CMSs" right now, and it's easy to see why. Content types are more than flexible enough for our use cases, and the UI is friendly enough for designers and product managers to navigate confidently. It thrives with "structured content" in particular which is great for static sites like ours! Still, if you're looking for a simplified, key-value store for your copy, there are some [shiny alternatives](https://www.sanity.io) to consider.
3. [**GraphQL**](https://graphql.org) is the _perfect_ pairing for a CMS in our opinion. You simply define the "shape" of the content you want (with necessary filtering and sorting), and the CMS responds with the associated values. We'll dive into some code samples soon, but it's *much* simpler than a traditional REST endpoint.

There's roughly 10 billion ways to build a static site these days (citation needed), with another [10 billion blog posts](https://www.netlify.com/blog/) on how to tackle the problem. So don't take these reasons as perscriptive! 

## Setting up our Contentful environment

Let's open up Contentful first. We'll be diving straight into our API queries for the sake of this tutorial. Still, if you're 100% new to the platform, Contentful documents a lot of [core concepts over here](https://www.contentful.com/developers/docs/concepts/) to get up to speed!

When you're feeling comfortable, whip up a new workspace and create a new Content Model of your choosing. We'll use our "Executive Board Member" model as an example here.

![Contentful model example](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/contentful-model.jpg)

Once you've saved this model, go and make some content entries in the "Content" panel. We'll be pulling these down with GraphQL later, so I recommend making more than 1 entry to demo sorting and filtering! You can filter by your content type for a sanity check:

![Contentful entry example, filtered by "Executive Board Member"](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/contentful-entries.jpg)

Before moving on, let's get some API keys for our website to use. Just head to "Settings > API keys" and choose "Add API key" in the top right. This should allow you to find two important variables: a **Space ID** and a **Content Delivery API access token.** You'll need these for some important environment variables in your local repo!

## Whipping up a basic NextJS site

If you already have a Next project to work off of, great! Go `cd` into that thing now. Otherwise, it's super easy to make a NextJS project from scratch using their `npx` command:

```bash
npx create-next-app dope-contentful-example
```

💡 _**Note:** You can optionally include the `--use-npm` flag if you want to ditch Yarn. By default, Next will set up your project with [Yarn](https://classic.yarnpkg.com/en/) if you have it installed globally. [It's your prerogative](https://www.youtube.com/watch?v=5cDLZqe735k) though!_

You may have found a "NextJS + Contentful" example in their docs as well. **Don't install that one!** We'll be using GraphQL for this demo, which has a slightly different setup.

Now, just `cd` into your new NextJS project and create a `.env` file with the following info:

```bash
NEXT_PUBLIC_CONTENTFUL_SPACE_ID=[Your Space ID from Contentful]
NEXT_PUBLIC_CONTENTFUL_ACCESS_TOKEN=[Your Content Delivery API access token from Contentful]
```

Just fill these in with your API keys and you're good to go! Yes, **the `NEXT_PUBLIC` prefix is necessary for these to work.** It's a little verbose, but it allows Next to pick up your keys without the hassle of setting up, say, [dotenv](https://github.com/motdotla/dotenv).

## Fetching some GraphQL data

Alright, so we've set the stage. _Now let's fetch our data!_

We'll be using GraphiQL to so we can view our "schemas" with a nice GUI. [**You can install this tool here**](https://www.electronjs.org/apps/graphiql), using either homebrew on MacOS or the [Linux Subsystem](https://docs.microsoft.com/en-us/windows/wsl/install-win10) on Windows. Otherwise, if you want to follow along as a `curl` or Postman warrior, be my guest!

Opening the app for the first time, you should see a screen like this:

![GraphiQL welcome screen](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/graphiql-welcome.jpg)

Let's point GraphiQL to our Contentful server. You can start by entering the following URL, filling in **[Space ID]** with your API key from the previous section:

```bash
https://graphql.contentful.com/content/v1/spaces/[Space ID]
```

If you try to hit the play button ▶️ after this step, you should get an authorizaton error. That's because we haven't passed an access token with our query!

To fix this, click **Edit HTTP Headers** and create a new header entry like so, filling in **[Contentful access token]** with the value from your API keys:

![Edit HTTP headers screen. Make sure Header name = "authorizaton" and Header value = "Bearer [Contentful access token]"](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/graphiql-headers.jpg)

After saving, you should see some info appear in your "Documentation Explorer." If you click on the **query: Query** link, you'll see an overview of all your Content models from Contentful.

![Schema explorer overview](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/graphiql-docs.jpg)

Neat! From here, you should see all of the content models you created in your Contentful space. You'll notice there's a distinction between individual entries and a "collection" (i.e. `executiveBoardMember` vs. `executiveBoardMemberCollection`). This is because each represents a different **query** you can perform in your API call. If this terminology confuses you, here's a quick breakdown:

- items highlighted in **blue** represent **queries** you can perform. These are similar to REST endpoints, as they accept parameters and return a structured response. The main difference is being able to _nest queries within other queries_ to retrieve nested content. We'll explore this concept through example!
- items highlighted in **purple** represent **parameters** you can pass for a given query. For example, I can query for an individual `ExecutiveBoardMember` based on `id` or `locale` (we'll ignore the `preview` param for this tutorial), or query for a collection / list of members (`ExecutiveBoardMemberCollection`) filtering by `locale`, amount of entries (`limit`), sort `order`, and a number of other properties.
- items highlighted in **yellow** represent **the shape of the response** you receive from a given query. This allows you to pull out the _exact_ keys of a given content entry that you want, with type checking built-in. Each of these are hyperlinks, so click on them to inspect the nested queries and response types!

### Getting our hands dirty

Let's jump into an example. First, let's just get the list of names and emails for all "Executive Board Member" entries. You can follow along Since we're looking for multiple entries, we'll use the `executiveBoardMemberCollection` query for this.

Clicking into the yellow `ExecutiveBoardMemberCollection` link (following the colon : at the end of the query), we should see a few options we're free to retrieve: **total, skip, limit, and items.** You'll see these 4 queries on every collection you create, where **items** represents the actual list of items you're hoping to retrieve. Let's click into the response type for **items** to see the shape of our content:

![Schema for our Executive Board Member content model](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/graphiql-exec-member-entry.jpg)

This should look really similar to the content model you wrote in Contentful! As you can see, we can query for any of these fields to retrieve a response (most of them being strings in this example).

###Writing your first query

Alright, we've walked through the docs and found the queries we want... so how do we get that data?

Well, the recap, here's the basic skeleton of info we need to retrieve:

```
executiveBoardMemberCollection -> query for a collection of entries
  items -> retrieve the list items
    name -> retrieve the name for each list item
    email -> and the email
```

We can simply convert this skeleton to the JSON-y syntax GraphQL expects:

```js
{
  executiveBoardMemberCollection {
    items {
      name
      email
    }
  }
}
```

Just enter this into GraphiQL's textbook and hit play ▶️

![Entering our query into GraphiQL, with response data appearing on the right](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/graphiql-response.jpg)

Boom! There's all the data we entered into Contentful, formatted as a nice JSON response. If you typed your query into GraphiQL by hand, you may have noticed some nifty autocomplete as you went. This is the beauty of GraphQL: since we know the shape of any possible response, it can autofill your query as you go! 🚀

### Applying filters

You may have noticed some purple items in parenthesis while exploring the docs. These are parameters we can pass to Contentful to further refine our results.

Let's use some of the `collection` filters as an example; say we only want to retrieve board members that have a LinkedIn profile. We can apply this filter using the **where** parameter:

![Applying a filter using "where." Shows how GraphiQL autocomplete will reveal potential parameter values.](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/graphiql-filter.jpg)

As you can see, `where` accepts an object as a value, where we can apply a set of pre-determined filters from Contentful. As we type, we're greated with a number of comparison options that might remind you of SQL, including the **exists** operator for nullable values. You can find the complete list of supported filters in the docs off to the right, but autocomplete will usually take you to the filter you want 💪

In our case, our query should look something like this: 

```
executiveBoardMemberCollection(where: {linkedIn_exists: true}) { ... }
```

...and our result should filter out members without a LinkedIn entry!

## Pulling our data into NextJS

Alright, we figured out how to retrieve our data. All we need is an API call on our NextJS site and we're off to the races 🏎

Let's pop open a random page component in our `/pages` directory and add a call to `getStaticProps()`:

```js
// pages/about
export async function getStaticProps() {
  return {
    props: {
      // our beautiful Contentful content
    }
  }
}
```

If you're unfamiliar with Next, this function allows you to pull in data _while your app is getting built,_ so you access that data in your component's `props` at runtime. This means you _don't have to call Contentful_ when your component mounts! The data is just... there, ready for you to use 👍

💡 _**Note:** There's a distinction between getting these props "on every page request" versus retrieval "at build time." For a full rundown of the difference, [check out the NextJS docs](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering)._

Inside this function, we'll make a simple call to Contentful using [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) (but feel free to use axios if that's more your speed):

```js
export async function getStaticProps() {
  // first, grab our Contentful keys from the .env file
  const space = process.env.NEXT_PUBLIC_CONTENTFUL_SPACE_ID;
	const accessToken = process.env.NEXT_PUBLIC_CONTENTFUL_ACCESS_TOKEN;
  
  // then, send a request to Contentful (using the same URL from GraphiQL)
  const res = await fetch(
      `https://graphql.contentful.com/content/v1/spaces/${space}`,
      {
        method: 'POST', // GraphQL *always* uses POST requests!
        headers: {
          'content-type': 'application/json',
          authorization: `Bearer ${accessToken}`, // add our access token header
        },
        // send the query we wrote in GraphiQL as a string
        body: JSON.stringify({
          // all requests start with "query: ", so we'll stringify that for convenience
          query: `
					{
            executiveBoardMemberCollection {
              items {
                name
                email
              }
            }
          }
				`,
        },
      },
    );
	// grab the data from our response
	const { data } = await res.json()
  ...
}
```

Woah, that's a lot going on! In the end, we're just re-writing the logic that GraphiQL does under the hood. Some key takeaways:

1. **We need to grab our API keys for the URL and authorization header.** This should look super familiar after our GraphiQL setup!
2. **Every GraphQL query should be a POST request.** This is because we're sending a `body` field to Contentful, containing the "shape" of the content we want to receive. 
3. **Our query should start with the JSON key `{ "query": "string" }`.** To make this easier to type, we create a JavaScript object starting with "query" and convert this to a string.

🏁 As a checkpoint, add a `console.log` statement to see what our `data` object looks like. If all goes well, you should get a collection of contentful entries!

Now, we just need to return the data we want as props (in this case, the items in our `executiveBoardMemberCollection`):

```js
export async function getStaticProps() {
	...
  return {
    props: {
    	execBoardMembers: data.executiveBoardMemberCollection.items,
    },
  }
}
```

...and do something with those props in our page component:

```jsx
function AboutPage({ execBoardMembers }) {
  return (
    <div>
    	{execBoardMembers.map(execBoardMember => (
      	<div className="exec-member-profile">
        	<h2>{execBoardMember.name}</h2>
          <p>{execBoardMember.email}</p>
        </div>
      ))}
    </div>
  )
}

export default AboutPage;
```

Hopefully, you'll see your own Contentful entries pop onto the page 🎉

### Writing a reusable helper functions

This all works great, but it gets pretty repetitive generating this API call on every page. That's why we wrote a little helper function on our project to streamline the process!

In short, we're gonna move all our API call logic into a utility function, accepting the actual "body" of our query as a parameter. Here's how that could look:

```js
// utils/contentful
const space = process.env.NEXT_PUBLIC_CONTENTFUL_SPACE_ID;
const accessToken = process.env.NEXT_PUBLIC_CONTENTFUL_ACCESS_TOKEN;

export async function fetchContent(query) {
  // add a try / catch loop for nicer error handling
  try {
    const res = await fetch(
      `https://graphql.contentful.com/content/v1/spaces/${space}/environments/master`,
      {
        method: 'POST',
        headers: {
          'content-type': 'application/json',
          authorization: `Bearer ${accessToken}`,
        },
        // throw our query (a string) into the body directly
        body: JSON.stringify({ query }),
      },
    );
    const { data } = await res.json();
    return data;
  } catch (error) {
    // add a descriptive error message first,
    // so we know which GraphQL query caused the issue
    console.error(`There was a problem retrieving entries with the query ${query}`);
    console.error(error);
  }
}

export default fetchContent;

```

Aside from our little catch statement for errors, this should look super familiar. Now, we can refactor our `getStaticProps` function to something like this:

```js
import { fetchContent } from '@utils/contentful'

export async function getStaticProps() {
  const response = await fetchContent(`
		{
			executiveBoardMemberCollection {
				items {
    			name
    			email
    		}
    	}
    }
	`);
  return {
    props: {
      execBoardMembers: response.executiveBoardMemberCollection.items,
    }
  }
}
```

Now, you're ready to make contentful queries all across your site ✨

### Aside: use the "@" as a shortcut to directories

You may have noticed that `import` statement in the above example, accessing `fetchContent` from `@utils/contentful`. This is using a slick webpack shortcut under the hood, which you can set up too! Just create a `next.config.json` that looks like this:

```json
{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@utils/*": ["utils/*"],
      "@components/*": ["components/*"]
    },
  }
}
```

Now, you can reference anything inside `/utils` using this decorator! For convenience, we added an entry for `@components` as well, since NextJS projects tend to pull from that directory a lot 👍

### Using a special Contentful package to format rich text

Chances are, you'll probably set up some rich text fields in Contentful to handle hyperlinks, headers, and the like. By default, Contentful will return a big JSON blob representing your formatted content:

![GraphiQL response when querying for rich text](https://raw.githubusercontent.com/Holben888/personal-blog/main/nextjs-contentful/graphiql-rich-text.jpg)

...which isn't super useful on its own. To convert this into some nice HTML, you'll need a [special package from Contentful](https://www.npmjs.com/package/@contentful/rich-text-html-renderer):

```bash
npm i --save-dev @contentful/rich-text-html-renderer
```

This will take in the JSON object and (safely) render HTML for your component:

```jsx
import { documentToHtmlString } from '@contentful/rich-text-html-renderer';

function AboutPage(execBoardMember) {
  return (
    <div
    dangerouslySetInnerHTML={{
    __html: documentToHtmlString(execBoardMember.description.json),
		}}></div>
  )
}
```

Yes, using `dangerouslySetInnerHTML` is pretty tedious. We'd suggest making a `RichText` component to render your HTML as your app expands.

## Check out our project to see how we put it together 🚀

If you're interested, we deployed our entire project to a CodeSandbox for you to explore!

**[Head over here](https://codesandbox.io/s/hack4impact-website-0kn5m?file=/pages/about/index.tsx)** to see how we retrieve our executive board members on [our about page](https://hack4impact.org/about). Also, check out the `utils/contentful` directory to see how we defined our schemas using TypeScript.

[Our repo](https://github.com/hack4impact/hack4impact-website) is 100% open as well, so give it a ⭐️ if this article helped you!

## Thanks for reading! If this article was helpful...

I love writing about this sort of stuff 👨‍💻

❤️ [**First, please check out the incredible work Hack4Impact is cooking up!**](https://hack4impact.org/)

🐦 [**Follow my Twitter for random web dev tips and articles I find cool**](https://twitter.com/bholmesdev)

📗 [**Follow my personal blog for new posts every 2-3 weeks**](https://dev.to/bholmesdev)

