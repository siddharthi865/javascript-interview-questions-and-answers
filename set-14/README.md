# Set 14

| S.No. | Question                                                                                                                                                          |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [Difference between callback hell and promises](#question-1-difference-between-callback-hell-and-promises)                                                        |
| 2.    | [What is Promise chaining?](#question-2-what-is-promise-chaining-implied-from-the-content-about-chaining-promises)                                                |
| 3.    | [Difference between `Promise.all`, `Promise.race`, and `Promise.allSettled`](#question-3-difference-between-promiseall-promiserace-and-promiseallsettled)         |
| 4.    | [How to handle errors in `async/await`](#question-4-how-to-handle-errors-in-asyncawait)                                                                           |
| 5.    | [Difference between event delegation and direct event listener](#question-5-difference-between-event-delegation-and-direct-event-listener)                        |
| 6.    | [How to stop event propagation](#question-6-how-to-stop-event-propagation)                                                                                        |
| 7.    | [How to use `JSON.stringify` and `JSON.parse` with reviver/replacer functions](#question-7-how-to-use-jsonstringify-and-jsonparse-with-reviverreplacer-functions) |
| 8.    | [How to detect if a browser supports a certain feature (feature detection)](#question-8-how-to-detect-if-a-browser-supports-a-certain-feature-feature-detection)  |
| 9.    | [Difference between `document.readyState` and `DOMContentLoaded`](#question-9-difference-between-documentreadystate-and-domcontentloaded)                         |
| 10.   | [Explain JavaScript's event loop with call stack and task queue](#question-10-explain-javascripts-event-loop-with-call-stack-and-task-queue)                      |
| 11.   | [Difference between microtasks and macrotasks](#question-11-difference-between-microtasks-and-macrotasks)                                                         |
| 12.   | [How to identify and fix memory leaks](#question-12-how-to-identify-and-fix-memory-leaks)                                                                         |
| 13.   | [Difference between `localStorage`, `sessionStorage`, and cookies](#question-13-difference-between-localstorage-sessionstorage-and-cookies)                       |
| 14.   | [How to optimize rendering performance in JS-heavy apps](#question-14-how-to-optimize-rendering-performance-in-js-heavy-apps)                                     |
| 15.   | [How to implement a custom iterator in JavaScript](#question-15-how-to-implement-a-custom-iterator-in-javascript)                                                 |
| 16.   | [Difference between iterable and iterator](#question-16-difference-between-iterable-and-iterator)                                                                 |
| 17.   | [Difference between generators and async generators](#question-17-difference-between-generators-and-async-generators)                                             |
| 18.   | [How to implement a custom Promise](#question-18-how-to-implement-a-custom-promise)                                                                               |
| 19.   | [Difference between ES6 modules and CommonJS](#question-19-difference-between-es6-modules-and-commonjs)                                                           |
| 20.   | [How to dynamically import a module](#question-20-how-to-dynamically-import-a-module)                                                                             |

## Question 1. Difference between callback hell and promises

**Callback hell** is a situation where multiple asynchronous operations are nested inside each other using callbacks, making code hard to read, maintain, and debug.

**Promises** are a cleaner way to handle asynchronous operations. They avoid deep nesting by using `.then()`, `.catch()`, and `.finally()` chaining, resulting in more readable and manageable code.

### 1. What is Callback Hell?

Callback hell happens when asynchronous callbacks are deeply nested.

It is common in older JavaScript code that relies heavily on callback functions.

#### Example of Callback Hell

```js
getUser(function (user) {
  getOrders(user.id, function (orders) {
    getPayment(orders[0], function (payment) {
      updateUI(payment, function () {
        console.log("Done");
      });
    });
  });
});
```

This pyramid-shaped structure is often called:

- Pyramid of Doom
- Nested callbacks
- Callback hell

### 2. Problems with Callback Hell

#### a) Poor Readability

Deep nesting makes code difficult to understand.

```js
a(function () {
  b(function () {
    c(function () {
      d(function () {
        // hard to follow
      });
    });
  });
});
```

#### b) Difficult Error Handling

Each callback may require separate error handling.

```js
getData(function (err, data) {
  if (err) {
    console.log(err);
    return;
  }

  processData(data, function (err, result) {
    if (err) {
      console.log(err);
      return;
    }
  });
});
```

Error propagation becomes messy.

#### c) Hard to Maintain

Adding new logic increases nesting and complexity.

#### d) Inversion of Control

You pass control to another function and trust it to execute your callback correctly.

### 3. What are Promises?

A Promise is an object representing the eventual completion or failure of an asynchronous operation.

A Promise has 3 states:

1. Pending
2. Fulfilled
3. Rejected

### 4. Promise Version of the Same Example

```js
getUser()
  .then((user) => getOrders(user.id))
  .then((orders) => getPayment(orders[0]))
  .then((payment) => updateUI(payment))
  .then(() => console.log("Done"))
  .catch((err) => console.log(err));
```

This is:

- flatter
- cleaner
- easier to debug
- easier to maintain

### 5. Key Differences

| Feature         | Callback Hell        | Promises                              |
| --------------- | -------------------- | ------------------------------------- |
| Structure       | Deep nesting         | Flat chaining                         |
| Readability     | Poor                 | Better                                |
| Error handling  | Manual at each level | Centralized with `.catch()`           |
| Maintainability | Difficult            | Easier                                |
| Control flow    | Hard to manage       | Cleaner                               |
| Composition     | Difficult            | Easy with `Promise.all`, `race`, etc. |
| Debugging       | Hard                 | Easier                                |

### 6. Error Handling Comparison

#### Callback Style

```js
doTask(function (err, result) {
  if (err) {
    console.log(err);
    return;
  }

  nextTask(result, function (err, data) {
    if (err) {
      console.log(err);
    }
  });
});
```

#### Promise Style

```js
doTask()
  .then((result) => nextTask(result))
  .then((data) => console.log(data))
  .catch((err) => console.log(err));
```

A single `.catch()` can handle errors from the entire chain.

### 7. Promise Chaining

Promises allow sequential async operations.

```js
fetchUser()
  .then((user) => fetchPosts(user.id))
  .then((posts) => fetchComments(posts[0].id))
  .then((comments) => console.log(comments));
```

Each `.then()` returns a new Promise.

### 8. Promise Utility Methods

Promises provide powerful utilities:

#### `Promise.all()`

Runs multiple promises in parallel.

```js
Promise.all([fetchUsers(), fetchPosts()]).then(([users, posts]) => {
  console.log(users, posts);
});
```

#### `Promise.race()`

Returns the first settled promise.

```js
Promise.race([p1, p2]).then((result) => console.log(result));
```

### 9. Relationship with Async/Await

`async/await` is built on top of Promises.

This:

```js
getUser()
  .then((user) => getOrders(user.id))
  .catch(console.error);
```

becomes:

```js
async function run() {
  try {
    const user = await getUser();
    const orders = await getOrders(user.id);

    console.log(orders);
  } catch (err) {
    console.error(err);
  }
}
```

`async/await` further improves readability.

### 10. Common Interview Points

#### Why were Promises introduced?

To solve:

- callback hell
- poor async error handling
- unreadable nested code

#### Do Promises completely eliminate callbacks?

No.

Promises internally still use callbacks (`then`, `catch` handlers are callbacks).

They mainly improve:

- structure
- chaining
- error propagation

#### Are callbacks bad?

No. Callbacks are fundamental in JavaScript.

Examples:

- event listeners
- array methods (`map`, `filter`)
- timers (`setTimeout`)

Problem arises mainly with deeply nested async callbacks.

### 11. Best Practices

#### Prefer

- Promises
- async/await

#### Avoid

- deeply nested callbacks

#### Always

- return promises from `.then()`
- handle errors with `.catch()`
- use `try/catch` with async/await

### 12. Modern Recommendation

In modern JavaScript:

- Avoid callback-based async APIs when possible
- Prefer Promises
- Prefer `async/await` for complex flows

Most modern APIs like:

- `fetch`
- database libraries
- modern Node.js APIs

are Promise-based.

### Interview Summary

You can summarize in an interview like this:

> Callback hell occurs when multiple asynchronous callbacks are nested deeply, making code unreadable and difficult to maintain. Promises solve this by providing a cleaner chaining mechanism with better error handling using `.then()` and `.catch()`. Modern JavaScript further simplifies Promise handling using `async/await`.

## Question 2. What is Promise chaining? (Implied from the content about chaining promises)

## Concise Answer

**Promise chaining** is a technique where multiple asynchronous operations are executed in sequence by linking multiple `.then()` handlers, where each `.then()` returns a new Promise.

---

# 1. What is Promise Chaining?

Promise chaining allows you to perform a series of asynchronous tasks one after another, where each step depends on the result of the previous step.

Instead of nesting callbacks, each `.then()` returns a new Promise, enabling a **flat and readable flow**.

---

# 2. Basic Example

```js
getUser()
  .then((user) => {
    return getOrders(user.id);
  })
  .then((orders) => {
    return getOrderDetails(orders[0].id);
  })
  .then((details) => {
    console.log(details);
  })
  .catch((err) => {
    console.log("Error:", err);
  });
```

### Key idea:

Each `.then()`:

- receives the previous result
- returns a new Promise (or value)
- passes output to the next `.then()`

---

# 3. How Promise Chaining Works Internally

When you do:

```js
promise.then(fn1).then(fn2).then(fn3);
```

Internally:

1. `fn1` runs after promise resolves
2. Its return value becomes input to `fn2`
3. `fn2` return value becomes input to `fn3`

If any step returns a Promise, the chain **waits until it resolves**.

---

# 4. Important Rule of Chaining

## 👉 A `.then()` always returns a new Promise

Even if you return a simple value:

```js
Promise.resolve(10)
  .then((num) => num * 2)
  .then((result) => console.log(result)); // 20
```

Behind the scenes:

- `num * 2` is wrapped in `Promise.resolve(20)`

---

# 5. Returning a Promise vs Value

## Case 1: Returning a value

```js
.then(x => x + 1)
```

✔ Automatically wrapped in resolved Promise

---

## Case 2: Returning a Promise

```js
.then(x => fetchData(x))
```

✔ Next `.then()` waits for this Promise to resolve

---

# 6. Real-World Example

```js
fetchUser()
  .then((user) => fetchPosts(user.id))
  .then((posts) => fetchComments(posts[0].id))
  .then((comments) => {
    console.log("Comments:", comments);
  })
  .catch((error) => {
    console.error("Something failed:", error);
  });
```

### Flow:

1. Get user
2. Use user → get posts
3. Use posts → get comments
4. Handle all errors in one place

---

# 7. Why Promise Chaining is Important

## a) Avoids Callback Hell

Instead of:

```js
a(function () {
  b(function () {
    c(function () {});
  });
});
```

You get:

```js
a().then(b).then(c);
```

---

## b) Linear Execution Flow

Code reads top-to-bottom like synchronous logic.

---

## c) Centralized Error Handling

Single `.catch()` handles entire chain.

---

# 8. Common Pitfalls in Promise Chaining

## ❌ Forgetting to return a Promise

```js
getUser()
  .then((user) => {
    getOrders(user.id); // ❌ missing return
  })
  .then((orders) => {
    console.log(orders); // undefined
  });
```

### Fix:

```js
.then(user => {
  return getOrders(user.id);
});
```

---

## ❌ Mixing async logic without return

Always ensure each `.then()` returns something meaningful.

---

## ❌ Silent failures if `.catch()` is misplaced

```js
getUser()
  .then(...)
  .catch(...)
  .then(...) // may still run unexpectedly
```

---

# 9. Promise Chaining vs async/await

| Feature        | Promise Chaining | async/await      |
| -------------- | ---------------- | ---------------- |
| Style          | Functional chain | Synchronous-like |
| Readability    | Good             | Better           |
| Error handling | `.catch()`       | `try/catch`      |
| Debugging      | Slightly harder  | Easier           |

Example:

### Chain

```js
getUser()
  .then((user) => getOrders(user.id))
  .then(console.log)
  .catch(console.error);
```

### async/await

```js
async function run() {
  try {
    const user = await getUser();
    const orders = await getOrders(user.id);
    console.log(orders);
  } catch (err) {
    console.error(err);
  }
}
```

---

# 10. Interview One-Liner

> Promise chaining is the process of linking multiple `.then()` calls where each step returns a Promise, allowing asynchronous operations to run sequentially in a clean and readable manner.

## Question 3. Difference between `Promise.all`, `Promise.race`, and `Promise.allSettled`

## Question 4. How to handle errors in `async/await`

## Question 5. Difference between event delegation and direct event listener

## Question 6. How to stop event propagation

## Question 7. How to use `JSON.stringify` and `JSON.parse` with reviver/replacer functions

## Question 8. How to detect if a browser supports a certain feature (feature detection)

## Question 9. Difference between `document.readyState` and `DOMContentLoaded`

## Question 10. Explain JavaScript's event loop with call stack and task queue

## Question 11. Difference between microtasks and macrotasks

## Question 12. How to identify and fix memory leaks

## Question 13. Difference between `localStorage`, `sessionStorage`, and cookies

## Question 14. How to optimize rendering performance in JS-heavy apps

## Question 15. How to implement a custom iterator in JavaScript

## Question 16. Difference between iterable and iterator

## Question 17. Difference between generators and async generators

## Question 18. How to implement a custom Promise

## Question 19. Difference between ES6 modules and CommonJS

## Question 20. How to dynamically import a module
