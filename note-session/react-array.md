# React with Arrays

## Learning Objectives

- [ ] Knowing how to use `.map()` to render lists in JSX
- [ ] Understanding how to render items from an array of objects
- [ ] Knowing to add a unique key for list items

---

## Arrays in JSX

To render elements from an array in React we use the array method `.map()`.

The array method `.map()` is used to apply a transformation to all items of an array. When rendering
an array to JSX we like to do exactly this. We like to transform each item of an array into a JSX
tag. This is why we are using `.map()`.

```js
function Drinks() {
  const drinks = ["water", "lemonade", "coffee", "tee"];

  return (
    <ul>
      {drinks.map((drink) => (
        <li>{drink}</li>
      ))}
    </ul>
  );
}
```

---

## Key Property

The example above misses a tiny, but very important part: the `key` prop!

Without the `key` prop you will see an error in the console:

> `Warning: Each child in a list should have a unique "key" prop.`

When rendering an array in JSX you need to pass a **unique identifier** as value for the `key` prop
of the first JSX tag returned in `.map()`. This is important for React to keep track of changes that
happens to the data when re-rendering.

Therefore you must always make sure your array contains a unique id per item. You can ensure this by
using objects to define the data in your arrays.

```js
function Drinks() {
  const drinks = [
    { id: 0, name: "water" },
    { id: 1, name: "lemonade" },
    { id: 2, name: "coffee" },
    { id: 3, name: "tea" },
  ];

  return (
    <ul>
      {drinks.map(({ id, name }) => (
        <li key={id}>{name}</li>
      ))}
    </ul>
  );
}
```

> 📙 If you are interested in understanding the details behind this, you can read about
> [**the `key` prop** in the React Docs](https://react.dev/learn/rendering-lists#keeping-list-items-in-order-with-key).

> 💡 If you pass the `key` prop to a component, you cannot access it in the component. It is a special prop that React only uses internally.
>
> ```js
> function Drink({ name, key }) {
>   console.log(key); // → undefined
>   return <li>{name}</li>;
> }
>
> function Drinks() {
>   const drinks = [
>     { id: 0, name: "water" },
>     { id: 1, name: "lemonade" },
>     { id: 2, name: "coffee" },
>     { id: 3, name: "tea" },
>   ];
>
>   return (
>     <ul>
>       {drinks.map(({ id, name }) => (
>         <Drink key={id} name={name} />
>       ))}
>     </ul>
>   );
> }
> ```
>
> If you want to access the `id` in this example you can pass it again as a prop:  
> `<Drink key={id} id={id} name={name} />`.

## Keyed Fragments

If you are rendering a list of items that are not wrapped in a single JSX tag, you can use a
`<Fragment>` to wrap the items.

```js
import { Fragment } from "react";

function Drinks() {
  const drinks = [
    { id: 0, name: "water", description: "very wet" },
    { id: 1, name: "lemonade", description: "quite sweet" },
    { id: 2, name: "coffee", description: "cold brew" },
    { id: 3, name: "tea", description: "earl grey, hot" },
  ];

  return (
    <dl>
      {drinks.map(({ id, name, description }) => (
        <Fragment key={id}>
          <dt>{name}</dt>
          <dd>{description}</dd>
        </Fragment>
      ))}
    </dl>
  );
}
```

> 💡 Here you cannot use the short syntax (`<>…</>`) for the `<Fragment>` because you need to
> pass the `key` prop to the `<Fragment>`. The short syntax does not allow to pass props.

---

## Resources

- [React Docs: Rendering Lists](https://react.dev/learn/rendering-lists)
