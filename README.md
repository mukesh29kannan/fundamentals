## JavaScript (ES6+)

### Fundamentals

- #### Scopes (var, let, const)
  - __var__
    - **Scope:** Function-scoped
    - **Hoisting:** Hoisted to the top of the function, initialized with `undefined`
    - **Re-declaration:** Allowed in the same scope
  - __let__
    - **Scope:** Block-scoped
    - **Hoisting:** Hoisted but not initialized (in temporal dead zone until declared)
    - **Re-declaration:** Not allowed in the same scope
  - __const__
    - **Scope:** Block-scoped
    - **Hoisting:** Hoisted but not initialized (in temporal dead zone until declared)
    - **Re-declaration:** Not allowed
    - **Re-assignment:** Not allowed
  - __Best Practice__
    - Use `const` by default
    - Use `let` only when you need to reassign
    - Avoid `var` unless you have a very specific reason

- #### Closures & Lexical Scope
  - __Lexical Scope__  
    The lexical scope is also known as static scope, where the scope is determined at **compile time**, based on where variables and blocks are written in the code.

    ```js
    function outer() {
      let outerVar = 'I am outer';

      function inner() {
        console.log(outerVar); // ✅ Has access
      }

      inner();
    }
    outer();
    ```
  - __Closures__  
    A closure is formed when an inner function remembers the variables of its outer function even after the outer function has returned.

    ```js
    function outer() {
      let count = 0;
      return function() {
        count++;
        console.log(count);
      };
    }

    const counter = outer();
    counter(); // Outputs: 1
    counter(); // Outputs: 2
- #### Hoisting
  Hoisting is JavaScript’s default behavior of moving variable and function declarations to the top of their scope during the compilation phase before the code executes. This means you can use variables and functions before you actually declare them in the code.
  - **Function declarations** are fully hoisted, so you can call a function before its declaration.
  - **Variables declared with var** are hoisted, but only the declaration part—not the initialization. So their value is undefined until the assignment line runs.
  - **Variables declared with let and const** are hoisted too, but they are not initialized. Accessing them before declaration causes a ReferenceError due to the Temporal Dead Zone (TDZ).

- ### `this` keyword
  the `this` keyword refers to the context in which a function is executed i.e it points to the object that is calling the function.
  The value of `this` depends on how a function is called, not where it's defined
  ```js 
  console.log(this); // In browsers, this refers to the window object

  const user = {
    name: "Alice",
    greet() {
      console.log("Hi, I'm " + this.name);
    }
  };
  user.greet(); // "Hi, I'm Alice"

  const person = {
    name: "Bob",
    greet: () => {
      console.log("Hi, I'm " + this.name);
    }
  };

  person.greet(); // "Hi, I'm undefined"

- ### Prototypes & Inheritance
  JavaScript uses prototype-based inheritance, not class-based like Java though class syntax exists.
  Every JavaScript object has an internal link to another object called its prototype. This prototype object can also have a prototype,
  ```js 
  const animal = {
      eats: true
  };

  const dog = Object.create(animal);
  dog.barks = true;

  console.log(dog.eats); // true (inherited)

- ### Event Loop, Microtask Queue
  JavaScript is single-threaded, which means it can execute one task at a time. To handle asynchronous operations efficiently, it uses an Event Loop.
  - #### Runtime Components
    - **Call Stack** - Where functions get pushed and popped.
    - **Web APIs** - Browser-provided APIs like setTimeout, fetch, etc.
    - **Macrotask Queue** - For setTimeout, setInterval, setImmediate, etc.
    - **Microtask Queue** - For promises (.then, .catch, .finally).
  - ## Event Loop Working
    Executes code from the Call Stack. When it encounters an async task (e.g., setTimeout), it delegates it to Web APIs. Once async tasks complete, their callbacks go to either Microtask or Macrotask accordingly.Event Loop checks if the Call Stack is empty If yes, then it runs all microtasks (clears microtask queue) then runs one macrotask. Repeats the loop.
    ```js
    console.log('Start');

    setTimeout(() => {
      console.log('Macrotask: setTimeout');
    }, 0);

    Promise.resolve().then(() => {
      console.log('Microtask: Promise');
    });

    console.log('End');
    //Start
    //End
    //Microtask: Promise
    //Macrotask: setTimeout


- ### Promises, async/await

  - #### Promises  
    A **Promise** is an object that represents the eventual **completion** (or **failure**) of an asynchronous operation and its resulting value.  
    There are **3 states** of a Promise:
    - **Pending** – Initial state, neither fulfilled nor rejected
    - **Fulfilled** – The operation completed successfully
    - **Rejected** – The operation failed

    ```js
    const myPromise = new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("Success!");
      }, 1000);
    });

    myPromise
      .then(result => console.log(result))   // "Success!"
      .catch(error => console.error(error)) // handles rejection
      .finally(() => console.log("Done"));  // always runs
    ```

  - #### async/await  
    `async/await` is a syntactic sugar over Promises to write asynchronous code that looks synchronous.

    - `async` functions always return a Promise.
    - `await` pauses the execution inside an `async` function until the Promise is resolved or rejected.

    ```js
    async function fetchData() {
      try {
        const response = await fetch("https://api.example.com/data");
        const data = await response.json();
        console.log(data);
      } catch (error) {
        console.error("Error:", error);
      } finally {
        console.log("Fetch attempt finished");
      }
    }

    fetchData();
    ```

    > 🔹 Note: `await` can only be used inside `async` functions.

   
