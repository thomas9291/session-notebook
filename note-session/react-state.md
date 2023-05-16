# React State

## Learning Objectives

- [ ] Knowing how to attach events ins React
- [ ] Understanding the concept of "state"
- [ ] Using `useState()` to handle state in React
- [ ] Understanding the React Lifecycle

---

## What is state?

State is data that changes over time. Think of the lamp on you desk. It can be switched on or
switched of. The lamp is in a particular state at a given time and that state can change over time.

Another example could be the amount of money in your purse. At any given time your have a certain amount
of money in your purse, but the amount of money can change. The state of your purse can change.
Going to the grocery store will decrease the amount of money, while going to the ATM will increases
it.

This concepts also applies to software. Your app can have data that changes over time.

Think of a post on a social media app. You might have liked a specific post, or not. The "liked"
state of a post can be on or off, like the lamp on your desk.

The website of your bank refers to your purse in the analog world. At any given time, the banking software
displays the current balance of your account, the current state. You can use the banking software to
change that state. For example you could transfer money to another account to decrease the number
stored in the "balance" state.

Oftentimes such stateful data changes after a user interaction, like a click on a button.

---

## State in React

In React we work with state by using the `useState` hook function.

We call the `useState` function and pass the **initial state** value as argument. This is the value
that is used in our app until something changes.

Calling the `useState` function gives us two things in return:

- a variable with the **current state** as value
- the `set` function to set a **new state**

```js
import { useState } from "react";

function SocialMediaPost() {
  const [liked, setLiked] = useState(false);

  function toggleLiked() {
    setLiked(!liked);
  }

  return (
    <article>
      <p>Liked: {liked ? "Yes" : "No"}</p>
      <button type="button" onClick={toggleLiked}>
        {liked ? "Remove like" : "Add like"}
      </button>
    </article>
  );
}
```

> 💡 There is a naming convention for React apps that the state variable and the function always
> follow the pattern of `x` and `setX`

> 📙 Read more about the
> [**concept of state** in the React Docs](https://react.dev/learn/adding-interactivity).

In React state is encapsulated per instance of a component. Think of a feed in a social media app.
The feed is a list of posts. Each post is an individual instance of the `SocialMediaPost` component,
each with individual state. When you change the "liked" state of one specific post, all other post
stay as the are.

A React component can have multiple states. You can use the `useState` function as much as you need.

You can store all kinds of data in state (like booleans, numbers, strings, objects or arrays).

```js
import { useState } from "react";

function SocialMediaPost() {
  const [liked, setLiked] = useState(false);
  const [comments, setComments] = useState([]);
  const [views, setViews] = useState(0);

  /* ... */

  return <article>{/* ... */}</article>;
}
```

---

## What happens when state changes?

To handle state in React we can not simply use a "normal" variable and assign a new value. React
needs to be informed that the data was changed.

This is related to the render cycle of React components.

When React renders a component it executes the component function, which returns JSX. If the JSX
includes a state variable, it uses the variable's value at that time to place into the JSX. Calling
the `set` function with a new value informs React, that state has changed.

> 💡 Changing a state triggers a re-render of the component.

When re-rendering the component, React executes the component function again from top to bottom,
which will again return JSX. However, this time the variable has a new value - the value that was
passed with the call of the `set` function. This means the return JSX includes the new value.

> 📙 Read more about
> [**state updates and re-rendering** in the React Docs](https://react.dev/learn/render-and-commit).

---

## Resources

- [React Docs: Adding Interactivity](https://react.dev/learn/adding-interactivity)
- [React Docs: Responding to Events](https://react.dev/learn/responding-to-events)
- [React Docs: A simple variable is not enough](https://react.dev/learn/state-a-components-memory#when-a-regular-variable-isnt-enough)
- [React Docs: Render and commit](https://react.dev/learn/render-and-commit)
- [MDN: react events and state](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_interactivity_events_state)
