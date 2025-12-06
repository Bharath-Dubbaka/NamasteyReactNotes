I apologize if my previous response changed the text too much. My goal was to convert the images into a clear, structured, and easy-to-read **Markdown format**, similar to the example you provided, which involved organizing the content with headings, bullet points, and code blocks. I was attempting to make the content more accessible and searchable.

I understand now that you want the **exact text** from the images, rendered as a single Markdown file, while still using the formatting elements (like code blocks and lists) to represent the structure of the original pages.

Here is the combined, exact text from your images, formatted using Markdown:

````markdown
## 📝 Redux Store and Slices

### Episode 12 - Let's Build Our Store

**Introduction**

Note - **Redux** is not mandatory to use in our application.

When we build large-scale applications where we have to manage the state of a lot of components and the application has a number of features, then using **Redux** in our application makes sense.

In small-scale or mid-scale applications, we can still manage the state without using Redux.

Redux and React both are not the same thing. Redux is not part of React. They both are different libraries.
All the applications built using Redux can also be built without using it.

Redux is not the only library for state management. There is also another library named **Zustand**.

Just like we have **React Dev Tools**, we also have Redux Dev Tools which help us to debug our application when we use Redux.
There are 2 libraries that Redux team offers:

1. **react-redux**: This is like a bridge between React and Redux.
2. **Redux toolkit**: This is a newer way of writing redux. This package is intended to be the standard way of writing Redux logic.

**Redux Store** is like a very big JavaScript object, which has a lot of data in it, stored in a global central space.

**Is it a good idea to store all the data in one place ?** **Yes**

---

### Redux Slices and Store Configuration

Since the Redux store contains a lot of data, we do not want it to become very big, so we make use of **Slices** offered by Redux.

We can assume **slice** as a small portion of **Redux store**. We can create multiple slices in our store.
To keep data separate, we create logical partitions in our store. These partitions are known as **Slices**.
If we want to keep the data related to the cart, then we will create a separate slice for the cart. If we want to keep the data related to the logged in user, then we will create a separate slice for that as well.

Redux says that we cannot directly modify the data in the slice. Redux offers a way to do that.
Assume that we have a **cartSlice** which keeps track of the data in the cart. We have an Add to cart button which adds the item into the cart.
By clicking on this button, we cannot directly modify our cartSlice.

To modify the cartSlice, when the user clicks on the Add To Cart button, we have to **dispatch an action**.

When we dispatch an action, it calls a function and then this function modifies the cart.

Here is the flow:
**User clicks the button -> Dispatch an action -> Action calls a function -> Function modifies the cart slice**

The function which is being called by the action is known as **Reducer Function**.

---

### Install Redux

```bash
npm install @reduxjs/toolkit
npm install react-redux
```
````

**Create/Configure the store**

```javascript
import { configureStore } from "@reduxjs/toolkit";

const appStore = configureStore({});

export default appStore;
```

-  Configuring the store is Redux's job. That's why we imported **`configureStore`** from **`@reduxjs/toolkit`**.

**Providing the store to the application**

```javascript
import { Provider } from "react-redux";
import appStore from "./appStore";

const App = () => {
   return (
      <Provider store={appStore}>
         <Header />
         <Body />
      </Provider>
   );
};

export default App;
```

-  **Providing this store to the application is the job of `react-redux`**.
-  That's why we imported **`Provider`** from **`react-redux`**.
-  We then use this **`<Provider></Provider>`** as a wrapper to wrap our application inside it.
-  `Provider` takes a property **`store`** to which we can assign our configured store.

---

### Create a slice

```javascript
import { createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
   name: "cart",
   initialState: {
      items: [],
   },
   reducers: {
      addItem: (state, action) => {
         state.items.push(action.payload);
      },
   },
});

export const { addItem } = cartSlice.actions;
export default cartSlice.reducer;
```

-  **`createSlice()`** returns an object which has following properties:

   -  Name
   -  Initial state
   -  Reducers

-  The **`initialState`** is the state which a slice has in the beginning before it gets modified.

-  **Reducers** have actions and these actions have callback functions.

-  **`.addItem`** is a reducer function which is called as an **action**.

-  The function which is assigned to it is responsible for modifying or updating the cartSlice.

-  We have exported the reducer as a default export.

-  We have also exported the actions from the slice.

-  The **payload** of the action will contain a new item in this case which will be pushed to the items array in the state.

---

### Add the slice to the store

```javascript
import { configureStore } from "@reduxjs/toolkit";
import cartReducer from "./utils/cartSlice";

const appStore = configureStore({
   reducer: {
      cart: cartReducer,
   },
});

export default appStore;
```

