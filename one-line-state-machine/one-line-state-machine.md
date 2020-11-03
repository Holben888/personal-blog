# Writing a state machine in one line with TypeScript

Ah yes, state machines. That thing [David K Piano](https://twitter.com/DavidKPiano) keeps tweeting about, or that CS concept that pops up in college once a semester (and seemingly never returns...). As the frontend world gets more and more disatisfied with Redux, state machines are [one](https://mobx.js.org/README.html) [of](https://rxjs-dev.firebaseapp.com) [*many*](https://github.com/facebookexperimental/Recoil) [concepts](https://www.apollographql.com/docs/react/) devs are talking about these days.

But unlike Redux et al., **state machines don't have to be a library you install into your project!** Once you understand them conceptually, they become their own **way of thinking** about problems. 

**In short, this article should help you...**

1. Identify when boolean flags and state variables are getting too complex
2. Write your own scrappy state machine with no libraries
3. Learn a bit more about state machines as a concept, and when XState might be a good idea

Onwards!

*⚠️ **Note: ** we'll be using React for the following examples. Still, the core learning concepts transfer to any frontend framework*

## First, a scenario

If we're talking UI complexity, form management is the easiest place to look. Let's say we have a simple sign-up screen we need to implement with a username and password. To make things a little interesting, let's say we're reviving the incredible childhood memory that is [Club Penguin](https://cprewritten.net)!

![image-20200827100332385](/Users/benholmes/Creative Cloud Files/Documents/Blog/one-line-state-machine/club-penguin-ui.png)

*Try not the cringe. At least it's not built on Flash* 😬

We also want to consider some scenarios as the user fills out the form. Namely, we should support **a) password validation before you submit** and **b) disabling the submit button while we're sending to the API.** Here's what that flow might look like:

 ![button-states-vid](/Users/benholmes/Creative Cloud Files/Documents/Blog/one-line-state-machine/button-states-vid.gif)

## A common approach: brute-force booleans

First, let's cover the approach a lot of devs might take [(especially coming from a Redux background)](https://hackernoon.com/handling-loading-actions-the-proper-way-in-redux-t3k36e8). Based on the interactions we want, we should probably have some flags for

1. When the password is invalid
2. When we're submitting to the API
3. Whether we've submitted successfully (maybe to move to the next screen)

I won't bore you with the HTML + _colorful_ CSS we need (check this [CodeSandbox](https://codesandbox.io/s/forms-the-bad-way-mvm2w) for such goodies!), so let's just look at the pieces we care about:

```tsx
const ClubPenguinSignup = () => {
  const [invalid, setInvalid] = React.useState(false);
  const [submitting, setSubmitting] = React.useState(false);
  const [submitted, setSubmitted] = React.useState(false);
  ...
  // state vars for username and password, markup, etc.
}
```

For the submitting / submitted flags, we can use a nice callback function for whenever our form submits:

```tsx
const onSubmit = async (event: React.FormEvent) => {
  event.preventDefault();
  setSubmitting(true); // we're now submitting
  const addedUser = await arcticAuthService({ username, password });
  if (addedUser?.success) { // if signup call worked...
    setSubmitting(false); // we're no longer submitting
  	setSubmitted(true); // we've submitted
	}
};
```

Finally, we can make a super basic callback to validate our password as the user types it in. In this case, we'll listen for whenever the input's value changes (i.e. using a [controlled input](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)), and run the value through an insecure phrase checker:

```tsx
const onChangePassword = (event: React.FormEvent<HTMLInputElement>) => {
  setPassword(event.currentTarget.value);
  checkPasswordSecurity(event.currentTarget.value);
};

const checkPasswordSecurity = (changedPassword: string) => {
  let insecure = false; // we first assume the value is secure (excuse the double negative)
  ["club", "penguin", "puffle"].forEach((badWord) => {
    if (changedPassword.includes(badWord)) {
      insecure = true;
    }
  });
  setInvalid(insecure);
};
```

### Where it starts to get hairy

Great! This doesn't seem too bad... but we're not done yet. If you check that mockup again, you'll notice that our button has 3 different indicators to display (normal, loading, and finished). Since we're using separate boolean flags for each of these, we'll need some mappers to set the button backgrounds + flavor text:

```tsx
const getButtonLabel = (): string => {
  if (submitting) {
    return "•••";
  } else if (submitted) {
    return "Time to play!";
  } else {
    return "Let's get sliding!";
  }
};

const getButtonClass = (): string => {
  if (submitting) {
    return "submitting";
  } else if (submitted) {
    return "submitted";
  } else if (invalid) {
    return "invalid";
  } else {
    return "";
  }
};

return (
	...
  <button type="submit" className={getButtonClass()}>
    {getButtonLabel()}
  </button>
)
```

