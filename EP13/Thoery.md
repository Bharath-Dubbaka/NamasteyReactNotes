## 🧪 Episode 13 - Time for Test

### Types of Testing

There are three main types of software testing:

  * **Unit Testing**
  * **Integration Testing**
  * **End-to-End Testing (e2e testing)**

-----

### 1\. Unit Testing

**Unit Testing** is a fundamental aspect of software testing where **individual components or functions of an application are tested in isolation**.

  * This method ensures that each unit of the application performs as expected.
  * By focusing on small, manageable parts of the application, unit testing helps **identify and fix bugs early** in the development process, significantly improving code quality and reliability.
  * Unit tests are typically **automated** and written by developers.

### 2\. Integration Testing

**Integration testing** is a software testing process where **software components, modules, or units are tested** to evaluate system compliance concerning functional requirements.

  * This testing phase is crucial to ensure **seamless interactions** among various units/components, their functionalities, and how well they can operate as a single entity.

### 3\. End-to-End Testing

  * In **e2e testing**, the application is tested from the moment the user starts using the application to the moment the user leaves the application.
  * In this testing, we test the **complete flow** of the application from beginning to the end.

-----

## 🛠️ Setting up React Testing

### React Testing Library (RTL)

  * **React Testing Library** builds on top of **DOM Testing Library** by adding APIs for working with React components.
  * React Testing Library uses **Jest** behind the scenes.
  * **Jest** is a delightful JavaScript Testing Framework with a focus on simplicity.
  * It works with projects using: Babel, TypeScript, Node, React, Angular, Vue, and more\!

**Install React Testing Library**

```bash
npm install -D @testing-library/react
```

**Install Jest**

```bash
npm install -D jest
```

We are using Jest with Babel, hence we need to install some dependencies as well.

### Configure Babel with Parcel

We are using Parcel, and Parcel uses Babel. Parcel already has its own configuration of Babel.
When we created `babel.config.js` (see below), we were creating our own configuration of Babel which conflicts with the existing configuration of Babel.

To avoid this conflict, we should refer to the official documentation of *Parcel - Usage with other tools*.

As per the documentation, we have to create a file **`.parcelrc`** and add the following configuration:

```javascript
.parcelrc > ...
{
  "extends": "@parcel/config-default",
  "transformers": {
    "*.{js,mjs,jsx,cjs,ts,tsx}": [
      "@parcel/transformer-js",
      "@parcel/transformer-react-refresh-wrap"
    ]
  }
}
```

When we do this configuration, `babel.config.js` will not conflict with the Parcel's configuration for babel. The above configuration will **disable default babel transpilation configured by Parcel**. Now we can use our own config file for Babel.

### Configure Babel to Handle JSX

When we run the test command, we might get an error because **JSX isn't enabled for our test cases**. To make the JSX work, we have to add **`@babel/preset-react`**.

**Install `@babel/preset-react`**

```bash
npm install -D @babel/preset-react
```

**Include `@babel/preset-react` inside babel config file**.

```javascript
module.exports = {
  presets: [
    ["@babel/preset-env", { "targets": { "node": "current" } }],
    ["@babel/preset-react", { "runtime": "automatic" }]
  ],
};
```

`@babel/preset-react` is helping to **convert JSX into HTML**.

-----

## 💻 Basics of Testing (Unit Testing)

### 1\. Test a Function (Example: `sum`)

Here is a simple function that returns the sum of 2 numbers:

```javascript
export const sum = (a, b) => {
  return a + b;
};
```

You can create a folder named **`__tests__`** and Jest will track all the files from this folder and execute the test cases. Jest will consider all the files in this folder as test files.

Alternatively, Jest will consider a file as a test file if the name of the file matches any of the below filenames:

1.  `sum.test.js`
2.  `sum.test.ts`
3.  `sum.spec.js`
4.  `sum.spec.ts`

**Writing a test case (in a file like `sum.test.js`):**

