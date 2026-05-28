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

## Question 3. Difference between CommonJS and ES modules in Node.js

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
