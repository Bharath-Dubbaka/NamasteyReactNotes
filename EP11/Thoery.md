## Namaste React Course by Akshay Saini

# Chapter 11 - Data is the new oil

## Q: What are Higher Order Components (HOCs)?

A:

-  A Higher Order Component is a function that takes a component and returns a component.
-  It takes a component as an input, enhances that component, adds some features into it, and returns the component.
-  Higher Order Components are **pure functions** because they do not change the existing behavior of the input component.

#### Example

```javascript
import React from "react";

// This is our Higher Order Component
function withGreeting(WrappedComponent) {
   return function WithGreeting(props) {
      return (
         <div>
            <h1>Hello!</h1>
            <WrappedComponent {...props} />
         </div>
      );
   };
}

// This is a simple component that we will enhance with our HOC
function MyComponent() {
   return <div>I'm a simple component.</div>;
}

// We create a new component by applying our HOC to the simple component
const EnhancedComponent = withGreeting(MyComponent);

function App() {
   return (
      <div className="App">
         <EnhancedComponent />
      </div>
   );
}

export default App;
```

## Q: What are Controlled and Uncontrolled Components?

A:

### 1\. Uncontrolled Components

-  If a component is managing its own state and controlling the behavior on its own, then the component will be known as an **Uncontrolled component**.
-  The parent component will have no power or control over this component.
-  **Example:** An `ItemCard` component that has a state variable `showHeading` (default false) and toggles it internally when a button is clicked. The parent `ItemCardList` simply renders it but controls nothing.

### 2\. Controlled Components

-  If the state and behavior of a component is being managed by its **parent component**, then it is referred to as a **Controlled component**.
-  **Example:** If the `ItemCard` component receives `showHeading` as a prop from its parent `ItemCardList` and does not manage it internally, it is controlled. The parent decides whether to show the heading or not.

#### Controlled vs Uncontrolled Example

```javascript
// Uncontrolled: Manages its own state
const ItemCard = () => {
   const [show, setShow] = useState(false);
   return (
      <div onClick={() => setShow(!show)}>{show ? "Visible" : "Hidden"}</div>
   );
};

// Controlled: State managed by parent
const ItemCard = ({ show, onShow }) => {
   return <div onClick={onShow}>{show ? "Visible" : "Hidden"}</div>;
};
```

## Q: What is "Lifting the State Up"?

A:

-  Sometimes, we need a parent component to control the state of a child component, or siblings need to share state.
-  Since React has a **one-way data stream** (parent to child), we cannot directly pass data up from child to parent.
-  To solve this, we move the state to the closest common ancestor (parent) and pass the state down as props. This is called **Lifting the State Up**.
-  We also pass a callback function (e.g., `onShow`) from the parent to the child. When the child interacts (e.g., clicks a button), it calls this function to notify the parent to update the state.

## Q: What is Props Drilling?

A:

-  Passing props is a great way to explicitly pipe data through your UI tree to the components that use it.
-  But passing props can become inconvenient when there is a huge tree of components.
-  If a parent component has children, and those children have children, and you need to pass data from the root to a deep leaf node, you have to pass the prop through every intermediate component.
-  This situation, where data is passed through many levels of components that don't need it just to reach a child that does, is called **Prop Drilling**.

## Q: What is React Context?

A:

-  React Context is a method to pass props from parent to child components by storing the props in a "store" (similar to Redux) and allowing child components to access them without manually passing them at each level.
-  Whenever you want a store to keep your states or variables and use them elsewhere in your program, use **Context**.
-  It avoids the complexity of Redux for small to mid-size applications and solves the problem of Prop Drilling.
-  Generally, when we have two or more levels in our component tree, it is viable to use a store instead of lifting state and drilling props.

## Q: How do we create and provide Context?

A:

1. **Create Context:** Use `createContext` from React. We can give it a default value.
   ```javascript
   import { createContext } from "react";
   export const CartContext = createContext({
      items: [],
   });
   ```
2. **Provide Context:** Wrap the components that need access to the context with `<ContextName.Provider>`. You must pass the value to the provider.

   ```javascript
   import { CartContext } from "./store";

   const App = () => {
      return (
         <CartContext.Provider value={{ items: [] }}>
            <Header />
            <Body />
         </CartContext.Provider>
      );
   };
   ```

## Q: How do we consume Context?

A:

-  To consume the context, we make use of the `useContext` hook.
-  `useContext` returns the **context value** for the context you passed.
-  React searches the component tree and finds the closest context provider above for that particular context.

<!-- end list -->

```javascript
import { useContext } from "react";
import { CartContext } from "./store";

const Header = () => {
   const cartCtx = useContext(CartContext);
   return <h1>{cartCtx.items.length} items</h1>;
};
```

_Note: It is suggested to use Context in small and mid-size applications. In large-scale applications, we can make use of Redux._
