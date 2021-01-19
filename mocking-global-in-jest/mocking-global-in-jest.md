# Mocking browser APIs (fetch, localStorage, Dates...) the easy way with Jest

I hit a snag recently trying to test a `localStorage` helper written in React. Figuring out how to test all my state and render changes was certainly the easy part (thanks as always [React Testing Library](https://github.com/testing-library/react-testing-library) 🐐).

But soon, I found myself wondering... is there an easy way to "mock" a browser API like storage? Or better yet, how should I test **any function using X API?** 

Well, hope you're hungry! We're gonna explore

- 🚅 Why dependency injection doesn't feel like a silver bullet
- 📦 How we can mock `localStorage` using the `global` object
- 📶 Ways to go further mocking the `fetch` API
- 🔎 An alternative approach using `jest.spyOn`

_Onwards!_

## Let's grab something to eat first

Here's a simple (and tasty) example of a function worth testing:

```js
function saveForLater(leftoverChili) {
  try {
		const whatsInTheFridge = localStorage.getItem('mealPrepOfTheWeek')
    if (whatsInTheFridge === undefined) {
      // if our fridge is empty, chili's on the menu 🌶
     	localStorage.setItem('mealPrepOfTheWeek', leftoverChili) 
    } else {
      // otherwise, we'll just bring it to our neighbor's potluck 🍽
      goToPotluck(leftoverChili)
    }
  } catch {
    // if something went wrong, we're going to the potluck empty-handed 😬
    goToPotluck()
  }
}
```

This is pretty straightforward... but it's got some `localStorage` madness baked-in. We could probably start with the **inject all the things strategy (TM)** to address this:

```js
function saveForLater(leftoverChili, {
  // treat our storage calls as parameters to the function,
  // with the default value set to our desired behavior
  getFromStorage = localStorage.getItem('mealPrepOfTheWeek'),
  setInStorage = (food) => localStorage.setItem('mealPrepOfTheWeek', food) 
}) => {
  try {
    // then, sub these values into our function
    const whatsInTheFridge = getFromStorage()
    ...
    setInStorage(leftoverChili)
		...
}
```

Then, our test file can pass some tasty mock functions we can work with:

```js
it('puts the chili in the fridge when the fridge is empty', () => {
  // just make some dummy functions, where the getter returns undefined
  const getFromStorage = jest.fn().mockReturnValueOnce(undefined)
  // then, make a mock storage object to check
  // whether the chili was put in the fridge
  let mockStorage
  const setInStorage = jest.fn((value) => { mockStorage = value })
  
	saveForLater('chili', { getFromStorage, setInStorage })
  expect(setInStorage).toHaveBeenCalledOnce()
  expect(mockFridge).toEqual('chili')
})
```

This isn't _too_ bad. Now we can check whether our `localStorage` functions get called, and verify that we're sending the right values.

Still, there's something a bit ugly here: **we just restructured our code for the sake of cleaner tests!** I don't know about you, but I feel a little uneasy moving the internals of my function to a set of _parameters_. And what if the unit tests move away or get rewritten years down the line? That leaves us with another odd design choice to pass onto the next developer 😕

## 📦 What if we could mock browser storage directly? 

Sure, [mocking module functions _we wrote ourselves_ is pretty tough.](https://stackoverflow.com/questions/45111198/how-to-mock-functions-in-the-same-module-using-jest) But mocking native APIs is surprisingly straightforward! Let me stir the pot a little 🥘

```js
// let's make a mock fridge (storage) for all our tests to use
let mockFridge = {}

beforeAll(() => {
  global.Storage.prototype.setItem = jest.fn((key, value) => {
    mockFridge[key] = value
  })
  global.Storage.prototype.getItem = jest.fn((key) => mockFridge[key])
})

beforeEach(() => {
  // make sure the fridge starts out empty for each test
  mockFridge = {}
})

afterAll(() => {
  // return our mocks to their original values
  // 🚨 THIS IS VERY IMPORTANT to avoid polluting future tests!
	global.Storage.prototype.setItem.mockReset()
  global.Storage.prototype.getItem.mockReset()
})
```

Oh, take a look at that meaty piece in the middle! There's some big takeaways from this:

1. **Node gives you an explicit `global` object to work with.** This offers a treasure trove of APIs for you to mock. And, as we've discovered, it includes our favorite browser APIs as well!
2. **We can use the `prototype` to mock functions inside a JS class.** You're right to wonder _why_ we need to mock `Storage.prototype`, rather than mocking `localStorage` directly. In short: `localStorage` is actually an *instance* of a Storage _class._ Sadly, mocking methods on a class instance (i.e. `localStorage.getItem`) doesn't work with our `jest.fn` approach. But don't fret! You can [mock the entire `localStorage` class](https://stackoverflow.com/a/41434763) as well if this `prototype` madness makes you feel uneasy 😁 Fair warning though: it's a bit harder to test whether class methods were called with `toHaveBeenCalled` compared to a plan ole' `jest.fn`.

💡 _**Note:** This strategy will mock both `localStorage` and `sessionStorage` with the same set of functions. If you need to mock these independently, you might need to split up your test suites or [mock the storage class](https://stackoverflow.com/a/41434763) as previously suggested._

Now, we're good to test our original function injection-free!

```js
it('puts the chili in the fridge when the fridge is empty', () => {
	saveForLater('chili')
  expect(global.Storage.prototoype.setItem).toHaveBeenCalledOnce()
  expect(mockStorage['mealPrepOfTheWeek']).toEqual('chili')
})
```

There's almost no setup to speak of now that we're mocking `global` values. **Just remember to clean the kitchen in that `afterAll` block,** and we're good to go 👍

## 📶 So what else could we mock?

Now that we're cooking with crisco, let's try our hand at some more `global` functions. [**The `fetch` API**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) is a great candidate for this:

```js
// let's fetch some ingredients from the store
async function grabSomeIngredients() {
  try {
  	const res = await fetch('https://wholefoods.com/overpriced-organic-spices')
  	const { cumin, paprika, chiliPowder } = await res.json()
		return [cumin, paprika, chiliPowder] 
  } catch {
    return []
  }
}
```

Seems simple enough! We're must making sure that the Cumin, Paprika, and Chili Powder get fetched and returned in an array of chili spices 🌶

As you might expect, we're using the same `global` strategy as before:

```js
it('fetches the right ingredients', async () => {
  const cumin = 'McCormick ground cumin'
  const paprika = 'Smoked paprika'
  const chiliPowder = 'Spice Islands Chili Powder'
  let spices = { cumin, paprika, chiliPowder, garlicSalt: 'Yuck. Fresh garlic only!' }
  
  global.fetch = jest.fn().mockImplementationOnce(
    () => new Promise((resolve) => {
      resolve({
        // first, mock the "json" function containing our result
        json: () => new Promise((resolve) => {
          // then, resolve this second promise with our spices
          resolve(spices)
        }),
      })
    })
  )
  const res = await grabSomeIngredients()
  expect(res).toEqual([cumin, paprika, chiliPowder])
})
```

Not too bad! You'll probably need a second to process that doubly-nested `Promise` we're mocking (remember, `fetch` returns _another_ promise for the `json` result!). Still, our test stayed pretty lean while thoroughly testing our function.

You'll also notice that we used **`mockImplementationOnce`** here. Sure, we could have used the same `beforeAll` technique as before, but we probably want to mock different implementations for `fetch` once we get into the error scenarios. Here's how that might look:

```js
it('returns an empty array on bad fetch', async () => {
    global.fetch = jest.fn().mockImplementationOnce(
      () => new Promise((_, reject) => {
        reject(404)
      })
    )
    const res = await fetchSomething()
    // if our fetch fails, we don't get any spices!
    expect(res).toEqual([])
  })
  it('returns an empty array on bad json format', async () => {
    global.fetch = jest.fn().mockImplementationOnce(
      () => new Promise((resolve) => {
        resolve({
          json: () => new Promise((_, reject) => reject(error)),
        })
      })
    )
    const res = await fetchSomething()
    expect(res).toEqual([])
  })
```

And since we're mocking implementation *once,* there's no `afterAll` cleanup to worry about! Pays to clean your dishes as soon as you're done with them 🧽

##🔎 Addendum: using "spies"

Before wrapping up, I want to point out an alternative approach: mocking `global` using [Jest spies](https://jestjs.io/docs/en/jest-object#jestspyonobject-methodname).

Let's refactor our `localStorage` example from before:

```js
...
// first, we'll need to make some variables to hold onto our spies
// we'll use these for clean-up later
let setItemSpy, getItemSpy

beforeAll(() => {
  // previously: global.Storage.prototype.setItem = jest.fn(...)
	setItemSpy = jest
    .spyOn(global.Storage.prototype, 'setItem')
    .mockImplementation((key, value) => {
      mockStorage[key] = value
    })
  // previously: global.Storage.prototype.getItem = jest.fn(...)
  getItemSpy = jest
    .spyOn(global.Storage.prototype, 'getItem')
    .mockImplementation((key) => mockStorage[key])
})

afterAll(() => {
  // then, detach our spies to avoid breaking other test suites
  getItemSpy.mockRestore()
  setItemSpy.mockRestore()
})
```

Overall, this is pretty much identical to our original approach. The only difference is in the semantics; instead of **assigning new behavior** to these global functions (i.e. `= jest.fn()`), we're **intercepting requests to these functions** and using our own implementation.

This might feel a little "safer" to some people, since we're not explicitly overwriting the behavior of these functions anymore. But as long as you pay attention to your cleanup in the `afterAll` block, either approach is valid 😁

## Thanks for reading! If this article was helpful...

I love writing about this sort of stuff. With this, I'm officially 3 posts into my goal to post once a week in 2021! So, go ahead and [**drop a follow**](https://dev.to/bholmesdev) to hold me accountable 😁