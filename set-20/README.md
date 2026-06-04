# Set 20

| S.No. | Question                                                                                                                                                |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How does Node.js handle asynchronous I/O internally](#question-1-how-does-nodejs-handle-asynchronous-io-internally)                                    |
| 2.    | [How to implement a simple HTTP server in Node.js](#question-2-how-to-implement-a-simple-http-server-in-nodejs)                                         |
| 3.    | [Difference between CommonJS and ES modules in Node.js](#question-3-difference-between-commonjs-and-es-modules-in-nodejs)                               |
| 4.    | [How to implement a simple Express.js middleware](#question-4-how-to-implement-a-simple-expressjs-middleware)                                           |
| 5.    | [How to implement error handling in async/await in Node.js](#question-5-how-to-implement-error-handling-in-asyncawait-in-nodejs)                        |
| 6.    | [How to stream large files efficiently in Node.js](#question-6-how-to-stream-large-files-efficiently-in-nodejs)                                         |
| 7.    | [Difference between `fs.readFile` and streams](#question-7-difference-between-fsreadfile-and-streams)                                                   |
| 8.    | [How to prevent callback hell in Node.js](#question-8-how-to-prevent-callback-hell-in-nodejs)                                                           |
| 9.    | [How to handle unhandled promise rejections in Node.js](#question-9-how-to-handle-unhandled-promise-rejections-in-nodejs)                               |
| 10.   | [How to implement rate limiting in Node.js API](#question-10-how-to-implement-rate-limiting-in-nodejs-api)                                              |
| 11.   | [How to implement JWT authentication in Node.js](#question-11-how-to-implement-jwt-authentication-in-nodejs)                                            |
| 12.   | [How to handle concurrent requests in Node.js](#question-12-how-to-handle-concurrent-requests-in-nodejs)                                                |
| 13.   | [Difference between worker threads and cluster module in Node.js](#question-13-difference-between-worker-threads-and-cluster-module-in-nodejs)          |
| 14.   | [How to implement WebSocket server in Node.js](#question-14-how-to-implement-websocket-server-in-nodejs)                                                |
| 15.   | [Difference between ES2023 Array.at() and `[]` access](#question-15-difference-between-es2023-arrayat-and--access)                                      |
| 16.   | [How to use `Object.hasOwn()` in ES2023](#question-16-how-to-use-objecthasown-in-es2023)                                                                |
| 17.   | [How to use top-level await in ES modules](#question-17-how-to-use-top-level-await-in-es-modules)                                                       |
| 18.   | [How to implement private class methods with `#` syntax](#question-18-how-to-implement-private-class-methods-with--syntax)                              |
| 19.   | [How to implement `Promise.any()` and differences from `Promise.race`](#question-19-how-to-implement-promiseany-and-differences-from-promiserace)       |
| 20.   | [How to implement custom error classes and inheritance in JavaScript](#question-20-how-to-implement-custom-error-classes-and-inheritance-in-javascript) |

## Question 1. How does Node.js handle asynchronous I/O internally

Node.js handles asynchronous I/O using an **event-driven, non-blocking architecture** built on top of:

- the **V8 JavaScript engine**
- the **libuv library**
- the **event loop**
- the **OS kernel’s async capabilities**
- and a **thread pool** for operations the OS cannot perform asynchronously

This design allows Node.js to handle thousands of concurrent connections efficiently using a single JavaScript thread.

### High-Level Architecture

Node.js internally consists of:

1. **V8 Engine**
   - Executes JavaScript code
   - Runs on a single main thread

2. **libuv**
   - Core C library responsible for:
     - Event loop
     - Thread pool
     - Async I/O handling
     - Timers
     - Networking

3. **Operating System APIs**
   - Linux: `epoll`
   - macOS: `kqueue`
   - Windows: `IOCP`

4. **Thread Pool**
   - Used for tasks that cannot be done asynchronously by the OS

### Core Idea: Non-Blocking I/O

Traditional blocking I/O:

```js
const data = fs.readFileSync("file.txt");
console.log(data.toString());
```

The thread waits until the file is fully read.

Node.js async version:

```js
fs.readFile("file.txt", (err, data) => {
  console.log(data.toString());
});

console.log("This runs first");
```

Output:

```txt
This runs first
[file content]
```

The main thread never blocks.

### Internal Flow of Async I/O

When Node.js encounters an async operation:

```js
fs.readFile("test.txt", callback);
```

Internally:

#### Step 1: JavaScript Calls Node APIs

The JS code runs in V8:

```js
fs.readFile(...)
```

This API is implemented in C++ bindings.

#### Step 2: Node Delegates to libuv

Node passes the operation to `libuv`.

libuv decides:

- Can the OS handle this asynchronously?
- Or should it use the thread pool?

### Two Different Internal Mechanisms

#### 1. OS-Level Async I/O (Networking)

Operations like:

- HTTP requests
- TCP sockets
- WebSockets

are usually handled directly by the OS.

Example:

```js
http.createServer(...)
```

The OS notifies Node when data is ready.

No extra thread is needed.

This is extremely scalable.

#### 2. Thread Pool-Based Async I/O

Some operations are NOT truly async at OS level across all platforms.

Examples:

- `fs.readFile`
- `crypto.pbkdf2`
- `zlib`
- DNS lookups

libuv sends these tasks to a thread pool.

Default size:

```txt
4 threads
```

Can be changed:

```bash
UV_THREADPOOL_SIZE=8
```

### Event Loop: The Heart of Node.js

Once async work finishes, callbacks are queued into the event loop.

The event loop continuously checks:

- completed timers
- completed I/O
- pending callbacks
- microtasks

### Event Loop Phases

Main phases:

```txt
1. Timers
2. Pending callbacks
3. Idle/prepare
4. Poll
5. Check
6. Close callbacks
```

### Simplified Flow

```txt
JS Code
   ↓
Node API
   ↓
libuv
   ↓
OS / Thread Pool
   ↓
Completion Event
   ↓
Event Loop Queue
   ↓
Callback Execution
```

### Example Walkthrough

```js
const fs = require("fs");

console.log("Start");

fs.readFile("test.txt", () => {
  console.log("File read complete");
});

console.log("End");
```

Execution:

### Main Thread

```txt
Start
```

`fs.readFile` delegated to libuv thread pool.

Main thread continues:

```txt
End
```

### Background Thread

File is read asynchronously.

### Event Loop

When complete:

```txt
File read complete
```

callback gets pushed into the callback queue.

### How Promises and async/await Fit In

Promises do NOT create threads.

They use:

- microtask queue
- event loop

Example:

```js
async function read() {
  const data = await fs.promises.readFile("test.txt");
  console.log(data.toString());
}
```

Internally:

1. File read delegated to libuv
2. Promise resolved when done
3. Continuation placed in microtask queue
4. Event loop executes it

### Microtasks vs Macrotasks

Important interview topic.

#### Microtasks

Higher priority queue:

- Promise callbacks
- `queueMicrotask`
- `process.nextTick`

Example:

```js
Promise.resolve().then(() => console.log("microtask"));
```

#### Macrotasks

Lower priority:

- `setTimeout`
- I/O callbacks
- `setImmediate`

#### process.nextTick Special Case

`process.nextTick()` runs BEFORE regular microtasks.

Example:

```js
setTimeout(() => console.log("timeout"));

Promise.resolve().then(() => console.log("promise"));

process.nextTick(() => console.log("nextTick"));
```

Output:

```txt
nextTick
promise
timeout
```

### Why Node.js Scales Well

Traditional threaded server:

```txt
1 request = 1 thread
```

Problem:

- memory overhead
- context switching

Node.js:

```txt
Many requests = one event loop
```

Benefits:

- lightweight
- scalable
- efficient for I/O-heavy apps

Perfect for:

- APIs
- chat apps
- streaming
- real-time systems

### Important Limitation: CPU-Bound Tasks

Node.js is NOT ideal for heavy CPU computation on the main thread.

Bad example:

```js
while (true) {}
```

This blocks the event loop entirely.

Solutions:

- Worker Threads
- Child Processes
- Clustering
- Offloading to external services

### Worker Threads vs libuv Thread Pool

Common interview confusion.

#### libuv Thread Pool

Used internally for:

- fs
- crypto
- zlib

You do NOT directly execute JS there.

#### Worker Threads

Allow parallel JavaScript execution.

Example:

```js
const { Worker } = require("worker_threads");
```

Useful for CPU-heavy work.

### Summary

A strong interview answer:

> Node.js handles asynchronous I/O using an event-driven, non-blocking architecture powered by libuv and the event loop. When async operations are initiated, Node delegates them either to OS async APIs (for networking) or to libuv’s thread pool (for file system and certain CPU-bound operations). Once the operation completes, callbacks are queued in the event loop and executed on the main JavaScript thread. This allows Node.js to efficiently handle many concurrent operations without creating a thread per request.

## Question 2. How to implement a simple HTTP server in Node.js

## Short Answer

You can create a simple HTTP server in Node.js using the built-in **`http` module** by calling `http.createServer()` and listening on a port.

---

# Detailed Interview-Style Explanation

Node.js provides a core module called **`http`** that allows you to build web servers without any external framework like Express.

At a high level, an HTTP server in Node.js works like this:

1. Node creates a server using `http.createServer()`
2. It registers a callback that runs for every incoming request
3. The callback receives `request` and `response` objects
4. You use these objects to read request data and send responses
5. The server listens on a port using `.listen()`

---

# Basic HTTP Server Example

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader("Content-Type", "text/plain");
  res.end("Hello, World!");
});

server.listen(3000, () => {
  console.log("Server running on http://localhost:3000");
});
```

---

# What Happens Internally

When a request comes in:

1. OS receives TCP connection
2. Node (via **libuv + OS event notification like epoll/kqueue/IOCP**) is notified
3. Event loop picks up the request
4. Callback passed to `createServer` is executed
5. Response is written back to socket

This is fully **non-blocking and event-driven**

---

# Understanding Request & Response Objects

## 1. `req` (Incoming Request)

Contains:

- URL
- Method (GET, POST, etc.)
- Headers
- Body (stream)

Example:

```js
req.method; // "GET"
req.url; // "/home"
req.headers;
```

---

## 2. `res` (Outgoing Response)

Used to send data back:

```js
res.statusCode = 200;
res.setHeader("Content-Type", "text/html");
res.write("Hello ");
res.end("World");
```

Important:

- `res.end()` must be called to finish the response
- Until then, connection stays open

---

# Handling Routes (Basic Routing Without Express)

Node core does NOT provide routing, so you implement it manually:

```js
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.url === "/" && req.method === "GET") {
    res.end("Home Page");
  } else if (req.url === "/about") {
    res.end("About Page");
  } else {
    res.statusCode = 404;
    res.end("Not Found");
  }
});

server.listen(3000);
```

---

# Handling JSON Response

Very common in APIs:

```js
const http = require("http");

const server = http.createServer((req, res) => {
  const data = {
    message: "Hello API",
    success: true,
  };

  res.setHeader("Content-Type", "application/json");
  res.end(JSON.stringify(data));
});

server.listen(3000);
```

---

# Handling POST Request (With Body Parsing)

Request body is a stream, so you must collect it manually:

```js
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.method === "POST") {
    let body = "";

    req.on("data", (chunk) => {
      body += chunk.toString();
    });

    req.on("end", () => {
      const parsed = JSON.parse(body);
      res.end(`Received: ${parsed.name}`);
    });
  }
});

server.listen(3000);
```

---

# Important Concept: Streams

`req` is a **Readable Stream**

Why?

- Body may be large
- Node avoids loading everything into memory at once
- Data arrives in chunks

This is critical for performance and scalability.

---

# Common Pitfalls

## 1. Forgetting `res.end()`

```js
res.write("Hello");
// missing res.end() → request hangs forever
```

---

## 2. Blocking Event Loop

```js
while (true) {}
```

This freezes all requests.

---

## 3. Not handling errors

```js
req.on("error", () => {});
res.on("error", () => {});
```

Important for production servers.

---

# Better Structure (Production Pattern)

Even without Express, you can structure it better:

```js
const http = require("http");

function handler(req, res) {
  switch (req.url) {
    case "/":
      return res.end("Home");

    case "/api":
      res.setHeader("Content-Type", "application/json");
      return res.end(JSON.stringify({ ok: true }));

    default:
      res.statusCode = 404;
      return res.end("Not Found");
  }
}

const server = http.createServer(handler);

server.listen(3000, () => {
  console.log("Server running");
});
```

---

# Comparison: http module vs Express

| Feature          | http module | Express     |
| ---------------- | ----------- | ----------- |
| Built-in routing | ❌ manual   | ✅ built-in |
| Middleware       | ❌          | ✅          |
| Body parsing     | ❌ manual   | ✅          |
| Flexibility      | high        | medium      |
| Complexity       | high        | low         |

---

# Key Interview Takeaway

A strong answer:

> Node.js provides the built-in `http` module to create servers. The `http.createServer()` method registers a callback that receives request and response objects for each incoming request. The server listens on a port and handles requests asynchronously using Node’s event-driven architecture. Internally, requests are handled via the event loop and libuv, making it non-blocking and highly scalable.

## Question 3. Difference between CommonJS and ES modules in Node.js

## Short Answer

**CommonJS (CJS)** is Node.js’s older module system using `require()` and `module.exports`, while **ES Modules (ESM)** is the modern JavaScript standard using `import` and `export`.
ESM is statically analyzable, supports top-level `await`, and is the future standard, while CommonJS is synchronous and Node-specific.

---

# 1. Core Difference (Interview One-Liner)

- **CommonJS** → _runtime, synchronous module loading (`require`)_
- **ES Modules** → _static, compile-time module system (`import/export`)_

---

# 2. Syntax Comparison

## CommonJS

```js
// math.js
function add(a, b) {
  return a + b;
}

module.exports = { add };
```

```js
// app.js
const math = require("./math");

console.log(math.add(2, 3));
```

---

## ES Modules

```js
// math.js
export function add(a, b) {
  return a + b;
}
```

```js
// app.js
import { add } from "./math.js";

console.log(add(2, 3));
```

---

# 3. Key Differences (Deep Interview Breakdown)

## 1. Loading Mechanism

### CommonJS

- **Synchronous loading**
- Modules are loaded at runtime

```js
const fs = require("fs");
```

✔ Works well for server-side Node.js
❌ Not optimized for tree-shaking

---

### ES Modules

- **Asynchronous + static structure**
- Imported before execution starts

```js
import fs from "fs";
```

✔ Enables optimization and tree-shaking
✔ Better suited for modern tooling

---

## 2. Execution Timing

### CommonJS

- Executed when `require()` is called

```js
console.log("A");

const mod = require("./mod");

console.log("B");
```

---

### ES Modules

- Imports are resolved **before code runs**

```js
console.log("A");

import "./mod.js";

console.log("B");
```

(Import is hoisted and evaluated first)

---

## 3. Export Behavior

### CommonJS → Mutable Export Object

```js
module.exports = {
  value: 10,
};
```

You can mutate exports anytime:

```js
exports.value = 20;
```

---

### ES Modules → Live Bindings (Immutable Reference)

```js
export let count = 1;
count++;
```

Imported values are **live bindings**, not copies.

---

## 4. `this` Behavior

### CommonJS

```js
console.log(this); // {}
```

Top-level `this` refers to `module.exports`

---

### ES Modules

```js
console.log(this); // undefined
```

ESM runs in strict mode automatically.

---

## 5. File Extension & Usage in Node.js

### CommonJS (default in Node)

- `.js` files
- No config needed

```bash
node app.js
```

---

### ES Modules

You must enable ESM:

### Option 1: `package.json`

```json
{
  "type": "module"
}
```

### Option 2: `.mjs` extension

```bash
app.mjs
```

---

## 6. Top-Level Await

### CommonJS ❌ Not supported

```js
await fetch(); // SyntaxError
```

---

### ES Modules ✅ Supported

```js
const data = await fetch("https://api.com");
console.log(await data.json());
```

---

## 7. Dynamic vs Static Nature

### CommonJS → Dynamic

```js
if (condition) {
  const mod = require("./mod");
}
```

✔ Flexible
❌ Hard for bundlers to optimize

---

### ES Modules → Static

```js
if (condition) {
  import mod from "./mod.js"; // ❌ not allowed
}
```

✔ Enables:

- tree-shaking
- better bundling
- static analysis

---

## 8. Circular Dependencies

### CommonJS (can be tricky)

```js
// a.js
const b = require("./b");

// b.js
const a = require("./a");
```

May return **partial exports**

---

### ES Modules (better handling)

ESM uses live bindings, so:

✔ More predictable resolution
✔ Less “undefined” partial state issues

---

## 9. Performance & Tooling

### CommonJS

- Slower for large apps
- Harder to optimize

---

### ES Modules

- Better for:
  - bundlers (Webpack, Vite, Rollup)
  - tree-shaking
  - static optimization

---

# 10. Interoperability (Important in Interviews)

## Using CJS in ESM

```js
import pkg from "lodash";
```

Node handles CJS default export automatically.

---

## Using ESM in CJS (harder)

```js
const mod = await import("./esm-module.js");
```

Uses dynamic import.

---

# 11. Real-World Usage Today (2026 Context)

- **CommonJS**
  - legacy Node.js projects
  - older libraries

- **ES Modules**
  - modern Node.js apps
  - frontend frameworks
  - full-stack JS (Next.js, Vite, etc.)

---

# 12. Summary Table

| Feature         | CommonJS               | ES Modules          |
| --------------- | ---------------------- | ------------------- |
| Syntax          | require/module.exports | import/export       |
| Loading         | Sync                   | Async (static)      |
| Execution       | Runtime                | Compile-time        |
| Top-level await | ❌                     | ✅                  |
| Tree-shaking    | ❌                     | ✅                  |
| Dynamic imports | ✅                     | Limited             |
| Default in Node | ✅                     | Requires config     |
| Standard        | Node-specific          | ECMAScript standard |

---

# Final Interview Answer (Polished)

> CommonJS is Node.js’s traditional module system that uses `require()` and `module.exports` with synchronous runtime loading. ES Modules are the standardized JavaScript module system using `import` and `export`, which are statically analyzed and support features like tree-shaking and top-level await. ES Modules are the modern approach and preferred for new applications due to better performance optimization and alignment with the JavaScript standard, while CommonJS is still widely used for legacy compatibility.

## Question 4. How to implement a simple Express.js middleware

## Short Answer

An **Express middleware** is a function that has access to the request (`req`), response (`res`), and next middleware function (`next`). It can execute code, modify the request/response objects, end the request-response cycle, or pass control to the next middleware.

Basic example:

```js
function logger(req, res, next) {
  console.log(`${req.method} ${req.url}`);
  next(); // Pass control to the next middleware
}
```

```js
app.use(logger);
```

---

# What is Middleware?

Middleware sits between:

```txt
Incoming Request
       ↓
   Middleware
       ↓
Route Handler
       ↓
Response
```

Every request passes through the middleware chain in the order they are registered.

---

# Simple Middleware Example

```js
const express = require("express");
const app = express();

function logger(req, res, next) {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
}

app.use(logger);

app.get("/", (req, res) => {
  res.send("Hello World");
});

app.listen(3000);
```

Request:

```txt
GET /
```

Console:

```txt
[2026-06-10T10:00:00.000Z] GET /
```

Response:

```txt
Hello World
```

---

# Understanding `next()`

The `next` function tells Express:

> "I'm done, continue to the next middleware."

```js
function middleware(req, res, next) {
  console.log("Middleware executed");
  next();
}
```

Without `next()`, the request will hang unless a response is sent.

---

## Common Mistake

```js
function middleware(req, res, next) {
  console.log("Running...");
  // next() missing
}
```

The browser will wait forever because Express never moves to the next step.

---

# Middleware That Modifies the Request

Middleware often attaches data to the request object.

```js
function addUser(req, res, next) {
  req.user = {
    id: 1,
    name: "John",
  };

  next();
}
```

```js
app.use(addUser);

app.get("/profile", (req, res) => {
  res.json(req.user);
});
```

Output:

```json
{
  "id": 1,
  "name": "John"
}
```

---

# Middleware That Ends the Request

A middleware doesn't have to call `next()` if it sends a response.

```js
function maintenanceMode(req, res, next) {
  res.status(503).send("Server under maintenance");
}
```

```txt
Request
   ↓
Maintenance Middleware
   ↓
Response Sent
```

No other middleware or route handler executes.

---

# Route-Specific Middleware

Instead of applying middleware globally:

```js
app.use(logger);
```

You can apply it to specific routes:

```js
function auth(req, res, next) {
  if (req.headers.authorization) {
    next();
  } else {
    res.status(401).send("Unauthorized");
  }
}

app.get("/dashboard", auth, (req, res) => {
  res.send("Dashboard");
});
```

---

# Multiple Middleware Functions

```js
function first(req, res, next) {
  console.log("First");
  next();
}

function second(req, res, next) {
  console.log("Second");
  next();
}

app.get("/", first, second, (req, res) => {
  res.send("Done");
});
```

Output:

```txt
First
Second
```

---

# Built-in Middleware Example

Express provides built-in middleware:

```js
app.use(express.json());
```

This parses JSON request bodies.

Without it:

```js
req.body; // undefined
```

With it:

```js
req.body;
```

contains the parsed JSON object.

---

# Error-Handling Middleware

Express recognizes error middleware by the **four parameters**:

```js
function errorHandler(err, req, res, next) {
  console.error(err);

  res.status(500).json({
    error: "Something went wrong",
  });
}
```

Register it after routes:

```js
app.use(errorHandler);
```

Throwing an error:

```js
app.get("/", (req, res, next) => {
  next(new Error("Database failed"));
});
```

---

# Middleware Execution Order

Order matters.

```js
app.use(first);
app.use(second);

app.get("/", handler);
```

Execution:

```txt
first
   ↓
second
   ↓
handler
```

If reversed:

```js
app.use(second);
app.use(first);
```

Execution changes accordingly.

---

# Async Middleware

Modern Express middleware often uses `async/await`.

```js
async function loadUser(req, res, next) {
  try {
    const user = await getUserFromDB();

    req.user = user;
    next();
  } catch (err) {
    next(err);
  }
}
```

Important: pass errors to `next(err)`.

---

# Real-World Authentication Middleware

```js
function auth(req, res, next) {
  const token = req.headers.authorization;

  if (!token) {
    return res.status(401).json({
      message: "Authentication required",
    });
  }

  req.user = {
    id: 123,
  };

  next();
}
```

Usage:

```js
app.get("/account", auth, (req, res) => {
  res.json({
    userId: req.user.id,
  });
});
```

---

# Common Interview Follow-Up: `app.use()` vs Route Middleware

### Global Middleware

```js
app.use(logger);
```

Runs for every request.

---

### Route Middleware

```js
app.get("/admin", auth, handler);
```

Runs only for that route.

---

# Best Practices

### Always call `next()`

Unless you send a response.

```js
next();
```

---

### Keep middleware focused

Good:

```js
logger();
auth();
validate();
```

Avoid giant middleware functions doing everything.

---

### Handle async errors

```js
try {
  await something();
  next();
} catch (err) {
  next(err);
}
```

---

### Register error middleware last

```js
app.use(errorHandler);
```

---

# Final Interview Answer

> In Express.js, middleware is a function with the signature `(req, res, next)` that executes during the request-response cycle. Middleware can inspect or modify requests, perform authentication, logging, validation, or send responses directly. If it wants processing to continue, it must call `next()`. Middleware can be applied globally using `app.use()` or to specific routes, and Express executes middleware in the order it is registered. Error-handling middleware is defined with four parameters: `(err, req, res, next)`.

## Question 5. How to implement error handling in async/await in Node.js

## Short Answer

When using `async/await` in Node.js, the primary way to handle errors is with **`try...catch`**. Since an `async` function always returns a Promise, any thrown error or rejected Promise can be caught using `try...catch` inside the async function or `.catch()` where the function is called.

```js
async function getUser() {
  try {
    const user = await fetchUser();
    return user;
  } catch (err) {
    console.error("Failed to fetch user:", err);
  }
}
```

---

# Why Error Handling is Needed

With Promises:

```js
fetchUser()
  .then((user) => console.log(user))
  .catch((err) => console.error(err));
```

Using `async/await`, the equivalent is:

```js
async function getUser() {
  try {
    const user = await fetchUser();
    console.log(user);
  } catch (err) {
    console.error(err);
  }
}
```

Any Promise rejection becomes a thrown exception that can be caught by `catch`.

---

# Basic try/catch Pattern

```js
async function readData() {
  try {
    const data = await someAsyncOperation();
    console.log(data);
  } catch (err) {
    console.error("Error:", err.message);
  }
}
```

Execution flow:

```txt
await Promise
      ↓
Success → continue
Failure → throw error
      ↓
catch block
```

---

# Handling Multiple Await Operations

```js
async function processUser() {
  try {
    const user = await getUser();
    const orders = await getOrders(user.id);

    console.log(orders);
  } catch (err) {
    console.error("Operation failed:", err);
  }
}
```

A single `catch` handles errors from either `await`.

---

# Catching Errors at the Caller

Sometimes it's cleaner to let errors propagate upward.

```js
async function getUser() {
  const user = await fetchUser();
  return user;
}
```

Caller:

```js
async function main() {
  try {
    const user = await getUser();
    console.log(user);
  } catch (err) {
    console.error("Main error:", err);
  }
}
```

This approach centralizes error handling.

---

# Using `.catch()` with Async Functions

Since async functions return Promises:

```js
async function getUser() {
  throw new Error("User not found");
}

getUser().catch((err) => {
  console.error(err.message);
});
```

Output:

```txt
User not found
```

---

# Custom Error Classes

A common production pattern is creating custom errors.

```js
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}
```

Usage:

```js
async function createUser(data) {
  if (!data.email) {
    throw new ValidationError("Email is required");
  }
}
```

Handling:

```js
try {
  await createUser({});
} catch (err) {
  if (err instanceof ValidationError) {
    console.log("Validation failed");
  }
}
```

---

# Express.js Async Error Handling

A very common interview topic.

### Problem

```js
app.get("/users", async (req, res) => {
  const users = await getUsers();
  res.json(users);
});
```

If `getUsers()` rejects, Express 4 will not automatically catch it.

---

### Solution 1: try/catch

```js
app.get("/users", async (req, res, next) => {
  try {
    const users = await getUsers();
    res.json(users);
  } catch (err) {
    next(err);
  }
});
```

---

### Solution 2: Async Wrapper

```js
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);
```

Usage:

```js
app.get(
  "/users",
  asyncHandler(async (req, res) => {
    const users = await getUsers();
    res.json(users);
  }),
);
```

This removes repetitive try/catch blocks.

---

# Global Express Error Middleware

```js
app.use((err, req, res, next) => {
  console.error(err);

  res.status(500).json({
    message: "Internal Server Error",
  });
});
```

Flow:

```txt
Route Error
    ↓
next(err)
    ↓
Error Middleware
    ↓
Response
```

---

# Unhandled Promise Rejections

Bad:

```js
async function test() {
  throw new Error("Boom");
}

test();
```

This creates an unhandled rejection.

Modern Node.js treats unhandled rejections seriously and may terminate the process depending on configuration/version.

---

## Handling Globally

```js
process.on("unhandledRejection", (err) => {
  console.error("Unhandled Rejection:", err);
});
```

Useful for logging, but not a substitute for proper local error handling.

---

# Handling Errors in Parallel Operations

Consider:

```js
const users = await Promise.all([getUser1(), getUser2(), getUser3()]);
```

If one Promise rejects:

```txt
Promise.all rejects immediately
```

Handle it:

```js
try {
  const users = await Promise.all([getUser1(), getUser2(), getUser3()]);
} catch (err) {
  console.error(err);
}
```

---

## Using Promise.allSettled

If you want all results regardless of failures:

```js
const results = await Promise.allSettled([getUser1(), getUser2(), getUser3()]);

console.log(results);
```

Output:

```js
[
  { status: "fulfilled", value: ... },
  { status: "rejected", reason: ... },
  { status: "fulfilled", value: ... }
]
```

---

# Using finally

Cleanup logic belongs in `finally`.

```js
async function processFile() {
  let connection;

  try {
    connection = await connectDB();
    await doWork();
  } catch (err) {
    console.error(err);
  } finally {
    if (connection) {
      await connection.close();
    }
  }
}
```

`finally` runs whether success or failure occurs.

---

# Common Pitfalls

## 1. Forgetting await

```js
try {
  getUser(); // Missing await
} catch (err) {
  console.log(err);
}
```

This won't catch async errors because the Promise rejection happens later.

Correct:

```js
await getUser();
```

---

## 2. Swallowing Errors

Bad:

```js
catch (err) {
  console.log(err);
}
```

The caller never knows something failed.

Better:

```js
catch (err) {
  console.error(err);
  throw err;
}
```

---

## 3. Mixing Patterns Unnecessarily

Avoid:

```js
try {
  await getUser().catch(console.error);
} catch (err) {
  console.error(err);
}
```

Use either:

```js
try {
  await getUser();
} catch (err) {
  console.error(err);
}
```

or

```js
getUser().catch(console.error);
```

---

# Best Practices

### Use try/catch around awaited operations

```js
try {
  await operation();
} catch (err) {
  handleError(err);
}
```

### Create custom error types

```js
class NotFoundError extends Error {}
```

### Let errors bubble when appropriate

```js
throw err;
```

### Use centralized error middleware in Express

```js
next(err);
```

### Use `finally` for cleanup

```js
finally {
  await closeConnection();
}
```

### Avoid unhandled Promise rejections

Always await or catch Promises.

---

# Final Interview Answer

> In Node.js, errors in `async/await` code are typically handled using `try...catch`. Since async functions return Promises, any rejected Promise is converted into a thrown exception that can be caught in a `catch` block. Errors can either be handled locally or propagated to higher layers for centralized handling. In Express applications, async route handlers should pass errors to `next(err)` so that global error middleware can process them. For cleanup tasks, `finally` should be used, and developers should avoid unhandled Promise rejections by always awaiting or catching Promises.

## Question 6. How to stream large files efficiently in Node.js

## Short Answer

To stream large files efficiently in Node.js, use **Streams** (`fs.createReadStream()` and `fs.createWriteStream()`) instead of reading the entire file into memory with `fs.readFile()`. Streams process data in chunks, which reduces memory usage and improves performance.

```js
const fs = require("fs");

const readStream = fs.createReadStream("large-file.zip");

readStream.on("data", (chunk) => {
  console.log(`Received ${chunk.length} bytes`);
});
```

---

# Why Streaming is Important

### Bad Approach: Read Entire File

```js
const fs = require("fs");

fs.readFile("10GB-file.zip", (err, data) => {
  console.log(data.length);
});
```

Problems:

- Loads entire file into memory
- Can cause memory exhaustion
- Poor scalability
- Slow startup before processing begins

Memory usage:

```txt
10GB file
↓
10GB RAM required
```

---

### Good Approach: Stream File

```js
const fs = require("fs");

const stream = fs.createReadStream("10GB-file.zip");

stream.on("data", (chunk) => {
  console.log(chunk.length);
});
```

Memory usage:

```txt
10GB file
↓
64KB chunk
↓
Process
↓
Next chunk
```

Only a small portion of the file is in memory at any time.

---

# What is a Stream?

A stream is an object that lets you process data incrementally as it becomes available.

Node.js provides four stream types:

| Type      | Description                 |
| --------- | --------------------------- |
| Readable  | Read data                   |
| Writable  | Write data                  |
| Duplex    | Read + Write                |
| Transform | Modify data while streaming |

---

# Reading a Large File

```js
const fs = require("fs");

const readStream = fs.createReadStream("large.txt");

readStream.on("data", (chunk) => {
  console.log(chunk.toString());
});

readStream.on("end", () => {
  console.log("Finished reading");
});
```

Events:

```txt
open
 ↓
data
 ↓
data
 ↓
data
 ↓
end
 ↓
close
```

---

# Writing to a File

```js
const fs = require("fs");

const writeStream = fs.createWriteStream("output.txt");

writeStream.write("Hello\n");
writeStream.write("World\n");

writeStream.end();
```

---

# Copying Large Files Efficiently

### Bad

```js
fs.readFile("input.zip", (err, data) => {
  fs.writeFile("copy.zip", data, () => {});
});
```

Entire file loaded into memory.

---

### Better

```js
const fs = require("fs");

const readStream = fs.createReadStream("input.zip");
const writeStream = fs.createWriteStream("copy.zip");

readStream.pipe(writeStream);
```

Flow:

```txt
File
 ↓
Read Stream
 ↓
Pipe
 ↓
Write Stream
 ↓
New File
```

---

# Understanding `pipe()`

`pipe()` automatically:

- Reads chunks
- Writes chunks
- Handles flow control
- Manages backpressure

Example:

```js
readStream.pipe(writeStream);
```

Equivalent to manually doing:

```js
readStream.on("data", (chunk) => {
  writeStream.write(chunk);
});
```

but much safer and more efficient.

---

# Backpressure (Very Important Interview Topic)

## What is Backpressure?

Occurs when:

```txt
Producer > Consumer
```

Example:

```txt
Read stream: 100 MB/sec
Write stream: 20 MB/sec
```

Data accumulates faster than it can be processed.

---

### Without Backpressure

```txt
Read Fast
 ↓
Memory fills up
 ↓
Potential crash
```

---

### With Backpressure

Node automatically pauses reading when the writable stream is overwhelmed.

```txt
Read Stream
    ↓
Writable Full
    ↓
Pause Reading
    ↓
Resume Later
```

This is one reason `pipe()` is preferred.

---

# Using `pipeline()`

Modern Node.js provides `pipeline()`.

```js
const fs = require("fs");
const { pipeline } = require("stream");

pipeline(
  fs.createReadStream("input.zip"),
  fs.createWriteStream("output.zip"),
  (err) => {
    if (err) {
      console.error("Pipeline failed:", err);
    } else {
      console.log("Success");
    }
  },
);
```

Advantages:

- Proper error handling
- Automatic cleanup
- Recommended over manual piping

---

# Promise-Based Pipeline

```js
const fs = require("fs");
const { pipeline } = require("stream/promises");

async function copy() {
  await pipeline(
    fs.createReadStream("input.zip"),
    fs.createWriteStream("output.zip"),
  );
}
```

Modern async/await style.

---

# Streaming HTTP Responses

Very common in production.

```js
const http = require("http");
const fs = require("fs");

http
  .createServer((req, res) => {
    const stream = fs.createReadStream("movie.mp4");

    stream.pipe(res);
  })
  .listen(3000);
```

Benefits:

- Starts sending immediately
- Minimal memory usage
- Supports huge files

---

# Streaming File Downloads

```js
app.get("/download", (req, res) => {
  res.setHeader("Content-Disposition", 'attachment; filename="file.zip"');

  fs.createReadStream("file.zip").pipe(res);
});
```

Used by:

- Cloud storage services
- Video platforms
- CDN systems

---

# Transform Streams

Transform data while streaming.

Example:

```js
const { Transform } = require("stream");

const upperCase = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  },
});
```

Usage:

```js
fs.createReadStream("input.txt")
  .pipe(upperCase)
  .pipe(fs.createWriteStream("output.txt"));
```

Flow:

```txt
Input
 ↓
Transform
 ↓
Output
```

---

# Handling Errors

Always handle stream errors.

```js
readStream.on("error", (err) => {
  console.error(err);
});

writeStream.on("error", (err) => {
  console.error(err);
});
```

Or use `pipeline()` which handles them automatically.

---

# HighWaterMark (Chunk Size)

Default chunk size:

```txt
64KB
```

Can be customized:

```js
fs.createReadStream("large.txt", {
  highWaterMark: 1024 * 1024,
});
```

This uses:

```txt
1MB chunks
```

Trade-off:

| Larger Chunks     | Smaller Chunks        |
| ----------------- | --------------------- |
| Fewer reads       | More reads            |
| Higher memory     | Lower memory          |
| Better throughput | Better responsiveness |

---

# Streams vs readFile()

| Feature                       | readFile() | Streams   |
| ----------------------------- | ---------- | --------- |
| Loads whole file              | ✅         | ❌        |
| Memory efficient              | ❌         | ✅        |
| Large files                   | Poor       | Excellent |
| Starts processing immediately | ❌         | ✅        |
| Backpressure support          | ❌         | ✅        |

---

# Best Practices

### Use streams for large files

```js
fs.createReadStream();
```

### Prefer `pipeline()`

```js
await pipeline(...)
```

### Handle errors properly

```js
stream.on("error", ...)
```

### Let `pipe()` manage backpressure

```js
readable.pipe(writable);
```

### Avoid `readFile()` for huge files

```js
// Not recommended
fs.readFile("20GB.dat");
```

---

# Final Interview Answer

> To efficiently handle large files in Node.js, use streams such as `fs.createReadStream()` and `fs.createWriteStream()` instead of loading the entire file into memory with `fs.readFile()`. Streams process data in chunks, significantly reducing memory usage and allowing processing to begin immediately. For transferring data between streams, `pipe()` or the modern `pipeline()` API should be used because they automatically manage backpressure, handle errors more reliably, and provide better scalability. This makes streams ideal for file uploads, downloads, video streaming, log processing, and other large-data workloads.

## Question 7. Difference between `fs.readFile` and streams

## Short Answer

The main difference is that **`fs.readFile()` loads the entire file into memory before returning it**, whereas **streams (`fs.createReadStream()`) read the file in small chunks and process it incrementally**.

- **`fs.readFile()`** → Simple, good for small files.
- **Streams** → Memory-efficient, ideal for large files.

---

# Basic Comparison

## `fs.readFile()`

```js
const fs = require("fs");

fs.readFile("file.txt", "utf8", (err, data) => {
  console.log(data);
});
```

Behavior:

```txt
Entire File
     ↓
Load into Memory
     ↓
Callback Executes
```

The callback runs only after the whole file has been read.

---

## Stream

```js
const fs = require("fs");

const stream = fs.createReadStream("file.txt", {
  encoding: "utf8",
});

stream.on("data", (chunk) => {
  console.log(chunk);
});
```

Behavior:

```txt
File
 ↓
Chunk 1
 ↓
Chunk 2
 ↓
Chunk 3
```

Data is available immediately as chunks arrive.

---

# Memory Usage

## `fs.readFile()`

Suppose you read a 5 GB file:

```js
fs.readFile("5GB.zip", callback);
```

Memory:

```txt
5GB File
 ↓
5GB RAM
```

Potential issues:

- High memory consumption
- Out-of-memory crashes
- Poor scalability

---

## Streams

```js
fs.createReadStream("5GB.zip");
```

Memory:

```txt
5GB File
 ↓
64KB Chunk
 ↓
Process
 ↓
Next Chunk
```

Only a small chunk is kept in memory at any time.

---

# Performance

### `fs.readFile()`

```txt
Read Entire File
       ↓
Process Data
```

No processing can begin until reading finishes.

---

### Streams

```txt
Read Chunk
     ↓
Process Chunk
     ↓
Read Next Chunk
```

Processing can start immediately.

---

# Internal Behavior

## `fs.readFile()`

Internally:

```txt
fs.readFile
      ↓
libuv Thread Pool
      ↓
Entire Buffer Created
      ↓
Callback Invoked
```

Returns one large Buffer.

---

## Streams

Internally:

```txt
createReadStream
        ↓
libuv
        ↓
Chunk
        ↓
Chunk
        ↓
Chunk
```

Emits multiple `data` events.

---

# Example: Reading a Small Config File

Good use case for `readFile()`:

```js
fs.readFile("config.json", "utf8", (err, data) => {
  const config = JSON.parse(data);
});
```

File size:

```txt
2 KB
```

Loading everything into memory is perfectly fine.

---

# Example: Processing a Large Log File

Bad:

```js
fs.readFile("server.log", (err, data) => {
  console.log(data.toString());
});
```

If:

```txt
server.log = 20GB
```

you may exhaust memory.

---

Better:

```js
const stream = fs.createReadStream("server.log");

stream.on("data", (chunk) => {
  console.log(chunk.toString());
});
```

---

# Copying Files

## Using `readFile()`

```js
fs.readFile("input.zip", (err, data) => {
  fs.writeFile("output.zip", data, () => {});
});
```

Memory:

```txt
Input File
      ↓
Huge Buffer
      ↓
Output File
```

---

## Using Streams

```js
fs.createReadStream("input.zip").pipe(fs.createWriteStream("output.zip"));
```

Memory:

```txt
Input
 ↓
Chunk
 ↓
Chunk
 ↓
Output
```

Much more efficient.

---

# Backpressure Support

A critical interview topic.

## `fs.readFile()`

No backpressure.

```txt
Read Everything
       ↓
Store Everything
```

Memory usage can spike.

---

## Streams

Built-in backpressure.

```txt
Readable
    ↓
Writable Busy
    ↓
Pause Reading
    ↓
Resume Later
```

This prevents memory overload.

---

# Event-Based Nature

## `readFile()`

Single callback:

```js
fs.readFile(file, (err, data) => {
  console.log(data);
});
```

Only one result.

---

## Stream

Multiple events:

```js
stream.on("data", (chunk) => {});
stream.on("end", () => {});
stream.on("error", (err) => {});
```

Lifecycle:

```txt
open
 ↓
data
 ↓
data
 ↓
data
 ↓
end
```

---

# When to Use Each

## Use `fs.readFile()` When

✔ Small files

```txt
config.json
settings.json
template.html
```

✔ Need entire content at once

✔ Simpler code

---

## Use Streams When

✔ Large files

```txt
video.mp4
backup.zip
logs.txt
database dumps
```

✔ File uploads/downloads

✔ Real-time processing

✔ Low memory usage required

---

# Async/Await Versions

## readFile

```js
const fs = require("fs/promises");

const data = await fs.readFile("file.txt", "utf8");
```

Simple and clean.

---

## Stream

```js
const fs = require("fs");

const stream = fs.createReadStream("file.txt");
```

Works incrementally.

---

# Real-World Examples

### `readFile()`

- Load configuration
- Read small JSON
- Load templates
- Read environment files

---

### Streams

- Video streaming
- File uploads
- File downloads
- Log processing
- ETL pipelines
- Data migration

---

# Interview Comparison Table

| Feature                      | `fs.readFile()` | Streams |
| ---------------------------- | --------------- | ------- |
| Reads whole file             | ✅              | ❌      |
| Reads in chunks              | ❌              | ✅      |
| Memory efficient             | ❌              | ✅      |
| Suitable for large files     | ❌              | ✅      |
| Simpler API                  | ✅              | ❌      |
| Supports backpressure        | ❌              | ✅      |
| Start processing immediately | ❌              | ✅      |
| Best for small files         | ✅              | ⚠️      |

---

# Common Interview Follow-Up

### Is `fs.readFile()` asynchronous?

Yes.

```js
fs.readFile(...)
```

is asynchronous because it uses libuv's thread pool.

However:

```txt
Asynchronous ≠ Streaming
```

A common misconception is that because `readFile()` is async, it is memory-efficient. It is not—it still loads the entire file into memory before invoking the callback.

---

# Final Interview Answer

> `fs.readFile()` and streams are both asynchronous ways to read files in Node.js, but they differ significantly in memory usage and scalability. `fs.readFile()` reads the entire file into memory before executing the callback, making it suitable for small files and simple use cases. Streams, created with `fs.createReadStream()`, read files incrementally in chunks, allowing processing to begin immediately and keeping memory usage low. Streams also support backpressure, making them the preferred choice for large files, file transfers, video streaming, and other high-performance applications.

## Question 8. How to prevent callback hell in Node.js

## Short Answer

To prevent **callback hell in Node.js**, you should avoid deeply nested callbacks by using:

- **Promises**
- **async/await (best modern solution)**
- **modular code (splitting functions)**
- **named functions instead of inline callbacks**

These approaches make asynchronous code **flat, readable, and maintainable**.

---

# 1. What is Callback Hell?

Callback hell happens when multiple asynchronous operations are nested inside each other, creating deeply indented and hard-to-read code.

### Example (Bad Code)

```js id="k9v3p2"
fs.readFile("a.txt", (err, dataA) => {
  fs.readFile("b.txt", (err, dataB) => {
    fs.readFile("c.txt", (err, dataC) => {
      console.log(dataA, dataB, dataC);
    });
  });
});
```

### Problems:

- Hard to read
- Difficult to maintain
- Poor error handling
- Hard to debug
- Tight coupling of logic

---

# 2. Solution 1: Use Promises

Promises flatten asynchronous flow.

## Convert callback-style APIs → Promise-based

```js id="1z4q7c"
const fs = require("fs/promises");

Promise.all([
  fs.readFile("a.txt", "utf8"),
  fs.readFile("b.txt", "utf8"),
  fs.readFile("c.txt", "utf8"),
]).then(([a, b, c]) => {
  console.log(a, b, c);
});
```

---

## Sequential Promises (still readable)

```js id="v8xk2p"
fs.readFile("a.txt", "utf8")
  .then((a) => {
    return fs.readFile("b.txt", "utf8").then((b) => [a, b]);
  })
  .then(([a, b]) => {
    return fs.readFile("c.txt", "utf8").then((c) => [a, b, c]);
  })
  .then(([a, b, c]) => {
    console.log(a, b, c);
  });
```

Better than callbacks, but still can get messy.

---

# 3. Solution 2: async/await (Best Approach)

Async/await makes async code look synchronous.

```js id="h2r8qk"
const fs = require("fs/promises");

async function readFiles() {
  const a = await fs.readFile("a.txt", "utf8");
  const b = await fs.readFile("b.txt", "utf8");
  const c = await fs.readFile("c.txt", "utf8");

  console.log(a, b, c);
}

readFiles();
```

---

## Why this is better:

- No nesting
- Easier debugging
- Clear flow
- Try/catch for error handling

---

## Parallel execution with async/await

```js id="m9t2kq"
async function readFiles() {
  const [a, b, c] = await Promise.all([
    fs.readFile("a.txt", "utf8"),
    fs.readFile("b.txt", "utf8"),
    fs.readFile("c.txt", "utf8"),
  ]);

  console.log(a, b, c);
}
```

---

# 4. Solution 3: Modularize Code

Break logic into smaller functions.

### Bad:

```js id="g7p1xq"
fs.readFile("a.txt", (err, a) => {
  fs.readFile("b.txt", (err, b) => {
    fs.readFile("c.txt", (err, c) => {
      // huge logic here
    });
  });
});
```

---

### Good:

```js id="d3v8pm"
async function readA() {
  return fs.readFile("a.txt", "utf8");
}

async function readB() {
  return fs.readFile("b.txt", "utf8");
}

async function readC() {
  return fs.readFile("c.txt", "utf8");
}

async function main() {
  const [a, b, c] = await Promise.all([readA(), readB(), readC()]);
  console.log(a, b, c);
}
```

---

# 5. Solution 4: Named Functions Instead of Anonymous Callbacks

Instead of nesting:

```js id="k2v9mx"
fs.readFile("a.txt", function handleA(err, a) {
  fs.readFile("b.txt", function handleB(err, b) {
    console.log(a, b);
  });
});
```

You can flatten:

```js id="p1n6rz"
function handleB(err, b, a) {
  console.log(a, b);
}

function handleA(err, a) {
  fs.readFile("b.txt", (err, b) => handleB(err, b, a));
}

fs.readFile("a.txt", handleA);
```

Still not ideal, but improves readability in older codebases.

---

# 6. Solution 5: Use Control Flow Libraries (Legacy)

Before Promises were standard:

- async.js
- Q
- Bluebird

Example:

```js id="r5m3tx"
async.series(
  [(cb) => fs.readFile("a.txt", cb), (cb) => fs.readFile("b.txt", cb)],
  (err, results) => {
    console.log(results);
  },
);
```

⚠️ Rare in modern codebases.

---

# 7. Error Handling Improvement

## Callback Hell Problem

```js id="x2n9qp"
fs.readFile("a.txt", (err, a) => {
  if (err) throw err;

  fs.readFile("b.txt", (err, b) => {
    if (err) throw err;

    fs.readFile("c.txt", (err, c) => {
      if (err) throw err;
    });
  });
});
```

---

## With async/await

```js id="w4k9zd"
async function read() {
  try {
    const a = await fs.readFile("a.txt", "utf8");
    const b = await fs.readFile("b.txt", "utf8");
    const c = await fs.readFile("c.txt", "utf8");

    console.log(a, b, c);
  } catch (err) {
    console.error("Error:", err);
  }
}
```

Much cleaner centralized error handling.

---

# 8. Key Concept: Why Callback Hell Happens

It happens due to:

- Inversion of control (callbacks passed into APIs)
- Sequential async dependencies
- Lack of composability in older Node.js APIs

Modern solutions solve this by:

```txt id="y7m3qp"
Callbacks → Promises → async/await
```

---

# 9. Best Practices (Interview Ready)

### 1. Prefer async/await

```js id="a9k2qp"
await doTask();
```

---

### 2. Use Promise.all for parallel tasks

```js id="p8x3mn"
await Promise.all([task1(), task2()]);
```

---

### 3. Avoid deep nesting

```txt id="z3n8qp"
❌ callback inside callback inside callback
```

---

### 4. Break logic into functions

```js id="c7m2qz"
function fetchUser() {}
function fetchOrders() {}
```

---

### 5. Always handle errors centrally

```js id="t5n8xp"
try {
  await run();
} catch (err) {}
```

---

# 10. Final Interview Answer

> Callback hell in Node.js occurs when multiple asynchronous callbacks are deeply nested, making code hard to read and maintain. It can be prevented using Promises, which flatten asynchronous chains, and more effectively using async/await, which allows asynchronous code to be written in a synchronous style. Additionally, breaking logic into smaller functions, using Promise.all for parallel execution, and centralizing error handling with try/catch further improves code readability and maintainability. Modern Node.js development strongly favors async/await over callback-based designs to avoid callback hell entirely.

## Question 9. How to handle unhandled promise rejections in Node.js

## Short Answer

In Node.js, **unhandled promise rejections** can be handled using:

1. **`.catch()` on Promises (best practice)**
2. **`try...catch` with async/await**
3. **Global handler: `process.on('unhandledRejection')` (last safety net)**

The correct approach is to **always handle errors at the Promise level**, and use global handlers only for logging and crash prevention.

---

# 1. What is an Unhandled Promise Rejection?

It happens when a Promise is rejected but no `.catch()` or `try...catch` handles it.

### Example (Problematic Code)

```js id="k2m9qz"
Promise.reject(new Error("Something failed"));
```

Or:

```js id="v7n2pm"
async function test() {
  throw new Error("Fail");
}

test(); // ❌ no await or catch
```

Node.js treats this as an **unhandled rejection**.

---

# 2. Why It Matters

Unhandled rejections can lead to:

- Silent failures
- Broken application state
- Memory leaks
- In modern Node.js → **process crash (in strict mode or future versions)**

---

# 3. Proper Way #1: Always Use `.catch()`

```js id="x8p2mn"
fetchData()
  .then((data) => {
    console.log(data);
  })
  .catch((err) => {
    console.error("Error handled:", err);
  });
```

✔ Ensures every Promise is handled
✔ Prevents unhandled rejection warnings

---

# 4. Proper Way #2: Use async/await with try...catch

This is the **preferred modern approach**.

```js id="b4k9xq"
async function run() {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (err) {
    console.error("Caught error:", err);
  }
}

run();
```

✔ Clean
✔ Centralized error handling
✔ Prevents unhandled rejections completely

---

# 5. Common Mistake (Very Important)

### ❌ Forgetting await

```js id="p3m8qz"
async function run() {
  try {
    fetchData(); // ❌ missing await
  } catch (err) {
    console.log("This will NOT catch errors");
  }
}
```

Because the Promise runs outside try/catch scope.

---

# 6. Proper Way #3: Global Handler (Safety Net)

Node.js provides a global event:

```js id="n7x2kp"
process.on("unhandledRejection", (reason, promise) => {
  console.error("Unhandled Rejection:", reason);
});
```

---

## What it gives you:

- Logs unhandled errors
- Helps debugging
- Prevents silent failures

---

## BUT Important:

👉 This is NOT a replacement for proper handling
👉 It is only a **last line of defense**

---

# 7. Recommended Production Pattern

### Log + Graceful Shutdown (Best Practice)

```js id="m9x2qp"
process.on("unhandledRejection", (reason) => {
  console.error("Unhandled Rejection detected:", reason);

  // Optional: cleanup resources
  server.close(() => {
    process.exit(1); // fail fast
  });
});
```

---

## Why shutdown?

Because unhandled rejections often indicate:

- DB failure
- broken state
- critical bug

Continuing execution may corrupt application state.

---

# 8. Also Handle Uncaught Exceptions

Related but different:

```js id="q8m3xz"
process.on("uncaughtException", (err) => {
  console.error("Uncaught Exception:", err);
  process.exit(1);
});
```

Difference:

| Type               | Cause                         |
| ------------------ | ----------------------------- |
| unhandledRejection | Promise rejection not handled |
| uncaughtException  | Sync runtime error            |

---

# 9. Modern Node.js Behavior

Depending on Node version:

- Old versions → warning only
- Newer versions → warning + potential future crash behavior

So best practice is:

```txt id="t6k2mp"
Always handle Promises explicitly
Never rely on global handlers alone
```

---

# 10. Handling in Express.js (Real-world Case)

### Bad

```js id="h2n9qp"
app.get("/user", async (req, res) => {
  const user = await getUser(); // ❌ no error handling
  res.json(user);
});
```

---

### Good

```js id="c8m2xz"
app.get("/user", async (req, res, next) => {
  try {
    const user = await getUser();
    res.json(user);
  } catch (err) {
    next(err);
  }
});
```

---

Or use wrapper:

```js id="f7k3mn"
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);
```

---

# 11. Best Practices (Interview Ready)

### 1. Always handle Promises locally

```js id="p9m2xq"
await task().catch(handleError);
```

---

### 2. Prefer async/await + try/catch

```js id="r4k8qp"
try {
  await task();
} catch (err) {}
```

---

### 3. Avoid fire-and-forget Promises

```js id="x1m9qp"
// ❌ bad
doWork();
```

---

### 4. Use global handler only for logging + shutdown

```js id="z7n2kp"
process.on("unhandledRejection", handler);
```

---

### 5. Always await async functions

```js id="w3m8xz"
await doWork();
```

---

# 12. Final Interview Answer

> Unhandled promise rejections occur when a Promise is rejected but no `.catch()` or `try...catch` handles the error. In Node.js, they should be prevented by always handling Promises explicitly using `.catch()` or async/await with try/catch blocks. As a safety net, Node provides the `process.on('unhandledRejection')` event to log and optionally terminate the process, but it should not replace proper error handling. In production systems, unhandled rejections are typically treated as critical errors, and applications often shut down gracefully to avoid inconsistent states.

## Question 10. How to implement rate limiting in Node.js API

## Question 11. How to implement JWT authentication in Node.js

## Question 12. How to handle concurrent requests in Node.js

## Question 13. Difference between worker threads and cluster module in Node.js

## Question 14. How to implement WebSocket server in Node.js

## Question 15. Difference between ES2023 Array.at() and `[]` access

## Question 16. How to use `Object.hasOwn()` in ES2023

## Question 17. How to use top-level await in ES modules

## Question 18. How to implement private class methods with `#` syntax

## Question 19. How to implement `Promise.any()` and differences from `Promise.race`

## Question 20. How to implement custom error classes and inheritance in JavaScript
