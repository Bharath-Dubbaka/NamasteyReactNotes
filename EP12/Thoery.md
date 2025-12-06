## 📝 Redux Store and Slices

### Introduction to Redux

Redux is not mandatory for every application, but it is highly beneficial for **large-scale applications** where you need to manage the state of many components and the application has a number of features. In small-to-mid-scale applications, state can often be managed without Redux.

  * Redux and React are **separate libraries**. Redux can be used with other frameworks or even standalone.
  * The **Redux Store** is a very big JavaScript object, which holds all your application's data in a **global central space**.
  * **Redux Toolkit** is the recommended, modern way of writing Redux logic.
  * **`react-redux`** is the library that provides a bridge (like hooks and the `Provider` component) between React and Redux.

-----

### Redux Slices 🍕

Since the Redux store contains a lot of data, we use **Slices** to keep the store from becoming too big and to organize the data.

  * A **Slice** is a **small, logical partition** of the Redux store, often dedicated to a specific feature (e.g., a `cartSlice` for all cart-related data).
  * Redux states that you **cannot directly modify** the data in the slice (it must be **immutable**).

#### Creating a Slice

You use the `createSlice` function from `@reduxjs/toolkit` to define a slice.
The `createSlice()` function returns an object with the following properties:

  * **`name`**: A string that will be used in the action types.
  * **`initialState`**: The state of the slice when the application starts.
  * **`reducers`**: An object where each function corresponds to an action that can modify the state.
      * For example, `addItem` is a **reducer function** (also called an **action**).
      * **Note**: With Redux Toolkit (which uses the **Immer** library), you can write code that looks like it's mutating the state directly (e.g., `state.items.push(action.payload)`), but Redux handles the immutability behind the scenes. In older (vanilla) Redux, you had to manually create a copy of the state and return the new state.

<!-- end list -->

```javascript
import { createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
  name: "cart",
  initialState: {
    items: [],
  },
  reducers: {
    addItem: (state, action) => {
      state.items.push(action.payload); // Looks like mutation, but is safe with Immer
    },
  },
});

export const { addItem } = cartSlice.actions; // Exporting the action creator
export default cartSlice.reducer; // Exporting the reducer function
```

-----

### Create/Configure the Store 🛠️

You use `configureStore` from `@reduxjs/toolkit` to create and set up the main store.

#### Configuring the Store

The `configureStore` function takes an object where the **`reducer`** key is assigned an object containing all the slice reducers.

```javascript
import { configureStore } from "@reduxjs/toolkit";
import cartReducer from "./utils/cartSlice"; // Assuming the path to your slice

const appStore = configureStore({
  reducer: {
    cart: cartReducer, // The key 'cart' is the name of the slice in the store's state
  },
});

export default appStore;
```

#### Providing the Store to the Application

The `Provider` component from `react-redux` is used as a wrapper around your application's root component to make the store available to all components.

```javascript
import { Provider } from "react-redux";
import appStore from "./appStore"; // Assuming the path to your store

const App = () => {
  return (
    <Provider store={appStore}>
      {/* Your components */}
    </Provider>
  );
};

export default App;
```

-----

### Writing Data into the Store (The Flow) ➡️

To modify the state in a slice (e.g., adding an item to the cart):

1.  **User clicks the button** (e.g., "Add to Cart").
2.  The component **dispatches an action** using the `useDispatch` hook.
3.  The dispatched action (e.g., `addItem("apple")`) is sent to the Redux store.
4.  The action calls the corresponding **reducer function** (`addItem` in the `cartSlice`).
5.  The reducer function updates the state in the **slice** (now the cart has an "apple").

#### Dispatching an Action

  * The **`useDispatch`** hook returns a function called **`dispatch`**.
  * The `dispatch` function takes an **action creator** (like `addItem`) as an argument.
  * The argument passed to the action creator (e.g., `"apple"`) becomes the **payload** of the action object that is sent to the reducer.

<!-- end list -->

```javascript
import { useDispatch } from "react-redux";
import { addItem } from "./utils/cartSlice";

const Body = () => {
  const dispatch = useDispatch();

  const handleAddItem = () => {
    dispatch(addItem("apple")); // The string "apple" is the payload
  };

  return (
    <div>
      <button onClick={handleAddItem}>Add Item</button>
    </div>
  );
};
```

-----

### Reading Data from the Store (Subscribing) 🔄

To read data from the store and ensure your component updates when the data changes, you use **Selectors**.

  * This process is known as **Subscribing to the store**; the component is always in sync with the state in the store.

#### Subscribing with `useSelector`

  * The **`useSelector`** hook from `react-redux` allows a component to **subscribe** to a specific part of the store.
  * The hook takes a selector function that receives the entire store state and returns the specific piece of data you need.

<!-- end list -->

```javascript
import { useSelector } from "react-redux";

const Cart = () => {
  // Subscribing only to store.cart.items to optimize re-renders
  const cartItems = useSelector((store) => store.cart.items);

  return (
    <div>
      <h1>{cartItems.length}</h1>
    </div>
  );
};
```

  * **Best Practice**: Subscribe only to the specific state variable you need (e.g., `store.cart.items`) rather than the entire slice (e.g., `store.cart`). This ensures the component re-renders **only** when the specific variable changes, improving performance.

-----

### `onClick` Event Handlers in React 🖱️

When setting a function for the `onClick` event in React:

1.  **`onClick={handleAddItem}`**: This is the standard way. The function `handleAddItem` is assigned as the event handler and is called by React **only when the button is clicked**, without any arguments.
2.  **`onClick={() => handleAddItem(item)}`**: Use an **anonymous arrow function** when you need to pass specific **arguments** (`item`) to your handler function. The arrow function is assigned as the handler, and when clicked, it calls `handleAddItem` with the argument.
3.  **`onClick={handleAddItem(item)}`**: **This syntax is incorrect** for passing arguments. The `handleAddItem(item)` function is **immediately invoked** when the component renders, and its **return value** (which is usually `undefined`) is assigned to `onClick`, not the function itself. The function will *not* be called on a button click.

Would you like to explore another Redux concept, such as asynchronous logic with Thunks, or see an example of how to remove an item from the cart?