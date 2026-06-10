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

## Question 4. Difference between passive and non-passive event listeners

## Question 5. How does `stopImmediatePropagation` differ from `stopPropagation`?

## Question 6. How to check if an object has a property directly (not in prototype chain)

## Question 7. Difference between `Object.create()` and `{}` object literal

## Question 8. How to use `instanceof` operator in JavaScript

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
