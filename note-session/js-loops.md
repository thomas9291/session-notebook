# JS Loops

## Learning Objectives

- understanding the concept of loops
- understanding classic `for` loops
- understanding modern `for...in` and `for...of` loops
- understanding `while` loops

---

## What is a loop

A loop executes a respective block of code over and over again until an end criteria is met. In
JavaScript, two basic types of loops exist:

- `while` loop: are used when a task needs to be executed until a specific criteria is met.
- `for` loop: are commonly used when a given task needs to be executed x times or for each element
  in an object / array.

---

## `while`

The while loop is the most fundamental type of loop. It repeats a code block as long as the stated
criteria is `true`.

```js
let string = "a";

while (string.length <= 8) {
  console.log(string);
  string = string + string;
}

// 'a'
// 'aa'
// 'aaaa'
// 'aaaaaaaa'
```

In this example, the while loop repeats itself 4 times, until the string becomes too long and the
loop criteria changes to `false`.

---

## `for`

`for` loops are intended for repeating a task a certain number of times. They consist of three
internal parts:

- an internal counter which is increased / decreased every iteration.
- a criteria which checks the value of the counter. As long as the criteria is `true`, the loop is
  executed.
- a rule how the counter is increased / decreased (in most cases it is increased by 1)

```js
for (let counter = 0; counter < 4; counter++) {
  console.log(counter);
}
// 0
// 1
// 2
// 3
```

The body of the for loop contains the code which is executed on each iteration. For the example
above it is a `console.log` which logs the value of the counter on each iteration until the value of
`counter` reached 4 and the loop is terminated.

---

## `for...in`

The `for...in` is a shorthand notation to loop through all keys of an object:

```js
const user = {
  name: "Alex",
  age: 28,
  email: "alex@mail.com",
};

for (const key in user) {
  console.log(user[key]);
}

// 'Alex'
// 28
// 'alex@mail.com'
```

The loop has an iterator variable, in this case `key` which is assigned the respective key value in
each iteration (first 'name', then 'age' and finally 'email').

---

## `for...of`

Similar to `for...in` the `for...of` loop is a shorthand notation, but for looping through all items
of an array.

```js
const fruits = ["apple", "banana", "melon"];

for (const fruit of fruits) {
  console.log(fruit);
}

// 'apple'
// 'banana'
// 'melon'
```

This time the iterator variable `fruit` is assigned the respective array item in each iteration.

---

## Resources

- [MDN article about loops and iterations](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration)
