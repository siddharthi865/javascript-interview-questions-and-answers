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
