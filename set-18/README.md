# Set 18

| S.No. | Question                                                                                                                                                         |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to implement a debounce function from scratch](#question-1-how-to-implement-a-debounce-function-from-scratch)                                               |
| 2.    | [How to implement a throttle function from scratch](#question-2-how-to-implement-a-throttle-function-from-scratch)                                               |
| 3.    | [Difference between `requestAnimationFrame` and `setTimeout` for animations](#question-3-difference-between-requestanimationframe-and-settimeout-for-animations) |
| 4.    | [How to implement a promise-based delay function](#question-4-how-to-implement-a-promise-based-delay-function)                                                   |
| 5.    | [How to chain promises sequentially](#question-5-how-to-chain-promises-sequentially)                                                                             |
| 6.    | [How to handle errors in promise chains](#question-6-how-to-handle-errors-in-promise-chains)                                                                     |
| 7.    | [Difference between fetch API and XMLHttpRequest](#question-7-difference-between-fetch-api-and-xmlhttprequest)                                                   |
| 8.    | [How to cancel a fetch request using AbortController](#question-8-how-to-cancel-a-fetch-request-using-abortcontroller)                                           |
| 9.    | [How to upload files using JavaScript](#question-9-how-to-upload-files-using-javascript)                                                                         |
| 10.   | [How to track upload progress using JavaScript](#question-10-how-to-track-upload-progress-using-javascript)                                                      |
| 11.   | [Difference between `localStorage` and `IndexedDB`](#question-11-difference-between-localstorage-and-indexeddb)                                                  |
| 12.   | [How to store complex objects in `localStorage`](#question-12-how-to-store-complex-objects-in-localstorage)                                                      |
| 13.   | [How to use `sessionStorage` effectively](#question-13-how-to-use-sessionstorage-effectively)                                                                    |
| 14.   | [How to implement a simple cache using Map](#question-14-how-to-implement-a-simple-cache-using-map)                                                              |
| 15.   | [How to detect memory leaks in a web application](#question-15-how-to-detect-memory-leaks-in-a-web-application)                                                  |
| 16.   | [How to implement a simple pub/sub system](#question-16-how-to-implement-a-simple-pubsub-system)                                                                 |
| 17.   | [How to implement event delegation efficiently](#question-17-how-to-implement-event-delegation-efficiently)                                                      |
| 18.   | [How to create a chainable API in JavaScript](#question-18-how-to-create-a-chainable-api-in-javascript)                                                          |
| 19.   | [How to create custom events and dispatch them](#question-19-how-to-create-custom-events-and-dispatch-them)                                                      |
| 20.   | [How to implement a singleton pattern in JavaScript](#question-20-how-to-implement-a-singleton-pattern-in-javascript)                                            |

## Question 1. How to implement a debounce function from scratch

A **debounce function** limits how often a function executes by delaying its execution until a certain amount of time has passed since the last call.

It’s commonly used for:

- Search input suggestions
- Window resize events
- Scroll handlers
- API calls triggered by typing

### Basic Debounce Implementation

```javascript
function debounce(fn, delay) {
  let timer;

  return function (...args) {
    clearTimeout(timer);

    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}
```

#### How It Works

Every time the returned function is called:

1. Previous timer is cleared
2. A new timer starts
3. If no new call happens within `delay` milliseconds:
   - original function executes

This ensures the function runs **only after the user stops triggering the event**.

#### Example: Search Input

```javascript
function search(query) {
  console.log("Searching for:", query);
}

const debouncedSearch = debounce(search, 500);

debouncedSearch("j");
debouncedSearch("ja");
debouncedSearch("jav");
debouncedSearch("java");
```

Only the final call executes after 500ms:

```javascript
Searching for: java
```

#### Real DOM Example

```javascript
const input = document.getElementById("search");

input.addEventListener(
  "keyup",
  debounce(function (e) {
    console.log("API Call:", e.target.value);
  }, 300),
);
```

Without debounce:

- API called on every keystroke

With debounce:

- API called only after typing pauses

#### Why `clearTimeout()` Is Important

```javascript
clearTimeout(timer);
```

This cancels the previous scheduled execution.

Without it:

- multiple timers would execute
- debounce behavior would fail

#### Why Use `fn.apply(this, args)`?

```javascript
fn.apply(this, args);
```

This preserves:

- correct `this`
- all arguments

Example:

```javascript
const obj = {
  name: "John",
  greet: debounce(function () {
    console.log(this.name);
  }, 300),
};

obj.greet();
```

Using `apply` ensures `this.name` remains `"John"`.

#### Modern Arrow Function Version

```javascript
const debounce = (fn, delay) => {
  let timer;

  return (...args) => {
    clearTimeout(timer);

    timer = setTimeout(() => {
      fn(...args);
    }, delay);
  };
};
```

This works well in many cases, but note:

- arrow functions do NOT have their own `this`
- may not work correctly for object methods

#### Advanced Debounce (Leading + Trailing)

Interviewers often ask for a more complete implementation.

```javascript
function debounce(fn, delay, immediate = false) {
  let timer;

  return function (...args) {
    const callNow = immediate && !timer;

    clearTimeout(timer);

    timer = setTimeout(() => {
      timer = null;

      if (!immediate) {
        fn.apply(this, args);
      }
    }, delay);

    if (callNow) {
      fn.apply(this, args);
    }
  };
}
```

### Behavior Modes

#### Trailing Debounce (default)

Executes after user stops triggering.

```javascript
debounce(fn, 300);
```

Use case:

- search bars
- autosave

#### Leading Debounce

Executes immediately once, then ignores calls until delay ends.

```javascript
debounce(fn, 300, true);
```

Use case:

- preventing double-click submissions

### Debounce vs Throttle

| Feature   | Debounce         | Throttle                  |
| --------- | ---------------- | ------------------------- |
| Execution | After delay ends | At fixed intervals        |
| Best For  | Typing/search    | Scroll/resize/game events |
| Frequency | Less frequent    | Regular intervals         |

### Common Interview Follow-Up

#### Q: Where is `timer` stored?

Inside a **closure**.

```javascript
let timer;
```

The returned function remembers `timer` even after `debounce()` execution finishes.

This is a classic JavaScript closure interview topic.

### Event Loop Perspective

`setTimeout` places callback execution into:

- Web APIs
- then callback queue
- then event loop processes it

Debounce relies heavily on JavaScript’s asynchronous event loop model.

### Common Mistakes

#### 1. Forgetting `clearTimeout`

```javascript
// Wrong
setTimeout(fn, delay);
```

Creates multiple executions.

#### 2. Losing `this`

```javascript
fn(args);
```

May break object methods.

Prefer:

```javascript
fn.apply(this, args);
```

#### 3. Recreating Debounce Repeatedly

Wrong:

```javascript
input.addEventListener("keyup", debounce(fn, 300));
```

inside loops or rerenders repeatedly.

This creates new timers every render.

### Time Complexity

- Each call:
  - `clearTimeout`: O(1)
  - `setTimeout`: O(1)

Overall:

- **Time Complexity:** O(1)
- **Space Complexity:** O(1)

### Production Notes

Libraries like [Lodash](https://lodash.com?utm_source=chatgpt.com) provide highly optimized debounce utilities:

```javascript
_.debounce(fn, 300);
```

They support:

- cancel
- flush
- leading/trailing options
- max wait time

### Summary

A debounce function delays execution until repeated calls stop for a specified delay period. It is implemented using:

- closures
- `setTimeout`
- `clearTimeout`

It helps optimize performance by preventing unnecessary repeated function executions, especially in high-frequency events like typing, scrolling, and resizing.

## Question 2. How to implement a throttle function from scratch

A **throttle function** ensures that a function is executed at most once in a specified time interval, no matter how many times it is triggered.

---

# ✅ Direct Answer

A throttle function limits execution by allowing a function to run **once per fixed time window**, ignoring intermediate calls.

---

# 🧠 Core Idea (Interview Explanation)

Unlike debounce (which waits for “quiet time”), throttle ensures:

> “Execute immediately, then ignore subsequent calls until the cooldown period ends.”

This is useful for:

- scroll events
- resize handlers
- mouse move tracking
- game loops / animations
- analytics tracking

---

# 🧩 Basic Throttle Implementation (Time-Based)

```javascript
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
```

---

# 🧠 How It Works

Every call:

1. Current timestamp is checked
2. Compare with `lastCall`
3. If enough time passed:
   - execute function
   - update `lastCall`

4. Otherwise ignore call

---

# 📌 Example Usage

```javascript
function onScroll() {
  console.log("Scroll event fired");
}

const throttledScroll = throttle(onScroll, 1000);

window.addEventListener("scroll", throttledScroll);
```

Even if scroll fires 100 times per second:

👉 Function runs only once per 1 second

---

# ⚙️ Alternative Implementation (Using setTimeout)

This version is often asked in interviews because it shows deeper async understanding:

```javascript
function throttle(fn, limit) {
  let inThrottle = false;

  return function (...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;

      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}
```

---

# 🧠 Key Difference Between Two Approaches

| Approach        | Mechanism                | Precision     |
| --------------- | ------------------------ | ------------- |
| timestamp-based | compares time difference | more accurate |
| timeout-based   | locks execution window   | simpler       |

---

# 🔥 Advanced Throttle (Leading + Trailing Edge)

Real-world throttle often supports both:

```javascript
function throttle(fn, limit) {
  let lastCall = 0;
  let timeout;

  return function (...args) {
    const now = Date.now();
    const remaining = limit - (now - lastCall);

    if (remaining <= 0) {
      if (timeout) {
        clearTimeout(timeout);
        timeout = null;
      }

      lastCall = now;
      fn.apply(this, args);
    } else if (!timeout) {
      timeout = setTimeout(() => {
        lastCall = Date.now();
        timeout = null;
        fn.apply(this, args);
      }, remaining);
    }
  };
}
```

---

# 🧠 What This Version Solves

It ensures:

- Immediate execution (leading edge)
- Guaranteed final execution (trailing edge)
- No event loss during high-frequency triggers

---

# ⚖️ Throttle vs Debounce (Critical Interview Concept)

| Feature           | Throttle          | Debounce          |
| ----------------- | ----------------- | ----------------- |
| Execution         | Regular intervals | After inactivity  |
| Frequency control | Fixed rate        | Reset timer       |
| Use case          | scroll, resize    | search input      |
| Behavior          | “Run every X ms”  | “Run after pause” |

---

# 🧪 Real Example Comparison

### Scroll (Throttle)

```javascript
window.addEventListener(
  "scroll",
  throttle(() => console.log("scroll"), 500),
);
```

Runs every 500ms while scrolling.

---

### Search (Debounce)

```javascript
input.addEventListener(
  "input",
  debounce((e) => console.log(e.target.value), 300),
);
```

Runs only after user stops typing.

---

# ⚠️ Common Pitfalls

## 1. Using Date incorrectly

```javascript
const now = new Date(); // ❌ avoid
```

Better:

```javascript
Date.now(); // ✅ faster and cleaner
```

---

## 2. Losing `this`

Always use:

```javascript
fn.apply(this, args);
```

Otherwise methods inside objects break.

---

## 3. Creating throttle inside render loops (React issue)

Bad:

```javascript
onScroll={throttle(fn, 200)}
```

This recreates throttle every render.

Fix:

```javascript
const throttledFn = useMemo(() => throttle(fn, 200), []);
```

---

# 🧠 Closure Concept (Important Interview Insight)

Throttle relies on closure:

```javascript
let lastCall = 0;
```

This variable persists between calls because:

- returned function forms a closure
- retains access to outer scope variables

---

# ⚙️ Event Loop Perspective

- Function call → runs synchronously
- `setTimeout` → moves callback to Web API
- Callback → enters task queue
- Event loop executes when stack is empty

Throttle is basically controlling how often tasks enter execution.

---

# 📊 Complexity

- Time complexity: **O(1) per call**
- Space complexity: **O(1)**

---

# 🚀 Interview-Ready Summary

A throttle function limits execution of a function to once every fixed interval. It uses timestamps or timers to ensure controlled execution during high-frequency events.

Key concepts involved:

- Closures
- `Date.now()`
- `setTimeout`
- Function context (`this`)
- Event loop behavior

## Question 3. Difference between `requestAnimationFrame` and `setTimeout` for animations

## ✅ Direct Answer

`requestAnimationFrame` is the **preferred API for animations** because it synchronizes updates with the browser’s paint cycle for smooth rendering. `setTimeout` runs code on a fixed timer, which is not aligned with rendering and can cause jank, wasted frames, or inconsistent animations.

---

# 🧠 Interview-Level Explanation

Both `requestAnimationFrame` and `setTimeout` can be used to create animations, but they differ fundamentally in **how and when they execute relative to the browser rendering pipeline**.

---

# 🎬 1. `requestAnimationFrame` (rAF)

### ✔ Key idea:

> “Run this function just before the next repaint.”

### Syntax:

```javascript
requestAnimationFrame(callback);
```

### Example animation:

```javascript
let x = 0;

function animate() {
  x += 2;
  document.getElementById("box").style.transform = `translateX(${x}px)`;

  requestAnimationFrame(animate);
}

requestAnimationFrame(animate);
```

---

## 🧠 How it works internally

- Browser has a render loop (~60fps typical)
- Each frame:
  1. Run JS callbacks (rAF)
  2. Recalculate styles/layout
  3. Paint

- rAF executes **right before repaint**

So animation is:

> synchronized with screen refresh rate

---

## ✅ Advantages of rAF

- Smooth animations (no frame drops)
- Automatically pauses when tab is inactive (battery-friendly)
- Avoids unnecessary rendering
- Syncs with 60Hz / 120Hz displays

---

## ❌ Limitations

- Not precise timing control (frame-based, not time-based)
- Cannot guarantee exact delays like timers

---

# ⏱ 2. `setTimeout` for animations

### ✔ Key idea:

> “Run this function after at least X milliseconds.”

### Example:

```javascript
let x = 0;

function animate() {
  x += 2;
  document.getElementById("box").style.left = x + "px";

  setTimeout(animate, 16); // ~60fps
}

setTimeout(animate, 16);
```

---

## 🧠 How it works internally

- Runs in the **task queue**
- No awareness of rendering cycle
- Timing is approximate, not exact

---

## ❌ Problems with setTimeout animations

### 1. Frame drift

If execution takes time:

```text
expected: every 16ms
actual: 16ms + JS execution + queue delay
```

➡ leads to jittery animation

---

### 2. Layout thrashing risk

Can trigger updates mid-render cycle

---

### 3. Not synced with refresh rate

- Monitor may be 60Hz, 120Hz, 144Hz
- `setTimeout` does not adapt

---

# ⚖️ Key Differences (Interview Table)

| Feature                 | requestAnimationFrame | setTimeout               |
| ----------------------- | --------------------- | ------------------------ |
| Sync with browser paint | ✅ Yes                | ❌ No                    |
| Frame rate control      | Adaptive              | Manual (e.g. 16ms)       |
| Smoothness              | High                  | Inconsistent             |
| CPU efficiency          | High                  | Lower                    |
| Background tab behavior | Paused                | Continues                |
| Use case                | animations            | simple delays / fallback |

---

# 🧠 Event Loop Perspective

## setTimeout

- Goes to Web APIs → Task Queue
- Runs when call stack is free
- Not tied to rendering pipeline

## requestAnimationFrame

- Goes into **render phase callback queue**
- Executed **before paint step**

👉 This is the key interview insight:

> rAF is part of the rendering lifecycle, setTimeout is not.

---

# 🎯 When to Use What

## Use `requestAnimationFrame` when:

- DOM animations
- Canvas animations
- Smooth transitions
- Game loops
- Scroll-based effects

## Use `setTimeout` when:

- Delays unrelated to rendering
- Retry logic
- Debounce/throttle fallback
- Simple timing tasks

---

# 🔥 Real-world Example Comparison

## ❌ Bad animation (setTimeout)

```javascript
function move() {
  box.style.transform = "translateX(200px)";
  setTimeout(move, 16);
}
move();
```

Problem:

- can drift
- not synced with paint

---

## ✅ Good animation (rAF)

```javascript
function move() {
  box.style.transform = "translateX(200px)";
  requestAnimationFrame(move);
}
requestAnimationFrame(move);
```

Smooth and consistent.

---

# 🧠 Advanced Insight (FAANG-level)

## Why rAF is smoother:

Because browser can:

- batch DOM updates
- skip frames if overloaded
- align with GPU compositing

## Bonus: FPS control with rAF

```javascript
let last = 0;
const fps = 30;
const interval = 1000 / fps;

function animate(time) {
  if (time - last >= interval) {
    last = time;
    // update frame
  }

  requestAnimationFrame(animate);
}

requestAnimationFrame(animate);
```

---

# ⚠️ Common Interview Pitfalls

### 1. Thinking setTimeout = 60fps

Wrong. It is only an approximation.

---

### 2. Using rAF for non-visual logic

Not ideal for:

- API calls
- heavy computation loops

---

### 3. Forgetting cancellation

```javascript
cancelAnimationFrame(id);
```

---

# 🚀 Interview Summary

`requestAnimationFrame` is a browser-optimized animation API that runs callbacks before repaint, ensuring smooth and efficient animations. `setTimeout` is a general-purpose timer that is not synchronized with rendering, making it less reliable for animations.

👉 In modern frontend engineering:

> Always prefer `requestAnimationFrame` for animations, and use `setTimeout` only for timing logic unrelated to rendering.

## Question 4. How to implement a promise-based delay function

## ✅ Direct Answer

A promise-based delay function returns a Promise that resolves after a specified amount of time, allowing it to be used with `.then()` or `async/await`.

```javascript
function delay(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
```

---

# 🧠 Interview Explanation

The idea is simple:

1. Create a Promise.
2. Use `setTimeout`.
3. Resolve the Promise when the timer expires.

This converts a callback-based API (`setTimeout`) into a Promise-based API.

```javascript
function delay(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
```

---

# Using with `.then()`

```javascript
console.log("Start");

delay(2000).then(() => {
  console.log("2 seconds later");
});

console.log("End");
```

### Output

```javascript
Start
End
2 seconds later
```

Explanation:

- `delay(2000)` creates a pending Promise.
- `setTimeout` schedules the resolution after 2 seconds.
- Synchronous code finishes first.
- Promise resolves later.

---

# Using with `async/await`

This is the most common usage.

```javascript
function delay(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function run() {
  console.log("Start");

  await delay(2000);

  console.log("After 2 seconds");
}

run();
```

### Output

```javascript
Start
After 2 seconds
```

---

# Returning a Value After Delay

Sometimes interviewers ask:

> Can delay resolve with a value?

Yes.

```javascript
function delay(ms, value) {
  return new Promise((resolve) => {
    setTimeout(() => resolve(value), ms);
  });
}
```

Usage:

```javascript
delay(1000, "Hello").then(console.log);
```

Output:

```javascript
Hello;
```

---

# Async/Await Example with Returned Value

```javascript
async function main() {
  const result = await delay(1000, 42);

  console.log(result);
}

main();
```

Output:

```javascript
42;
```

---

# Delay Inside Sequential Async Operations

```javascript
async function process() {
  console.log("Step 1");

  await delay(1000);

  console.log("Step 2");

  await delay(1000);

  console.log("Step 3");
}

process();
```

Output:

```javascript
Step 1
(wait 1s)
Step 2
(wait 1s)
Step 3
```

---

# Real-World Use Cases

### Retry Logic

```javascript
async function retry() {
  try {
    return await fetchData();
  } catch {
    await delay(1000);
    return retry();
  }
}
```

---

### Rate Limiting

```javascript
for (const user of users) {
  await processUser(user);
  await delay(500);
}
```

---

### Simulating Network Latency

```javascript
async function fakeApi() {
  await delay(1500);

  return { success: true };
}
```

---

# Advanced Version: Reject After Delay

Sometimes you want a Promise that fails after a timeout.

```javascript
function timeout(ms) {
  return new Promise((_, reject) => {
    setTimeout(() => {
      reject(new Error("Timeout"));
    }, ms);
  });
}
```

Usage:

```javascript
timeout(2000).catch((err) => console.log(err.message));
```

Output:

```javascript
Timeout;
```

---

# Combining Delay with Promise.race

A very common senior-level interview pattern:

```javascript
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function fetchWithTimeout(fetchPromise, ms) {
  return Promise.race([
    fetchPromise,
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Timeout")), ms),
    ),
  ]);
}
```

This prevents operations from hanging indefinitely.

---

# Event Loop Explanation

Consider:

```javascript
delay(1000).then(() => {
  console.log("Done");
});
```

What happens?

1. Promise is created.
2. `setTimeout` is registered in Web APIs.
3. After 1 second, timer callback enters the task queue.
4. Callback executes and calls `resolve()`.
5. Promise resolution queues a microtask.
6. Microtask runs before the next macrotask.
7. `"Done"` is printed.

This demonstrates the interaction between:

- `setTimeout` (macrotask)
- Promise resolution (microtask)
- Event Loop

---

# Common Interview Pitfalls

### ❌ Forgetting to return the Promise

```javascript
function delay(ms) {
  new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
```

Returns `undefined`.

---

### ❌ Calling resolve immediately

```javascript
setTimeout(resolve(), ms);
```

This executes `resolve()` instantly.

Correct:

```javascript
setTimeout(resolve, ms);
```

or

```javascript
setTimeout(() => resolve(), ms);
```

---

### ❌ Blocking Sleep

JavaScript has no true synchronous sleep in normal execution.

Bad idea:

```javascript
while (Date.now() < target) {}
```

This blocks the event loop.

Use Promise-based delay instead.

---

# Time & Space Complexity

For each call:

- Time Complexity: **O(1)**
- Space Complexity: **O(1)**

(The timer itself is managed by the runtime.)

---

# Interview-Ready Summary

A promise-based delay function wraps `setTimeout` inside a Promise:

```javascript
function delay(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
```

It is commonly used with `async/await` to pause asynchronous workflows without blocking the event loop. The implementation demonstrates understanding of Promises, `setTimeout`, asynchronous programming, and the JavaScript event loop.

## Question 5. How to chain promises sequentially

## ✅ Direct Answer

To execute promises **sequentially**, each promise must be started only after the previous one resolves. This is done by returning a promise from `.then()` callbacks or by using `async/await`.

```javascript
task1()
  .then(() => task2())
  .then(() => task3())
  .then(() => console.log("Done"));
```

Or:

```javascript
await task1();
await task2();
await task3();
```

---

# 🧠 What Does "Sequentially" Mean?

Sequential execution means:

```text
Task 1 finishes
     ↓
Task 2 starts
     ↓
Task 3 starts
```

Not:

```text
Task 1 starts
Task 2 starts
Task 3 starts
```

(which is concurrent execution)

---

# Basic Promise Chaining

Suppose:

```javascript
function fetchUser() {
  return Promise.resolve("User");
}

function fetchOrders() {
  return Promise.resolve("Orders");
}

function fetchPayments() {
  return Promise.resolve("Payments");
}
```

Sequential chain:

```javascript
fetchUser()
  .then((user) => {
    console.log(user);
    return fetchOrders();
  })
  .then((orders) => {
    console.log(orders);
    return fetchPayments();
  })
  .then((payments) => {
    console.log(payments);
  });
```

Output:

```javascript
User;
Orders;
Payments;
```

---

# Why Returning the Promise Matters

Correct:

```javascript
task1()
  .then(() => {
    return task2();
  })
  .then(() => {
    return task3();
  });
```

Incorrect:

```javascript
task1()
  .then(() => {
    task2();
  })
  .then(() => {
    task3();
  });
```

Problem:

```text
task2() starts
next .then() executes immediately
```

because nothing was returned.

This is one of the most common interview mistakes.

---

# Sequential Delays Example

```javascript
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

delay(1000)
  .then(() => {
    console.log("1");
    return delay(1000);
  })
  .then(() => {
    console.log("2");
    return delay(1000);
  })
  .then(() => {
    console.log("3");
  });
```

Output:

```javascript
1  (after 1s)
2  (after 2s)
3  (after 3s)
```

---

# Sequential Chaining with async/await

Modern JavaScript usually prefers:

```javascript
async function run() {
  await task1();
  await task2();
  await task3();

  console.log("Done");
}

run();
```

This is functionally equivalent to promise chaining but easier to read.

---

# Chaining Dynamic Tasks

Interviewers often ask:

> "What if you have an array of async functions?"

Example:

```javascript
const tasks = [
  () => Promise.resolve(1),
  () => Promise.resolve(2),
  () => Promise.resolve(3),
];
```

Sequential execution:

```javascript
tasks.reduce((promise, task) => {
  return promise.then(() => task());
}, Promise.resolve());
```

Output:

```javascript
1;
2;
3;
```

---

# Collecting Results Sequentially

```javascript
const tasks = [
  () => Promise.resolve("A"),
  () => Promise.resolve("B"),
  () => Promise.resolve("C"),
];

async function runSequentially() {
  const results = [];

  for (const task of tasks) {
    results.push(await task());
  }

  return results;
}

runSequentially().then(console.log);
```

Output:

```javascript
["A", "B", "C"];
```

---

# Sequential Execution Using reduce()

A classic FAANG interview question:

```javascript
const tasks = [1, 2, 3];

tasks.reduce((chain, num) => {
  return chain.then(() => {
    console.log(num);
  });
}, Promise.resolve());
```

Output:

```javascript
1;
2;
3;
```

### How it works

Iteration 1:

```javascript
Promise.resolve().then(() => console.log(1));
```

Iteration 2:

```javascript
previousPromise.then(() => console.log(2));
```

Iteration 3:

```javascript
previousPromise.then(() => console.log(3));
```

The chain grows sequentially.

---

# Sequential vs Parallel

### Sequential

```javascript
await task1();
await task2();
await task3();
```

Time:

```text
T1 + T2 + T3
```

---

### Parallel

```javascript
await Promise.all([task1(), task2(), task3()]);
```

Time:

```text
max(T1, T2, T3)
```

Example:

```javascript
// Sequential: ~3 seconds
await delay(1000);
await delay(1000);
await delay(1000);

// Parallel: ~1 second
await Promise.all([delay(1000), delay(1000), delay(1000)]);
```

---

# Error Handling in Sequential Chains

Using `.catch()`:

```javascript
task1()
  .then(() => task2())
  .then(() => task3())
  .catch((err) => {
    console.error(err);
  });
```

If `task2()` rejects:

```text
task3() never runs
catch executes
```

---

# Error Handling with async/await

```javascript
async function run() {
  try {
    await task1();
    await task2();
    await task3();
  } catch (err) {
    console.error(err);
  }
}
```

---

# Advanced: Continue Even if One Fails

```javascript
for (const task of tasks) {
  try {
    await task();
  } catch (err) {
    console.error("Failed:", err);
  }
}
```

This allows later tasks to continue.

---

# Event Loop Perspective

When a promise resolves:

```javascript
promise.then(...)
```

the callback is placed in the **microtask queue**.

Example:

```javascript
Promise.resolve()
  .then(() => console.log("A"))
  .then(() => console.log("B"))
  .then(() => console.log("C"));
```

Output:

```javascript
A;
B;
C;
```

Each `.then()` waits for the previous promise to settle before scheduling the next microtask.

---

# Common Interview Pitfalls

### ❌ Starting all promises immediately

```javascript
const p1 = task1();
const p2 = task2();
const p3 = task3();

await p1;
await p2;
await p3;
```

These run concurrently, not sequentially.

---

### ❌ Forgetting `return`

```javascript
.then(() => {
  task2();
})
```

Must be:

```javascript
.then(() => {
  return task2();
})
```

or simply:

```javascript
.then(() => task2())
```

---

### ❌ Using `forEach` with async

```javascript
tasks.forEach(async (task) => {
  await task();
});
```

`forEach` does not wait.

Use:

```javascript
for (const task of tasks) {
  await task();
}
```

---

# 🚀 Interview-Ready Summary

Promises are chained sequentially by returning a promise from each `.then()` callback:

```javascript
task1()
  .then(() => task2())
  .then(() => task3());
```

or more commonly with `async/await`:

```javascript
await task1();
await task2();
await task3();
```

Key interview concepts include:

- Promise chaining
- Returning promises from `.then()`
- Microtask queue behavior
- Sequential vs parallel execution
- Error propagation
- Dynamic chaining with `reduce()` and `for...of`

Understanding these patterns is essential for building reliable asynchronous workflows in JavaScript.

## Question 6. How to handle errors in promise chains

## ✅ Direct Answer

Errors in promise chains are typically handled using `.catch()`. Any error thrown inside a `.then()` callback or any rejected promise in the chain will propagate down to the nearest `.catch()`.

```javascript
fetchData()
  .then((data) => processData(data))
  .then((result) => saveResult(result))
  .catch((error) => {
    console.error("Something went wrong:", error);
  });
```

---

# 🧠 How Error Propagation Works

A promise chain automatically propagates errors downward.

```javascript
Promise.resolve()
  .then(() => {
    throw new Error("Boom!");
  })
  .catch((err) => {
    console.log(err.message);
  });
```

### Output

```javascript
Boom!
```

When an error is thrown inside a `.then()`, JavaScript automatically converts it into a rejected promise.

Conceptually:

```javascript
.then(() => {
  throw new Error("Boom");
})
```

becomes:

```javascript
.then(() => {
  return Promise.reject(new Error("Boom"));
})
```

---

# Error from a Rejected Promise

```javascript
Promise.reject("Network Error")
  .then(() => {
    console.log("Won't run");
  })
  .catch((err) => {
    console.log(err);
  });
```

### Output

```javascript
Network Error
```

The chain skips all subsequent `.then()` handlers until a `.catch()` is found.

---

# Chain Skipping Behavior

```javascript
Promise.resolve()
  .then(() => {
    console.log("Step 1");
    throw new Error("Failed");
  })
  .then(() => {
    console.log("Step 2");
  })
  .then(() => {
    console.log("Step 3");
  })
  .catch((err) => {
    console.log("Caught:", err.message);
  });
```

### Output

```javascript
Step 1
Caught: Failed
```

`Step 2` and `Step 3` never execute.

---

# Recovering from Errors

A `.catch()` can recover by returning a value.

```javascript
Promise.resolve()
  .then(() => {
    throw new Error("Oops");
  })
  .catch((err) => {
    console.log("Recovered");
    return "default value";
  })
  .then((value) => {
    console.log(value);
  });
```

### Output

```javascript
Recovered
default value
```

The chain continues because `.catch()` returned a resolved value.

---

# Multiple Catch Blocks

```javascript
Promise.resolve()
  .then(() => {
    throw new Error("First Error");
  })
  .catch((err) => {
    console.log("Catch 1");
    throw err;
  })
  .catch((err) => {
    console.log("Catch 2");
  });
```

### Output

```javascript
Catch 1
Catch 2
```

The first catch rethrows the error, causing the next catch to handle it.

---

# Using `.then(success, failure)`

Promises support:

```javascript
promise.then(successHandler, errorHandler);
```

Example:

```javascript
Promise.reject("Failed").then(
  (value) => console.log(value),
  (err) => console.log("Handled:", err),
);
```

Output:

```javascript
Handled: Failed;
```

### Why `.catch()` is Preferred

Consider:

```javascript
Promise.resolve()
  .then(() => {
    throw new Error("Boom");
  })
  .then(
    (result) => console.log(result),
    (err) => console.log("Error:", err),
  );
```

The error occurs before this `.then()`, so behavior can become harder to reason about in larger chains.

Best practice:

```javascript
Promise.resolve()
  .then(...)
  .then(...)
  .catch(...);
```

---

# Error Handling with Async Operations

```javascript
function fetchUser() {
  return Promise.reject("User not found");
}

fetchUser()
  .then((user) => {
    console.log(user);
  })
  .catch((err) => {
    console.error(err);
  });
```

Output:

```javascript
User not found
```

---

# Centralized Error Handling

One of the biggest advantages of promises:

```javascript
task1()
  .then(() => task2())
  .then(() => task3())
  .then(() => task4())
  .catch((err) => {
    console.error("Chain failed:", err);
  });
```

A single `.catch()` handles failures from any previous step.

---

# Finally Block

`finally()` runs regardless of success or failure.

```javascript
Promise.resolve("Success").finally(() => {
  console.log("Cleanup");
});
```

Output:

```javascript
Cleanup;
```

Error case:

```javascript
Promise.reject("Failed")
  .catch((err) => console.log(err))
  .finally(() => console.log("Cleanup"));
```

Output:

```javascript
Failed;
Cleanup;
```

Useful for:

- closing connections
- hiding loaders
- releasing resources

---

# Error Handling with Async/Await

Promise chains:

```javascript
fetchData()
  .then((data) => processData(data))
  .catch((err) => console.error(err));
```

Equivalent:

```javascript
try {
  const data = await fetchData();
  const result = await processData(data);
} catch (err) {
  console.error(err);
}
```

This is the modern preferred approach.

---

# Common Interview Pitfalls

## 1. Forgetting to Return a Promise

Wrong:

```javascript
fetchData()
  .then(() => {
    saveData();
  })
  .catch(console.error);
```

If `saveData()` returns a promise and rejects, the rejection may not be properly chained.

Correct:

```javascript
fetchData()
  .then(() => {
    return saveData();
  })
  .catch(console.error);
```

Or:

```javascript
fetchData()
  .then(() => saveData())
  .catch(console.error);
```

---

## 2. Throwing Inside Catch

```javascript
Promise.reject("A").catch((err) => {
  throw new Error("B");
});
```

Creates a new rejected promise.

You need another catch:

```javascript
Promise.reject("A")
  .catch((err) => {
    throw new Error("B");
  })
  .catch((err) => {
    console.log(err.message);
  });
```

Output:

```javascript
B;
```

---

## 3. Swallowing Errors

```javascript
Promise.reject("Error").catch(() => {});
```

The error disappears completely.

Better:

```javascript
.catch(err => {
  console.error(err);
  throw err;
});
```

when propagation is needed.

---

## 4. Unhandled Promise Rejections

```javascript
Promise.reject("Failure");
```

No catch handler:

```text
UnhandledPromiseRejectionWarning
```

(Behavior varies by environment, but it should always be handled.)

---

# Event Loop Perspective

```javascript
Promise.reject("Error").catch((err) => console.log(err));
```

The `.catch()` callback is scheduled as a **microtask**.

Order:

```javascript
console.log("A");

Promise.reject("Error").catch(() => console.log("B"));

console.log("C");
```

Output:

```javascript
A;
C;
B;
```

because promise handlers execute after the current synchronous code finishes.

---

# 🚀 Interview-Ready Summary

Errors in promise chains are handled using `.catch()`. Any:

- thrown exception inside `.then()`
- rejected promise returned from `.then()`

automatically propagates down the chain until a `.catch()` is encountered.

```javascript
task1()
  .then(() => task2())
  .then(() => task3())
  .catch((err) => {
    console.error(err);
  });
```

Key concepts interviewers expect:

- Error propagation
- Rejected promises
- Chain skipping
- Recovery by returning values from `.catch()`
- `finally()`
- Unhandled promise rejections
- Relationship between promise handlers and the microtask queue
- Equivalent `try...catch` handling with `async/await`

## Question 7. Difference between fetch API and XMLHttpRequest

## Question 8. How to cancel a fetch request using AbortController

## Question 9. How to upload files using JavaScript

## Question 10. How to track upload progress using JavaScript

## Question 11. Difference between `localStorage` and `IndexedDB`

## Question 12. How to store complex objects in `localStorage`

## Question 13. How to use `sessionStorage` effectively

## Question 14. How to implement a simple cache using Map

## Question 15. How to detect memory leaks in a web application

## Question 16. How to implement a simple pub/sub system

## Question 17. How to implement event delegation efficiently

## Question 18. How to create a chainable API in JavaScript

## Question 19. How to create custom events and dispatch them

## Question 20. How to implement a singleton pattern in JavaScript
