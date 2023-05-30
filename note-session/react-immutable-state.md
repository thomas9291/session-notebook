# React Immutable State

## Learning Objectives

- [ ] Understanding why you should never mutate state directly
- [ ] Working with nested arrays and objects in state
- [ ] Knowing the `useImmer` hook

---

## Never mutate state

In the session **React State 3** we discussed how to work with objects and arrays stored in state.

As you have learned you cannot change (mutate) data stored in state directly. You have to treat the state as **read only**. To change state you call the setter function and pass the complete next state.

Consider an object like this in state:

```js
const [user, setUser] = useState({
  name: "John Doe",
  email: "john@doe.com",
});
```

You might be tempted to mutate a value in the object and pass it to the setter function.

```js
user.email = "john_doe@example.com"; // ❌ direct state mutation: don't try this at home!
setUser(user);
```

**This code will not work as expected**: it mutates the object stored in state directly!

When you call the setter function, React checks if the object in state has changed and the UI needs to be updated. Since you mutated the previous state object, it's equal to the new state you passed to the setter function. React won't recognize a difference and won't update the UI.

Therefore, you need to make a copy of the data using the spread syntax and apply the changes to the copy. This way you won't mutate the previous state object.

```js
setUser({
  ...user,
  email: "john_doe@example.com",
});
```

---

## Updating nested states

It can get a bit complicated when you need to change data in a deeper nested state.

```js
const [user, setUser] = useState({
  name: "John Doe",
  contact: {
    email: "john@doe.com",
    phone: {
      mobile: "+001111111111",
      work: "+001234567890",
    },
  },
});
```

When `user.contact.phone.mobile` should be changed, you need to create a copy of each level.

```js
setUser({
  ...user,
  contact: {
    ...user.contact,
    phone: {
      ...user.contact.phone,
      mobile: "+009999999999",
    },
  },
});
```

This code works totally fine! However, you need to write a lot of code to change a single value.

The [`immer`](https://immerjs.github.io/immer/) library helps you updating values in deeper nested states.

It will create a complete copy of the previous state for you. This copy is the `draft` for the next state. Since its a copy you can apply mutations in any way you like. The `immer` library will take care to update the state accordingly.

---

## Using `immer` in React: `useImmer` hook

The [`useImmer` hook](https://github.com/immerjs/use-immer) let's you add `immer` easily to React components.

- Instead of calling `useState` to declare a state, you call `useImmer`
- The returned function should be prefixed with `update` instead of `set`.

The previous example looks like this with the `userImmer` hook.

```js
// useState → useImmer
// setUser → updateUser
const [user, updateUser] = useImmer({
  name: "John Doe",
  contact: {
    email: "john@doe.com",
    phone: {
      mobile: "+001111111111",
      work: "+001234567890",
    },
  },
});
```

When you call the `update` function, you pass a callback. The callback receives a `draft` for the next state as parameter. You can apply mutations to the `draft` directly.

```js
updateUser((draft) => {
  // Mutate the draft directly
  draft.contact.phone.mobile = "+009999999999";
});
```

> 💡 You can find a good guide on [update patterns](https://immerjs.github.io/immer/update-patterns) in the `immer` docs.

---

## Working with objects in arrays

The examples above focusses on mutations in an object. However, in many applications you are likely to work with objects nested in arrays.

Your state might have such a shape:

```js
const [users, setUsers] = useState([
  {
    id: 1,
    name: "John Doe",
    email: "john@doe.com",
  },
  {
    id: 2,
    name: "Jane Doe",
    email: "jane@doe.com",
  },
  {
    id: 3,
    name: "James Doe",
    email: "james@doe.com",
  },
]);
```

You can perform an update to change the `email` of a user with the `id` of `1` like this:

```js
setUsers(
  users.map((user) =>
    user.id === 1
      ? {
          ...user,
          email: "john_doe@example.com",
        }
      : user
  )
);
```

The same operation with the `update` function provided by the `useImmer` hook looks like this:

```js
updateUsers((draft) => {
  const user = draft.find((user) => user.id === 1);
  user.email = "john_doe@example.com";
});
```

The exact code you need to write depends heavily on the type of operation (update, insert, delete) and on the shape of the data you store in state.

Whether to use `immer` or not depends on personal preference and on the complexity of the data structure. With deeper nested structures using `immer` might allow you to create simpler code.

---

## Resources

- [React docs: Updating Objects in State](https://react.dev/learn/updating-objects-in-state)
- [useImmer hook](https://github.com/immerjs/use-immer)
- [Immer: update patterns](https://immerjs.github.io/immer/update-patterns)
