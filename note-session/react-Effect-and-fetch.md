# React Effects and Fetch

## Learning Objectives

- [ ] Knowing that `fetch` is a side effect
- [ ] Knowing how to use the `useEffect` hook
- [ ] Understanding the callback function of `useEffect`
- [ ] Understanding the dependency array of `useEffect`
- [ ] Understanding the cleanup function of `useEffect`
- [ ] Knowing other side effects and cases for`useEffect`

---

## Effects in React

Effects are a way to synchronize React components with external systems.

Examples for interactions with external systems include:

- manipulating the DOM directly (e.g. setting the document title)
- making network requests to fetch data
- working with other Web APIs
- setting up and tearing down subscriptions and global event handlers
- setting timers
- integrating with third-party libraries.

Using an effect is an escape hatch from the declarative world of React. It is a way to run imperative code that is not directly related to rendering the UI. While the component function is required to be pure, effect functions by design are not. They encapsulate side effects.

An effect is defined as a function that is executed after the component was rendered (and the DOM was updated). It can be synchronized to run not only on mounting, but when all or only certain reactive values within the component function have changed.

Effect functions can return a cleanup function that is executed before the effect function runs again, or when the component is unmounted.

> 💡 A **reactive value** is a value that changes: props, state, derivatives thereof or values and functions declared inside of a component function.

> 💡 **Mounting** means a component was rendered, put into the DOM and displayed on the screen for first time. After that various updates and re-renders might occur (e.g. due to state changes). **Unmounting** means the component gets removed and is no longer displayed on the screen.

## `useEffect`

The `useEffect` hook is used to add effects to a React component. It takes two arguments:

- a function that defines the effect (usually an anonymous function)
- an array of variables that the effect depends on

For example, the following code will update the component title to the value of the `title` prop:

```js
import { useEffect } from "react";

function Title({ title }) {
  useEffect(() => {
    // updating the document title is a side effect
    // that is not directly related to rendering the UI
    document.title = title;
  });

  return <h1>{title}</h1>;
}
```

### Effect Dependencies

The effect above will run after the component was rendered and the DOM was updated. But that is way more often than necessary. The effect should only run when the `title` prop changes. To achieve this, we can pass an array of reactive values to the `useEffect()` hook. The effect will only run when one of the reactive values in the array changes.

```js
import { useEffect } from "react";

function Title({ title }) {
  useEffect(() => {
    document.title = title;
  }, [title]);

  return <h1>{title}</h1>;
}
```

This becomes important when the component function has more than one prop or state variable. Imagine having a `count` state in the component:

```js
import { useEffect, useState } from "react";

function Title({ title }) {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = title;
  }, [title]);

  return (
    <div>
      <h1>{title}</h1>
      <p>{count}</p>
      <button type="button" onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

The effect function will only run when the `title` variable is different from the previous value. The `count` state variable is not part of the array, so the effect will not run when the `count` state changes.

> 💡 Always add all reactive values that are used in the effect function to the array of dependencies. React comes with ESLint rules that will warn you if you forget to add a variable to the array of dependencies.
>
> **If the effect has no dependencies the dependency array should be empty: `[]`**.
>
> An empty dependency array tells React to run this effect only once: when the component appears on the screen for the first time.

### Cleanup Function

The effect function can return a cleanup function that is executed before the effect function runs again, or when the component is unmounted.

```js
import { useEffect } from "react";

function Title({ title }) {
  useEffect(() => {
    // make a copy of the old title
    const oldTitle = document.title;

    document.title = title;

    // cleanup function
    return () => {
      // undo what we have done by setting the old title again
      document.title = oldTitle;
    };
  }, [title]);

  return <h1>{title}</h1>;
}
```

The cleanup function should undo the side effects of the effect function. In the example above, the cleanup function resets the document title to the default value.

When the effect function is used to set up a subscription or global event handler, the cleanup function should remove the subscription or event handler.

```js
import { useEffect, useState } from "react";

function WindowWidth() {
  const [windowWidth, setWindowWidth] = useState();

  useEffect(() => {
    function handleResize(event) {
      setWindowWidth(event.target.innerWidth);
    }

    window.addEventListener("resize", handleResize);

    // cleanup function
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return <p>The window is {windowWidth}px wide. 📏</p>;
}
```

For timers, the cleanup function should clear the timer.

```js
import { useEffect, useState } from "react";

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setSeconds((s) => s + 1);
    }, 1000);

    // cleanup function
    return () => {
      clearInterval(timer);
    };
  }, []);

  return <p>The timer is at {seconds} seconds. ⏱</p>;
}
```

> 📙 Read more about [**Synchronizing with Effects** in the React docs](https://react.dev/learn/synchronizing-with-effects).

> 📙 Even though effects can be incredibly useful, you might not actually need an effect to do what you want. Read more about [**You might not need an effect** in the React docs](https://react.dev/learn/you-might-not-need-an-effect).

## How to Fetch Data in React

One of the most common use cases for effects is to fetch data from an external API.

The concept is a follows: After the component first renders, an effect function will run and fetch data from an external API. Once the data is fetched, a state variable is set from within the effect function. If the fetch is dependent on a prop or state variable, the effect function will run again when the variable changes.

The effect function itself can not be async, but it can call async functions. To get around this you can define an async function inside the effect function and call it immediately (without actually awaiting its result).

The following example shows how to fetch data from an API and display the data in a component:

```js
import { useEffect, useState } from "react";

function Jokes() {
  const [jokes, setJokes] = useState([]);

  useEffect(() => {
    async function startFetching() {
      const response = await fetch(
        "https://example-apis.vercel.app/api/bad-jokes"
      );
      const jokes = await response.json();

      setJokes(jokes);
    }

    startFetching();
  }, []);

  return (
    <ul>
      {jokes.map(({ id, joke }) => (
        <li key={id}>{joke}</li>
      ))}
    </ul>
  );
}
```

If the data you want to fetch is dependent on a prop or state variable, you need to add it to the array of variables that the effect depends on:

```js
import { useEffect, useState } from "react";

function Joke({ id }) {
  const [joke, setJoke] = useState();

  useEffect(() => {
    async function startFetching() {
      const response = await fetch(
        `https://example-apis.vercel.app/api/bad-jokes/${id}`
      );
      const joke = await response.json();

      setJoke(joke);
    }

    startFetching();
  }, [id]);

  if (!joke) {
    return null;
  }

  return <h2>{joke.joke}</h2>;
}
```

The above approach works well enough for simple use cases. It does not cover a few of important features, though:

- It does not handle race conditions. (If the user changes the `id` prop before the first fetch is complete, there is a chance that the wrong joke is displayed.)
- It does not handle loading states.
- It does not handle errors. (Neither network errors nor errors from the API.)
- It does not handle caching.

In the future we will use a data fetching library to address these issues.

> 💡 Even when you use a data fetching library, the library will use effects (and the `useEffect` hook) under the hood to fetch data.

> 📙 Read more about [**Fetching Data** in the React docs](https://react.dev/learn/synchronizing-with-effects#fetching-data). The docs also describe a way to handle race conditions.

---

## Resources

- [React docs: Synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects)
- [React docs: Fetching data example with useEffect](https://react.dev/learn/synchronizing-with-effects#fetching-dat)
- [React docs: You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