- ### Error Handling (try/catch, throw)

  JavaScript provides structured mechanisms to **handle runtime errors** using:

  - `try` block: Wraps code that may throw an error
  - `catch` block: Handles the error if one occurs
  - `throw` statement: Manually throws an error
  - `finally` block *(optional)*: Always executes, regardless of whether an error occurred

  #### Example – Basic try/catch

  ```js
  try {
    const result = riskyOperation();
    console.log(result);
  } catch (error) {
    console.error("Something went wrong:", error.message);
  } finally {
    console.log("Cleanup or final steps");
  }

- ### Debounce vs Throttle

  Both **debounce** and **throttle** are techniques to limit the rate at which a function is executed, especially in response to events like `scroll`, `resize`, or `keypress`.

  ---

  - #### Debounce  
    **Debounce** ensures a function is called **only after a specified time has passed** since the last time the event was triggered.  
    Useful for:
    - Search input suggestions
    - Auto-saving form data

    > ✅ Triggers **only once**, after the user stops triggering the event.

    #### Example:
    ```js
    function debounce(fn, delay) {
      let timeout;
      return function (...args) {
        clearTimeout(timeout);
        timeout = setTimeout(() => fn.apply(this, args), delay);
      };
    }

    const log = () => console.log("Debounced!");
    window.addEventListener("resize", debounce(log, 300));
    ```

  ---

  - #### Throttle  
    **Throttle** ensures a function is called **at most once every specified interval**, regardless of how many times the event fires.

    Useful for:
    - Scroll position tracking
    - Analytics event logging

    > ✅ Triggers the function **at regular intervals**, even if events keep firing.

    #### Example:
    ```js
    function throttle(fn, limit) {
      let lastCall = 0;
      return function (...args) {
        const now = Date.now();
        if (now - lastCall >= limit) {
          lastCall = now;
          fn.apply(this, args);
        }
      };
    }

    const log = () => console.log("Throttled!");
    window.addEventListener("scroll", throttle(log, 500));
    ```
  ---


###  Advanced Topics
- Call, Apply, Bind
- Currying & Partial Application
- Memoization
- IIFE (Immediately Invoked Function Expression)
- Module patterns (CommonJS, ESM)
- Symbols, Iterators, Generators
- Set, Map, WeakSet, WeakMap
- Proxy & Reflect
- Optional chaining (`?.`), Nullish Coalescing (`??`)
- Destructuring, Spread & Rest
- Array methods: `map`, `filter`, `reduce`, `some`, `every`, `find`

---

##  React

###  Basics
- JSX & rendering
- Components (Functional vs Class)
- Props, State
- Lifting State Up
- Conditional Rendering
- Lists & Keys

###  Hooks
- `useState`, `useEffect`
- `useContext`
- `useRef`, `useMemo`, `useCallback`
- `useReducer`
- Custom Hooks

###  Advanced
- Reconciliation & Virtual DOM
- React.memo, PureComponent
- Error Boundaries
- Portals
- Suspense & Lazy loading
- Controlled vs Uncontrolled components
- Form handling
- Context API vs Redux

---

##  TypeScript

###  Basics
- `type` vs `interface`
- Primitive & complex types
- Optional and readonly properties
- Union (`|`) and Intersection (`&`)
- Type inference
- Type aliases

###  Advanced
- Generics
- Enums
- Utility types: `Partial`, `Pick`, `Omit`, `Record`, `ReturnType`, `Readonly`
- `keyof`, `typeof`, `infer`, `as`
- Type narrowing & type guards
- Declaration merging
- Module augmentation

###  React + TypeScript
- Typing `useState`, `useRef`, `useReducer`
- Props & event types
- FC vs normal function components
- `React.ChangeEvent`, `MouseEvent`, etc.

---

##  Redux

###  Core Concepts
- Store, Reducers, Actions
- Pure functions
- `combineReducers`
- Middleware
- Redux DevTools

###  Redux Toolkit (RTK)
- `configureStore`, `createSlice`, `createAsyncThunk`
- RTK Query basics
- Immer (immutable state update)

###  Asynchronous Flows
- Redux Thunk
  - `dispatch`, `getState`
- Redux Saga
  - `call`, `put`, `takeLatest`, `takeEvery`, `fork`
  - Generator functions

---

## 🛠️ Common Patterns

###  Project Structure
src/
components/
pages/
hooks/
services/
store/
slices/
sagas/
index.ts
utils/
types/


###  Common Interview Patterns
- HOC (Higher Order Components)
- Render props
- Compound components
- Debouncing input in search
- Infinite scrolling
- Global error handler
- Lazy loading routes/components

---

##  Sample Questions

### JavaScript
- What is the difference between `==` and `===`?
- How does the event loop work?
- Explain closures with an example.
- What is a Promise? How is it different from async/await?

### React
- Difference between `useEffect` and `useLayoutEffect`?
- What is React Fiber?
- How does reconciliation work?
- Explain the Context API.

### TypeScript
- What are generics? Why are they useful?
- Difference between `interface` and `type`?
- How to type React props and events?

### Redux
- How does Redux work under the hood?
- Difference between Redux Thunk and Saga?
- What is an action creator?
- How would you persist Redux state?

---