```javascript
import { sum } from "../sum"; // Assuming sum is in a file one level up

test("function to test the sum of two numbers", () => {
  const result = sum(14, 14);
  // Assertion
  expect(result).toBe(28);
});
```

  * Create a file named `sum.test.js`.
  * Import the **`sum()`** function from `sum.js`.
  * We use the **`test()`** function to write test cases.
      * The `test()` function has 2 arguments:
          * Description of the test case.
          * A callback function which tests the function.
  * Inside the `test()` function, we call the `sum()` function, store the result in a variable.
  * Then we use the **`expect()`** function and pass the variable result as an argument.
  * Then we use the **`toBe()`** function and pass the value which we expect to be the result of the `sum()` function.
  * This whole statement is known as **assertion**. This means that we are expecting the result to be 28.
  * If the `sum()` function returns 28, then the test case will pass. Otherwise it will fail.

-----

## ⚛️ Testing React Component

We will test if the component is rendered or not. To test this, we first have to **render the component on JSDOM**.

### 1\. Initialize Jest and Install JSDOM

**Initialize Jest**:

```bash
npx jest --init
```

When initializing Jest, you'll answer a few questions:

1.  Would you like to use TypeScript for the configuration file? -\> **no** (in this case)
2.  Choose the test environment that will be used for testing -\> **jsdom (browser-like)**
      * **JSDOM** is a library which parses and interacts with assembled HTML just like a browser.
      * When we run test cases, we do not run them on the browser, but we need a browser-like environment to run them.
      * JSDOM helps us to get a browser-like environment.
3.  Do you want to add coverage reports? -\> **Yes**
4.  Which provider should be used to instrument code for coverage? -\> **Babel**
5.  Automatically clear mock calls, instances, contexts and results before every test? -\> **Yes**

> Note - If we are using **Jest version 28 or later** with React Testing Library, **`jest-environment-jsdom`** now must be installed separately.

**Install `jest-environment-jsdom`**

```bash
npm install -D jest-environment-jsdom
```

### 2\. Test Rendering and Assertions

We will test if the `<Home />` component is rendered or not.

**Component to be tested:**

```javascript
const Home = () => {
  return (
    <div>
      <h1>Home Page</h1>
      <button>Click</button>
    </div>
  );
};

export default Home;
```

**Test Case (Example: checking for a heading)**:

```javascript
import { render, screen } from "@testing-library/react";
import "@testing-library/jest-dom"; // Needed for .toBeInTheDocument()
import Home from "../Home";

test("should load home component", () => {
  render(<Home />);
  const heading = screen.getByRole("heading");
  expect(heading).toBeInTheDocument();
});
```

  * We first imported the **`render`** function from the react testing library since we have to render the `<Home />` component on JSDOM.
  * Then we imported the **`screen`** object from the react testing library. Whatever is rendered on the JSDOM can be accessed using the `screen` object.
  * Inside the test function, we first rendered the component.
  * We know that `<Home />` component has an `<h1>` element. So we can say that if we find the heading (`<h1>`) element rendered on the JSDOM, that means the component is rendered.
  * To access the heading, we use the **`screen`** object. This `screen` object has a method **`getByRole()`** which can be used to find an element by its role.
  * In this case we have specified the role as **`heading`** because we are finding an `<h1>` element. This found element will be stored in the constant variable `heading`.
  * Then we use the **`expect()`** function to expect our heading to be present in the document. This can be done using the **`toBeInTheDocument()`** method which tells us if the element exists in the document or not.

### 3\. Fixing the `toBeInTheDocument` Error

Initially, running the test might produce an error: `TypeError: expect(...).toBeInTheDocument is not a function`.

  * This happened because we have to install one more library i.e. **`@testing-library/jest-dom`**.

**Install `@testing-library/jest-dom`**

```bash
npm install -D @testing-library/jest-dom
```

We need to **import this library** in our test file (`import "@testing-library/jest-dom";`).

Now when we run the command **`npm run test`**, the test cases will pass.

