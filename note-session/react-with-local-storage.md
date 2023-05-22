# React with Local Storage

## Learning Objectives

- [ ] Understanding the concept of persistent storage in the browser
- [ ] Knowing the difference between `localStorage` and `sessionStorage`
- [ ] Using the methods `setItem()` and `getItem()`
- [ ] Using a library to handle local storage in React apps

---

## The Web Storage API

> 💡 Note that the Web Storage API is not part of React. It is a browser API that is available in all modern browsers.

The Web Storage API provides two methods for storing data on the client:

- `localStorage` stores data with no expiration date
- `sessionStorage` stores data for one session (data is lost when the browser tab is closed)

Data is stored in the browser and per domain, i.e. all data stored by `example.com` can be accessed by `www.example.com` and `subdomain.example.com`, but not by `others.org`.

This makes it possible to store data across page reloads and browser restarts in a secure way.

To store data the API uses key-value pairs. The key is a string and the value can be a string, a number or a boolean.

> 💡 All following examples use `localStorage` but the same applies to `sessionStorage`.

> 📙 Read more about the [**Web Storage API** on the mdn web docs](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API).

### Storing Data

To store data, use the `setItem()` method:

```js
localStorage.setItem("name", "Alex");
localStorage.setItem("age", 28);
localStorage.setItem("isOnline", true);
```

### Retrieving Data

To retrieve data, use the `getItem()` method:

```js
const name = localStorage.getItem("name"); // → "Alex"
const age = localStorage.getItem("age"); // → 28
const isOnline = localStorage.getItem("isOnline"); // → true
```

Calling `getItem` returns `null` if the key does not exist.

```js
const nope = localStorage.getItem("nope"); // → null
```

### Removing Data

To remove data, use the `removeItem()` method:

```js
localStorage.removeItem("name");
```

### Clearing All Data

To remove all data, use the `clear()` method:

```js
localStorage.clear();
```

### Storing Complex Data

The Web Storage API only supports strings, numbers and booleans. To store more complex data, you need to serialize it first. This can be done using the `JSON.stringify()` method:

```js
const user = {
  name: "Alex",
  age: 28,
  isOnline: true,
};

localStorage.setItem("user", JSON.stringify(user));
```

To retrieve the data, you need to parse it using the `JSON.parse()` method:

```js
const user = JSON.parse(localStorage.getItem("user"));
```

### Helper Functions

To make working with the Web Storage API easier, you can create helper functions that encapsulate the serialization and deserialization:

```js
// store data
function setItem(key, value) {
  localStorage.setItem(key, JSON.stringify(value));
}

// retrieve data
function getItem(key) {
  return JSON.parse(localStorage.getItem(key));
}
```

These functions will work with simple data types like strings and numbers as well as complex data types:

```js
setItem("user", {
  name: "Alex",
  age: 28,
  isOnline: true,
});
setItem("count", 42);

const user = getItem("user");
const count = getItem("count");
```

## React with Local Storage

You can also use the Web Storage API in React. Most commonly, you'll want to persist state in local storage so that it survives page reloads.

React has several ways that allow you to synchronize state with local storage. The general concept is to retrieve the initial state from local storage and to store the state in local storage whenever it changes.

Because it becomes quite tricky to correctly wire up all the different pieces yourself, you'll want to use a library that provides a hook for this.

### `use-local-storage-state`

The [`use-local-storage-state`](https://github.com/astoilkov/use-local-storage-state) library provides a hook that allows you to persist state in local storage.

You can use it as a drop-in replacement for the `useState` hook (commented out in the example below):

```js
// import { useState } from "react";
import useLocalStorageState from "use-local-storage-state";

function Counter() {
  // const [count, setCount] = useState(0);
  const [count, setCount] = useLocalStorageState("count", { defaultValue: 0 });

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

> 💡 Notice that the first argument of the `useLocalStorageState` hook is the key that is used to store the state in local storage. If you use the same key for multiple components, they will share the same state.

> 💡 You don't need to handle serialization or parsing of complex data yourself with `use-local-storage-state`. The library takes care of it for you in the background.

> 📙 Read more about [**how to use the `use-local-storage-state` hook** in its docs](https://github.com/astoilkov/use-local-storage-state#usage).

---

## Resources

- [Web Storage API on the mdn web docs](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
- [use-local-storage-state on GitHub](https://github.com/astoilkov/use-local-storage-state)