-  The **`reducer`** key is assigned with an object which will have all the slices (**cartSlice** in this case).
-  The reducer contains the slices where each of the slices contains its own reducer functions.

---

### Subscribing to the store

-  We can subscribe to the store using a **selector** to read the data of the store.
-  Redux offers a hook named **`useSelector`** which can be used to subscribe to the store.

So when the user clicks the button, it dispatches an action. This action calls the reducer function and this reducer function updates the slice.

\*\*\*\*\*\*\*This was about writing the data into the store.\*\*\*\*\*\*\*

**How to read data from the store ?**

Suppose I want to show the count of items in the cart on my navigation bar.
We can read the data from the store by something known as **Selectors**.
When we use a selector to read the data, this phenomenon is known as **Subscribing to the store**.

So we can say that the navigation bar is subscribed to our store. That means the navigation bar will always be in sync with the store. If the data in the store (**cart slice** in this case) changes, then the data shown on the navigation bar will also change.

---

### Reading Data Example

```javascript
import React from "react";
import { useSelector } from "react-redux";

const Cart = () => {
   const cartItems = useSelector((store) => store.cart.items);

   return (
      <div>
         <h1>{cartItems.length}</h1>
      </div>
   );
};

export default Cart;
```

-  This **`useSelector`** gives us access to the store where we can find the **cart.items**.
-  The items of the cart will be stored in **`cartItems`** which we can use in our component.
-  **Note** - Make sure to access a particular state variable. In our case, we have accessed **`cart.items`** which gives us the exact value of items.
-  If we subscribe only to **`store.cart`**, sometimes this **`store.cart`** may contain state variables other than **`items`**.
-  If there is a change in any of those state variables, then it will **re-render the cart component** as well.
-  So subscribing only to **`store.cart.items`** will make the Cart component re-render only when there is a change in the **`items`**.

---

### Dispatch an action

```javascript
import React from "react";
import { useDispatch } from "react-redux";
import { addItem } from "../utils/cartSlice";

const Body = () => {
   const dispatch = useDispatch();

   const handleAddItem = () => {
      dispatch(addItem("apple"));
   };

   return (
      <div>
         <button onClick={handleAddItem}>Add Item</button>
      </div>
   );
};

export default Body;
```

-  **`React-redux`** offers a hook to dispatch an action i.e. **`useDispatch`**.
-  This hook returns a function i.e. **`dispatch()`**.
-  The **`dispatch()`** function takes an argument i.e. **`addItem`** action.
-  The `addItem` action has a reducer function which takes one argument i.e. **`payload`** (**apple**).
-  When an action is dispatched, an object is created. This object has a key i.e. **`payload`** and it will have the value which is passed to the reducer function i.e. **`apple`**.

---

### Event Handler Details

-  **Payload: "apple"**,
-  The **`handleAddItem`** will be responsible for dispatching the action and it is assigned to the click event of the button. So whenever the user clicks the button, the **`addItem`** action will be dispatched and it will add that item to the cart.

**`onClick={handleAddItem}` vs `onClick={() => handleAddItem(item)}` vs `onClick={handleAddItem(item)}`**

**1. `onClick={handleAddItem}`**
a. This syntax assigns the **`handleAddItem`** function directly to the **`onClick`** event.
b. When the button is clicked, the `handleAddItem` function will be called without any arguments.

**2. `onClick={() => handleAddItem(item)}`**
a. This syntax uses an arrow function to call `handleAddItem` with the **`item`** argument.
b. This is useful when you need to pass specific arguments to the `handleAddItem` function when the button is clicked.
c. Here, the **`item`** needs to be defined or in scope when the button is rendered.
d. If **`item`** is a variable available in the component's context, it will be passed to `handleAddItem` when the button is clicked.

**3. `onClick={handleAddItem(item)}`**
a. This syntax is **incorrect** and will not work as expected.
b. Here, `handleAddItem(item)` is **immediately invoked** when the component renders, rather than being set as a callback to be invoked on the click event.
c. The return value of `handleAddItem(item)` (which is typically **`undefined`** unless the function returns another function) will be assigned to **`onClick`**.
d. To properly call `handleAddItem` with an argument when the button is clicked, you should wrap it in an anonymous function, like in the second example.

In older redux (vanilla redux), it was not allowed to **mutate the state**.
We used to create a copy of our state and then modify that. We also had to return the new state.
This whole process is still done by Redux behind the scenes but now it is not asking developers to do it. Redux is using the **immer** library to do it.

In the new redux, we have to **mutate the state**. And it is not mandatory to return the state as well.

```

I hope this is exactly what you were looking for! Would you like me to focus on a specific section of this text, like the `useSelector` hook, or explain a concept in more detail?
```