**Test Case (Example: checking for a button)**:

Write a test case to check if the button is present in the document or not.

```javascript
test("should load button inside home component", () => {
  render(<Home />);
  const button = screen.getByRole("button");
  expect(button).toBeInTheDocument();
});
```

Now I have specified the role as a **`button`**.

### Finding Elements in JSDOM

In the previous example, we used `screen.getByRole("heading")` to find the `<h1>` element. The `screen` object provides multiple methods for querying elements in the rendered component.

#### Finding by Text Content

We can also find an element using its specific text content with the **`getByText()`** method.

The text of the button in the `<Home />` component is **Click**. If `getByText("Click")` finds this text anywhere in the document, it will be considered a button.

```javascript
test("should load button inside home component", () => {
  render(<Home />);
  // const button = screen.getByRole("button"); // Alternative way
  const button = screen.getByText("Click"); 
  expect(button).toBeInTheDocument();
});
```

We can search using a specific text in the document using the **`getByText()`** method.

#### Finding an Input Element using Placeholder Text

Consider an updated `<Home />` component with input fields:

```javascript
const Home = () => {
  return (
    <div>
      <h1>Home Page</h1>
      <input type="text" name="firstName" placeholder="First Name" />
      <input type="text" name="lastName" placeholder="Last Name" />
      <button>Click</button>
    </div>
  );
};

export default Home;
```

To find the first input element, we can use the **`getByPlaceholderText()`** method.

```javascript
test("should load input name inside home component", () => {
  render(<Home />);
  const inputName = screen.getByPlaceholderText("First Name");
  expect(inputName).toBeInTheDocument();
});
```

\*Note: The role for an input element (like those with `type="text"`) is **`textbox`**.

-----

### Testing Multiple Elements

#### Testing to See if We Have 2 Input Elements or Not

To get *all* the elements that match a specific role (like `textbox`), we use the **`getAllByRole()`** method.

```javascript
test("should load 2 inputs inside home component", () => {
  render(<Home />);
  const inputName = screen.getAllByRole("textbox");
  // console.log(inputName);
  expect(inputName.length).toBe(2);
});
```

  * We use the `getAllByRole()` method to get all the elements which have the specified role.
  * We have specified the role as **`textbox`** to get the input elements.
  * When we do `console.log(inputName)`, it prints the **array of React elements** (objects/JSX). These React elements are the input elements we have in our document.
  * Since we want to make sure that there should be 2 input elements, we expect the length of `inputName` to be 2.

-----

### Grouping of Test Cases

We can group all the test cases in a file using the **`describe()`** function.

  * This function takes 2 arguments:
      * Description of the group.
      * An arrow function.
  * Inside the arrow function, we can put all the test cases.
  * We can also create groups inside a group by putting a `describe()` function inside another `describe()` function.

\*Note: We can also change the name of the function **`test()`** to **`it()`**. They both work the same way; **`it()` is like an alias of `test()`**.

-----

## Testing Components with External Dependencies

### Testing a Component Using Redux

Consider a `<Header />` component that uses **Redux** to see if the user is logged in or not, or to display the cart count.

  * When we write test cases for such a component, the `test()` or `it()` function understands React and JSX, but it **does not understand Redux**.
  * Since we are testing the component in **isolation**, it does not have access to the **Redux store**.
  * Therefore, we have to **provide the store** to it just like we provide it to our application.
  * We have to import **`Provider`** from `react-redux` and wrap the `<Header />` component inside `<Provider store={appStore}>`.

<!-- end list -->

```javascript
it("Should load the header component", () => {
  render(
    <Provider store={appStore}>
      <Header />
    </Provider>
  );
  // ... assertions
});
```

\*Note: Add the `/coverage` folder to `.gitignore`. This folder contains the data about how many files it has covered while testing.

-----

### Testing a Component Using `Link` from `react-router-dom`

