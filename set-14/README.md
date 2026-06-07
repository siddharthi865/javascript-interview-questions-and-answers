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

## Concise Answer

- **`Promise.all`** → waits for **all promises to fulfill**, fails fast if any one rejects.
- **`Promise.race`** → returns the **first settled promise** (fulfilled or rejected).
- **`Promise.allSettled`** → waits for **all promises to finish**, always returns results (no failure short-circuit).

---

# 1. `Promise.all`

### What it does:

Runs multiple promises in parallel and **resolves when all succeed**. If any one fails, it **immediately rejects**.

---

### Example:

```js id="pall1"
Promise.all([Promise.resolve(1), Promise.resolve(2), Promise.resolve(3)]).then(
  console.log,
);
```

### Output:

```
[1, 2, 3]
```

---

### Failure case (FAANG favorite):

```js id="pall2"
Promise.all([Promise.resolve(1), Promise.reject("Error"), Promise.resolve(3)])
  .then(console.log)
  .catch(console.log);
```

### Output:

```
Error
```

---

### Key behavior:

- Fails fast (short-circuit)
- Order of results is preserved (important!)

---

### Use case:

- Load multiple API calls where all are required
- Parallel data fetching with dependency on full success

---

# 2. `Promise.race`

### What it does:

Returns the **first promise to settle** (resolve OR reject).

---

### Example:

```js id="prace1"
Promise.race([
  new Promise((res) => setTimeout(() => res("A"), 100)),
  new Promise((res) => setTimeout(() => res("B"), 50)),
]).then(console.log);
```

### Output:

```
B
```

---

### Rejection example:

```js id="prace2"
Promise.race([
  new Promise((_, rej) => setTimeout(() => rej("Error"), 50)),
  new Promise((res) => setTimeout(() => res("Success"), 100)),
])
  .then(console.log)
  .catch(console.log);
```

### Output:

```
Error
```

---

### Key behavior:

- First settled wins
- Doesn’t wait for others
- Can resolve or reject first

---

### Use case:

- Timeout handling
- Fastest API response
- Competing requests (fallback systems)

---

### Example: timeout pattern

```js id="timeout1"
Promise.race([
  fetch("/api/data"),
  new Promise((_, reject) => setTimeout(() => reject("Timeout"), 5000)),
]);
```

---

# 3. `Promise.allSettled`

### What it does:

Waits for **all promises to finish**, regardless of success or failure.

Never rejects.

---

### Example:

```js id="psettled1"
Promise.allSettled([
  Promise.resolve(1),
  Promise.reject("Error"),
  Promise.resolve(3),
]).then(console.log);
```

### Output:

```js
[
  { status: "fulfilled", value: 1 },
  { status: "rejected", reason: "Error" },
  { status: "fulfilled", value: 3 },
];
```

---

### Key behavior:

- Always resolves
- Gives full report of outcomes
- Does NOT short-circuit

---

### Use case:

- Batch processing where partial failure is OK
- Logging results of multiple API calls
- UI dashboards showing mixed results

---

# 4. Quick Comparison Table

| Feature          | Promise.all               | Promise.race         | Promise.allSettled      |
| ---------------- | ------------------------- | -------------------- | ----------------------- |
| Waits for all?   | Yes                       | No                   | Yes                     |
| Fails fast?      | Yes                       | No                   | No                      |
| Reject behavior  | Any rejection fails whole | First rejection wins | Never rejects           |
| Result format    | Array of values           | Single value         | Array of status objects |
| Order preserved? | Yes                       | N/A                  | Yes                     |

---

# 5. Mental Model (Interview Gold)

### Think like this:

### 🔵 `Promise.all`

> “I need EVERYTHING to succeed or I fail.”

---

### 🟡 `Promise.race`

> “Give me the FIRST result, I don’t care about others.”

---

### 🟢 `Promise.allSettled`

> “Give me EVERYTHING, success or failure, I’ll decide later.”

---

# 6. Common Pitfalls (FAANG traps)

## ❌ Using `Promise.all` when partial failure is acceptable

Bad:

```js
Promise.all([api1(), api2(), api3()]);
```

