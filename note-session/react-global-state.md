# React Global State

## Learning Objectives

- [ ] Understanding prop drilling
- [ ] Knowing naming conventions with prop drilling
- [ ] Knowing the concept of global state

---

## Lifting State Up

In many situations a state that already exists in one component is also needed in another component. The components must share a common state. To solve this you need to move the state up the component hierarchy to the first shared parent component. This is called "lifting state up". From this point, state variables or functions to change the state are passed to lower-level components via props.

You find more information on this topic in the [React State 2 handout](../react-state-2/react-state-2.md).

---

## Prop drilling

Individual components consuming state variables and their shared ancestor, where the state is defined, can be far away from each other in the component hierarchy. The consequence is that state variables must be passed via props through several other components until they arrive in the target component. This is called "prop drilling".

Consider the following example:

```jsx
function App() {
  const [userIsLoggedIn, setUserIsLoggedIn] = useState(false);

  return <ProductsPage userIsLoggedIn={userIsLoggedIn} />;
}

function ProductsPage({ userIsLoggedIn }) {
  return <ProductsList userIsLoggedIn={userIsLoggedIn} />;
}

function ProductsList({ userIsLoggedIn }) {
  return products.map((product) => (
    <ProductCard {...product} userIsLoggedIn={userIsLoggedIn} />
  ));
}

function ProductCard({ userIsLoggedIn }) {
  return <ProductActions userIsLoggedIn={userIsLoggedIn} />;
}

function ProductActions({ userIsLoggedIn }) {
  return userIsLoggedIn ? (
    <button>One-click Buy</button>
  ) : (
    <button>Add to Basket</button>
  );
}
```

The state variable `userIsLoggedIn` is defined in the `App` component. It is passed down four times until it can be used in the `ProductActions` component.

Imagine there are many more components in this app, some of which also need to know if a user is logged in or not.

Prop drilling through a few levels is perfectly fine. However, if the path gets longer and several state variables are passed via props, the complexity increases and the maintainability of the code is reduced. On the way, passing each prop must not be forgotten in any component.

---

## Naming conventions for props and functions

In the [React Props handout](../react-props/react-props.md) you will find general information about the naming of variables and functions that are passed via props.

In the context of prop drilling, be careful not to rename props halfway through. If a prop is renamed, the logical reference might be lost, which makes the code harder to understand.

Although we recommend prefixing function names `handle` and corresponding props with `on`, you don't need to rename drilled props in every component along the way.

A solution like this is easier to read:

```jsx
function App() {
  const [userIsLoggedIn, setUserIsLoggedIn] = useState(false);

  function handleLogIn() {
    setUserIsLoggedIn(true);
  }

  return <Layout handleLogIn={handleLogIn} />;
}

function Layout({ handleLogIn }) {
  return <Header onLogIn={handleLogIn} />;
}

function Header({ onLogIn }) {
  return <button onClick={onLogIn}>Log In</button>;
}
```

---

## State management libraries

In the React ecosystem, a variety of different libraries have evolved to simplify the handling of complex state. To avoid heavy prop drilling, the concept of "global state" is an established solution.

The idea: state is not defined in a component and passed through via props, but defined outside the component hierarchy. Each component can access the global state.

There are many libraries that offer implementations for global state. We recommend ["zustand"](https://github.com/pmndrs/zustand). The various libraries differ in some details, but the idea of global state is common.

With "zustand" the above example would look like this. Passing down the `userIsLoggedIn` is no longer required.

```jsx
import create from "zustand";

const useUserStore = create((set) => ({
  isLoggedIn: false,
  logIn: () => set(() => ({ isLoggedIn: true })),
  logOut: () => set(() => ({ isLoggedIn: false })),
}));

function App() {
  return <ProductsList />;
}

function ProductsPage() {
  return <ProductsList />;
}

function ProductsList() {
  return products.map((product) => <ProductCard {...product} />);
}

function ProductCard() {
  return <ProductActions />;
}

function ProductActions() {
  const userIsLoggedIn = useUserStore((state) => state.isLoggedIn);

  return userIsLoggedIn ? (
    <button>One-click Buy</button>
  ) : (
    <button>Add to Basket</button>
  );
}
```

> ❗️ Using global state does not mean that you should avoid prop drilling everywhere in your app. It would be an anti-pattern to store all data in global state.
>
> Think carefully about what data really needs to be available globally for many components. Only such data should be stored in global state.
>
> If the state only refers to a part of your app, it should be defined locally in a component and passed on via props.

---

## Resources

- [zustand on GitHub](https://github.com/pmndrs/zustand)