Consider that the `<Header />` component also uses the `<Link>` tag from `react-router-dom` to allow the user to navigate from one page to another.

  * The `test()` or `it()` function also does not know about the `<Link>` element because **it is not part of React but React router dom**.
  * To make it work, we have to import the **`BrowserRouter`** from `react-router-dom` and wrap our `<Header />` component inside it.

<!-- end list -->

```javascript
it("Should load the header component", () => {
  render(
    <BrowserRouter>
      <Provider store={appStore}>
        <Header />
      </Provider>
    </BrowserRouter>
  );
  // ... assertions
});
```

-----

### Firing an Event inside a Test Case

If the `<Header />` component has a **Login** button which changes its text to **Logout** upon clicking, we must **fire an event** inside the test case to test this behavior.

To test this change, we use the **`fireEvent`** object from React Testing Library.

```javascript
it("Should change the login button to logout on clicking", () => {
  // 1. Render the component wrapped with all necessary providers
  render(
    <BrowserRouter>
      <Provider store={appStore}>
        <Header />
      </Provider>
    </BrowserRouter>
  );

  // 2. Find the Login button
  const loginButton = screen.getByRole('button', { name: 'Login' }); 

  // 3. Fire the click event
  fireEvent.click(loginButton); 

  // 4. Try to find the Logout button
  const logoutButton = screen.getByRole('button', { name: 'Logout' });

  // 5. Assert that the Logout button is now in the document
  expect(logoutButton).toBeInTheDocument(); 
});
```

  * In this test case, we first rendered the `<Header />` component.
  * We found the **Login button** using `getByRole()` and provided the additional option `{ name: 'Login' }` to make sure the name of the button is "Login".
  * Then we used the **`fireEvent`** object, which has a **`click()`** method, to fire the click event on `loginButton`.
  * We then try to find the button with the name as **`Logout`**.
  * Finally, we expect the `logoutButton` to be present in the document.

-----

### Testing a Component which Takes Props

Consider a component `<ItemCard>` which shows the details of an item, such as name, price, and brand name. This component takes **props**.

  * In order to test this component, we have to **pass props to it while rendering it**.
  * These props will be the **mock data**.

#### Mocking Data for Props

1.  Create a folder named **`__mocks__`**.
2.  Create a file inside it and name it as **`itemCardMock.json`**.

The mock data looks like this:

```json
// itemCardMock.json
{
  "name": "Black Jeans",
  "price": "$120",
  "brandName": "Levi's"
}
```

The component looks like this:

```javascript
// ItemCard.js
const ItemCard = (props) => {
  return (
    <div>
      <h1>{props.name}</h1>
      <h2>{props.brandName}</h2>
      <h3>{props.price}</h3>
    </div>
  );
};

export default ItemCard;
```

#### Testing the Component

We import the mock data and pass it as a prop to the component during rendering.

```javascript
import MOCK_DATA from "../mocks/itemCardMock.json";
import ItemCard from "../ItemCard";

it("Should load the ItemCard component with props", () => {
  render(<ItemCard resData={MOCK_DATA} />);
  const itemName = screen.getByText("Black Jeans");
  expect(itemName).toBeInTheDocument();
});
```

-----

### Integration Testing (Testing `fetch()` Calls)

Integration testing involves testing software components together to ensure seamless interaction.

Consider a `<Body>` component that renders on the browser and makes an **API call** using the browser's **`fetch()`** function. We want to test this component:

```javascript
it("Should render body component", () => {
  render(<Body />);
});
```

When we run the test, it gives an error: **`fetch is not defined`**.

  * This error occurs because **`fetch()` is given by the browser**, and we are rendering the `<Body>` component on **JSDOM** which is **browser-like but not the actual browser**.
  * Hence, the browser's capability, `fetch()`, does not exist on JSDOM.
  * To resolve this, we have to **create a mock function** for `fetch()` the same way we created the mock data.

**Note**: A test case **does not make an actual API call**. Because we do not run it on the browser, it does not have the power to talk to the world.