If `api2` fails → everything fails.

---

## ❌ Misusing `race` thinking it waits for all

It does NOT wait.

---

## ❌ Assuming `allSettled` gives values directly

Wrong:

```js
.then(results => results[0]) // still needs status check
```

Correct:

```js
results.filter((r) => r.status === "fulfilled");
```

---

# 7. Modern Usage Tip

- Use `Promise.all` → strict dependency
- Use `Promise.allSettled` → dashboards, reporting
- Use `Promise.race` → timeout, fallback, performance optimization

---

# 8. Interview One-Liner

> `Promise.all` waits for all promises and fails fast on error, `Promise.race` returns the first settled promise, and `Promise.allSettled` waits for all promises and returns their final status regardless of success or failure.

## Question 4. How to handle errors in `async/await`

## Concise Answer

Errors in `async/await` are typically handled using **`try...catch`** blocks. Since an `async` function automatically returns a Promise, any thrown error or rejected Promise can be caught with `catch`.

---

# 1. Basic Error Handling with `try...catch`

```js
async function getUser() {
  try {
    const response = await fetch("/api/user");

    if (!response.ok) {
      throw new Error("Failed to fetch user");
    }

    const user = await response.json();
    console.log(user);
  } catch (error) {
    console.error("Error:", error.message);
  }
}
```

### How it works

1. `await` pauses execution until the Promise settles.
2. If the Promise rejects, control jumps to `catch`.
3. Any manually thrown error is also caught.

---

# 2. Rejected Promise Example

```js
async function test() {
  try {
    await Promise.reject("Something went wrong");
  } catch (err) {
    console.log(err);
  }
}

test();
```

### Output

```js
Something went wrong
```

---

# 3. Errors Thrown Inside Async Functions

```js
async function divide(a, b) {
  if (b === 0) {
    throw new Error("Cannot divide by zero");
  }

  return a / b;
}

async function run() {
  try {
    const result = await divide(10, 0);
    console.log(result);
  } catch (err) {
    console.log(err.message);
  }
}

run();
```

### Output

```js
Cannot divide by zero
```

---

# 4. Async Functions Return Rejected Promises

This is important for interviews.

```js
async function test() {
  throw new Error("Oops");
}

test().catch((err) => console.log(err.message));
```

### Output

```js
Oops;
```

### Equivalent Promise Version

```js
function test() {
  return Promise.reject(new Error("Oops"));
}
```

---

# 5. Using `.catch()` Instead of `try...catch`

```js
async function getData() {
  const data = await Promise.resolve("Hello");
  return data;
}

getData().then(console.log).catch(console.error);
```

### Output

```js
Hello;
```

You can handle errors either:

```js
try {
  await something();
} catch (err) {
  // handle
}
```

or

```js
something().catch((err) => {});
```

---

# 6. Handling Multiple Awaits

```js
async function loadData() {
  try {
    const user = await getUser();
    const orders = await getOrders(user.id);
    const payment = await getPayment(orders[0].id);

    console.log(payment);
  } catch (err) {
    console.error("Failed:", err);
  }
}
```

### Benefit

A single `catch` handles errors from:

- `getUser`
- `getOrders`
- `getPayment`

---

# 7. Independent Error Handling

Sometimes each operation needs its own handling.

```js
async function run() {
  try {
    const user = await getUser();
    console.log(user);
  } catch {
    console.log("User fetch failed");
  }

  try {
    const orders = await getOrders();
    console.log(orders);
  } catch {
    console.log("Orders fetch failed");
  }
}
```

---

# 8. Using `finally`

`finally` always runs whether success or failure.

```js
async function fetchData() {
  try {
    console.log("Loading...");
    await Promise.resolve();
  } catch (err) {
    console.error(err);
  } finally {
    console.log("Cleanup");
  }
}

fetchData();
```

### Output

```js
Loading...
Cleanup
```

Common uses:

- Hide loading spinner
- Close database connection
- Release resources

---

# 9. Common Interview Pitfall: Forgotten `await`

