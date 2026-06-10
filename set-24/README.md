# Set 24

| S.No. | Question                                                                                                                                               |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How to prevent memory leaks in long-running Node.js processes](#question-1-how-to-prevent-memory-leaks-in-long-running-nodejs-processes)              |
| 2.    | [How to implement graceful shutdown in Node.js server](#question-2-how-to-implement-graceful-shutdown-in-nodejs-server)                                |
| 3.    | [How to implement a task queue with async workers](#question-3-how-to-implement-a-task-queue-with-async-workers)                                       |
| 4.    | [How to handle backpressure with streams in Node.js](#question-4-how-to-handle-backpressure-with-streams-in-nodejs)                                    |
| 5.    | [How to implement throttling and debouncing in backend APIs](#question-5-how-to-implement-throttling-and-debouncing-in-backend-apis)                   |
| 6.    | [How to use EventEmitter for pub/sub in Node.js](#question-6-how-to-use-eventemitter-for-pubsub-in-nodejs)                                             |
| 7.    | [Difference between EventEmitter and Observables](#question-7-difference-between-eventemitter-and-observables)                                         |
| 8.    | [How to implement a microservices architecture in Node.js](#question-8-how-to-implement-a-microservices-architecture-in-nodejs)                        |
| 9.    | [How to handle cross-service communication efficiently](#question-9-how-to-handle-cross-service-communication-efficiently)                             |
| 10.   | [How to monitor and profile Node.js performance using built-in tools](#question-10-how-to-monitor-and-profile-nodejs-performance-using-built-in-tools) |
| 11.   | [How to implement private class fields and methods using `#`](#question-11-how-to-implement-private-class-fields-and-methods-using-)                   |
| 12.   | [How to use Top-Level await in ES modules](#question-12-how-to-use-top-level-await-in-es-modules)                                                      |
| 13.   | [How to use `Array.prototype.at()` effectively](#question-13-how-to-use-arrayprototypeat-effectively)                                                  |
| 14.   | [How to use `Object.hasOwn()` instead of `hasOwnProperty()`](#question-14-how-to-use-objecthasown-instead-of-hasownproperty)                           |
| 15.   | [Difference between `Promise.any()` and `Promise.race()`](#question-15-difference-between-promiseany-and-promiserace)                                  |
| 16.   | [How to implement custom error classes and error inheritance](#question-16-how-to-implement-custom-error-classes-and-error-inheritance)                |
| 17.   | [How to prevent XSS vulnerabilities in JavaScript apps](#question-17-how-to-prevent-xss-vulnerabilities-in-javascript-apps)                            |
| 18.   | [How to prevent CSRF in frontend-backend communication](#question-18-how-to-prevent-csrf-in-frontend-backend-communication)                            |
| 19.   | [How to safely evaluate dynamic JS code (avoiding `eval`)](#question-19-how-to-safely-evaluate-dynamic-js-code-avoiding-eval)                          |
| 20.   | [How to implement the module pattern to protect private variables](#question-20-how-to-implement-the-module-pattern-to-protect-private-variables)      |

## Question 1. How to prevent memory leaks in long-running Node.js processes

> Preventing memory leaks in long-running Node.js processes requires understanding how memory is allocated, retained, and garbage collected in the V8 engine. The key principle is: **a memory leak happens when objects are no longer needed but are still referenced somewhere, preventing garbage collection**.

### How Memory Management Works in Node.js

Node.js uses:

- The **V8 JavaScript engine**
- An automatic **garbage collector (GC)**

When objects become unreachable, V8 frees them automatically.

Example:

```js
function createUser() {
  const user = { name: "John" };
  return user;
}

createUser();
```

After the function finishes, `user` becomes unreachable and is garbage collected.

A leak occurs when references remain alive unintentionally.

### Common Causes of Memory Leaks in Node.js

#### 1. Global Variables

Globals stay alive for the lifetime of the process.

```js
global.cache = [];
```

Or accidentally:

```js
users = []; // Missing let/const
```

##### Problem

Objects stored globally are never garbage collected.

##### Prevention

Use:

- `const`
- `let`
- module-scoped variables carefully

```js
const users = [];
```

Enable strict mode:

```js
"use strict";
```

#### 2. Unbounded Caches

A very common production issue.

##### Bad Example

```js
const cache = {};

function getUser(id) {
  if (!cache[id]) {
    cache[id] = fetchUser(id);
  }

  return cache[id];
}
```

This cache grows forever.

##### Better Approach: LRU Cache

Use bounded caches with expiration.

```js
const LRU = require("lru-cache");

const cache = new LRU({
  max: 500,
  ttl: 1000 * 60 * 5,
});
```

##### Best Practices

- Add size limits
- Use TTL (time-to-live)
- Periodically evict stale data

#### 3. Event Listener Leaks

Listeners retain references to objects.

##### Bad Example

```js
emitter.on("data", handler);
```

If never removed, memory accumulates.

##### Prevention

Use:

```js
emitter.off("data", handler);
```

Or:

```js
emitter.once("data", handler);
```

##### Warning Sign

Node.js warns:

```txt
MaxListenersExceededWarning
```

This often indicates leaks.

#### 4. Timers and Intervals

Forgotten timers keep closures alive.

##### Problem

```js
setInterval(() => {
  console.log(bigObject);
}, 1000);
```

`bigObject` can never be freed.

##### Prevention

Clear timers:

```js
const interval = setInterval(doWork, 1000);

clearInterval(interval);
```

For long-running apps:

- Track active timers
- Clean them during shutdown

#### 5. Closures Holding Large Objects

Closures preserve outer scope references.

##### Leak Example

```js
function createHandler() {
  const largeData = new Array(1000000).fill("*");

  return function () {
    console.log("handler active");
  };
}

const handler = createHandler();
```

Even though `largeData` isn't used, the closure may retain it.

##### Prevention

Avoid capturing unnecessary variables.

```js
function createHandler() {
  return function () {
    console.log("handler active");
  };
}
```

#### 6. Streams Not Properly Closed

Unclosed streams/file descriptors leak resources.

##### Bad Example

```js
fs.createReadStream("bigfile.txt");
```

Without cleanup, descriptors remain open.

##### Proper Handling

```js
const stream = fs.createReadStream("bigfile.txt");

stream.on("error", handleError);

stream.on("close", () => {
  console.log("closed");
});
```

Or use pipeline:

```js
const { pipeline } = require("stream");

pipeline(src, dest, (err) => {
  if (err) console.error(err);
});
```

#### 7. Detached Objects in Express Apps

Sometimes request/response objects get stored accidentally.

##### Bad Example

```js
const requests = [];

app.use((req, res, next) => {
  requests.push(req);
  next();
});
```

Every request remains in memory forever.

#### 8. Promise Leaks

Pending promises may retain memory.

##### Example

```js
new Promise(() => {});
```

This promise never resolves or rejects.

##### Prevention

Always settle promises.

Use timeouts when needed:

```js
const controller = new AbortController();

setTimeout(() => controller.abort(), 5000);
```

#### 9. Large Buffers

Buffers allocate memory outside the V8 heap.

##### Example

```js
const buffer = Buffer.alloc(500 * 1024 * 1024);
```

Huge buffers can crash processes.

##### Prevention

- Stream data instead of loading everything
- Avoid unnecessary copies
- Use backpressure properly

### Monitoring and Detecting Memory Leaks

Interviewers often expect tooling knowledge.

#### 1. Monitor Process Memory

```js
console.log(process.memoryUsage());
```

Output:

```js
{
  (rss, heapTotal, heapUsed, external, arrayBuffers);
}
```

Important:

- `heapUsed` continuously growing may indicate leaks

#### 2. Use Heap Snapshots

Tools:

- Chrome DevTools
- Node Inspector
- Heapdump

Start Node with:

```bash
node --inspect app.js
```

Then inspect memory in Chrome DevTools.

#### 3. Use Clinic.js

Excellent production diagnostics tool.

```bash
npm install -g clinic
```

Run:

```bash
clinic doctor -- node app.js
```

#### 4. Use heapdump

```bash
npm install heapdump
```

```js
const heapdump = require("heapdump");

heapdump.writeSnapshot("./snapshot.heapsnapshot");
```

Analyze snapshots in Chrome.

#### 5. Monitor Event Loop + GC

Useful packages:

- `node --trace-gc`
- `clinic`
- `pm2 monit`

### Best Practices for Preventing Memory Leaks

#### Use Streaming for Large Data

Instead of:

```js
const data = fs.readFileSync("huge.json");
```

Use:

```js
fs.createReadStream("huge.json");
```

#### Avoid Massive In-Memory State

Prefer:

- Redis
- Databases
- External caches

Over:

- Huge JS objects
- Giant arrays

#### Clean Up Resources

Always close:

- DB connections
- Streams
- Sockets
- Intervals
- File handles

#### Use WeakMap / WeakSet When Appropriate

Weak references allow GC.

```js
const weakMap = new WeakMap();
```

Useful for:

- Metadata
- Object-associated caches

##### Example: WeakMap vs Map

```js
const map = new Map();
let user = { id: 1 };

map.set(user, "metadata");

user = null;
```

Memory is retained because `Map` keeps strong references.

With `WeakMap`:

```js
const weakMap = new WeakMap();

let user = { id: 1 };

weakMap.set(user, "metadata");

user = null;
```

Now GC can reclaim memory.

### Production Strategies

#### Use Process Isolation

Run workers separately:

- Worker Threads
- Cluster mode
- Containers

This limits leak impact.

#### Restart Strategies

Tools like PM2 can restart unhealthy processes automatically.

Example:

```bash
pm2 start app.js --max-memory-restart 500M
```

### Important Node.js Concepts

#### Heap vs Stack

- Stack → primitives/function calls
- Heap → objects/functions/closures

Most leaks happen in the heap.

#### V8 Generational GC

V8 separates memory into:

- Young generation
- Old generation

Long-lived leaked objects move into old space, making GC slower.

### Summary

- Memory leaks occur due to retained references
- Common causes:
  - Globals
  - Closures
  - Event listeners
  - Timers
  - Unbounded caches
  - Streams

- Prevention techniques:
  - Proper cleanup
  - WeakMap
  - Bounded caches
  - Streaming
  - Removing listeners

- Detection tools:
  - `process.memoryUsage`
  - Heap snapshots
  - Chrome DevTools
  - Clinic.js
  - `--inspect`

- Production strategies:
  - Monitoring
  - Restart policies
  - Process isolation

## Question 2. How to implement graceful shutdown in Node.js server

Graceful shutdown in Node.js means **stopping a server safely without abruptly terminating active requests, database operations, or background jobs**.

A proper graceful shutdown process typically:

1. Stops accepting new requests
2. Finishes ongoing requests
3. Closes database/socket connections
4. Cleans up resources
5. Exits the process safely

This is especially important in:

- Production APIs
- Docker/Kubernetes deployments
- Microservices
- Long-running Node.js processes

---

# Why Graceful Shutdown Matters

Without graceful shutdown:

- Active HTTP requests may fail
- Database writes may be interrupted
- File operations may corrupt data
- Users may receive errors
- Memory/resources may leak

Example:

- Kubernetes sends `SIGTERM`
- Your app immediately exits
- In-flight requests are lost

Graceful shutdown prevents this.

---

# Common Shutdown Signals

Node.js applications usually listen for OS signals.

## Important Signals

| Signal    | Meaning                                 |
| --------- | --------------------------------------- |
| `SIGINT`  | Ctrl+C in terminal                      |
| `SIGTERM` | Termination request (Docker/Kubernetes) |
| `SIGHUP`  | Terminal/session closed                 |

---

# Basic Graceful Shutdown Example

```js id="sdc6dv"
const http = require("http");

const server = http.createServer((req, res) => {
  setTimeout(() => {
    res.end("Done");
  }, 2000);
});

server.listen(3000, () => {
  console.log("Server running on port 3000");
});

function gracefulShutdown(signal) {
  console.log(`Received ${signal}`);

  server.close(() => {
    console.log("HTTP server closed");
    process.exit(0);
  });
}

process.on("SIGINT", () => gracefulShutdown("SIGINT"));
process.on("SIGTERM", () => gracefulShutdown("SIGTERM"));
```

---

# How `server.close()` Works

```js id="u24q8l"
server.close(callback);
```

It:

- Stops accepting new connections
- Waits for existing requests to finish
- Calls callback after all connections close

Important:

- Existing keep-alive connections may delay shutdown

---

# Handling Hanging Connections

Some clients may keep sockets open forever.

Track sockets manually.

---

# Advanced Socket Tracking

```js id="zbbv18"
const sockets = new Set();

server.on("connection", (socket) => {
  sockets.add(socket);

  socket.on("close", () => {
    sockets.delete(socket);
  });
});
```

Destroy remaining sockets during shutdown:

```js id="tjlwm2"
function gracefulShutdown() {
  server.close(() => {
    console.log("Server closed");
  });

  setTimeout(() => {
    sockets.forEach((socket) => socket.destroy());
  }, 5000);
}
```

This prevents infinite shutdown hangs.

---

# Graceful Shutdown with Express

```js id="m0vtbr"
const express = require("express");

const app = express();

const server = app.listen(3000, () => {
  console.log("Running");
});

async function shutdown() {
  console.log("Shutdown started");

  server.close(() => {
    console.log("HTTP server closed");
  });

  process.exit(0);
}

process.on("SIGTERM", shutdown);
process.on("SIGINT", shutdown);
```

---

# Proper Async Cleanup

Real applications also close:

- Databases
- Redis
- Queues
- Kafka consumers
- WebSockets

---

# Example with MongoDB

```js id="ycdy8r"
const mongoose = require("mongoose");

async function shutdown() {
  console.log("Graceful shutdown");

  server.close(async () => {
    console.log("HTTP closed");

    await mongoose.connection.close();

    console.log("MongoDB closed");

    process.exit(0);
  });
}
```

---

# Example with PostgreSQL

```js id="tptiyv"
const { Pool } = require("pg");

const pool = new Pool();

async function shutdown() {
  server.close(async () => {
    await pool.end();
    process.exit(0);
  });
}
```

---

# Shutdown Timeout Strategy

Sometimes cleanup hangs forever.

Use a force-exit timeout.

```js id="f9hupm"
async function shutdown() {
  console.log("Shutdown initiated");

  const forceTimeout = setTimeout(() => {
    console.error("Force shutdown");
    process.exit(1);
  }, 10000);

  try {
    await closeResources();

    clearTimeout(forceTimeout);

    process.exit(0);
  } catch (err) {
    console.error(err);
    process.exit(1);
  }
}
```

Best practice:

- 10–30 second timeout

---

# Prevent New Requests During Shutdown

Sometimes you should reject new requests immediately.

---

# Example

```js id="odc6uj"
let isShuttingDown = false;

app.use((req, res, next) => {
  if (isShuttingDown) {
    return res.status(503).send("Server restarting");
  }

  next();
});
```

During shutdown:

```js id="p8k8gh"
isShuttingDown = true;
```

Useful in:

- Load-balanced systems
- Kubernetes rolling deployments

---

# Handling Background Jobs

If using queues:

- BullMQ
- RabbitMQ
- Kafka
- Agenda

Stop consuming new jobs first.

---

# Example

```js id="ly4z0u"
await worker.close();
```

Or:

```js id="ngf7h5"
consumer.disconnect();
```

Important:

- Finish current jobs safely
- Avoid duplicate processing

---

# Kubernetes Graceful Shutdown

In Kubernetes:

1. Pod receives `SIGTERM`
2. Pod removed from load balancer
3. Grace period begins
4. App must shut down cleanly

Default grace period:

- 30 seconds

---

# Docker Graceful Shutdown

Docker sends:

```bash id="0bjh65"
SIGTERM
```

Then after timeout:

```bash id="38y9t2"
SIGKILL
```

Your Node.js app should handle `SIGTERM`.

---

# Common Mistakes

## 1. Calling `process.exit()` Immediately

Bad:

```js id="v04njf"
process.on("SIGTERM", () => {
  process.exit(0);
});
```

This kills active requests instantly.

---

## 2. Forgetting Async Cleanup

Bad:

```js id="3j4a0n"
mongoose.connection.close();
process.exit(0);
```

The DB close may not complete.

Always await cleanup.

---

## 3. Not Handling Open Connections

WebSockets or keep-alive sockets may block shutdown forever.

Track and close them.

---

# Using AbortController (Modern Approach)

Modern Node.js supports `AbortController`.

---

# Example

```js id="dybhav"
const controller = new AbortController();

process.on("SIGTERM", () => {
  controller.abort();
});
```

Useful for:

- Fetch requests
- Streams
- Long-running async tasks

---

# Production-Grade Graceful Shutdown Pattern

```js id="wwq5g6"
const express = require("express");

const app = express();

const server = app.listen(3000);

let shuttingDown = false;

process.on("SIGTERM", shutdown);
process.on("SIGINT", shutdown);

async function shutdown() {
  if (shuttingDown) return;

  shuttingDown = true;

  console.log("Shutdown started");

  const timeout = setTimeout(() => {
    console.error("Forced shutdown");
    process.exit(1);
  }, 10000);

  server.close(async () => {
    try {
      console.log("HTTP server closed");

      // Close DB
      // await mongoose.connection.close();

      // Close queues
      // await worker.close();

      clearTimeout(timeout);

      console.log("Shutdown complete");

      process.exit(0);
    } catch (err) {
      console.error(err);
      process.exit(1);
    }
  });
}
```

---

# Best Practices

## Always Handle

- `SIGINT`
- `SIGTERM`

---

## Use Timeouts

Prevent hanging shutdowns.

---

## Stop Accepting Traffic First

Close HTTP server immediately.

---

## Await Resource Cleanup

- DB pools
- Redis
- Queues
- Streams

---

## Make Shutdown Idempotent

Avoid running cleanup twice.

```js id="kym5h4"
if (shuttingDown) return;
```

---

# Interview-Level Insights

Senior-level discussion points:

## 1. Zero-Downtime Deployments

Graceful shutdown enables:

- Rolling deployments
- Blue-green deployments
- Canary releases

Without dropped requests.

---

## 2. Connection Draining

Load balancers should stop routing traffic before termination.

---

## 3. HTTP Keep-Alive Considerations

Persistent connections can delay shutdown.

Modern Node.js APIs help:

```js id="u0rq6f"
server.closeIdleConnections();
```

and:

```js id="x0vhf4"
server.closeAllConnections();
```

(Available in newer Node.js versions.)

---

# Event Loop Consideration

Node exits automatically when:

- Event loop becomes empty
- No active handles remain

Leaks during shutdown often happen because:

- Timers still exist
- Sockets remain open
- Streams are active

---

# Interview Summary

A strong interview answer should mention:

- Graceful shutdown safely terminates applications
- Handle `SIGINT` and `SIGTERM`
- Use `server.close()` to stop new requests
- Finish in-flight requests
- Close DBs, queues, streams, sockets
- Add force-exit timeout
- Handle keep-alive connections
- Important for Docker/Kubernetes deployments
- Avoid immediate `process.exit()`

That demonstrates both Node.js internals knowledge and real production engineering experience.

## Question 3. How to implement a task queue with async workers

## Question 4. How to handle backpressure with streams in Node.js

## Question 5. How to implement throttling and debouncing in backend APIs

## Question 6. How to use EventEmitter for pub/sub in Node.js

## Question 7. Difference between EventEmitter and Observables

## Question 8. How to implement a microservices architecture in Node.js

## Question 9. How to handle cross-service communication efficiently

## Question 10. How to monitor and profile Node.js performance using built-in tools

## Question 11. How to implement private class fields and methods using `#`

## Question 12. How to use Top-Level await in ES modules

## Question 13. How to use `Array.prototype.at()` effectively

## Question 14. How to use `Object.hasOwn()` instead of `hasOwnProperty()`

## Question 15. Difference between `Promise.any()` and `Promise.race()`

## Question 16. How to implement custom error classes and error inheritance

## Question 17. How to prevent XSS vulnerabilities in JavaScript apps

## Question 18. How to prevent CSRF in frontend-backend communication

## Question 19. How to safely evaluate dynamic JS code (avoiding `eval`)

## Question 20. How to implement the module pattern to protect private variables