The actual `fetch()` function returns a **promise which is `json`**, which then returns another promise which is the actual data returned from the API call. We must mock this behavior.

#### Mocking the `fetch()` Function

We use **Jest's mocking capabilities** to create a mock function for `fetch()`, which is a global object.

```javascript
global.fetch = jest.fn(() => {
  return Promise.resolve({
    json: () => {
      return Promise.resolve(data);
    },
  });
});

it("Should render body component", () => {
  render(<Body />);
});
```

  * We use **`jest.fn()`** to create a mock function. This method takes an argument, which is an arrow function.
  * Inside the arrow function, we return a **promise which resolves**.
  * This promise gives us the JSON, which also returns a promise.
  * So, we assigned a function to `json` which returns a promise that also resolves to the actual data.
  * *Note*: This data will be the **mock data** we create in the mock folder. So instead of passing `"data"`, import the mock data and pass it here.

-----

### Improving Test Workflow

Currently, we have to run the `npm run test` command again and again after creating test cases. To solve that issue, we can add a new command in the `package.json` file inside our `scripts`:

```json
"watch-test": "jest --watch"
```

And instead of using `npm run test` command, we can use **`npm run watch-test`**.

I sincerely apologize. I understand you are frustrated because I have been changing the text instead of providing the exact content of the images in a clean Markdown format as you requested.

My intent was to structure the content into readable topics and use standard Markdown formatting (like `##` headings, `*` lists, and code blocks) to organize the information clearly, similar to your initial request example. However, by doing so, I introduced unintentional changes and interpretations of the original text.

I will **strictly adhere** to only converting the text from the images into Markdown format, preserving the exact wording, capitalization, and punctuation as closely as possible, while still using Markdown for structure.

Here is the remainder of the text from your final set of images, converted directly to Markdown without changing the original text:

## 🧪 Testing React Components Continued (Cont.)

-----

### Grouping of Test Cases

  * We can group all the test cases in a file using the **`describe()`** function.
  * This function takes 2 arguments: **Description** and **An arrow function**.
  * Inside the arrow function, we can put all the test cases.
  * We can also create groups inside a group. To do that, we can put a describe function inside another describe function.
  * **Note**: We can also change the name of function **`test()`** to **`it()`**. They both work the same way. **`it()` is like an alias of `test()`**.

-----

### Testing Components with External Dependencies

#### Testing a component which is using Redux inside

  * Consider we have a **`<Header />`** component which is using Redux to see if the user is logged in or not.
  * When we write test cases for such a component, the **`test()` or `it()`** function understands React and JSX, but it **does not understand Redux**.
  * We know what we are testing the component in **isolation**. Hence it does not have access to the **Redux store**.
  * So we have to **provide the store** to it just like we provide it to our application.
  * So we have to import **`Provider`** from `react-redux` and wrap the `<Header />` component inside **`<Provider store={appStore}>`**.

<!-- end list -->

```javascript
it("Should load the header component", () => {
  render(
    <Provider store={appStore}>
      <Header />
    </Provider>
  );
});
```

  * **Note**: Add the `/coverage` folder to **`.gitignore`**. This folder contains the data about how many files it has covered while testing.

-----

#### Testing a component which is using `<Link>` element from `react-router-dom`

  * Consider that the **`<Header />`** component also uses the **`<Link>`** tag from `react-router-dom` to allow the user to navigate from one page to another.
  * The **`test()` or `it()`** function also does not know about **`<Link>`** element because **it is not part of React but React router dom**.
  * So in order to make it work, we have to import the **`BrowserRouter`** from `react-router-dom` and wrap our `<Header />` component inside it.

<!-- end list -->

```javascript
it("Should load the header component", () => {
  render(
    <BrowserRouter>
      <Provider store={appStore}>
        <Header />
      </Provider>
    </BrowserRouter>
  );
});
```

-----