```js
async function run() {
  try {
    Promise.reject("Error");
  } catch (err) {
    console.log("Caught");
  }
}

run();
```

### Output

```js
Unhandled Promise Rejection
```

### Why?

`try...catch` only catches:

- synchronous errors
- rejected Promises that are **awaited**

Correct version:

```js
async function run() {
  try {
    await Promise.reject("Error");
  } catch (err) {
    console.log("Caught");
  }
}
```

### Output

```js
Caught;
```

---

# 10. Parallel Operations with Error Handling

### Using `Promise.all`

```js
async function load() {
  try {
    const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()]);

    console.log(users, posts);
  } catch (err) {
    console.error("One request failed");
  }
}
```

### Important

If any Promise rejects:

```js
Promise.all(...)
```

rejects immediately.

---

# 11. Partial Failure Handling

Use `Promise.allSettled`.

```js
async function load() {
  const results = await Promise.allSettled([
    fetchUsers(),
    fetchPosts(),
    fetchComments(),
  ]);

  console.log(results);
}
```

This allows processing successful results even when some fail.

---

# 12. Advanced: Custom Error Types

```js
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

async function register(user) {
  if (!user.email) {
    throw new ValidationError("Email required");
  }
}
```

```js
try {
  await register({});
} catch (err) {
  if (err instanceof ValidationError) {
    console.log("Validation failed");
  }
}
```

This pattern is common in large applications.

---

# 13. Common Mistakes

### ❌ Empty catch blocks

```js
try {
  await fetchData();
} catch (err) {}
```

Swallows errors and makes debugging difficult.

---

### ❌ Catching and ignoring errors

```js
catch (err) {
  return null;
}
```

Only do this intentionally.

---

### ❌ Forgetting to rethrow

```js
catch (err) {
  console.error(err);
}
```

Sometimes you should propagate:

```js
catch (err) {
  console.error(err);
  throw err;
}
```

---

# Interview Summary

> In `async/await`, errors are usually handled with `try...catch`. When an awaited Promise rejects or an error is thrown, execution jumps to the `catch` block. `finally` can be used for cleanup logic, and for parallel operations, `Promise.all` or `Promise.allSettled` can be combined with `await` depending on whether partial failures should be tolerated. A common pitfall is forgetting to use `await`, because `try...catch` cannot catch unawaited Promise rejections.

## Question 5. Difference between event delegation and direct event listener

## Concise Answer

**Direct event listeners** attach an event handler to each individual element.

**Event delegation** attaches a single event listener to a common ancestor and uses **event bubbling** to handle events from its child elements.

Event delegation is generally more memory-efficient and works well for dynamically added elements.

---

# 1. Direct Event Listener

In direct binding, each element gets its own listener.

```js
const buttons = document.querySelectorAll(".btn");

buttons.forEach((button) => {
  button.addEventListener("click", () => {
    console.log("Clicked");
  });
});
```

### How it works

```html
<button class="btn">A</button>
<button class="btn">B</button>
<button class="btn">C</button>
```

Each button has a separate event listener attached.

---

# 2. Event Delegation

Instead of attaching listeners to every button, attach one listener to their parent.

```js
document.querySelector("#container").addEventListener("click", (event) => {
  if (event.target.matches(".btn")) {
    console.log("Button clicked");
  }
});
```

```html
<div id="container">
  <button class="btn">A</button>
  <button class="btn">B</button>
  <button class="btn">C</button>
</div>
```

### How it works

1. Button receives click.
2. Event bubbles up.
3. Parent listener catches it.
4. `event.target` identifies which child triggered it.

---

# 3. Visual Flow

### Direct Listener

```text
Button A → Listener A
Button B → Listener B
Button C → Listener C
```

---

### Event Delegation

```text
Button A
Button B
Button C
    ↓
Parent Listener
```

Only one listener exists.

---

# 4. Why Event Delegation Works

Because of **event bubbling**.

When a click occurs:

```text
Button
  ↓
Parent
  ↓
Grandparent
  ↓
Document
```

Delegation intercepts the event during bubbling.

---

# 5. Memory Usage Comparison

### Direct Listeners

```js
1000 buttons
1000 listeners
```

