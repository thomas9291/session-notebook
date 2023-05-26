# React Styled Components

## Learning Objectives

- [ ] Understanding what a CSS-in-JS library is and why we prefer it over normal CSS
- [ ] Knowing how to use styled components

  - [ ] create basic styled components
  - [ ] style custom components
  - [ ] adapt styling based on props
  - [ ] use nested styles with pseudo-elements and pseudo-classes
  - [ ] write global styles

- [ ] Knowing how to use fonts from Google with Next.js

---

## What is CSS-in-JS and why do we use it?

CSS-in-JS refers to a collection of ideas to solve complex problems in CSS. There are several libraries which use this approach, one of them is **styled components**. All implementations have in common that they leverage JavaScript as a language for creating styles.

Here's a list of advantages of a CSS-in-JS library like **styled components**:

- automatic critical CSS injected (and nothing more)
- no class name bugs
- easier deletion of CSS
- simple dynamic styling
- painless maintenance
- automatic vendor prefixing

> 📙 Read more about the [motivation to use styled components](https://styled-components.com/docs/basics#motivation).

---

## Styling with styled components

### Basic Styling

To create a styled component,

- import `styled`
- use it to create a styled component like `ListItem`, and
- implement the styled component in the return statement of your component.

```js
//components/List.js
import styled from "styled-components";

export default function List() {
  return (
    <StyledList>
      <ListItem>Item 1</ListItem>
      <ListItem>Item 2</ListItem>
      <ListItem>Item 3</ListItem>
    </StyledList>
  );
}

const ListItem = styled.li`
  background-color: crimson;
`;

const StyledList = styled.ul`
  list-style-type: none;
`;
```

> 💡 Note that the name of a styled component is in uppercase (because it's a component), but must not equal the function name; a common naming pattern is to include the word `Styled`.

> 📙 Read more about [basic styling with styled component](https://styled-components.com/docs/basics#getting-started).

### Styling a Custom Component

Sometimes, there already is a component with predefined styles, but you want to further extend them:

- you might have defined a `Button` component with basic styling yourself [see a good example in the docs](https://styled-components.com/docs/basics#extending-styles) or
- a framework offers a component you want to use, like the `Link` component from `next/link`:

```js
import styled from "styled-components";
import Link from "next/link";

export default function List() {
  return (
    <ul>
      <li>
        <StyledLink>Let's go somewhere!</StyledLink>
      </li>
    </ul>
  );
}

const StyledLink = styled(Link)`
  text-decoration: none;
`;
```

> 📙 Read more about [styling any component](https://styled-components.com/docs/basics#styling-any-component) and .

### Adapting based on props

To adapt styling based on props, pass a function to a styled component's template literal and use the ternary operator to check the prop:

```js
import styled from "styled-components";

export default function List({ isOnFire }) {
  return (
    <StyledList isOnFire>
      <li>Item 1</li>
      <li>Item 2</li>
      <li>Item 3</li>
    </StyledList>
  );
}

const StyledList = styled.ul`
  list-style-type: ${(props) => (props.isOnFire ? "🔥" : "❄️")};
  /* or with destructuring: */
  list-style-type: ${({ isOnFire }) => (isOnFire ? "🔥" : "❄️")};
`;
```

If you want to set several CSS properties based on the same prop, you can use the `css` helper:

```js
import styled, { css } from "styled-components";

const StyledList = styled.ul`
  ${(props) =>
    props.isOnFire &&
    css`
      list-style-type: "🔥";
      background-color: red;
      color: white;
    `}
`;
```

> 💡 Besides others, the advantages of the `css` helper is syntax highlighting and performance optimization.

> 📙 Read more about [styling based on props](https://styled-components.com/docs/basics#adapting-based-on-props).

### Pseudoelements and Pseudoselectors

To apply pseudoelements, pseudoselectors, or nesting styles, you can use a single ampersand `&` which refers to the component itself:

```js
const StyledLink = styled(Link)`
  text-decoration: none;
  &:hover {
    color: red;
  }
`;
```

> 📙 Read more about [pseudoelements and pseudoselectors](https://styled-components.com/docs/basics#pseudoelements-pseudoselectors-and-nesting).

### Global Styling

To implement global styling, you'll need to create a global styled component. To keep the structure of the project clear, create a `styles.js` file in the root of the project for this:

```js
// styles.js
import { createGlobalStyle } from "styled-components";

export default createGlobalStyle`
  *,
  *::before,
  *::after {
    box-sizing: border-box;
  }
  body {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica,
      Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  }
	// more global styles here...
`;
```

Import the `GlobalStyle` component into the `pages/_app.js` file and render it above the `<Component />`:

```js
// pages/_app.js
import { GlobalStyle } from "../styles";

export default function App({ Component, pageProps }) {
  return (
    <>
      <GlobalStyle />
      <Component {...pageProps} />
    </>
  );
}
```

> 💡 There is no consensus as to where to put the `GlobalStyle` component. The decision for a `styles.js` file was made to mirror the fact that, until now, global styles were written in a `styles.css` file.

> 📙 Read more about [createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle).

### Google Fonts GDPR- (DSGVO-) compliant integration

Next.js features the `@next/font` npm package. It will automatically optimize your fonts (including custom fonts) and **remove external network requests for improved privacy and performance** by self-hosting Google fonts.

You need to [install `@next/font` in your project](https://nextjs.org/docs/basic-features/font-optimization#usage) first. To implement a font family, import it where needed and use it inside the styled component.

The following example sets the font-family in the `GlobalStyle` component for the HTML `body` element:

```js
import { createGlobalStyle } from "styled-components";
import { Open_Sans } from "@next/font/google";

const openSans = Open_Sans({ subsets: ["latin"] });

export default createGlobalStyle`
  // ... some globals styles here...
  }
  body {
    margin: 0;
    font-family: ${openSans.style.fontFamily}; 
    padding: 2rem;
  }
	// ... some more global styles here ...
`;
```

> 📙 Read more about [Google Fonts in Next.js](https://nextjs.org/docs/basic-features/font-optimization) and check the [api reference for `@next/font`](https://nextjs.org/docs/api-reference/next/font).

---

## Resources

- [What actually is CSS-in-JS?](https://medium.com/dailyjs/what-is-actually-css-in-js-f2f529a2757)
- [styled components](https://styled-components.com/)
- [Next.js: Font Optimization](https://nextjs.org/docs/basic-features/font-optimization)
- [Google Fonts](https://fonts.google.com/)