Since we only need mappers for a single element, this doesn't seem *that* terrible. Still, this could easily start ballooning out of control as we add more UI and more state variables...

```tsx
const [usenameTaken, setUsernameTaken] = React.useState(false);
const [apiError, setApiError] = React.useState(false);
const [serverAtMaxCapacity, setServerAtMaxCapacity] = React.useState(false);
const [invalid, setInvalid] = React.useState(false);
const [submitting, setSubmitting] = React.useState(false);
const [submitted, setSubmitted] = React.useState(false);

const getButtonClass = (): string => {
  // 100 lines of ifs
};
```

We're also allowing for a lot of states that shouldn't be possible. For example, we should never be "submitting" and "submitted" at the same time, and neither of these should be `true` when the password is invalid. Considering the crazy state explosion above, we'll end up restling all these variables to prevent such invalid states.

```ts
// api responds that servers are at max capacity, so no sign ups allowed
setServerAtMaxCapacity(true)
setSubmitting(false)
setSubmitted(false)
setApiError(true)
...
```

If anything, we just want to have a boolean with more than 2 values so we're not switching flags all over the place. Luckily, *TypeScript gives us such superpowers* 💪

## Our new approach: the poor man's state machine

As you may have guessed, we can solve this boolean bonanza with a simple state machine. I've heard this approach called the "[poor man's state machine](https://twitter.com/housecor/status/1292111064314851329)" which is a super apt title as well!

All we need is ~~the XState library~~ a one-liner to model our state variables as a single type:

```ts
type FormState = 'idle' | 'invalid' | 'submitting' | 'submitted'
```

You could certainly use an enum for this as well. I just prefer string literals since they're a bit shorter + more readable (I also wrote a [short article on the subject](https://dev.to/bholmesdev/using-enums-with-string-values-in-typescript-consider-string-literals-instead-486e) if you're still an enum stan).

With our type defined, we can condense all our state variables into one:

```tsx
const [formState, setFormState] = React.useState<FormState>("idle");
```

Refactoring our password and submit callback is pretty easy from here.

```tsx
const checkIfPasswordIsSecure = (changedPassword: string) => {
    setFormState("idle"); // not invalid yet
    ["club", "penguin", "puffle"].forEach((badWord) => {
      if (changedPassword.includes(badWord)) {
        setFormState("invalid"); // oops! Looks like it's invalid after all
      }
    });
  };

const onSubmit = async (event: React.FormEvent) => {
  event.preventDefault();
  if (formState === "invalid") return; // don't submit if our password is invalid
  setFormState("submitting");
  const addedUser = await arcticAuthService({ username, password });
  if (addedUser?.id) {
  	setFormState("submitted"); // no need to set submitting to false, since we don't have 2 states to consider anymore!
	}
};
```

And remember those button `className`s we needed to map? Well, since our state is represented as a string, we can just pass these directly to our CSS ✨

```tsx
return (
	<button type="submit" className={formState /* our state is our CSS */}>
		...
  </button>
)
```

This approach is super handy for keeping our CSS in check; instead of constantly adding and removing classes, we can just switch out which class gets applied.

**[Here's a working CodeSandbox](https://codesandbox.io/s/club-penguin-with-state-machines-1klrq?file=/src/styles.css) using our new approach ✨**

## Going further 🚀

Of course, this is a pretty simple example that may not *quite* fit your use case. For example, you may want to be in multiple states at a given time, or guard against "invalid transitions" (for example, it shouldn't be possible to go from `idle` to `submitted` without passing through `submitting` first).

The former could just require multiple state variables, so consider creating multiple `FormState` types to see how it feels. Still, you may have enough complexity that a state management library makes a lot of sense. Check out [XState](https://xstate.js.org/docs/) if this sounds like you!

To get your feet wet, I found a couple high quality demos around the Internet worth checking out:

- **[This one](https://www.youtube.com/watch?v=czi24DqUfSA) on building a more complex ReactJS form.** It's long, but worth your time!
- **[This one](https://www.youtube.com/watch?v=osaszd2aU9U) on creating a Vanilla JS drag-and-drop interaction.** This is more CSS-intensive and speaks to the `className` trick I showed above.
- **[This one](https://www.youtube.com/watch?v=hiT4Q1ntvzg) on modeling UI with state machines across frameworks.** Best conference talk on the subject hands down.

## Thanks for reading! If this article was helpful...

I love writing about this sort of stuff 👨‍💻
🐦 [**Follow my Twitter for random web dev tips and articles I find cool**](https://twitter.com/bholmesdev)
📗 [**Follow my blog for new posts every 2-3 weeks**](https://dev.to/bholmesdev)

