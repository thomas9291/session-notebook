> **Warning**  
> This document is only for internal use. You (as a coach) do not have to read this to teach this session. ❤️

# Rationale: Why We Are Not Building Our Own Local Storage Hook

Or: Why none of the simple solutions are ideal…

> 📙 _Please read [**Synchronizing with Effects**](https://react.dev/learn/synchronizing-with-effects) and [**You Might Not Need an Effect**](https://react.dev/learn/you-might-not-need-an-effect) in the React docs before reading this rationale._

Custom React hook solutions for local storage have two main characteristics:

- When and how is the data initialized?
- How is the stored data updated?

> 💡 Note that all examples below are a counter state that is not extracted into a custom hook. All concepts apply to a more generalised custom hook version as well.

## When and How Is the Data Initialized?

### Option 1: Initialize inside `useState`

The simplest solution is to initialize the data directly in the `useState` initializer.

```js
const [count, setCount] = useState(() => {
  const value = localStorage.getItem("count");
  return value !== null ? JSON.parse(value) : 0;
});
```

This works well for client side rendering. For server side rendering, this solution does not work. Local storage is not available on the server. You could of course do a check for `typeof window !== "undefined"` but that only solves part of the problem. You would still get a hydration mismatch when the client initializes the state with a different value than the server.

For our bootcamp that means that this solution breaks as soon as we use Next.js.

### Option 2: Initialize inside `useEffect`

Another solution is to initialize the data inside a `useEffect` hook.

```js
const [count, setCount] = useState(0);

useEffect(() => {
  const value = localStorage.getItem("count");
  if (value !== null) {
    setCount(JSON.parse(value));
  }
}, []);
```

This works for server side rendering because the `useEffect` hook is not executed on the server. The client always initializes the state with the default value and only updates it with the stored value after the first render.

The issue here is more subtle. The `useEffect` hook is executed after the first render. This means that the initial render will always use the default value. This _can_ cause a flash of content. If the default value is `0` and the stored value is `42`, the user will see `0` for a split second before the state is updated to `42`.

This can be solved by using a `useLayoutEffect` hook instead of a `useEffect` hook. This will execute the effect directly after the render without ever flushing the render result to the screen. React warns about using `useLayoutEffect` on the server though. Solutions like _isomorphic layout effect_ work around this issue but are not officially supported by React.

### Option 3: Avoiding the effect

The React docs offer another (arguably weird) solution to this issue by not using a `useEffect` hook at all and calling the `setCount` function directly inside the component function. This will update the state immediately after the render without ever flushing the render result to the screen. To avoid an infinite loop, you need a `didInit` state variable to make sure that the initial setter function is only called once.

```js
const [count, setCount] = useState(0);

const [didInit, setDidInit] = useState(false);

if (!didInit && typeof window !== "undefined") {
  const value = localStorage.getItem("count");

  if (value !== null) {
    setCount(JSON.parse(value));
  }
  setDidInit(true);
}
```

This requires a `typeof window !== "undefined"` check to avoid errors on the server. The problem with this solution is that it is very uncommon to use state setters directly inside the component function. It also requires an additional `didInit` state variable which is not ideal.

## How Is the Stored Data Updated?

### Option 1: Update inside `useEffect`

You can update the stored data inside a `useEffect` hook.

```js
useEffect(() => {
  localStorage.setItem("count", JSON.stringify(count));
}, [count]);
```

This is a simple solution and works quite well. The React docs do not recomment this pattern though. As per the documentation side effects should happen directly in the function that causes the state change in the first place. The effect function is run much later down the line.

### Option 2: Update inside the setter function

This means that it is better to update the stored data directly inside a custom setter function.

```js
const [count, setCount] = useState(0);

const setCountAndStore = (newCount) => {
  localStorage.setItem("count", JSON.stringify(newCount));
  setCount(newCount);
};
```

This follows the React recommendation to keep side effects close their triggering actions and avoids the need for a `useEffect` hook. The downside is that you need to create a custom setter function and remeber to use it instead of the original setter function. Implementing a custom setter function that also supports the `updater` function requires a bit more code:

```js
const [count, setCount] = useState(0);

const setCountAndStore = (updater) => {
  setCount((prevCount) => {
    const newCount =
      typeof updater === "function" ? updater(prevCount) : updater;
    localStorage.setItem("count", JSON.stringify(newCount));
    return newCount;
  });
};
```

## Using `useSyncExternalStore`

The `useSyncExternalStore` hook allows to subscribe to external stores. It is the most promising of all the solutions for local storage. It replaces the `useState` hook and allows to read the stored value from local storage on the client using `getSnapshot`. It also allows to read the default value on the server using `getServerSnapshot`. The update mechanism works using subscriptions. This allows the value to be updated by storage events which is not easily possible with the other solutions.

All in all it is quite complex to set up and nothing that should be done in user code. The hook is very uncommon and not suited to be taught in our bootcamp.

The [`use-local-storage-state`](https://github.com/astoilkov/use-local-storage-state) package uses this approach though and it seems like this is the most mature way to handle this problem.

## What do we want to teach?

Originally we thought that local storage is a good example to learn more about `useEffect` hooks. As you can see above, it is at least questionable if it makes sense to use `useEffect` when working with local storage. We had to decide what the goal of this session is: Teaching `useEffect` or how to use local storage in React.

Local storage is **not** a good example to learn more about `useEffect` hooks. In fact the React docs recommend to avoid using `useEffect` besides for specific use cases. The previous session contains better examples to learn about documented use cases for `useEffect`. This also includes fetching data in `useEffect` hooks. In the future even data fetching will most likely use the `use` hook and `Suspense` instead of `useEffect`. Once this becomes the standard, we should reconsider teaching `useEffect` at all.

All of the above is very **academic** and not in the spirit of a bootcamp in which we want to teach **practical** approaches. We do not expect any junior developer to ever be required to implement their own local storage hook.

We have decided that **it is more important to teach students a practical approch to use local storage in their React application** regardless of the implementation details. Thus we chose the [`use-local-storage-state`](https://github.com/astoilkov/use-local-storage-state) package. In the handout we talk about the rough idea how a local storage hook could be implemented.
