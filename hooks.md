The images provided in the final batch contain notes covering:

1.  **React Hooks and State Management:** `useState`, `useEffect`, `useRef`, `useMemo`, `useCallback`, `useReducer`.
2.  **Performance Optimization:** `useMemo` and `useCallback` difference.
3.  **Custom Hooks:** `useOnlineStatus` (revisiting the concept).
4.  **Error Handling:** `try...catch...finally` and `Error Boundaries`.

I will organize this content into a new chapter structure.

## Namaste React Course by Akshay Saini

# Chapter 12 - Finding the path 🧭 (React Hooks)

## Q: What are the main React Hooks?

A: Hooks are functions that let you "hook into" React state and lifecycle features from function components. They do not work inside classes. The main hooks covered are:

| Hook | Purpose |
| :--- | :--- |
| `useState()` | Manages state within a functional component. |
| `useEffect()` | Handles side effects (like data fetching, manual DOM manipulation, subscriptions). |
| `useRef()` | Accesses the DOM directly and persists mutable values between renders without causing a re-render. |
| `useMemo()` | Memoizes expensive computations, caching the result until dependencies change. |
| `useCallback()` | Memoizes a function instance, ensuring the same function reference is used across renders until dependencies change. |
| `useReducer()` | An alternative to `useState` for more complex state logic that involves multiple sub-values or when the next state depends on the previous one. |

-----

## Q: What is `useState()`?

A:

  - `useState` is a function that takes the **initial state** of the variable as an argument and returns an array with **two elements**:
    1.  The current state value.
    2.  A function to update that value (the setter).
  - When the setter function is called, React re-renders the component.

#### Example

```javascript
const [item, setItem] = useState(0); 
```

## Q: What is `useEffect()`?

A:

  - `useEffect` is used to run side effects after React has finished rendering.
  - It takes two arguments:
    1.  A **callback function** containing the side effect logic.
    2.  An optional **dependency array** (`deps`).
  - **Cleanup function:** The callback can optionally return a cleanup function, which runs before the component is destroyed or before the effect runs again.

| Dependency Array (`deps`) | When does the effect run? |
| :--- | :--- |
| **No Array** | Runs after *every* render. |
| **Empty Array (`[]`)** | Runs only **once** after the initial render (like `componentDidMount`). |
| **Array with values (`[value1, value2]`)** | Runs after the initial render and whenever any of the values in the array change. |

-----

## Q: What is `useRef()`?

A:

  - `useRef` returns a mutable **ref object** whose `.current` property is initialized to the passed argument.
  - It's mainly used for two purposes:
    1.  **Accessing the DOM:** Getting a direct reference to a DOM element (e.g., focusing an input field).
    2.  **Storing mutable values:** Persisting any mutable value across component re-renders without causing a re-render (unlike `useState`).

#### Example (Accessing DOM)

```javascript
const inputEl = useRef(null);

const onButtonClick = () => {
  inputEl.current.focus(); // Focus the input field
};

return <input ref={inputEl} type="text" />;
```

## Q: What is `useMemo()` vs `useCallback()`?

A: Both are hooks used for **memoization** (caching) to optimize performance, but they target different things:

| Hook | What does it return/memoize? | When to use it? |
| :--- | :--- | :--- |
| **`useMemo(fn, deps)`** | Returns a **memoized value** (the result of the function call). | When an expensive **calculation or data transformation** should only run when its dependencies change. |
| **`useCallback(fn, deps)`** | Returns a **memoized function instance** (the function definition itself). | When passing a function down to **child components** (especially those wrapped in `React.memo`) to prevent unnecessary re-renders of the child. |

### The need for `useCallback()`

When a parent component re-renders, all functions inside it are recreated, leading to a new reference. If this function is passed as a prop to a memoized child component (using `React.memo`), the child will still re-render because it sees a "new" prop (a new function reference), defeating the purpose of memoization. `useCallback` ensures the function reference remains the same.

-----

## Q: What is `useReducer()`?

A:

  - `useReducer` is an alternative to `useState` for managing complex state logic.
  - It takes a **reducer function** and an **initial state**, and returns the current state and a `dispatch` function.
  - The **reducer function** takes the current state and an action, and returns the new state. This pattern is similar to Redux.

#### Example

```javascript
const [state, dispatch] = useReducer(reducer, initialState);

// In the component:
<button onClick={() => dispatch({type: 'increment'})}>+</button>
```

-----

## Q: What are Custom Hooks? (Revisiting)

A:

  - A custom hook is a JavaScript function whose name starts with **`use`** and that may call other hooks.
  - It is a mechanism for **reusing stateful logic** (like API calls, listeners, timers) across different functional components.
  - **Example:** `useOnlineStatus` extracts the logic for setting up and tearing down 'online'/'offline' event listeners.

#### Example: `useOnlineStatus`

```javascript
const useOnlineStatus = () => {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    window.addEventListener("online", () => setIsOnline(true));
    window.addEventListener("offline", () => setIsOnline(false));
    
    // Cleanup function
    return () => {
        window.removeEventListener("online", () => setIsOnline(true));
        window.removeEventListener("offline", () => setIsOnline(false));
    }
  }, []);

  return isOnline;
};
```

-----

## Q: How to handle errors in React? (Error Boundaries)

A:

  - Errors inside React components can crash the whole application.
  - **Error Boundaries** are special React components that are used to catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree crashing.
  - They are implemented using **class components** and the lifecycle methods `static getDerivedStateFromError()` and `componentDidCatch()`.
  - Error Boundaries only catch errors in the **render phase, lifecycle methods, and constructors** of the tree below them, not inside event handlers or asynchronous code.

#### Example (Standard JavaScript Error Handling)

For errors in asynchronous code or event handlers, use standard `try...catch...finally`.

```javascript
// Function to fetch data
const fetchData = async () => {
    try {
        const data = await fetch('url');
        // Handle response
    } catch (error) {
        console.error("Fetch failed:", error);
    } finally {
        // Runs regardless of success or failure
    }
};
```