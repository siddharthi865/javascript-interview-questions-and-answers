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

## Question 5. How to chain promises sequentially

## Question 6. How to handle errors in promise chains

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
