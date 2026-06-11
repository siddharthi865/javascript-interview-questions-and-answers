# Set 5

| S.No. | Question                                                                                                                                                                                                   |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What are service workers?](#question-1-what-are-service-workers)                                                                                                                                          |
| 2.    | [Explain how JS handles promises under the hood](#question-2-explain-how-js-handles-promises-under-the-hood)                                                                                               |
| 3.    | [How to chain promises with error handling](#question-3-how-to-chain-promises-with-error-handling)                                                                                                         |
| 4.    | [Explain generator functions in JavaScript](#question-4-explain-generator-functions-in-javascript)                                                                                                         |
| 5.    | [Difference between generators and async functions](#question-5-difference-between-generators-and-async-functions)                                                                                         |
| 6.    | [How to use async iterators](#question-6-how-to-use-async-iterators)                                                                                                                                       |
| 7.    | [What is Proxy in JavaScript? Give use case](#question-7-what-is-proxy-in-javascript-give-use-case)                                                                                                        |
| 8.    | [What is Reflect API?](#question-8-what-is-reflect-api)                                                                                                                                                    |
| 9.    | [How to create private fields in JS classes](#question-9-how-to-create-private-fields-in-js-classes)                                                                                                       |
| 10.   | [What are decorators?](#question-10-what-are-decorators)                                                                                                                                                   |
| 11.   | [Explain the concept of Symbol.iterator](#question-11-explain-the-concept-of-symboliterator)                                                                                                               |
| 12.   | [Difference between deep copy using structuredClone vs JSON methods](#question-12-difference-between-deep-copy-using-structuredclone-vs-json-methods)                                                      |
| 13.   | [What are Tagged Template Literals?](#question-13-what-are-tagged-template-literals)                                                                                                                       |
| 14.   | [How to implement a simple event emitter in JavaScript](#question-14-how-to-implement-a-simple-event-emitter-in-javascript)                                                                                |
| 15.   | [Explain JS memory model: stack vs heap](#question-15-explain-js-memory-model-stack-vs-heap)                                                                                                               |
| 16.   | [Difference between shallow equality and deep equality](#question-16-difference-between-shallow-equality-and-deep-equality)                                                                                |
| 17.   | [How to optimize performance of large array operations](#question-17-how-to-optimize-performance-of-large-array-operations)                                                                                |
| 18.   | [Explain the difference between classical async patterns (callbacks) vs promises vs async/await](#question-18-explain-the-difference-between-classical-async-patterns-callbacks-vs-promises-vs-asyncawait) |
| 19.   | [What are Web Workers? How do they work?](#question-19-what-are-web-workers-how-do-they-work)                                                                                                              |
| 20.   | [How to prevent race conditions in asynchronous JS code](#question-20-how-to-prevent-race-conditions-in-asynchronous-js-code)                                                                              |

## Question 1. What are service workers?

> Service Workers are **background scripts in the browser that act as a network proxy**, enabling features like **offline support, caching, push notifications, and background sync** for web applications.

## Detailed Explanation

A **Service Worker** is a special type of JavaScript file that runs separately from the main browser thread. It sits between your web app and the network, intercepting network requests and deciding how to handle them (fetch from cache, network, or both).

It is a key technology behind **Progressive Web Apps (PWAs)**.

### Key Characteristics

#### 1. Runs in the background

- Not tied to a web page
- Runs independently of the main JavaScript thread
- Cannot directly access DOM

#### 2. Acts as a network proxy

It can intercept requests made by the page:

```javascript
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      return response || fetch(event.request);
    }),
  );
});
```

Here:

- It first checks cache
- If not found, fetches from network

#### 3. Event-driven lifecycle

Service workers follow a strict lifecycle:

- **Register**
- **Install**
- **Activate**

Example:

```javascript
// Register service worker
if ("serviceWorker" in navigator) {
  navigator.serviceWorker
    .register("/sw.js")
    .then((registration) => {
      console.log("Service Worker registered:", registration);
    })
    .catch((err) => {
      console.log("Registration failed:", err);
    });
}
```

Inside `sw.js`:

```javascript
self.addEventListener("install", (event) => {
  console.log("Service Worker installing...");
});

self.addEventListener("activate", (event) => {
  console.log("Service Worker activated");
});
```

### Key Features Enabled by Service Workers

#### 1. Offline Support

Using caching strategies (Cache API), apps can work without internet.

Example:

- Gmail offline mode
- Google Docs offline editing

#### 2. Caching Strategies

Common strategies:

- Cache First
- Network First
- Stale-While-Revalidate

Example (Cache First):

```javascript
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((cached) => {
      return cached || fetch(event.request);
    }),
  );
});
```

#### 3. Push Notifications

Service workers can receive push messages even when the browser is closed.

```javascript
self.addEventListener("push", (event) => {
  const data = event.data.json();

  self.registration.showNotification(data.title, {
    body: data.body,
    icon: "/icon.png",
  });
});
```

#### 4. Background Sync

Allows retrying failed network requests when connectivity returns.

```javascript
self.addEventListener("sync", (event) => {
  if (event.tag === "sync-data") {
    event.waitUntil(syncDataToServer());
  }
});
```

### Important Limitations

- No direct DOM access
- Runs only over HTTPS (except localhost)
- Event-based (terminates when idle)
- Requires explicit registration
- Browser support varies (but modern browsers support it well)

### Common Pitfalls

#### 1. Caching mistakes

Improper caching can serve stale or broken data.

#### 2. Complex debugging

Since it runs separately, debugging requires DevTools → Application tab.

#### 3. Versioning issues

Old service workers may remain active unless properly updated.

### Best Practices

- Always version your cache names:

  ```javascript
  const CACHE_NAME = "app-v1";
  ```

- Use proper update strategy:
  - Skip waiting when needed
  - Clean old caches in `activate`

- Keep service worker logic lightweight

### Real-World Use Cases

- Twitter Lite (fast loading, offline support)
- Pinterest (improved performance)
- Flipkart PWA (offline browsing + push notifications)

### Summary

A **Service Worker** is a background JavaScript file that:

- Intercepts network requests
- Enables offline-first web apps
- Handles caching, push notifications, and background sync
- Runs independently of the main browser thread

It is a core building block for **Progressive Web Apps (PWAs)** and modern web performance optimization.

## Question 2. Explain how JS handles promises under the hood

# How JavaScript Handles Promises Under the Hood

### Short Answer

Promises are handled through a combination of:

1. **The JavaScript engine** (V8, SpiderMonkey, JavaScriptCore, etc.)
2. **The Event Loop**
3. **Microtask Queue**
4. **Internal Promise states and reaction handlers**

When a Promise settles (fulfilled or rejected), its `.then()`, `.catch()`, and `.finally()` callbacks are not executed immediately. Instead, they are placed into the **microtask queue**, which is processed before the next macrotask (such as `setTimeout`).

---

# Promise Internals

A Promise has three internal states:

```text
Pending
   ↓
Fulfilled
```

or

```text
Pending
   ↓
Rejected
```

Once settled, the state cannot change.

Example:

```javascript
const promise = new Promise((resolve, reject) => {
  resolve("Success");
});
```

Internally:

```text
Promise {
  [[State]]: "fulfilled",
  [[Result]]: "Success"
}
```

These internal slots are part of the ECMAScript specification and are not directly accessible from JavaScript.

---

# Step-by-Step Execution

Consider:

```javascript
console.log("Start");

Promise.resolve("Done").then((value) => console.log(value));

console.log("End");
```

Output:

```text
Start
End
Done
```

### What happens internally?

### 1. Call Stack

```javascript
console.log("Start");
```

Prints:

```text
Start
```

---

### 2. Create Resolved Promise

```javascript
Promise.resolve("Done");
```

Creates:

```text
Promise {
  [[State]]: fulfilled,
  [[Result]]: "Done"
}
```

---

### 3. Register `.then()`

```javascript
.then(...)
```

The callback is stored as a **Promise Reaction**.

```text
PromiseReactions:
[
  callback => console.log(value)
]
```

Because the promise is already fulfilled, the callback is queued as a microtask.

---

### 4. Continue Execution

```javascript
console.log("End");
```

Prints:

```text
End
```

---

### 5. Call Stack Becomes Empty

Event Loop checks:

```text
Microtask Queue:
[
  console.log("Done")
]
```

Executes it.

Output:

```text
Done
```

---

# Why Promises Use Microtasks

Promises are intentionally designed to run asynchronously.

Even:

```javascript
Promise.resolve().then(() => {
  console.log("Promise");
});

console.log("Normal");
```

Output:

```text
Normal
Promise
```

This guarantees predictable behavior.

If callbacks ran synchronously:

```javascript
const p = Promise.resolve();

p.then(() => console.log("A"));
console.log("B");
```

The execution order could become inconsistent.

The specification prevents this by always scheduling promise callbacks as microtasks.

---

# Microtask Queue vs Macrotask Queue

Interviewers often ask this.

Example:

```javascript
console.log("1");

setTimeout(() => console.log("2"));

Promise.resolve().then(() => console.log("3"));

console.log("4");
```

Output:

```text
1
4
3
2
```

Why?

### Execution Flow

```text
Call Stack:
1
4
```

After stack empties:

```text
Microtask Queue:
3
```

Runs first.

Then:

```text
Macrotask Queue:
2
```

Runs afterward.

Priority:

```text
Current Stack
     ↓
Microtasks
     ↓
Macrotasks
```

---

# Promise Chaining Internals

Example:

```javascript
Promise.resolve(1)
  .then((x) => x + 1)
  .then((x) => x + 1)
  .then(console.log);
```

Output:

```text
3
```

Internally:

### First Promise

```text
Fulfilled → 1
```

### First `.then()`

Creates a NEW Promise

```text
Promise2
```

Returns:

```javascript
1 + 1;
```

Promise2 becomes:

```text
Fulfilled → 2
```

### Second `.then()`

Creates:

```text
Promise3
```

Returns:

```javascript
2 + 1;
```

Promise3:

```text
Fulfilled → 3
```

Every `.then()` returns a brand-new Promise.

---

# What Happens When a Promise Returns Another Promise

Example:

```javascript
Promise.resolve(1)
  .then(() => {
    return Promise.resolve(100);
  })
  .then(console.log);
```

Output:

```text
100
```

Internally:

```text
Promise A
   ↓
returns Promise B
```

The engine performs **Promise Resolution Procedure**:

Instead of:

```text
Promise<Promise<100>>
```

it automatically unwraps:

```text
Promise<100>
```

This process is called **Promise Flattening**.

---

# Error Handling Internals

Example:

```javascript
Promise.resolve()
  .then(() => {
    throw new Error("Oops");
  })
  .catch((err) => {
    console.log(err.message);
  });
```

Output:

```text
Oops
```

Internally:

```javascript
throw new Error(...)
```

is transformed into:

```javascript
return Promise.reject(error);
```

Conceptually:

```text
then()
   ↓
throws
   ↓
rejected promise
   ↓
catch()
```

That's why errors propagate through chains automatically.

---

# How `async/await` Uses Promises

Example:

```javascript
async function getData() {
  const result = await Promise.resolve("Hello");
  console.log(result);
}
```

Under the hood, it behaves roughly like:

```javascript
function getData() {
  return Promise.resolve("Hello").then((result) => {
    console.log(result);
  });
}
```

`await` pauses the async function and resumes it later via the microtask queue.

---

# Simplified Internal Model

Think of a Promise as:

```javascript
class FakePromise {
  state = "pending";
  value = undefined;
  handlers = [];

  resolve(value) {
    this.state = "fulfilled";
    this.value = value;

    queueMicrotask(() => {
      this.handlers.forEach((h) => h(value));
    });
  }

  then(handler) {
    this.handlers.push(handler);
  }
}
```

Real engines are far more complex, but this illustrates the core idea:

- Store callbacks
- Change state
- Queue callbacks as microtasks
- Execute them after current code finishes

---

# Interview-Ready Summary

When a Promise is created, JavaScript stores its internal state (`pending`, `fulfilled`, or `rejected`) and any attached reaction handlers. When the Promise settles, its `.then()`, `.catch()`, or `.finally()` callbacks are scheduled in the **microtask queue**, not executed immediately. The **event loop** processes all microtasks after the current call stack is empty and before any macrotasks like `setTimeout`. Every `.then()` returns a new Promise, enabling chaining, automatic error propagation, and promise flattening. `async/await` is built on top of the same Promise and microtask mechanisms.

## Question 3. How to chain promises with error handling

### Short Answer

You chain promises using `.then()` for success flow and `.catch()` for error handling. The best practice is to place a **single `.catch()` at the end of the chain**, so it handles errors from any step in the chain.

---

# How Promise Chaining Works with Error Handling

Promise chaining works because **each `.then()` returns a new Promise**, allowing sequential execution.

If any step throws an error or returns a rejected promise, the control automatically jumps to the nearest `.catch()`.

---

## 1. Basic Promise Chain

```javascript
fetchUser()
  .then((user) => {
    return fetchUserPosts(user.id);
  })
  .then((posts) => {
    return processPosts(posts);
  })
  .then((result) => {
    console.log("Final result:", result);
  })
  .catch((error) => {
    console.error("Something went wrong:", error);
  });
```

### Flow:

- Each `.then()` runs on success
- If any step fails → `.catch()` runs immediately

---

## 2. How Error Propagation Works

Example:

```javascript
Promise.resolve()
  .then(() => {
    console.log("Step 1");
    throw new Error("Failed in Step 1");
  })
  .then(() => {
    console.log("Step 2"); // skipped
  })
  .catch((err) => {
    console.log("Caught:", err.message);
  });
```

### Output:

```
Step 1
Caught: Failed in Step 1
```

### Key idea:

Once an error is thrown:

- The chain **skips all subsequent `.then()` handlers**
- It jumps directly to the nearest `.catch()`

---

## 3. Returning Promises in Chains

Each step can return a promise:

```javascript
getUser()
  .then((user) => {
    return getOrders(user.id); // returns a Promise
  })
  .then((orders) => {
    return getInvoice(orders); // returns another Promise
  })
  .catch((err) => {
    console.log("Error occurred:", err);
  });
```

### Important:

If a returned promise rejects → it is automatically caught.

---

## 4. Local Error Handling (Recovering from Errors)

You can also handle errors **inside a specific step**.

```javascript
fetchData()
  .then((data) => {
    return processData(data).catch((err) => {
      console.log("Processing failed, using fallback");
      return []; // recovery value
    });
  })
  .then((result) => {
    console.log("Recovered result:", result);
  })
  .catch((err) => {
    console.log("Unhandled error:", err);
  });
```

### Key idea:

- Inner `.catch()` = handle and recover locally
- Outer `.catch()` = global fallback handler

---

## 5. `finally()` in Promise Chains

Used for cleanup logic (runs always).

```javascript
fetchData()
  .then((data) => {
    console.log("Success:", data);
  })
  .catch((err) => {
    console.log("Error:", err);
  })
  .finally(() => {
    console.log("Cleanup done (loading spinner off, etc.)");
  });
```

### Important:

- Runs regardless of success or failure
- Does NOT receive error or result

---

## 6. Common Pitfalls (Interview Focus)

### ❌ Forgetting to return a promise

```javascript
fetchUser()
  .then((user) => {
    fetchOrders(user.id); // WRONG (not returned)
  })
  .then((orders) => {
    console.log(orders); // undefined
  });
```

### ✔ Correct version:

```javascript
fetchUser().then((user) => {
  return fetchOrders(user.id);
});
```

---

### ❌ Multiple catch blocks causing confusion

```javascript
fetchData()
  .catch((err) => {
    console.log("Handled once");
  })
  .catch((err) => {
    console.log("This will NOT run");
  });
```

Only the **first catch in flow order** executes for a given error path.

---

## 7. Best Practices (Interview Answer Points)

✔ Always return promises inside `.then()`
✔ Use a single `.catch()` at the end for global error handling
✔ Use inner `.catch()` only for recoverable errors
✔ Use `.finally()` for cleanup logic
✔ Avoid deeply nested `.then()` chains (prefer flattening or `async/await`)

---

## 8. Equivalent Using async/await (Modern Approach)

Promise chaining:

```javascript
fetchUser()
  .then((user) => fetchOrders(user.id))
  .then((orders) => processOrders(orders))
  .catch((err) => console.error(err));
```

Same logic using async/await:

```javascript
async function main() {
  try {
    const user = await fetchUser();
    const orders = await fetchOrders(user.id);
    const result = await processOrders(orders);

    console.log(result);
  } catch (err) {
    console.error("Error:", err);
  } finally {
    console.log("Done");
  }
}
```

---

## Interview-Ready Summary

Promise chaining works by returning a new promise at each `.then()`, allowing sequential execution. Errors thrown or rejected promises automatically propagate down the chain until a `.catch()` is encountered. A single `.catch()` at the end of the chain is the most common pattern for global error handling, while inner `.catch()` blocks can be used for localized recovery. `.finally()` is used for cleanup logic regardless of outcome. Modern JavaScript often replaces chaining with `async/await`, which uses the same underlying promise mechanism but provides more readable synchronous-style code.

## Question 4. Explain generator functions in JavaScript

### Short Answer

A **generator function** in JavaScript is a special function that can **pause and resume execution** using the `yield` keyword. It returns an **iterator object**, allowing you to control execution step-by-step instead of running the function all at once.

---

# Generator Functions in JavaScript (Interview-Ready Explanation)

## 1. What is a Generator Function?

A generator function is defined using an asterisk `function*` and uses `yield` to pause execution.

```javascript id="gen1"
function* myGenerator() {
  yield 1;
  yield 2;
  yield 3;
}
```

Unlike normal functions:

- It does NOT run completely when called
- It returns a **generator object (iterator)**

---

## 2. How to Use a Generator

```javascript id="gen2"
const gen = myGenerator();

console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }
```

### Key idea:

Each `.next()` call:

- Resumes execution
- Runs until next `yield`
- Pauses again

---

## 3. Internal Working Concept

Think of it like a **state machine**:

```text
Start → yield 1 → pause
Resume → yield 2 → pause
Resume → yield 3 → pause
End → done: true
```

Internally, JavaScript stores:

- Current execution context
- Local variables
- Position of `yield`

So it can resume exactly where it left off.

---

## 4. Yield vs Return

### `yield`

- Pauses function
- Produces intermediate values
- Can resume later

### `return`

- Ends generator completely

```javascript id="gen3"
function* demo() {
  yield 1;
  return 99;
  yield 2; // never executed
}
```

Output:

```javascript id="gen3out"
{ value: 1, done: false }
{ value: 99, done: true }
```

---

## 5. Iterating Generators

### Using `for...of`

```javascript id="gen4"
function* numbers() {
  yield 10;
  yield 20;
  yield 30;
}

for (const num of numbers()) {
  console.log(num);
}
```

Output:

```
10
20
30
```

👉 `for...of` automatically stops when `done: true`

---

## 6. Passing Values into Generators

You can send values back into the generator:

```javascript id="gen5"
function* calculator() {
  const a = yield "Enter first number";
  const b = yield "Enter second number";
  return a + b;
}

const gen = calculator();

console.log(gen.next().value); // Enter first number
console.log(gen.next(10).value); // Enter second number
console.log(gen.next(20).value); // 30
```

### Important concept:

The value passed to `.next(value)` becomes the result of the previous `yield`.

---

## 7. Error Handling in Generators

You can throw errors inside generators:

```javascript id="gen6"
function* demo() {
  try {
    yield 1;
  } catch (err) {
    console.log("Caught inside generator:", err);
  }
}

const gen = demo();

gen.next();
gen.throw(new Error("Something went wrong"));
```

---

## 8. Why Generators Exist (Real Use Cases)

### 1. Lazy Evaluation (Memory Efficient)

```javascript id="gen7"
function* infiniteNumbers() {
  let i = 1;
  while (true) {
    yield i++;
  }
}
```

Only generates values when needed.

---

### 2. Custom Iterators

```javascript id="gen8"
const obj = {
  *[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
  },
};
```

Now `obj` is iterable.

---

### 3. Async Control Flow (before async/await)

Generators were used with libraries like `co`:

```javascript id="gen9"
function* fetchData() {
  const user = yield fetch("/user");
  const posts = yield fetch(`/posts/${user.id}`);
}
```

👉 Modern replacement: `async/await`

---

## 9. Generator vs Normal Function

| Feature      | Function    | Generator            |
| ------------ | ----------- | -------------------- |
| Execution    | Runs fully  | Pauses/resumes       |
| Return type  | Value       | Iterator             |
| Control      | No control  | Controlled execution |
| Memory usage | All at once | Lazy evaluation      |

---

## 10. Generator vs Async Function

| Generator             | Async/Await         |
| --------------------- | ------------------- |
| `yield`               | `await`             |
| Manual iteration      | Automatic execution |
| Returns iterator      | Returns Promise     |
| Used for flow control | Used for async code |

👉 `async/await` is essentially a **specialized generator + promise system**

---

## 11. Common Pitfalls

### ❌ Forgetting `.next()`

```javascript
const gen = myGenerator();
console.log(gen); // does nothing useful
```

### ❌ Misunderstanding yield direction

```javascript
const value = yield 10;
```

👉 `yield` sends value OUT, `.next(value)` sends value IN

---

## Interview-Ready Summary

A **generator function** in JavaScript is a function defined with `function*` that can pause execution using `yield` and resume later using `.next()`. It returns an iterator object rather than a final value. This allows lazy evaluation, custom iteration, and controlled execution flow. Generators maintain their internal state between executions, making them useful for complex iteration patterns and were historically used for asynchronous control flow before `async/await` became standard.

## Question 5. Difference between generators and async functions

### Short Answer

**Generators** (`function*`) produce an **iterator that pauses and resumes execution using `yield`**, while **async functions** (`async function`) always return a **Promise and use `await` to handle asynchronous operations automatically**. In short: generators give manual control of execution flow, async functions are built specifically for async programming with automatic Promise handling.

---

# Generators vs Async Functions (Interview-Ready Explanation)

## 1. Core Difference

### Generator Function

- Defined using `function*`
- Returns an **iterator**
- Controlled manually using `.next()`

```javascript
function* gen() {
  yield 1;
  yield 2;
}
```

---

### Async Function

- Defined using `async function`
- Always returns a **Promise**
- Controlled automatically using `await`

```javascript
async function fn() {
  return 1;
}
```

---

## 2. Return Type Difference

### Generator

```javascript
function* gen() {
  yield 10;
}

const g = gen();
console.log(g.next());
```

Output:

```javascript
{ value: 10, done: false }
```

👉 Returns an **iterator object**

---

### Async Function

```javascript
async function fn() {
  return 10;
}

fn().then(console.log);
```

Output:

```javascript
10;
```

👉 Returns a **Promise**

---

## 3. Execution Control

### Generator (Manual control)

```javascript
function* gen() {
  console.log("Step 1");
  yield;

  console.log("Step 2");
  yield;
}

const g = gen();

g.next(); // Step 1
g.next(); // Step 2
```

👉 Developer controls execution step-by-step

---

### Async Function (Automatic control)

```javascript
async function fn() {
  console.log("Step 1");
  await Promise.resolve();

  console.log("Step 2");
}

fn();
```

👉 JavaScript automatically pauses/resumes at `await`

---

## 4. Handling Asynchronous Code

### Generator (manual orchestration required)

```javascript
function* fetchData() {
  const user = yield fetch("/user"); // manual handling needed
  const posts = yield fetch(`/posts/${user.id}`);
}
```

👉 Needs a runner (like `co` or custom logic)

---

### Async Function (built-in support)

```javascript
async function fetchData() {
  const user = await fetch("/user");
  const posts = await fetch(`/posts/${user.id}`);
}
```

👉 Clean and automatic async flow

---

## 5. Error Handling

### Generator

```javascript
function* gen() {
  try {
    yield 1;
  } catch (err) {
    console.log("Caught:", err.message);
  }
}
```

Errors must be manually injected via `.throw()`

```javascript
g.throw(new Error("fail"));
```

---

### Async Function

```javascript
async function fn() {
  try {
    await Promise.reject("error");
  } catch (err) {
    console.log("Caught:", err);
  }
}
```

👉 Uses normal `try/catch`, much simpler

---

## 6. Internal Mechanism (Important Interview Point)

### Generator Internals

- Maintains execution context manually
- Pauses at `yield`
- Resumes via `.next(value)`
- Works like a **state machine**

---

### Async Function Internals

- Built on **Promises + microtask queue**
- Compiler transforms it into a Promise chain

Rough equivalent:

```javascript
async function fn() {
  const a = await 10;
  return a;
}
```

becomes:

```javascript
function fn() {
  return Promise.resolve(10).then((a) => a);
}
```

👉 async/await is essentially **syntax sugar over Promises**

---

## 7. Key Differences Table

| Feature        | Generator                  | Async Function      |
| -------------- | -------------------------- | ------------------- |
| Declaration    | `function*`                | `async function`    |
| Return type    | Iterator                   | Promise             |
| Control flow   | Manual (`next()`)          | Automatic (`await`) |
| Async support  | Indirect                   | Native              |
| Error handling | `.throw()`                 | `try/catch`         |
| Execution      | Pausable function          | Async state machine |
| Use case       | Iteration, lazy evaluation | Async programming   |

---

## 8. Use Cases

### Generators are used for:

- Lazy evaluation (infinite sequences)
- Custom iterators
- Complex control flow
- State machines

```javascript
function* infinite() {
  let i = 0;
  while (true) yield i++;
}
```

---

### Async functions are used for:

- API calls
- File/network operations
- Database queries
- Any Promise-based workflow

```javascript
async function getUser() {
  const res = await fetch("/api/user");
  return res.json();
}
```

---

## 9. Important Conceptual Insight

👉 Generators = **control flow mechanism**
👉 Async functions = **asynchronous programming abstraction**

Even though both “pause execution,” they are fundamentally different:

- Generators pause for **iteration control**
- Async functions pause for **Promise resolution**

---

## 10. Interview-Ready Summary

Generators and async functions both allow pausing and resuming execution, but they serve different purposes. Generators return iterators and require manual control using `.next()`, making them suitable for custom iteration and lazy evaluation. Async functions always return Promises and automatically handle asynchronous flow using `await`, making them the modern standard for async programming in JavaScript. Internally, async/await is built on Promises and microtasks, while generators operate as state machines controlled by the developer.

## Question 6. How to use async iterators

### Short Answer

**Async iterators** let you iterate over asynchronous data sources (like streams, APIs, or timed events) using `for await...of`. They are built using `Symbol.asyncIterator` and `async function*`.

---

# Async Iterators in JavaScript (Interview-Ready Explanation)

## 1. What is an Async Iterator?

An **async iterator** is an object that:

- Produces values asynchronously
- Returns a **Promise for each value**
- Works with `for await...of`

It follows this contract:

```javascript id="ai1"
{
  next(): Promise<{ value, done }>
}
```

---

## 2. Basic Syntax using `async function*`

The easiest way to create an async iterator is using an **async generator function**.

```javascript id="ai2"
async function* asyncGenerator() {
  yield 1;
  yield 2;
  yield 3;
}
```

Each `yield` can also be asynchronous.

---

## 3. Consuming Async Iterators (`for await...of`)

```javascript id="ai3"
async function run() {
  const gen = asyncGenerator();

  for await (const value of gen) {
    console.log(value);
  }
}

run();
```

### Output:

```text id="ai3out"
1
2
3
```

👉 `for await...of` waits for each Promise to resolve before continuing.

---

## 4. Real Async Example (Simulating API calls)

```javascript id="ai4"
function wait(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function* fetchPages() {
  for (let i = 1; i <= 3; i++) {
    await wait(1000);
    yield `Page ${i}`;
  }
}

async function run() {
  for await (const page of fetchPages()) {
    console.log(page);
  }
}

run();
```

### Output (1 second delay each):

```
Page 1
Page 2
Page 3
```

---

## 5. How Async Iterators Work Internally

An async iterator is basically:

```javascript id="ai5"
{
  next() {
    return Promise.resolve({ value: 1, done: false });
  }
}
```

But when using `async function*`, JavaScript:

- Wraps each `yield` in a Promise
- Pauses execution until the Promise resolves
- Resumes automatically

---

## 6. Manual Async Iterator Implementation

You can implement one without generators:

```javascript id="ai6"
const asyncIterable = {
  data: [1, 2, 3],

  [Symbol.asyncIterator]() {
    let index = 0;
    const data = this.data;

    return {
      next() {
        if (index < data.length) {
          return Promise.resolve({
            value: data[index++],
            done: false,
          });
        }

        return Promise.resolve({ done: true });
      },
    };
  },
};

async function run() {
  for await (const value of asyncIterable) {
    console.log(value);
  }
}

run();
```

---

## 7. Key Concepts

### 1. `Symbol.asyncIterator`

Defines async iteration behavior.

```javascript id="ai7"
obj[Symbol.asyncIterator] = function () {
  return asyncIterator;
};
```

---

### 2. `for await...of`

Used to consume async iterables.

```javascript id="ai8"
for await (const item of asyncIterable) {
  console.log(item);
}
```

---

## 8. Async Iterator vs Sync Iterator

| Feature     | Iterator          | Async Iterator             |
| ----------- | ----------------- | -------------------------- |
| Method      | `next()`          | `next()`                   |
| Return      | `{ value, done }` | `Promise<{ value, done }>` |
| Loop        | `for...of`        | `for await...of`           |
| Data source | Sync data         | Async data (API, streams)  |

---

## 9. Real-World Use Cases

### 1. Streaming APIs

- Reading chunks of data
- File streams (Node.js)

### 2. Pagination APIs

```javascript id="ai9"
async function* getPages() {
  let page = 1;

  while (page <= 3) {
    const res = await fetch(`/api?page=${page}`);
    const data = await res.json();

    yield data;
    page++;
  }
}
```

---

### 3. Real-time data (WebSockets, events)

```javascript id="ai10"
async function* socketStream(socket) {
  while (true) {
    const msg = await new Promise((resolve) => {
      socket.onmessage = (e) => resolve(e.data);
    });

    yield msg;
  }
}
```

---

## 10. Common Pitfalls (Interview Focus)

### ❌ Using `for...of` with async iterators

```javascript id="ai11"
for (const value of asyncGenerator()) {
  console.log(value); // WRONG
}
```

### ✔ Correct:

```javascript id="ai12"
for await (const value of asyncGenerator()) {
  console.log(value);
}
```

---

### ❌ Forgetting async in generator

```javascript id="ai13"
function* gen() {
  yield fetch("/api"); // returns Promise, not awaited
}
```

---

## 11. Relationship with Promises

Async iterators are basically:

- Promises for sequences of values
- Not just one async result, but multiple over time

---

## 12. Interview-Ready Summary

Async iterators in JavaScript allow you to work with asynchronous sequences of data using `for await...of`. They are created using `async function*` or objects implementing `Symbol.asyncIterator`. Each `yield` produces a Promise-resolved value, enabling sequential asynchronous processing. They are commonly used for streams, paginated APIs, and real-time data. Internally, async iterators combine generator-based pausing with Promise-based resolution, making them ideal for handling asynchronous data flows cleanly and efficiently.

## Question 7. What is Proxy in JavaScript? Give use case

## Question 8. What is Reflect API?

## Question 9. How to create private fields in JS classes

## Question 10. What are decorators?

## Question 11. Explain the concept of Symbol.iterator

## Question 12. Difference between deep copy using structuredClone vs JSON methods

## Question 13. What are Tagged Template Literals?

## Question 14. How to implement a simple event emitter in JavaScript

## Question 15. Explain JS memory model: stack vs heap

## Question 16. Difference between shallow equality and deep equality

## Question 17. How to optimize performance of large array operations

## Question 18. Explain the difference between classical async patterns (callbacks) vs promises vs async/await

## Question 19. What are Web Workers? How do they work?

## Question 20. How to prevent race conditions in asynchronous JS code
