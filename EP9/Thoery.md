## Namaste React Course by Akshay Saini

# Chapter 09 - Optimizing our app

## Q: What is the Single Responsibility Principle?

A: The Single Responsibility Principle states that:

  - If we have a function, a class, or a single entity in our app, it should have a **single responsibility**.
  - For example, a `<Header>` component in our app should have only one responsibility: to display the header on the application.
  - If we have a component which is doing multiple things, then we should divide that component into multiple components where each one of them has a single responsibility.
  - Breaking down the code into small modules -\> **Modularity**.

## Q: What is a custom hook?

A:

  - A hook is nothing but a **utility function**.
  - Hooks are reusable functions.
  - When you have component logic that needs to be used by multiple components, we can extract that logic to a custom hook.
  - A custom hook in React is a JavaScript function that allows you to extract and reuse logic involving stateful behavior and side effects from function components.
  - Custom hooks enable you to encapsulate common logic in a way that can be shared across multiple components, promoting code reuse and better organization.

## Q: Why use custom hooks?

A:

1.  **Code Reusability:** Custom hooks allow you to reuse stateful logic across different components without duplicating code.
2.  **Cleaner Components:** By extracting complex logic into custom hooks, you can keep your components smaller and more focused on rendering.
3.  **Separation of Concerns:** Custom hooks help separate the logic from the UI, making your code easier to manage and understand.

## Q: Why should we name our hook as "useOnlineStatus"?

A:

  - It is a naming convention for custom hooks which is followed by most of the companies.
  - A lot of companies use a linter which throws an error if the custom hooks are not named like this.
  - It is a good practice to use the word **use** while naming the custom hook.
  - If someone else sees the code, they will get to know that this is not a normal function but a React hook.

## Q: Example: Creating a `useOnlineStatus` hook

A: The below hook checks if the user is online or offline using the window object. The callback function sets the value of `isOnline` and the hook returns that value. This value can be extracted into another component by importing the hook.

```javascript
import React, { useEffect, useState } from "react";

const useOnlineStatus = () => {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    window.addEventListener("offline", () => {
      setIsOnline(false);
    });

    window.addEventListener("online", () => {
      setIsOnline(true);
    });
  }, []);

  return isOnline;
};

export default useOnlineStatus;
```

## Q: Example: Refactoring Fetch Logic into a Custom Hook

A: Ideally, a component should only have one responsibility: to display data. If a component is fetching data *and* displaying it, it is doing two things. We can extract the fetching logic into a custom hook (e.g., `useDemo`).

#### 1\. The Custom Hook (`useDemo`)

This hook handles the API call and error handling.

```javascript
import React, { useEffect, useState } from "react";

const useDemo = () => {
  const [itemDetails, setItemDetails] = useState(null);

  // Fetch the data from API
  const fetchData = async (url) => {
    try {
      const data = await fetch(url);
      if (!data.ok) {
        throw new Error('Network response was not ok');
      }
      const response = await data.json();
      setItemDetails(response);
    } catch (error) {
      throw new Error(error);
    }
  };

  useEffect(() => {
    fetchData('https://example.com');
  }, []);

  return itemDetails;
}

export default useDemo;
```

#### 2\. The Component (`Home`)

Now, the Home component is clean and only responsible for displaying data.

```javascript
import React from 'react';
import useDemo from "./useDemo";

const Home = () => {
    
  const { itemDetails } = useDemo();

  return (
    <div>
      <div>
        {/* Display the data */}
        <h1>{itemDetails.name}</h1>
      </div>
    </div>
  );
};

export default Home;
```

## Q: What is Chunking / Code Splitting?

A: When we are building a large-scaled application, it is important to break it down into different components (Bundles). Having a single bundle will make our app slower since a single bundle will contain all the code of the application which takes a lot of time to load.
The solution for this is to split our app into smaller chunks (bundles). This process is known by several terms:

1.  Chunking
2.  Code Splitting
3.  Dynamic Bundling
4.  Lazy Loading
5.  On demand loading

**Example:** If developing an e-commerce app, the "Cart" component can be a separate bundle. It will not be loaded initially; it will be loaded only when the user visits the cart page. This means the app will have 2 bundles: the main bundle and the cart bundle (loaded **on demand**).

## Q: When and why do we need `lazy()`?

A:

  - In simpler terms, lazy loading is a design pattern.
  - It allows you to load parts of your application **on demand** to reduce the initial load time.
  - For example, you can initially load components related to user login/registration, and load the rest based on navigation.
  - You might not feel much difference in small-scaled applications, but it significantly impacts large-scaled applications.
  - Ultimately it improves both the user experience and application performance.

## Q: How to implement Lazy Loading with `Suspense`?

A: When we use `lazy()` on a component which fetches an API response, React can give us an error i.e. **A component suspended while responding to synchronous input**. To avoid or handle this error, React offers a component i.e. **Suspense**.

```javascript
// Without lazy loading
import About from './About';

// With React.lazy()
const About = React.lazy(() => import('./About'));
```

*Note: You must wrap the lazy-loaded component in `<Suspense fallback={<Loading />}>...</Suspense>` to handle the loading state.*

## Q: What are the Advantages and Disadvantages of Lazy Loading?

A:
**Advantages:**

1.  Reduces the initial load time by reducing the bundle size.
2.  Reduces browser workload.
3.  Improves application performance in low-bandwidth situations.
4.  Improves user experience at initial loading.
5.  Optimizes resource usage.

**Disadvantages:**

1.  Not suitable for small scale applications.
2.  Placeholder (fallback) can slow down quick scrolling.
3.  Requires additional communication with the server to fetch resources.
4.  Can affect SEO and ranking.