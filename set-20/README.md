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

## Question 5. How to implement error handling in async/await in Node.js

## Question 6. How to stream large files efficiently in Node.js

## Question 7. Difference between `fs.readFile` and streams

## Question 8. How to prevent callback hell in Node.js

## Question 9. How to handle unhandled promise rejections in Node.js

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