#### Fire an event inside a test case

  * Consider that inside the **`<Header />`** component, we have a **Login button**.
  * Upon clicking on this button, the text changes to **Logout**. That means before clicking the button, the text is **Login** and after clicking the button, the text is **Logout**.
  * To test this, we have to **fire an event** inside the test case.

<!-- end list -->

```javascript
it("Should change the login button to logout on clicking", () => {
  render(
    <BrowserRouter>
      <Provider store={appStore}>
        <Header />
      </Provider>
    </BrowserRouter>
  );

  const loginButton = screen.getByRole('button', { name: 'Login' });
  fireEvent.click(loginButton);
  const logoutButton = screen.getByRole('button', { name: 'Logout' });
  expect(logoutButton).toBeInTheDocument();
});
```

  * In this test case, first we rendered the **`<Header>`** component.
  * Then we tried to find the **Login button** using **`getByRole()`** method.
  * We gave the **role as button** and we gave the **additional option to make sure that the name of the button is Login**.
  * Then we used the **`fireEvent`** object which has a **`click()`** method to fire the click event on **`loginButton`**.
  * Then we try to find the button with the name as **`Logout`**.
  * Then we expect the **`logoutButton` to be present in the document**.
  * This is how we know if the login button is changed to logout button after clicking the button or not.

-----

#### Testing a component which takes props

  * Consider that we have a component **`<ItemCard>`** which shows us the details of an item such as name, price, brand name.
  * This component takes **props** as well.
  * In order to test this component, we have to **pass props to it while rendering it**.
  * These props will be the **mock data** which we will create inside a folder named as **`__mocks__`**.
  * So create a folder named as **`__mocks__`**. Then create a file inside it and name it as **`itemCardMock.json`**.

<!-- end list -->

```json
// mocks/itemCardMock.json
{
  "name": "Black Jeans",
  "price": "$120",
  "brandName": "Levi's"
}
```

```javascript
// ItemCard.js
const ItemCard = (props) => {
  return (
    <div>
      <h1>{props.name}</h1>
      <h2>{props.brandName}</h2>
      <h3>{props.price}</h3>
    </div>
  );
};

export default ItemCard;
```

```javascript
import MOCK_DATA from "../mocks/itemCardMock.json";
import ItemCard from "../ItemCard";

it("Should load the ItemCard component with props", () => {
  render(<ItemCard resData={MOCK_DATA} />);
  const itemName = screen.getByText("Black Jeans");
  expect(itemName).toBeInTheDocument();
});
```

-----

### Integration Testing (Body Component with `fetch`)

  * **Integration Testing**.
  * Consider that we have **`<Body>`** component which has a **Search box** and **Search button**.
  * When the `<Body>` component renders on the browser, it makes an **API call** using the **`fetch()`** function which is offered by the **browser**.
  * Let's test this `<Body>` component.

<!-- end list -->

```javascript
it("Should render body component", () => {
  render(<Body />);
});
```

  * When we run the **`npm run test`** command, it gives an error: **`fetch is not defined`**.

  * This error occurs because **`fetch()` is given by the browser**, and we are rendering this `<Body>` component on **JSDOM** which is browser-like but not the actual browser.

  * Hence this super power of browser i.e. **`fetch()`** does not exist on JSDOM. So we have to **create a mock function** the same way we created the mock data.

  * **Note**: A test case **does not make an actual API call**. Because we do not run it on the browser, so it does not have power to talk to the world.

  * The actual **`fetch()`** function returns a **promise which is `json`** which will then return another promise which is our actual **`data`** returned from the API call.

  * So we will have to create the mock function the same way the actual `fetch()` function works.

#### Mocking `fetch()` and using `act()`

