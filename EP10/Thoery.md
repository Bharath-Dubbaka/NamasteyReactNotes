## Namaste React Course by Akshay Saini

# Episode 10 - Jo dikhta hai wo bikta hai

## Q: What are the different ways of writing CSS in React?

A: There are several ways to style React applications:

### 1\. Inline CSS

We can pass an object containing styles to the `style` attribute.

```javascript
// Basic approach
<div className="main" style={{color:"red"}}>

// Better approach -> create an object which contains all the 
// styles and then assign it to the style attribute
const styles = {
  main: {
    backgroundColor: "#f1f1f1",
    width: "100%",
  },
  inputText: {
    padding: "10px",
    color: "red",
  },
};

return (
  <div className="main" style={styles.main}>
    <input type="text" style={styles.inputText} />
  </div>
);
```

### 2\. Importing external stylesheet

a. Create a new css file in your project directory.
b. Write css.
c. Import it into the React file.

```javascript
import { React } from "react";
import "./Components/css/App.css"; // Importing the external CSS

function App() {
  return (
    <div className="main">
    </div>
  );
}
export default App;
```

### 3\. Use CSS Modules

a. A CSS module stylesheet is similar to the regular stylesheet, only with a different extension (e.g. `styles.module.css`).
b. Create a file with `.module.css` extension.
c. Import the module in React app.
d. Add a class name to an element or component and reference the particular style from the imported styles.

#### Example

**styles.module.css**

```css
.font {
  color: #F00;
  font-size: 20px;
}
```

**App.js**

```javascript
import { React } from "react";
import styles from "./styles.module.css";

function App() {
  return (
    <h1 className={styles.heading}>Hello World</h1>
  );
}
export default App;
```

### 4\. Use Styled Components

a. Install the styled-components npm package in the command line.
`npm install styled-components`
b. Create a component and assign a styled property to it. Note the use of **template literals** denoted by backticks.

#### Basic Example

```javascript
import styled from "styled-components";

const Wrapper = styled.div`
  width: 100%;
  height: 100px;
  background-color: red;
  display: block;
`;

function App() {
  return <Wrapper />;
}
```

#### Conditional Styling Example

You can pass props to styled components to change styles dynamically.

```javascript
import { React, useState } from "react";
import styled from "styled-components";

// The wrapper styled component
const Wrapper = styled.div`
  width: 100%;
  height: 100px;
  background-color: red;
  display: ${(props) => (props.$display ? "block" : "none")}; 
`;

function App() {
  const [display, setDisplay] = useState(true);
  return (
    <>
      <Wrapper $display={display} />
      <button onClick={() => setDisplay(!display)}>Toggle</button>
    </>
  );
}
```

## Q: How do we configure Tailwind CSS?

A:

1.  **Install tailwindcss** and its peer dependencies via npm and create your `tailwind.config.js` file.
    ```bash
    npm install -D tailwindcss postcss autoprefixer
    npx tailwindcss init
    ```
2.  **Add tailwindcss and autoprefixer** to your `postcss.config.js` file (or whatever postCSS is configured in your project).
    ```javascript
    module.exports = {
      plugins: {
        tailwindcss: {},
        autoprefixer: {},
      }
    }
    ```
3.  **Add the paths** to all of your template files in your `tailwind.config.js` file.
    ```javascript
    module.exports = {
      content: ["./src/**/*.{html,js}"],
      theme: {
        extend: {},
      },
      plugins: [],
    }
    ```
4.  **Add the @tailwind directives** for each of tailwind's layers to your main css file.
    ```css
    @tailwind base;
    @tailwind components;
    @tailwind utilities;
    ```
5.  **Run your build process** with `npm run dev` or whatever command is configured in your `package.json` file.
6.  **Make sure your compiled css is included in the head.**
    ```html
    <head>
       <link href="/dist/main.css" rel="stylesheet">
    </head>
    ```

## Q: In `tailwind.config.js`, what do the keys mean (content, theme, extend, plugins)?

A:

1.  **Content:** This key specifies the paths to all of your template files in your project. Tailwind CSS will scan these files for class names and generate only the necessary styles. This helps keep the final CSS file small and optimized.
2.  **Theme:** This key is used to customize the default theme of Tailwind CSS. You can define your own values for colors, fonts, spacing, and more.
3.  **Extend:** This key is used inside the **theme** key to extend the default theme without completely overriding it. This is useful for adding additional utilities or modifying existing ones.
4.  **Plugins:** This key allows you to add plugins to Tailwind CSS. Plugins can add additional utilities, components, or modify the existing ones. Tailwind CSS has a variety of official plugins, or you can create your own.

## Q: Why do we have a `.postcssrc` file?

A: The `.postcssrc` file (or `postcss.config.js` file in some setups) is used to configure PostCSS, a tool for transforming CSS with JavaScript plugins. PostCSS is often used in conjunction with Tailwind CSS to enable additional CSS processing capabilities. Here's why you might have a `.postcssrc` file:

1.  **postCSS plugins:** PostCSS is a powerful tool that can use a variety of plugins to perform different tasks, such as autoprefixing, minifying CSS, and more. The `.postcssrc` file specifies which plugins to use and their configurations.
2.  **Tailwind CSS Integration:** Tailwind CSS is a PostCSS plugin. The `.postcssrc` file ensures that Tailwind CSS is processed correctly during the build process.
3.  **Autoprefixing:** Autoprefixer is a PostCSS plugin that adds vendor prefixes to CSS rules, ensuring compatibility with different browsers. Including it in your `.postcssrc` file helps maintain cross-browser compatibility.
4.  **CSS Minification and Optimization:** You can use plugins like `cssnano` for minifying and optimizing your CSS. This is particularly useful for production builds to reduce the file size.
5.  **Modularity and Maintainability:** Having a dedicated configuration file for PostCSS allows for better modularity and maintainability. It separates PostCSS-related configurations from other parts of your build setup, making it easier to manage and update.