Memory cost increases with element count.

---

### Delegation

```js
1000 buttons
1 listener
```

Much more efficient.

---

# 6. Dynamic Elements (Most Common Interview Question)

### Direct Listener Problem

```js
const button = document.querySelector(".btn");

button.addEventListener("click", handler);
```

Later:

```js
container.innerHTML += '<button class="btn">New</button>';
```

The new button has **no listener**.

---

### Delegation Solution

```js
container.addEventListener("click", (event) => {
  if (event.target.matches(".btn")) {
    console.log("Clicked");
  }
});
```

New buttons automatically work.

---

# 7. Example: Dynamic List

```js
const list = document.querySelector("#list");

list.addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    console.log(event.target.textContent);
  }
});
```

```html
<ul id="list">
  <li>One</li>
  <li>Two</li>
</ul>
```

Even newly added `<li>` elements are handled.

---

# 8. Using `closest()` (Best Practice)

Interviewers love this pattern.

### Problem

User clicks inside nested content:

```html
<button class="btn">
  <span>Click</span>
</button>
```

`event.target` may be `span`.

---

### Better Solution

```js
container.addEventListener("click", (event) => {
  const button = event.target.closest(".btn");

  if (!button) return;

  console.log("Clicked");
});
```

Works regardless of where inside the button the user clicks.

---

# 9. Performance Comparison

## Direct Listener

```js
for (let i = 0; i < 10000; i++) {
  element.addEventListener("click", handler);
}
```

- More memory
- More listener registrations

---

## Delegation

```js
parent.addEventListener("click", handler);
```

- Single listener
- Lower memory usage
- Easier maintenance

---

# 10. When Event Delegation Does NOT Work Well

Some events do not naturally bubble.

Examples:

```js
focus;
blur;
mouseenter;
mouseleave;
```

### Problem

```js
parent.addEventListener("focus", handler);
```

May not work as expected because `focus` doesn't bubble.

---

### Alternatives

Use:

```js
focusin;
focusout;
```

or capture phase:

```js
parent.addEventListener("focus", handler, true);
```

---

# 11. Common Interview Pitfalls

## Pitfall 1: Using `event.currentTarget`

```js
container.addEventListener("click", (e) => {
  console.log(e.currentTarget);
});
```

`currentTarget` is always:

```js
container;
```

Not the clicked child.

Use:

```js
event.target;
```

or

```js
event.target.closest(...)
```

---

## Pitfall 2: Forgetting Bubbling

If someone calls:

```js
event.stopPropagation();
```

before the event reaches the parent, delegated handlers won't run.

---

## Pitfall 3: Delegating Too High

```js
document.addEventListener("click", ...)
```

Works, but can cause unnecessary event processing.

Prefer the closest stable ancestor.

---

# 12. Direct Listener vs Event Delegation

| Feature                        | Direct Listener | Event Delegation    |
| ------------------------------ | --------------- | ------------------- |
| Number of listeners            | One per element | One on parent       |
| Memory usage                   | Higher          | Lower               |
| Dynamic elements               | Must reattach   | Works automatically |
| Performance with many elements | Worse           | Better              |
| Simplicity for few elements    | Simpler         | Slightly more logic |
| Relies on bubbling             | No              | Yes                 |
| Works with non-bubbling events | Yes             | Usually no          |

---

# 13. When to Use Which?

### Use Direct Listeners

When:

- Few elements exist
- Elements are static
- Event doesn't bubble well
- Simplicity is preferred

```js
saveButton.addEventListener("click", save);
```

---

### Use Event Delegation

When:

- Many elements exist
- Elements are created dynamically
- Performance matters
- Handling lists, tables, grids, menus

```js
table.addEventListener("click", handleCellClick);
```

---

# FAANG Interview Answer

> Direct event listeners attach handlers to individual elements, while event delegation attaches a single listener to a parent and relies on event bubbling to handle child events. Delegation reduces memory usage, improves performance for large numbers of elements, and automatically supports dynamically added elements. However, it depends on bubbling and is not suitable for some non-bubbling events like `focus` and `blur`.

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