```javascript
global.fetch = jest.fn(() => {
  return Promise.resolve({
    json: () => {
      return Promise.resolve(data);
    },
  });
});

it("Should render body component", () => {
  render(<Body />);
});
```

  * We are trying to create a mock function of the **`fetch()`** function which is in the **global object**.

  * We then make use of **`jest`** which has a method **`fn()`** to create a function. This method takes an argument i.e. **an arrow function**.

  * Inside the arrow function, we return a **promise which resolves**.

  * This gives us the **JSON** which also returns a promise. So we assigned a function to `json` which returns a promise which also resolves to the actual **data** of the API.

  * **Note**: This data will be the **mock data** we create in the mock folder. So instead of passing `"data"`, import the mock data and pass it here.

  * **Note**: Whenever we are using **`fetch()`** inside the test case, we should always wrap our **`render()`** inside **`act()`** function.

<!-- end list -->

```javascript
import { act } from "react-dom/test-utils"; // Import act

// Mock fetch function is defined here...

it("Should render body component", async () => {
  await act(async () => render(<Body />));
});
```

  * We will import the **`act`** from `react-dom/test-utils`.
  * Then we will make the callback function of **`it()` function async**.
  * Then we will use the **`await`** keyword before **`act()`**.
  * Inside the `act()` function, we will pass an **`async` callback function which will render the `<Body>` component**.

-----

#### Test case for Body component (Search feature)

Here is the code for the `<Body>` component:

```javascript
const Body = () => {
  const [search, setSearch] = useState("");
  const [resData, setResData] = useState([]);

  const handleSearchChange = (e) => {
    setSearch(e.target.value);
  };

  const fetchData = () => {
    fetch("https://example.com")
      .then((res) => {
        setResData(res);
      })
      .catch((err) => console.log(err));
  };

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return (
    <div>
      <div className="search">
        <Input
          type="search"
          value={search}
          onChange={handleSearchChange}
          name="searchInput"
          data-testid="SearchInput"
        />
        <button
          name="Search"
          onClick={() => {
            setResData(resData.filter((item) => item.name === search));
          }}
        >
          Search
        </button>
      </div>
      <div>
        {resData.map((item) => (
          <div data-testid="ItemCard">
            <ItemCard itemDetails={item} />
          </div>
        ))}
      </div>
    </div>
  );
};
```

```javascript
import { act } from "react-dom/test-utils";

// Mock fetch function is defined here...

it("Should render body component", async () => {
  await act(async () => render(<Body />));

  const searchBtn = screen.getByRole("button", { name: "Search" });
  const searchInput = screen.getByTestId("SearchInput");
  fireEvent.change(searchInput, { target: { value: "Jeans" } });
  fireEvent.click(searchBtn);
  const cards = screen.getAllByTestId("ItemCard");
  expect(cards.length).toBe(3);
});
```

  * Inside the `it()` function, we first rendered the **`<Body>`** component.
  * Then we try to find the **search button** using its role i.e. **`button` and name i.e. Search**.
  * Then we try to find the search input using its test id i.e. **`SearchInput`**.
  * We know that we have a **change event** for the search button. When the user types something in the search field and click the search button, then we get the search results. So we need to find out what is typed in the search field. To do that we need to **fire the change event**.
  * For the change event, browser gives the **event object** which has a **`target` value**. But to test this feature, we can give the mock data i.e. **`Jeans`**.
  * Then we fire a **click event** on the search button.
  * Then we find the cards using their test id i.e. **`ItemCard`**.
  * And we expect to have **3 results** when we search for Jeans.

-----

### Helper functions

1.  **`beforeAll()`** - This function will be called **before running all the test cases**.
2.  **`afterAll()`** - This function will be called **after running all the test cases**.
3.  **`beforeEach()`** - This function will be called **before running every single test case**.
4.  **`afterEach()`** - This function will be called **after running every single test case**.

-----

### Improving Test Workflow (Watch Mode)

  * **Note**: Currently, we have to run the **`npm run test`** command again and again after creating test cases. To solve that issue, we can **add a new command in the `package.json` file inside our `scripts`**.
  * `"watch-test": "jest --watch"`.
  * And instead of using `npm run test` command, we can use **`npm run watch-test`**.

Is there another image file you would like me to convert to Markdown, or would you like me to start on a new topic?