# Set 16

| S.No. | Question                                                                                                                                                                                      |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [Difference between synchronous and asynchronous HTTP requests in JavaScript](#question-1-difference-between-synchronous-and-asynchronous-http-requests-in-javascript)                        |
| 2.    | [What are browser events and how are they handled?](#question-2-what-are-browser-events-and-how-are-they-handled)                                                                             |
| 3.    | [How does `addEventListener` differ from `onClick` or other inline handlers?](#question-3-how-does-addeventlistener-differ-from-onclick-or-other-inline-handlers)                             |
| 4.    | [Difference between passive and non-passive event listeners](#question-4-difference-between-passive-and-non-passive-event-listeners)                                                          |
| 5.    | [How does `stopImmediatePropagation` differ from `stopPropagation`?](#question-5-how-does-stopimmediatepropagation-differ-from-stoppropagation)                                               |
| 6.    | [How to check if an object has a property directly (not in prototype chain)](#question-6-how-to-check-if-an-object-has-a-property-directly-not-in-prototype-chain)                            |
| 7.    | [Difference between `Object.create()` and `{}` object literal](#question-7-difference-between-objectcreate-and--object-literal)                                                               |
| 8.    | [How to use `instanceof` operator in JavaScript](#question-8-how-to-use-instanceof-operator-in-javascript)                                                                                    |
| 9.    | [Difference between `hasOwnProperty()` and `in` operator](#question-9-difference-between-hasownproperty-and-in-operator)                                                                      |
| 10.   | [Difference between `function` and `async function`](#question-10-difference-between-function-and-async-function)                                                                             |
| 11.   | [How does `import()` differ from static `import`?](#question-11-how-does-import-differ-from-static-import)                                                                                    |
| 12.   | [How to handle multiple asynchronous tasks with `Promise.allSettled`](#question-12-how-to-handle-multiple-asynchronous-tasks-with-promiseallsettled)                                          |
| 13.   | [How to convert a NodeList to an array](#question-13-how-to-convert-a-nodelist-to-an-array)                                                                                                   |
| 14.   | [How to implement tab switching using JavaScript](#question-14-how-to-implement-tab-switching-using-javascript)                                                                               |
| 15.   | [How to implement a modal popup (dialog box) using JavaScript](#question-15-how-to-implement-a-modal-popup-dialog-box-using-javascript)                                                       |
| 16.   | [Difference between `position: relative` and `position: absolute` when manipulated with JS](#question-16-difference-between-position-relative-and-position-absolute-when-manipulated-with-js) |
| 17.   | [How to detect window resizing using JavaScript](#question-17-how-to-detect-window-resizing-using-javascript)                                                                                 |
| 18.   | [How to implement lazy loading images in JS](#question-18-how-to-implement-lazy-loading-images-in-js)                                                                                         |
| 19.   | [How to detect user device or browser using JS](#question-19-how-to-detect-user-device-or-browser-using-js)                                                                                   |
| 20.   | [How to detect online/offline status using JS](#question-20-how-to-detect-onlineoffline-status-using-js)                                                                                      |

## Question 1. Difference between synchronous and asynchronous HTTP requests in JavaScript

In JavaScript, **synchronous HTTP requests** block the execution of code until the request completes, while **asynchronous HTTP requests** allow the program to continue executing other code while waiting for the server response.

- **Synchronous** = blocking
- **Asynchronous** = non-blocking

Modern JavaScript strongly prefers **asynchronous requests** because synchronous requests freeze the UI and hurt performance.

### Synchronous HTTP Requests

A synchronous request waits for the response before moving to the next line of code.

#### Example using `XMLHttpRequest`

```javascript
const xhr = new XMLHttpRequest();

xhr.open("GET", "https://api.example.com/data", false); // false = synchronous
xhr.send();

console.log(xhr.responseText);

console.log("This runs after response is received");
```

#### How It Works

Execution flow:

1. Request is sent
2. JavaScript thread pauses
3. Browser waits for server response
4. Response arrives
5. Code execution resumes

During this waiting period:

- UI can freeze
- Buttons may become unresponsive
- Animations stop
- Browser may show “Page Unresponsive”

### Problems with Synchronous Requests

#### 1. Blocks the Main Thread

JavaScript in browsers runs mainly on a single thread.

A synchronous request blocks:

- rendering
- user interactions
- event handling

#### 2. Poor User Experience

If the network is slow:

- app freezes
- scrolling stops
- typing lags

#### 3. Deprecated in Modern Browsers

Synchronous `XMLHttpRequest` on the main thread is discouraged and deprecated in many scenarios.

MDN and browser vendors recommend avoiding it.

### Asynchronous HTTP Requests

Asynchronous requests do not block execution.

JavaScript continues running while the request is processed in the background.

#### Example with `XMLHttpRequest`

```javascript
const xhr = new XMLHttpRequest();

xhr.open("GET", "https://api.example.com/data", true); // true = async

xhr.onload = function () {
  console.log(xhr.responseText);
};

xhr.send();

console.log("This runs immediately");
```

#### Execution Flow

1. Request sent
2. JS continues executing
3. Browser handles network request separately
4. Response callback executes later

### Modern Async Approaches

Today, async HTTP requests are usually done using:

- `fetch()`
- Promises
- `async/await`

#### Example Using Fetch API

```javascript
fetch("https://api.example.com/data")
  .then((response) => response.json())
  .then((data) => {
    console.log(data);
  });

console.log("Runs before fetch completes");
```

#### Example Using async/await

```javascript
async function getData() {
  try {
    const response = await fetch("https://api.example.com/data");
    const data = await response.json();

    console.log(data);
  } catch (error) {
    console.error(error);
  }
}

getData();
```

Important interview point:

`await` pauses only the async function, not the entire JavaScript thread.

#### Event Loop Connection

Asynchronous requests rely heavily on the JavaScript event loop.

When an async request is made:

1. Browser APIs handle the network call
2. JS thread continues
3. Callback/Promise is queued when response arrives
4. Event loop executes it later

This is a core JavaScript interview concept.

### Key Differences

| Feature          | Synchronous     | Asynchronous          |
| ---------------- | --------------- | --------------------- |
| Blocking         | Yes             | No                    |
| UI Freeze        | Possible        | No                    |
| Execution        | Sequential wait | Continues immediately |
| Performance      | Poor            | Better                |
| User Experience  | Bad             | Good                  |
| Modern Usage     | Rare            | Standard              |
| Event Loop Usage | Minimal         | Essential             |

### Timing Example

#### Synchronous

```javascript
console.log("Start");

const xhr = new XMLHttpRequest();
xhr.open("GET", "/data", false);
xhr.send();

console.log("After Request");
```

Output occurs only after response:

```text
Start
(wait...)
After Request
```

#### Asynchronous

```javascript
console.log("Start");

fetch("/data").then(() => console.log("Response received"));

console.log("After Request");
```

Output:

```text
Start
After Request
Response received
```

### Real-World Analogy

#### Synchronous

Like standing at a restaurant counter waiting for food before doing anything else.

#### Asynchronous

Like ordering food, taking a buzzer, and continuing other work until notified.

### Common Follow-Ups

#### Why is async preferred in JavaScript?

Because JavaScript is single-threaded on the main UI thread, blocking operations hurt responsiveness.

#### Is `await` synchronous?

No.

It gives synchronous-looking syntax, but internally still works asynchronously using Promises and the event loop.

#### Can synchronous requests still be used?

Technically yes in some environments, but they are discouraged in browsers.

Node.js APIs are mostly asynchronous by design.

### Best Practices

#### Prefer

- `fetch`
- `async/await`
- Promise-based APIs

#### Avoid

```javascript
xhr.open("GET", url, false);
```

because it blocks execution.

### Summary

- Synchronous HTTP requests block the JavaScript thread until completion.
- Asynchronous requests allow non-blocking execution.
- Modern JavaScript applications use async requests with Promises and `async/await`.
- Async behavior works through browser APIs and the event loop.
- Synchronous requests are considered bad practice in frontend applications.

## Question 2. What are browser events and how are they handled?

## Direct Answer

**Browser events** are signals generated by the browser when something happens in the DOM or environment (like a click, key press, page load, network completion, etc.). They are handled using an **event-driven programming model**, where you register **event listeners (handlers)** that the browser invokes when the event occurs.

---

# 1. What Are Browser Events?

Browser events represent **user actions or system changes**, such as:

- Mouse events: `click`, `dblclick`, `mousemove`
- Keyboard events: `keydown`, `keyup`
- Form events: `submit`, `change`, `input`
- Window events: `load`, `resize`, `scroll`
- Clipboard events: `copy`, `paste`
- Network/UI events: `DOMContentLoaded`, `fetch` completion (via promises)

Example:

```javascript
button.addEventListener("click", () => {
  console.log("Button clicked!");
});
```

Here, `"click"` is the event, and the function is the handler.

---

# 2. How Browser Events Are Handled (Event Flow)

When an event occurs, the browser processes it in **three phases**:

## 1. Capturing Phase (Top → Down)

Event travels from the **window → document → parent elements → target element**

## 2. Target Phase

Event reaches the actual element that triggered it.

## 3. Bubbling Phase (Bottom → Up)

Event bubbles back from the target → parent → document → window

---

## Visual Flow

```
Window
  ↓ (capturing)
Document
  ↓
Parent Div
  ↓
Button (target)
  ↑ (bubbling)
Parent Div
  ↑
Document
  ↑
Window
```

---

# 3. Event Listener Registration

## Basic syntax

```javascript
element.addEventListener(event, handler, useCapture);
```

- `event`: event type (e.g., "click")
- `handler`: function to execute
- `useCapture`: optional boolean (default `false` → bubbling phase)

---

## Example (Bubbling – default)

```javascript
document.getElementById("child").addEventListener("click", () => {
  console.log("Child clicked");
});
```

---

## Example (Capturing phase)

```javascript
document.getElementById("parent").addEventListener(
  "click",
  () => {
    console.log("Parent capturing");
  },
  true,
);
```

---

# 4. Event Bubbling Example

```javascript
document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent clicked");
});

document.getElementById("child").addEventListener("click", () => {
  console.log("Child clicked");
});
```

### Output when clicking child:

```
Child clicked
Parent clicked
```

Because event bubbles up.

---

# 5. Event Delegation (Important Interview Concept)

Instead of attaching listeners to multiple child elements, you attach one to a parent.

## Example

```javascript
document.getElementById("list").addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    console.log("List item clicked:", event.target.textContent);
  }
});
```

### Why it works:

- Uses **event bubbling**
- Efficient for dynamic DOM elements

---

# 6. Event Object

Every event handler receives an `event` object:

```javascript
button.addEventListener("click", (event) => {
  console.log(event.type); // "click"
  console.log(event.target); // element clicked
});
```

Key properties:

- `event.target` → actual element clicked
- `event.currentTarget` → element handling the event
- `event.type` → event name

---

# 7. Stopping Event Propagation

## stop bubbling/capturing

```javascript
event.stopPropagation();
```

Example:

```javascript
child.addEventListener("click", (event) => {
  event.stopPropagation();
  console.log("Child only");
});
```

Parent won’t receive the event.

---

# 8. Prevent Default Behavior

Some events have default browser behavior.

Example:

- form submit reloads page
- anchor tag navigates

```javascript
event.preventDefault();
```

Example:

```javascript
form.addEventListener("submit", (event) => {
  event.preventDefault();
  console.log("Form submitted via JS");
});
```

---

# 9. Asynchronous Nature of Events

Events are handled via the **event loop**.

When an event occurs:

1. Browser registers it
2. Event is placed in the **callback queue**
3. Event loop pushes it to the call stack when free

So event handling is **non-blocking and asynchronous in nature**.

---

# 10. Common Pitfalls

## 1. Losing `this` context

```javascript
button.addEventListener("click", function () {
  console.log(this); // button element
});
```

But with arrow function:

```javascript
button.addEventListener("click", () => {
  console.log(this); // lexical scope, not button
});
```

---

## 2. Memory leaks

Not removing listeners:

```javascript
element.removeEventListener("click", handler);
```

---

## 3. Attaching too many listeners

Bad:

```javascript
items.forEach((item) => item.addEventListener("click", handler));
```

Better: event delegation.

---

# 11. Summary (Interview Ready)

- Browser events are signals triggered by user/system actions.
- They are handled using `addEventListener`.
- Events follow **capturing → target → bubbling** phases.
- Event delegation leverages bubbling for efficient handling.
- The `event` object provides details like target, type, and propagation control.
- Events are processed asynchronously via the event loop.

## Question 3. How does `addEventListener` differ from `onClick` or other inline handlers?

## Direct Answer

`addEventListener` is a **modern, flexible way to attach event handlers** that supports multiple listeners, phases (capture/bubble), and better control.
Inline handlers like `onclick` or HTML `onClick` are **older, less flexible, and allow only one handler per event property**, and are tied directly to the element.

---

# 1. Basic Difference

## `addEventListener`

```javascript
button.addEventListener("click", () => {
  console.log("Clicked 1");
});

button.addEventListener("click", () => {
  console.log("Clicked 2");
});
```

### Output:

```
Clicked 1
Clicked 2
```

✔ Multiple handlers allowed
✔ Modern standard
✔ Supports capture/bubble phase
✔ Can remove listeners

---

## Inline / DOM property (`onclick`)

```javascript
button.onclick = () => {
  console.log("Clicked 1");
};

button.onclick = () => {
  console.log("Clicked 2");
};
```

### Output:

```
Clicked 2
```

❌ Only one handler allowed (overwrites previous)

---

# 2. Inline HTML handlers

```html
<button onclick="handleClick()">Click</button>
```

```javascript
function handleClick() {
  console.log("Clicked");
}
```

✔ Easy to write
❌ Tight coupling of HTML + JS
❌ Harder to maintain in large apps

---

# 3. Key Differences (Interview Table)

| Feature                       | `addEventListener`             | `onclick` / inline  |
| ----------------------------- | ------------------------------ | ------------------- |
| Multiple handlers             | ✅ Yes                         | ❌ No (overwritten) |
| Event phases (capture/bubble) | ✅ Yes                         | ❌ No               |
| Remove listener               | ✅ Yes (`removeEventListener`) | ❌ Not cleanly      |
| Separation of concerns        | ✅ Good                        | ❌ Poor             |
| Maintainability               | High                           | Low                 |
| Modern standard               | ✅ Yes                         | ❌ Legacy           |

---

# 4. Event Propagation Control

## `addEventListener` supports capture phase

```javascript
div.addEventListener(
  "click",
  () => {
    console.log("Capture phase example");
  },
  true,
);
```

✔ You can control phase
❌ `onclick` cannot do this

---

# 5. Removing Event Listeners (major advantage)

## Works only with `addEventListener`

```javascript
function handler() {
  console.log("clicked");
}

button.addEventListener("click", handler);

// later
button.removeEventListener("click", handler);
```

⚠️ Important: reference must be the same function

---

## Cannot reliably remove inline handler

```javascript
button.onclick = null;
```

✔ Works but limited
❌ No granular control for multiple handlers

---

# 6. `this` behavior difference (important interview point)

## `onclick`

```javascript
button.onclick = function () {
  console.log(this); // button element
};
```

✔ `this` refers to element

---

## `addEventListener`

```javascript
button.addEventListener("click", function () {
  console.log(this); // button element
});
```

✔ Same behavior for normal functions
❌ But arrow functions behave differently:

```javascript
button.addEventListener("click", () => {
  console.log(this); // lexical scope (NOT button)
});
```

---

# 7. Memory & Architecture Differences

## `addEventListener`

- Uses internal event listener registry
- Supports multiple independent listeners per event type
- More scalable in large applications

## `onclick`

- Direct property assignment on DOM object
- Only one slot per event type

---

# 8. Event Delegation Compatibility

Only `addEventListener` works cleanly with delegation:

```javascript
document.body.addEventListener("click", (e) => {
  if (e.target.matches(".btn")) {
    console.log("Button clicked via delegation");
  }
});
```

Inline handlers cannot support delegation patterns effectively.

---

# 9. Security & Best Practice

Inline handlers:

```html
<button onclick="alert('hi')">Click</button>
```

❌ Risk:

- mixes logic with markup
- harder to sanitize in large apps
- violates separation of concerns

`addEventListener`:

✔ safer
✔ cleaner architecture
✔ preferred in frameworks (React internally abstracts this)

---

# 10. Real-world usage (modern apps)

- React / Angular / Vue → internally use event listener systems
- DOM-level JS → always uses `addEventListener`
- Inline handlers → mostly discouraged (except quick prototypes)

---

# 11. Common Interview Traps

## Trap 1: overwriting handlers

```javascript
button.onclick = fn1;
button.onclick = fn2;
```

👉 fn1 is lost

---

## Trap 2: memory leaks

```javascript
button.addEventListener("click", () => {});
// cannot remove anonymous function later
```

---

## Trap 3: multiple listeners vs single handler confusion

Many candidates incorrectly assume both behave the same.

---

# 12. Interview Summary

- `addEventListener` is the modern, scalable, multi-listener event system supporting capture, bubble, and removal.
- `onclick` / inline handlers are legacy, single-handler, and less flexible.
- Always prefer `addEventListener` in production JavaScript.
- Key advanced advantage: event propagation control + delegation support.

## Question 4. Difference between passive and non-passive event listeners

## Direct Answer

A **passive event listener** tells the browser that the event handler **will not call `event.preventDefault()`**, allowing the browser to perform optimizations (especially for scrolling and touch events).

A **non-passive event listener** (the default behavior) allows `preventDefault()`, so the browser may need to wait for the handler to finish before performing certain actions like scrolling.

```javascript
element.addEventListener("touchmove", handler, {
  passive: true,
});
```

---

# Why Passive Event Listeners Exist

Consider a mobile page:

```javascript
document.addEventListener("touchmove", (e) => {
  // Some expensive logic
});
```

When the user scrolls:

1. Browser receives touch event.
2. Browser doesn't know whether you'll call:

```javascript
e.preventDefault();
```

3. Browser must wait for JavaScript execution.
4. Scrolling may feel laggy.

To solve this:

```javascript
document.addEventListener("touchmove", handler, {
  passive: true,
});
```

Now the browser knows:

> "This handler will never cancel scrolling."

So scrolling can start immediately.

---

# Non-Passive Listener (Default)

```javascript
document.addEventListener("touchmove", (e) => {
  e.preventDefault();
});
```

Here:

- Browser must wait
- Scrolling can be blocked
- Useful when you intentionally want to disable default behavior

Examples:

- Custom drag systems
- Gesture libraries
- Custom touch interactions

---

# Passive Listener Example

```javascript
document.addEventListener(
  "touchmove",
  (e) => {
    console.log("Scrolling...");
  },
  { passive: true },
);
```

Benefits:

- Smoother scrolling
- Better responsiveness
- Better mobile performance

---

# What Happens If You Call preventDefault()?

```javascript
document.addEventListener(
  "touchmove",
  (e) => {
    e.preventDefault();
  },
  { passive: true },
);
```

Browser warning:

```text
Unable to preventDefault inside passive event listener
```

The call is ignored.

---

# Syntax

## Non-passive

```javascript
element.addEventListener("wheel", handler);
```

Equivalent to:

```javascript
element.addEventListener("wheel", handler, {
  passive: false,
});
```

---

## Passive

```javascript
element.addEventListener("wheel", handler, {
  passive: true,
});
```

---

# Common Events That Benefit from Passive Listeners

### Touch Events

```javascript
touchstart;
touchmove;
touchend;
```

### Scroll-related Events

```javascript
wheel;
mousewheel;
```

These events can affect scrolling performance.

---

# Performance Impact

### Non-passive

```javascript
window.addEventListener("wheel", heavyFunction);
```

Browser:

```text
Receive wheel event
↓
Wait for JS
↓
Maybe preventDefault?
↓
Scroll
```

---

### Passive

```javascript
window.addEventListener("wheel", heavyFunction, {
  passive: true,
});
```

Browser:

```text
Receive wheel event
↓
Start scrolling immediately
↓
Run JS in parallel with scrolling
```

Result:

✅ smoother UI

---

# Combining Listener Options

`addEventListener` supports multiple options:

```javascript
element.addEventListener("click", handler, {
  capture: true,
  once: true,
  passive: true,
});
```

### Meaning

- `capture` → capture phase
- `once` → auto-remove after first call
- `passive` → cannot cancel default behavior

---

# Interview Trap #1

```javascript
document.addEventListener(
  "touchmove",
  (e) => {
    e.preventDefault();
  },
  { passive: true },
);
```

### Question

Will scrolling be prevented?

### Answer

❌ No

Because passive listeners cannot cancel default behavior.

---

# Interview Trap #2

```javascript
document.addEventListener(
  "wheel",
  () => {
    console.log("scroll");
  },
  { passive: true },
);
```

### Question

Can the event still be received?

### Answer

✅ Yes

Passive only affects cancellation, not event delivery.

---

# Interview Trap #3

```javascript
button.addEventListener(
  "click",
  (e) => {
    e.preventDefault();
  },
  { passive: true },
);
```

### Question

Will the click handler run?

### Answer

✅ Yes

The handler executes normally.

Only `preventDefault()` becomes ineffective.

---

# When to Use Passive Listeners

## Use Passive

```javascript
window.addEventListener("scroll", onScroll, {
  passive: true,
});
```

When:

- Monitoring scroll position
- Analytics
- Lazy loading
- Infinite scrolling
- Reading touch movement

---

## Use Non-Passive

```javascript
element.addEventListener("touchmove", (e) => {
  e.preventDefault();
});
```

When:

- Custom gestures
- Drag-and-drop systems
- Preventing page scroll
- Mobile games
- Drawing applications

---

# Browser Optimization Perspective

Without passive:

```text
Event
↓
Wait for JS
↓
Check preventDefault
↓
Perform scroll
```

With passive:

```text
Event
↓
Scroll immediately
↓
Run JS
```

This is why passive listeners significantly improve perceived performance on mobile devices.

---

# Best Practices

### Prefer passive for scroll/touch monitoring

```javascript
window.addEventListener("scroll", handleScroll, {
  passive: true,
});
```

### Use non-passive only when cancellation is required

```javascript
element.addEventListener("touchmove", dragHandler, {
  passive: false,
});
```

### Avoid unnecessary scroll-blocking listeners

They can cause jank and poor user experience.

---

# Interview Summary

| Feature                            | Passive Listener | Non-Passive Listener |
| ---------------------------------- | ---------------- | -------------------- |
| Can call `preventDefault()`        | ❌ No            | ✅ Yes               |
| Browser waits before scrolling     | ❌ No            | ✅ Yes               |
| Scroll performance                 | Faster           | Potentially slower   |
| Best for analytics/scroll tracking | ✅ Yes           | ❌ Not needed        |
| Best for custom gestures           | ❌ No            | ✅ Yes               |
| Default mode                       | ❌ No            | ✅ Yes               |

### One-line Interview Answer

**A passive event listener guarantees that it won't call `preventDefault()`, allowing the browser to optimize scrolling and touch performance, while a non-passive listener can cancel default behavior but may introduce scroll latency because the browser must wait for the handler to finish.**

## Question 5. How does `stopImmediatePropagation` differ from `stopPropagation`?

## Direct Answer

Both methods stop event propagation, but they differ in **how much they stop**:

- **`event.stopPropagation()`**
  - Stops the event from propagating to parent elements.
  - Other listeners on the **same element** will still execute.

- **`event.stopImmediatePropagation()`**
  - Stops propagation to parent elements.
  - Stops all remaining listeners on the **same element** from executing.

---

# Visual Example

Suppose we have:

```html
<div id="parent">
  <button id="btn">Click</button>
</div>
```

---

# 1. stopPropagation()

```javascript
btn.addEventListener("click", () => {
  console.log("Listener 1");
});

btn.addEventListener("click", (e) => {
  console.log("Listener 2");
  e.stopPropagation();
});

btn.addEventListener("click", () => {
  console.log("Listener 3");
});

parent.addEventListener("click", () => {
  console.log("Parent");
});
```

### 👉 Output

```text
Listener 1
Listener 2
Listener 3
```

### Why?

- `stopPropagation()` prevents bubbling to `parent`.
- It **does not stop other listeners on the same element**.
- Therefore Listener 3 still runs.

---

# 2. stopImmediatePropagation()

```javascript
btn.addEventListener("click", () => {
  console.log("Listener 1");
});

btn.addEventListener("click", (e) => {
  console.log("Listener 2");
  e.stopImmediatePropagation();
});

btn.addEventListener("click", () => {
  console.log("Listener 3");
});

parent.addEventListener("click", () => {
  console.log("Parent");
});
```

### 👉 Output

```text
Listener 1
Listener 2
```

### Why?

After `stopImmediatePropagation()`:

- Listener 3 is skipped.
- Parent listener is skipped.
- Event processing ends immediately.

---

# Key Difference Table

| Behavior                                  | stopPropagation() | stopImmediatePropagation() |
| ----------------------------------------- | ----------------- | -------------------------- |
| Stops bubbling to parent                  | ✅                | ✅                         |
| Stops capturing beyond current point      | ✅                | ✅                         |
| Stops remaining listeners on same element | ❌                | ✅                         |
| Ends event processing immediately         | ❌                | ✅                         |

---

# FAANG Interview Favorite Example

```javascript
button.addEventListener("click", () => console.log("A"));

button.addEventListener("click", (e) => {
  console.log("B");
  e.stopPropagation();
});

button.addEventListener("click", () => console.log("C"));

document.body.addEventListener("click", () => console.log("BODY"));
```

### Click button

Output:

```text
A
B
C
```

Notice:

```text
BODY
```

never runs.

---

Now replace:

```javascript
e.stopPropagation();
```

with:

```javascript
e.stopImmediatePropagation();
```

Output:

```text
A
B
```

`C` is skipped.

---

# Event Flow Illustration

Assume:

```text
BODY
 └── DIV
      └── BUTTON
```

Button has:

```javascript
Listener A
Listener B (calls stopImmediatePropagation)
Listener C
```

Click button:

```text
BUTTON Listener A
BUTTON Listener B
STOP
```

Listener C never executes.

---

# Capturing Phase Behavior

The same rule applies during capture.

```javascript
parent.addEventListener(
  "click",
  (e) => {
    console.log("Parent Capture");
    e.stopImmediatePropagation();
  },
  true,
);
```

Any remaining listeners on that element and further propagation are prevented.

---

# Common Real-World Use Cases

## stopPropagation()

Useful when:

```javascript
modal.addEventListener("click", (e) => {
  e.stopPropagation();
});
```

Prevent click from reaching page background.

---

## stopImmediatePropagation()

Useful when:

```javascript
button.addEventListener("click", (e) => {
  if (!userIsAuthorized) {
    e.stopImmediatePropagation();
    return;
  }
});
```

Prevent all other click handlers from executing.

---

# Common Pitfall

Many developers think:

```javascript
e.stopPropagation();
```

stops everything.

It does **not**.

Example:

```javascript
button.addEventListener("click", () => console.log("1"));

button.addEventListener("click", (e) => {
  console.log("2");
  e.stopPropagation();
});

button.addEventListener("click", () => console.log("3"));
```

Output:

```text
1
2
3
```

This is a very common interview trap.

---

# Relationship with preventDefault()

These APIs solve different problems:

```javascript
e.preventDefault();
```

- Prevents browser default behavior.

```javascript
e.stopPropagation();
```

- Stops event moving through DOM.

```javascript
e.stopImmediatePropagation();
```

- Stops event moving through DOM **and** prevents remaining listeners on current element.

Example:

```javascript
e.preventDefault();
e.stopImmediatePropagation();
```

Can be used together.

---

# Interview Summary

### `stopPropagation()`

```javascript
event.stopPropagation();
```

- Stops bubbling/capturing beyond current element.
- Other listeners on same element still execute.

---

### `stopImmediatePropagation()`

```javascript
event.stopImmediatePropagation();
```

- Stops bubbling/capturing.
- Stops all remaining listeners on same element.
- Ends event processing immediately.

---

### One-Line Interview Answer

**`stopPropagation()` prevents an event from reaching ancestor elements but allows other handlers on the same element to run, whereas `stopImmediatePropagation()` also prevents any remaining handlers on the current element from executing, effectively terminating event processing immediately.**

## Question 6. How to check if an object has a property directly (not in prototype chain)

## Direct Answer

To check whether an object has a property **directly on itself** (and not inherited from its prototype chain), use:

```javascript
Object.hasOwn(obj, "property");
```

or the older approach:

```javascript
obj.hasOwnProperty("property");
```

`Object.hasOwn()` is the modern and recommended method.

---

# Why This Matters

JavaScript objects inherit properties from their prototype chain.

Example:

```javascript
const parent = {
  role: "admin",
};

const user = Object.create(parent);

user.name = "John";
```

Now:

```javascript
console.log(user.name); // John
console.log(user.role); // admin
```

But:

- `name` belongs directly to `user`
- `role` comes from the prototype

---

# Using `Object.hasOwn()` (Recommended)

```javascript
console.log(Object.hasOwn(user, "name"));
```

Output:

```text
true
```

```javascript
console.log(Object.hasOwn(user, "role"));
```

Output:

```text
false
```

Because `role` is inherited.

---

# Using `hasOwnProperty()`

```javascript
console.log(user.hasOwnProperty("name"));
```

Output:

```text
true
```

```javascript
console.log(user.hasOwnProperty("role"));
```

Output:

```text
false
```

---

# Difference from the `in` Operator

Many interviewers ask this.

```javascript
console.log("name" in user);
```

Output:

```text
true
```

```javascript
console.log("role" in user);
```

Output:

```text
true
```

### Why?

The `in` operator checks:

- own properties ✅
- inherited properties ✅

It searches the entire prototype chain.

---

# Comparison Table

| Method                    | Own Property | Prototype Property |
| ------------------------- | ------------ | ------------------ |
| `Object.hasOwn(obj, key)` | ✅           | ❌                 |
| `obj.hasOwnProperty(key)` | ✅           | ❌                 |
| `key in obj`              | ✅           | ✅                 |

---

# Interview Trap #1

```javascript
const obj = Object.create({
  x: 10,
});

console.log("x" in obj);
console.log(Object.hasOwn(obj, "x"));
```

Output:

```text
true
false
```

Explanation:

- `x` exists in prototype chain
- `obj` doesn't own it

---

# Interview Trap #2: Objects Without Prototypes

```javascript
const obj = Object.create(null);

obj.name = "John";
```

Now:

```javascript
obj.hasOwnProperty("name");
```

Output:

```text
TypeError
```

Why?

Because:

```javascript
Object.create(null);
```

creates an object with **no prototype**, so it doesn't inherit `hasOwnProperty`.

---

## Safe Solution

```javascript
Object.hasOwn(obj, "name");
```

or

```javascript
Object.prototype.hasOwnProperty.call(obj, "name");
```

---

# Interview Trap #3

```javascript
const obj = {
  hasOwnProperty() {
    return false;
  },
  name: "John",
};

console.log(obj.hasOwnProperty("name"));
```

Output:

```text
false
```

The method was overwritten!

Safe approach:

```javascript
Object.prototype.hasOwnProperty.call(obj, "name");
```

or

```javascript
Object.hasOwn(obj, "name");
```

---

# Modern Best Practice

Prefer:

```javascript
Object.hasOwn(obj, "key");
```

Example:

```javascript
const person = {
  name: "Alice",
};

console.log(Object.hasOwn(person, "name"));
```

Output:

```text
true
```

This avoids issues with:

- overwritten methods
- null-prototype objects
- prototype chain confusion

---

# Interview Summary

### Check direct property only

```javascript
Object.hasOwn(obj, "prop");
```

### Older method

```javascript
obj.hasOwnProperty("prop");
```

### Check including prototype chain

```javascript
"prop" in obj;
```

### One-Line Interview Answer

**To check whether a property exists directly on an object and not in its prototype chain, use `Object.hasOwn(obj, property)` (recommended) or `hasOwnProperty()`. Unlike the `in` operator, these methods do not consider inherited properties.**

## Question 7. Difference between `Object.create()` and `{}` object literal

## Direct Answer

Both `Object.create()` and `{}` create objects, but they differ in **how the prototype is assigned** and **how the object is initialized**.

```javascript
const obj1 = {};
const obj2 = Object.create(Object.prototype);
```

These two are effectively equivalent because both create an object whose prototype is `Object.prototype`.

However, `Object.create()` provides explicit control over the prototype chain, while `{}` always uses `Object.prototype`.

---

# 1. Object Literal `{}`

The most common way to create objects:

```javascript
const user = {
  name: "John",
  age: 30,
};
```

Internally:

```javascript
Object.getPrototypeOf(user) === Object.prototype;
```

Output:

```text
true
```

Prototype chain:

```text
user
  ↓
Object.prototype
  ↓
null
```

---

# 2. Object.create()

`Object.create(proto)` creates a new object whose prototype is `proto`.

```javascript
const user = Object.create(Object.prototype);

user.name = "John";
```

Equivalent to:

```javascript
const user = {};
user.name = "John";
```

---

# 3. Main Difference: Prototype Control

With `{}`:

```javascript
const obj = {};
```

Prototype is always:

```javascript
Object.prototype;
```

With `Object.create()`:

```javascript
const obj = Object.create(customProto);
```

You choose the prototype.

Example:

```javascript
const animal = {
  speak() {
    console.log("Animal sound");
  },
};

const dog = Object.create(animal);

dog.speak();
```

Output:

```text
Animal sound
```

Prototype chain:

```text
dog
  ↓
animal
  ↓
Object.prototype
  ↓
null
```

---

# 4. Creating Objects Without a Prototype

One of the biggest interview points:

```javascript
const obj = Object.create(null);
```

Prototype chain:

```text
obj
 ↓
null
```

No inherited methods:

```javascript
console.log(obj.toString);
```

Output:

```text
undefined
```

---

## Compare with `{}`

```javascript
const obj = {};
```

Output:

```javascript
console.log(obj.toString);
```

```text
[Function: toString]
```

Because it inherits from `Object.prototype`.

---

# 5. Dictionary/Map-Like Objects

Using `Object.create(null)` avoids prototype pollution:

```javascript
const dictionary = Object.create(null);

dictionary.name = "John";
```

No inherited keys:

```javascript
console.log("toString" in dictionary);
```

Output:

```text
false
```

Whereas:

```javascript
const dictionary = {};

console.log("toString" in dictionary);
```

Output:

```text
true
```

---

# 6. Property Descriptors During Creation

`Object.create()` supports defining properties immediately:

```javascript
const person = Object.create(Object.prototype, {
  name: {
    value: "John",
    writable: false,
  },
});
```

Now:

```javascript
person.name = "Mike";

console.log(person.name);
```

Output:

```text
John
```

---

`{}` cannot do this directly.

---

# 7. Classical Prototypal Inheritance

Before ES6 classes:

```javascript
const Person = {
  greet() {
    console.log("Hello");
  },
};

const user = Object.create(Person);

user.greet();
```

Output:

```text
Hello
```

This is pure prototypal inheritance.

---

# 8. Performance Considerations

### Object Literal

```javascript
const obj = {};
```

- Simpler syntax
- Faster to read
- Most common
- Optimized heavily by JS engines

---

### Object.create()

```javascript
const obj = Object.create(proto);
```

- More explicit
- Useful for custom inheritance
- Slightly more verbose

Performance differences are usually negligible in modern engines.

---

# 9. Interview Trap #1

```javascript
const a = {};
const b = Object.create(Object.prototype);

console.log(Object.getPrototypeOf(a) === Object.getPrototypeOf(b));
```

Output:

```text
true
```

Many candidates incorrectly think they're different.

---

# 10. Interview Trap #2

```javascript
const obj = Object.create(null);

console.log(obj.hasOwnProperty("x"));
```

Output:

```text
TypeError
```

Why?

Because:

```text
hasOwnProperty
```

comes from `Object.prototype`, which doesn't exist here.

Safe check:

```javascript
Object.hasOwn(obj, "x");
```

---

# 11. Interview Trap #3

```javascript
const parent = {
  x: 10,
};

const child = Object.create(parent);

console.log(child.x);
```

Output:

```text
10
```

Because `x` is inherited through the prototype chain.

---

# ES6 Class Equivalent

```javascript
class Animal {
  speak() {
    console.log("sound");
  }
}
```

Under the hood, JavaScript still uses prototypes similar to:

```javascript
const animalProto = {
  speak() {
    console.log("sound");
  },
};

const animal = Object.create(animalProto);
```

---

# When to Use Which?

### Use `{}` when:

```javascript
const config = {};
```

- Regular objects
- Most application code
- Simple data structures

---

### Use `Object.create()` when:

```javascript
const obj = Object.create(proto);
```

- Custom prototype chains
- Prototypal inheritance
- Null-prototype dictionaries
- Fine-grained property descriptors

---

# Interview Summary

| Feature                               | `{}` | `Object.create()` |
| ------------------------------------- | ---- | ----------------- |
| Creates object                        | ✅   | ✅                |
| Prototype fixed to `Object.prototype` | ✅   | ❌                |
| Custom prototype                      | ❌   | ✅                |
| Null prototype object                 | ❌   | ✅                |
| Define descriptors at creation        | ❌   | ✅                |
| Simpler syntax                        | ✅   | ❌                |
| Common usage                          | ✅   | Less common       |

### One-Line Interview Answer

**`{}` creates a normal object whose prototype is `Object.prototype`, while `Object.create()` creates an object with a prototype you explicitly specify, including the ability to create objects with no prototype (`Object.create(null)`) or custom inheritance chains.**

## Question 8. How to use `instanceof` operator in JavaScript

## Direct Answer

The `instanceof` operator in JavaScript checks whether an object is an instance of a specific constructor function (or class) by verifying whether that constructor’s prototype exists in the object’s prototype chain.

```javascript
obj instanceof Constructor;
```

It returns `true` if `Constructor.prototype` is found anywhere in `obj`’s prototype chain.

---

# 1. Basic Usage

```javascript id="a1"
function Person(name) {
  this.name = name;
}

const user = new Person("John");

console.log(user instanceof Person);
```

### Output:

```text id="a2"
true
```

### Why?

Prototype chain:

```
user → Person.prototype → Object.prototype → null
```

Since `Person.prototype` exists in the chain → `true`.

---

# 2. How `instanceof` Works Internally

This is the core interview insight:

```javascript id="b1"
obj instanceof Constructor;
```

is roughly equivalent to:

```javascript id="b2"
Constructor.prototype.isPrototypeOf(obj);
```

Or conceptually:

```javascript id="b3"
while (obj !== null) {
  obj = Object.getPrototypeOf(obj);
  if (obj === Constructor.prototype) return true;
}
return false;
```

---

# 3. Class-based Example (ES6)

```javascript id="c1"
class Animal {}

const dog = new Animal();

console.log(dog instanceof Animal);
```

### Output:

```text id="c2"
true
```

Even though it looks like OOP, it still uses prototypes internally.

---

# 4. Inheritance with `instanceof`

```javascript id="d1"
class Animal {}
class Dog extends Animal {}

const d = new Dog();

console.log(d instanceof Dog);
console.log(d instanceof Animal);
```

### Output:

```text id="d2"
true
true
```

### Why?

Prototype chain:

```
d → Dog.prototype → Animal.prototype → Object.prototype → null
```

---

# 5. Important Behavior: Primitives

```javascript id="e1"
console.log("hello" instanceof String);
```

### Output:

```text id="e2"
false
```

But:

```javascript id="e3"
console.log(new String("hello") instanceof String);
```

### Output:

```text id="e4"
true
```

### Why?

- `"hello"` is a primitive
- `instanceof` only works with objects

---

# 6. Arrays with `instanceof`

```javascript id="f1"
const arr = [];

console.log(arr instanceof Array);
```

### Output:

```text id="f2"
true
```

Also:

```javascript id="f3"
console.log(arr instanceof Object);
```

### Output:

```text id="f4"
true
```

Because:

```
Array → Object → null
```

---

# 7. Cross-Frame / Cross-Realm Issue (IMPORTANT INTERVIEW POINT)

```javascript id="g1"
const iframeArray = iframeWindow.Array;

const arr = new iframeArray();

console.log(arr instanceof Array);
```

### Output:

```text id="g2"
false
```

### Why?

Each iframe has its own:

- `Array.prototype`
- `Object.prototype`

So prototype chains don’t match across realms.

---

# 8. Difference Between `instanceof` and `typeof`

| Feature                | `typeof` | `instanceof` |
| ---------------------- | -------- | ------------ |
| Works on primitives    | ✅       | ❌           |
| Works on objects       | Limited  | ✅           |
| Checks type category   | Yes      | No           |
| Checks prototype chain | No       | Yes          |

### Example:

```javascript id="h1"
typeof []        // "object"
[] instanceof Array // true
```

---

# 9. Custom `instanceof` Behavior (`Symbol.hasInstance`)

You can override `instanceof` logic:

```javascript id="i1"
class EvenNumber {
  static [Symbol.hasInstance](value) {
    return typeof value === "number" && value % 2 === 0;
  }
}

console.log(2 instanceof EvenNumber);
console.log(3 instanceof EvenNumber);
```

### Output:

```text id="i2"
true
false
```

### This is a VERY advanced interview concept.

---

# 10. Edge Case: null and undefined

```javascript id="j1"
console.log(null instanceof Object);
console.log(undefined instanceof Object);
```

### Output:

```text id="j2"
false
false
```

Because both are primitives and have no prototype chain.

---

# 11. Common Pitfalls

## Pitfall 1: assuming it checks "type"

```javascript id="k1"
console.log([] instanceof Object); // true
```

So `instanceof` is NOT a strict type check.

---

## Pitfall 2: broken prototype chain

```javascript id="k2"
function A() {}
const obj = new A();

A.prototype = {}; // replaced prototype

console.log(obj instanceof A); // false
```

### Why?

Prototype reference changed after object creation.

---

## Pitfall 3: null prototype objects

```javascript id="k3"
const obj = Object.create(null);

console.log(obj instanceof Object);
```

### Output:

```text id="k4"
false
```

No prototype chain exists.

---

# 12. Real-world Usage

### Use `instanceof` for:

- class-based checks
- error handling

```javascript id="l1"
if (err instanceof TypeError) {
  console.log("Type error occurred");
}
```

- custom objects

---

# 13. Interview Summary

### Core definition:

**`instanceof` checks whether a constructor’s prototype exists in an object’s prototype chain.**

---

### Key insights:

- Works on objects, not primitives
- Based on prototype chain lookup
- Can be broken by changing prototypes
- Fails across iframe boundaries
- Can be customized using `Symbol.hasInstance`

---

### One-line interview answer:

**The `instanceof` operator checks whether an object’s prototype chain contains a specific constructor’s prototype, allowing us to determine whether an object is an instance of a class or constructor function.**

## Question 9. Difference between `hasOwnProperty()` and `in` operator

## Question 10. Difference between `function` and `async function`

## Question 11. How does `import()` differ from static `import`?

## Question 12. How to handle multiple asynchronous tasks with `Promise.allSettled`

## Question 13. How to convert a NodeList to an array

## Question 14. How to implement tab switching using JavaScript

## Question 15. How to implement a modal popup (dialog box) using JavaScript

## Question 16. Difference between `position: relative` and `position: absolute` when manipulated with JS

## Question 17. How to detect window resizing using JavaScript

## Question 18. How to implement lazy loading images in JS

## Question 19. How to detect user device or browser using JS

## Question 20. How to detect online/offline status using JS
