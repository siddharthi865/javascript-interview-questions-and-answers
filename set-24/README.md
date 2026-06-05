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

A task queue with async workers is a system where:

1. Tasks are added to a queue
2. Multiple workers process tasks concurrently
3. Workers execute asynchronous operations efficiently
4. Concurrency and retries can be controlled

This pattern is common in:

- Background job processing
- Email sending
- File uploads/transcoding
- API rate-limited systems
- Web scraping
- Microservices
- Message brokers

In interviews, you should explain:

- Queue structure
- Worker lifecycle
- Concurrency management
- Error handling
- Retry/backpressure strategies

---

# Basic Concept

```txt
Producer -> Queue -> Workers -> Results
```

Example:

- API receives image upload
- Image processing job added to queue
- Workers process images asynchronously

---

# Simple In-Memory Async Task Queue

---

# Step 1: Queue Class

```js id="ny8w4u"
class TaskQueue {
  constructor(concurrency = 1) {
    this.queue = [];
    this.running = 0;
    this.concurrency = concurrency;
  }

  push(task) {
    this.queue.push(task);
    this.next();
  }

  async next() {
    if (this.running >= this.concurrency) {
      return;
    }

    const task = this.queue.shift();

    if (!task) {
      return;
    }

    this.running++;

    try {
      await task();
    } catch (err) {
      console.error("Task failed:", err);
    } finally {
      this.running--;
      this.next();
    }
  }
}
```

---

# Usage Example

```js id="vax86n"
const queue = new TaskQueue(2);

function createTask(id, delay) {
  return async () => {
    console.log(`Starting ${id}`);

    await new Promise((resolve) => setTimeout(resolve, delay));

    console.log(`Finished ${id}`);
  };
}

queue.push(createTask(1, 2000));
queue.push(createTask(2, 1000));
queue.push(createTask(3, 500));
queue.push(createTask(4, 1500));
```

---

# Output Behavior

With concurrency = 2:

```txt id="7ktmcr"
Starting 1
Starting 2
Finished 2
Starting 3
Finished 3
Starting 4
Finished 1
Finished 4
```

Only 2 tasks run simultaneously.

---

# Key Concepts

---

# 1. Concurrency Control

This is the heart of worker queues.

```js id="mjlwm1"
if (this.running >= this.concurrency)
```

Prevents:

- CPU exhaustion
- API throttling
- Memory overload

---

# 2. Async Workers

Workers execute async functions:

```js id="h0dfvv"
await task();
```

This allows:

- Parallel I/O
- Efficient event-loop usage
- Non-blocking execution

---

# 3. Recursive Scheduling

After a task completes:

```js id="w1hlh2"
this.next();
```

Triggers processing of the next queued task.

---

# Improved Worker Pool Implementation

A cleaner production-style pattern.

---

# Worker Pool Example

```js id="0l6h84"
class WorkerPool {
  constructor(workerCount) {
    this.queue = [];
    this.workerCount = workerCount;
  }

  addTask(task) {
    this.queue.push(task);
  }

  async worker(workerId) {
    while (this.queue.length > 0) {
      const task = this.queue.shift();

      if (!task) break;

      console.log(`Worker ${workerId} processing`);

      try {
        await task();
      } catch (err) {
        console.error(err);
      }
    }
  }

  async run() {
    const workers = Array.from({ length: this.workerCount }, (_, i) =>
      this.worker(i + 1),
    );

    await Promise.all(workers);
  }
}
```

---

# Usage

```js id="w9o2o4"
const pool = new WorkerPool(3);

for (let i = 1; i <= 10; i++) {
  pool.addTask(async () => {
    console.log(`Task ${i} started`);

    await new Promise((resolve) => setTimeout(resolve, 1000));

    console.log(`Task ${i} done`);
  });
}

pool.run();
```

---

# Why This Works Well

Workers continuously:

1. Pull tasks
2. Await completion
3. Pull next task

This mimics:

- Thread pools
- Job consumers
- Distributed workers

---

# Implementing Retries

Production queues need retry support.

---

# Retry Example

```js id="tjlwmz"
async function executeWithRetry(task, retries = 3) {
  for (let attempt = 1; attempt <= retries; attempt++) {
    try {
      return await task();
    } catch (err) {
      console.log(`Retry ${attempt}`);

      if (attempt === retries) {
        throw err;
      }
    }
  }
}
```

Usage:

```js id="j0sg8r"
await executeWithRetry(task);
```

---

# Delayed Retry with Backoff

Very common interview topic.

---

# Exponential Backoff

```js id="pqsg3j"
function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function retry(task, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      return await task();
    } catch (err) {
      const delay = 2 ** i * 1000;

      console.log(`Retrying in ${delay}ms`);

      await sleep(delay);
    }
  }
}
```

Useful for:

- APIs
- DB reconnections
- Rate limits

---

# Priority Queue Implementation

Tasks may have different priorities.

---

# Simple Priority Queue

```js id="0o64g2"
push(task, priority = 0) {
  this.queue.push({ task, priority });

  this.queue.sort(
    (a, b) => b.priority - a.priority
  );

  this.next();
}
```

Higher priority tasks execute first.

---

# Backpressure Handling

Important for large systems.

Problem:

- Producers add tasks faster than workers consume them

---

# Solution

Limit queue size:

```js id="f61h4z"
if (this.queue.length > 1000) {
  throw new Error("Queue overloaded");
}
```

Or:

- Pause producers
- Use message brokers
- Persist jobs externally

---

# Persistent Queues

In-memory queues lose tasks if the process crashes.

Production systems use:

- Redis
- RabbitMQ
- Kafka
- SQS
- PostgreSQL

---

# Popular Node.js Queue Libraries

---

# 1. BullMQ

Built on Redis.

Features:

- Retries
- Delayed jobs
- Concurrency
- Rate limiting
- Persistence

[BullMQ](https://bullmq.io?utm_source=chatgpt.com)

Example:

```js id="jx1r6t"
const { Queue, Worker } = require("bullmq");

const queue = new Queue("emails");

await queue.add("send-email", {
  to: "test@example.com",
});

const worker = new Worker(
  "emails",
  async (job) => {
    console.log(job.data);
  },
  { concurrency: 5 },
);
```

---

# 2. Bee-Queue

Lightweight Redis queue.

[Bee-Queue](https://github.com/bee-queue/bee-queue?utm_source=chatgpt.com)

---

# 3. Agenda

MongoDB-backed scheduler.

[Agenda](https://agenda.github.io/agenda/agenda/6.x/?utm_source=chatgpt.com)

---

# Real-World Architecture

```txt
API Server
   ↓
Redis Queue
   ↓
Worker Processes
   ↓
Database / Email / External APIs
```

Advantages:

- Horizontal scaling
- Fault tolerance
- Async processing

---

# Worker Threads vs Async Workers

Important distinction.

---

# Async Workers

Use event loop concurrency.

Best for:

- I/O tasks
- APIs
- DB calls
- Network operations

Example:

- Fetching URLs

---

# Worker Threads

Use actual threads.

Best for:

- CPU-intensive tasks
- Image processing
- Compression
- Cryptography

Node.js provides:

Worker Threads

---

# Graceful Shutdown for Workers

Production queues need cleanup.

Example:

```js id="fnd4l9"
process.on("SIGTERM", async () => {
  console.log("Stopping workers");

  await worker.close();

  process.exit(0);
});
```

---

# Common Pitfalls

---

# 1. Unbounded Concurrency

Bad:

```js id="k99c8f"
tasks.forEach(async (task) => {
  await task();
});
```

Can overwhelm:

- Memory
- APIs
- DB pools

---

# 2. Queue Starvation

High-priority tasks may block low-priority tasks forever.

Use:

- Fair scheduling
- Weighted queues

---

# 3. Lost Jobs

In-memory queues lose tasks on crashes.

Use persistent queues in production.

---

# 4. Memory Leaks

Large pending queues consume RAM.

Always:

- Monitor queue length
- Add backpressure
- Remove completed jobs

---

# Event Loop Considerations

Async queues rely on:

- Promise microtasks
- Event loop scheduling
- Non-blocking I/O

Efficient queues maximize throughput while avoiding blocking operations.

---

# Interview-Level Discussion Points

A senior-level answer should mention:

- Concurrency limits
- Worker pools
- Retry/backoff
- Backpressure
- Persistence
- Graceful shutdown
- Horizontal scaling
- CPU vs I/O workloads
- Distributed queue systems

---

# Interview Summary

A strong interview answer should explain:

- A task queue manages async job execution
- Workers process tasks concurrently
- Concurrency must be controlled
- Retries and backoff improve reliability
- Backpressure prevents overload
- Persistent queues are needed in production
- Libraries like [BullMQ](https://bullmq.io?utm_source=chatgpt.com) are commonly used
- Worker Threads are for CPU-intensive work
- Async workers are ideal for I/O-bound tasks

That demonstrates understanding of both JavaScript async internals and scalable backend architecture.

## Question 4. How to handle backpressure with streams in Node.js

Backpressure in Node.js streams is the mechanism that prevents a **fast producer** from overwhelming a **slow consumer**.

In simple terms:

- If data is produced faster than it can be processed,
- memory usage grows uncontrollably,
- eventually causing performance degradation or crashes.

Node.js streams solve this using built-in backpressure handling.

---

# What Is Backpressure?

Imagine:

```txt id="d2h5w4"
Fast source  --->  Slow destination
```

Example:

- Reading a huge file quickly
- Writing to a slow database/network/socket

Without backpressure:

- Buffers grow indefinitely
- RAM usage spikes
- Event loop becomes overloaded

With backpressure:

- Producer pauses automatically
- Consumer catches up
- Memory remains stable

---

# Real-World Example

```txt id="wkm18o"
Read stream -> gzip -> HTTP response
```

If the client internet connection is slow:

- Node.js slows file reading automatically.

---

# Streams in Node.js

Main stream types:

| Type      | Purpose       |
| --------- | ------------- |
| Readable  | Produces data |
| Writable  | Consumes data |
| Duplex    | Read + write  |
| Transform | Modify data   |

Backpressure mainly involves:

- Readable → Writable

---

# Core Backpressure Mechanism

The key signal is:

```js id="8pntw0"
writable.write(chunk);
```

It returns:

```js id="0v0kr0"
true;
```

or:

```js id="mjlwm7"
false;
```

---

# Meaning of Return Value

| Return  | Meaning                  |
| ------- | ------------------------ |
| `true`  | Continue writing         |
| `false` | Stop writing temporarily |

When `false` is returned:

- Internal buffer is full
- Producer should pause

Resume only after:

```js id="0xtzfa"
"drain";
```

event fires.

---

# Manual Backpressure Handling

---

# Example Without Backpressure

BAD:

```js id="2mwy5g"
readable.on("data", (chunk) => {
  writable.write(chunk);
});
```

Problem:

- Producer ignores writable capacity
- Memory can explode

---

# Proper Backpressure Handling

```js id="k1mduz"
readable.on("data", (chunk) => {
  const canContinue = writable.write(chunk);

  if (!canContinue) {
    readable.pause();

    writable.once("drain", () => {
      readable.resume();
    });
  }
});
```

---

# How This Works

## Step-by-step

1. Readable emits chunk
2. Write to writable
3. If buffer full:
   - `write()` returns `false`

4. Pause readable stream
5. Wait for `'drain'`
6. Resume readable

This keeps memory stable.

---

# Why Backpressure Matters

Without it:

```txt id="7nynxh"
Producer speed > Consumer speed
```

Results:

- Huge buffers
- High RAM usage
- GC pressure
- Event loop lag
- Possible OOM crash

With proper backpressure:

- Constant memory usage
- Smooth throughput
- Better scalability

---

# Using `pipe()` (Recommended)

The easiest solution:

```js id="cb0k5u"
readable.pipe(writable);
```

Node.js automatically handles:

- Backpressure
- Pause/resume
- Drain events

---

# Example

```js id="zl3g0s"
const fs = require("fs");

const readStream = fs.createReadStream("large.txt");

const writeStream = fs.createWriteStream("copy.txt");

readStream.pipe(writeStream);
```

This is memory-efficient even for huge files.

---

# How `pipe()` Internally Handles Backpressure

Internally equivalent to:

```txt id="btm62t"
if write() returns false:
    pause readable

when drain fires:
    resume readable
```

---

# Using `pipeline()` (Best Practice)

Modern Node.js recommends:

```js id="nux4k5"
const { pipeline } = require("stream");
```

Benefits:

- Automatic cleanup
- Proper error propagation
- Safer resource handling

---

# Example

```js id="s7s98e"
const fs = require("fs");
const { pipeline } = require("stream");

pipeline(
  fs.createReadStream("large.txt"),
  fs.createWriteStream("copy.txt"),
  (err) => {
    if (err) {
      console.error(err);
    } else {
      console.log("Done");
    }
  },
);
```

---

# Backpressure with Async Iterators

Modern Node.js streams support async iteration.

---

# Example

```js id="lq4jfc"
for await (const chunk of readable) {
  const canWrite = writable.write(chunk);

  if (!canWrite) {
    await once(writable, "drain");
  }
}
```

This provides elegant flow control.

---

# `highWaterMark`

Controls internal buffer size.

Example:

```js id="jlwm7s"
fs.createReadStream("file.txt", {
  highWaterMark: 64 * 1024,
});
```

---

# What It Means

- Threshold for buffering
- Not a strict limit
- Impacts memory and throughput

---

# Tuning `highWaterMark`

## Smaller Buffer

Pros:

- Lower memory usage

Cons:

- More I/O operations

---

## Larger Buffer

Pros:

- Better throughput

Cons:

- Higher RAM usage

---

# Transform Streams and Backpressure

Transform streams automatically propagate backpressure.

Example:

```js id="yv5oqn"
readable.pipe(transform).pipe(writable);
```

If writable slows:

- transform slows
- readable slows

Entire pipeline becomes flow-controlled.

---

# Example: Compression Pipeline

```js id="bpr5lf"
const fs = require("fs");
const zlib = require("zlib");
const { pipeline } = require("stream");

pipeline(
  fs.createReadStream("large.txt"),
  zlib.createGzip(),
  fs.createWriteStream("large.txt.gz"),
  (err) => {
    if (err) console.error(err);
  },
);
```

Efficient even for GB-scale files.

---

# Backpressure in HTTP Servers

Very important in production.

---

# Example

```js id="1gbv17"
app.get("/download", (req, res) => {
  fs.createReadStream("huge.zip").pipe(res);
});
```

If the client is slow:

- Node.js automatically slows disk reading.

Without streams:

- Entire file might load into memory.

---

# Backpressure with TCP Sockets

Sockets are writable streams.

```js id="vrr9gb"
socket.write(data);
```

If returns `false`:

- Wait for `'drain'`

Same principle applies.

---

# Common Mistakes

---

# 1. Ignoring `write()` Return Value

Bad:

```js id="4dujlwm"
while (hasMoreData()) {
  writable.write(generateData());
}
```

Can crash memory.

---

# 2. Reading Entire Files into Memory

Bad:

```js id="5e5x7o"
const data = fs.readFileSync("huge.txt");
```

Better:

```js id="f1dv3g"
fs.createReadStream("huge.txt");
```

---

# 3. Using Streams Without Error Handling

Always handle errors:

```js id="c6uwns"
stream.on("error", handler);
```

Or use `pipeline()`.

---

# Memory Usage Example

---

# Bad Approach

```js id="ij13mc"
const chunks = [];

readable.on("data", (chunk) => {
  chunks.push(chunk);
});
```

Problem:

- Entire dataset stored in RAM

---

# Good Approach

```js id="lrw6up"
readable.pipe(writable);
```

Processes incrementally.

---

# Internal Node.js Behavior

Node.js streams internally maintain:

- Buffer queues
- Read state
- Write state
- Flow control flags

Backpressure works through:

- Event loop coordination
- Internal buffering
- Pause/resume semantics

---

# Event Loop Relationship

Streams integrate tightly with:

- Non-blocking I/O
- libuv
- Event loop phases

Backpressure ensures:

- Event loop stays responsive
- I/O throughput remains stable

---

# Production Best Practices

---

# Use `pipeline()`

Preferred over manual piping.

---

# Stream Large Data

Avoid loading:

- Huge files
- Massive JSON payloads
- Large DB exports

Into memory entirely.

---

# Tune `highWaterMark`

For:

- Performance
- Memory constraints

---

# Handle Errors Properly

Streams can fail due to:

- Network errors
- Disk errors
- Permission issues

---

# Avoid Unbounded Buffers

Always respect:

- `write()` return value
- `'drain'` events

---

# Interview-Level Insights

Senior-level discussion points:

## 1. Backpressure Is Flow Control

It prevents:

- Producer overflow
- Memory exhaustion

---

## 2. Streams Are Pull-Based Under Pressure

Writable stream capacity influences readable flow.

---

## 3. `pipe()` Automates Backpressure

Most production code should use:

- `pipe()`
- `pipeline()`

---

## 4. Critical for Scalability

Streams enable:

- GB-scale file processing
- Efficient HTTP streaming
- Low-memory applications

---

# Interview Summary

A strong interview answer should mention:

- Backpressure occurs when producers outpace consumers
- `write()` returning `false` signals pressure
- Use `'drain'` to resume writes
- `pipe()` automatically manages backpressure
- `pipeline()` is the safest modern API
- Streams prevent excessive memory usage
- `highWaterMark` controls buffering behavior
- Essential for scalable Node.js applications

That demonstrates understanding of:

- Node.js streams
- Event loop behavior
- Memory management
- Production backend engineering.

## Question 5. How to implement throttling and debouncing in backend APIs

Throttling and debouncing are techniques used to **control how frequently operations are executed**. In backend APIs, they help protect systems from overload, reduce unnecessary work, and improve scalability.

Although these concepts are common in frontend development, they are also extremely important in backend systems for:

- Rate limiting
- Preventing API abuse
- Reducing database load
- Controlling expensive operations
- Handling bursts of traffic
- Queue optimization

---

# Difference Between Throttling and Debouncing

| Technique  | Behavior                                             |
| ---------- | ---------------------------------------------------- |
| Throttling | Execute at most once every X milliseconds            |
| Debouncing | Execute only after activity stops for X milliseconds |

---

# Real-World Examples

## Throttling

```txt id="ixmmdg"
Allow max 1 request per second
```

Useful for:

- Rate-limited APIs
- Login attempts
- Expensive DB queries

---

## Debouncing

```txt id="j6dzso"
Wait until requests stop coming
```

Useful for:

- Search indexing
- Batch processing
- Cache refreshes
- Aggregation jobs

---

# Throttling in Backend APIs

---

# Basic Throttle Implementation

```js id="9r3nsv"
function throttle(fn, delay) {
  let lastCall = 0;

  return async function (...args) {
    const now = Date.now();

    if (now - lastCall >= delay) {
      lastCall = now;

      return fn.apply(this, args);
    }
  };
}
```

---

# Example Usage

```js id="j7k8bk"
const sendEmail = throttle(async (email) => {
  console.log("Sending email to", email);
}, 1000);

sendEmail("a@test.com");
sendEmail("b@test.com");
sendEmail("c@test.com");
```

Only one execution per second occurs.

---

# How Throttling Works

```txt id="mjlwm9"
Call allowed?
   YES -> execute
   NO  -> ignore/delay
```

This limits execution frequency.

---

# API-Level Throttling (Rate Limiting)

Common backend pattern.

Example:

- Max 100 requests/minute/IP

---

# Express Middleware Example

```js id="jlwmap"
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 60 * 1000,
  max: 100,
});

app.use("/api", limiter);
```

This throttles incoming requests.

---

# Popular Rate Limiting Algorithms

---

# 1. Fixed Window

Simple counter per time window.

```txt id="1dzxkg"
100 requests per minute
```

Problem:

- Burst at window boundaries

---

# 2. Sliding Window

Tracks rolling request history.

More accurate but more memory-intensive.

---

# 3. Token Bucket

Widely used in production.

Concept:

- Tokens refill over time
- Requests consume tokens

Allows controlled bursts.

---

# Token Bucket Example

```txt id="zq07jm"
Bucket size: 10
Refill: 1 token/sec
```

Good balance between:

- Flexibility
- Burst tolerance

---

# 4. Leaky Bucket

Processes requests at constant rate.

Useful for:

- Traffic smoothing
- Queue systems

---

# Implementing Simple In-Memory Rate Limiter

---

# Example

```js id="mj6hcf"
const requests = new Map();

function rateLimiter(limit, windowMs) {
  return (req, res, next) => {
    const ip = req.ip;
    const now = Date.now();

    if (!requests.has(ip)) {
      requests.set(ip, []);
    }

    const timestamps = requests.get(ip).filter((ts) => now - ts < windowMs);

    timestamps.push(now);

    requests.set(ip, timestamps);

    if (timestamps.length > limit) {
      return res.status(429).send("Too many requests");
    }

    next();
  };
}
```

---

# Usage

```js id="jbjlwm"
app.use(rateLimiter(100, 60000));
```

---

# Problem with In-Memory Limiters

Not suitable for:

- Multiple servers
- Horizontal scaling
- Restarts

Because memory is process-local.

---

# Production Solution: Redis-Based Throttling

Use shared storage like Redis.

Popular packages:

- [rate-limiter-flexible](https://github.com/animir/node-rate-limiter-flexible?utm_source=chatgpt.com)
- [express-rate-limit](https://github.com/express-rate-limit/express-rate-limit?utm_source=chatgpt.com)

---

# Redis Example

```js id="hfzhah"
const { RateLimiterRedis } = require("rate-limiter-flexible");
```

Benefits:

- Distributed limits
- Atomic operations
- Scalable

---

# Debouncing in Backend APIs

Debouncing delays execution until requests stop arriving.

---

# Basic Debounce Implementation

```js id="d9l5mk"
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

---

# Example Usage

```js id="cxlx6f"
const rebuildSearchIndex = debounce(() => {
  console.log("Rebuilding index...");
}, 5000);

rebuildSearchIndex();
rebuildSearchIndex();
rebuildSearchIndex();
```

Only one rebuild happens after activity stops.

---

# Backend Use Cases for Debouncing

---

# 1. Search Index Updates

Avoid rebuilding index repeatedly during bursts.

---

# 2. Cache Invalidation

Delay refresh until updates settle.

---

# 3. Database Aggregation

Batch rapid writes together.

---

# 4. Analytics/Event Processing

Aggregate events before processing.

---

# Debouncing Database Writes

Example:

```txt id="5f6h8n"
100 updates arrive in 1 second
```

Instead of:

- 100 DB writes

Debounce:

- Single batched write

---

# Example

```js id="ztjlwm"
const saveStats = debounce(async () => {
  await db.save(aggregatedStats);
}, 2000);
```

---

# Combining Queue + Debounce

Very common architecture.

```txt id="t2v7hm"
API -> Queue -> Debounced Worker
```

Useful for:

- Notifications
- Batch jobs
- Analytics

---

# Throttle vs Debounce in APIs

| Scenario                   | Better Choice |
| -------------------------- | ------------- |
| Prevent API spam           | Throttle      |
| Batch updates              | Debounce      |
| Limit expensive operations | Throttle      |
| Delay repeated writes      | Debounce      |
| Traffic shaping            | Throttle      |
| Search indexing            | Debounce      |

---

# Distributed System Considerations

In microservices:

- In-memory timers are unreliable
- Multiple instances exist

Use:

- Redis
- Kafka
- Message queues
- Distributed locks

---

# Distributed Debouncing

Example strategy:

- Store pending operation in Redis
- Extend TTL on repeated requests
- Process only after quiet period

---

# API Gateway Throttling

Often implemented at:

- NGINX
- Kong
- AWS API Gateway
- Cloudflare

Instead of app layer.

Benefits:

- Earlier rejection
- Lower server load

---

# Example: NGINX Rate Limiting

```nginx id="akjlwm"
limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
```

---

# Handling Bursts Gracefully

Instead of rejecting requests immediately:

- Queue them
- Process gradually

Useful with:

- BullMQ
- RabbitMQ
- Kafka

---

# Throttling Async Tasks

Example:

- External API only allows 5 req/sec

---

# Concurrency + Throttle

```js id="1gvn8m"
const queue = new PQueue({
  interval: 1000,
  intervalCap: 5,
});
```

Using:

[PQueue](https://github.com/sindresorhus/p-queue?utm_source=chatgpt.com)

---

# Common Pitfalls

---

# 1. Memory Leaks with Debounce

Timers retain closures.

Always clear timers properly.

---

# 2. In-Memory Rate Limits in Clusters

Fails under:

- Load balancing
- Multiple containers

Use Redis/shared state.

---

# 3. Too Aggressive Throttling

Can:

- Hurt UX
- Cause request starvation

Choose sensible limits.

---

# 4. Ignoring Retry Headers

Good APIs return:

```txt id="jlwmcp"
Retry-After
```

With HTTP 429.

---

# Best Practices

---

# Use HTTP 429

```txt id="myiwj9"
Too Many Requests
```

---

# Add Retry Headers

```txt id="31i0yj"
Retry-After: 60
```

---

# Use Redis for Distributed Systems

Critical for scaling.

---

# Monitor Rate-Limit Metrics

Track:

- Rejected requests
- Burst traffic
- Queue sizes

---

# Protect Expensive Endpoints

Especially:

- Authentication
- Search
- File uploads
- AI inference
- Reporting

---

# Event Loop Considerations

Bad throttling implementations may:

- Create too many timers
- Block event loop
- Leak memory

Efficient implementations:

- Minimize timers
- Avoid large in-memory queues

---

# Interview-Level Insights

A senior-level answer should mention:

- Difference between throttle and debounce
- Rate-limiting algorithms
- Distributed coordination
- Redis-based implementations
- Queue integration
- Traffic shaping
- Backpressure handling
- API gateway throttling

---

# Interview Summary

A strong interview answer should explain:

- Throttling limits execution frequency
- Debouncing delays execution until inactivity
- Backend APIs use throttling for rate limiting
- Debouncing helps batch repeated operations
- Redis is preferred for distributed systems
- Token bucket is widely used in production
- HTTP 429 should be returned for limits
- Queues help smooth bursts
- API gateways often handle throttling

That demonstrates understanding of:

- Node.js async behavior
- Backend scalability
- Distributed systems
- Production API architecture.

## Question 6. How to use EventEmitter for pub/sub in Node.js

`EventEmitter` is Node.js’s built-in implementation of the **publish/subscribe (pub/sub) pattern**.

In pub/sub:

- **Publishers** emit events
- **Subscribers** listen for events
- Publishers and subscribers are loosely coupled

This enables:

- Event-driven architectures
- Decoupled modules
- Async workflows
- Real-time systems

Node.js provides this through:

```js id="7mjlwm"
const EventEmitter = require("events");
```

The `EventEmitter` class is one of the core building blocks of Node.js internals.

---

# Basic Pub/Sub Concept

```txt id="yo1j4q"
Publisher ---> Event Bus ---> Subscribers
```

Example:

- Order service emits `"orderCreated"`
- Email service sends confirmation
- Analytics service tracks purchase
- Inventory service updates stock

The publisher doesn’t know who is listening.

---

# Basic EventEmitter Example

---

# Step 1: Create Emitter

```js id="3hjlwm"
const EventEmitter = require("events");

const emitter = new EventEmitter();
```

---

# Step 2: Subscribe to Events

```js id="zjlwm8"
emitter.on("message", (data) => {
  console.log("Received:", data);
});
```

---

# Step 3: Publish Event

```js id="ljlwmc"
emitter.emit("message", "Hello World");
```

Output:

```txt id="rzbs7k"
Received: Hello World
```

---

# How It Works

## `emit()`

Publisher sends event:

```js id="njlwmr"
emitter.emit(eventName, payload);
```

---

## `on()`

Subscriber listens:

```js id="4mjlwm"
emitter.on(eventName, listener);
```

---

# Multiple Subscribers

One event can have many subscribers.

```js id="mjlwmc"
emitter.on("orderCreated", (order) => {
  console.log("Send email");
});

emitter.on("orderCreated", (order) => {
  console.log("Update analytics");
});

emitter.emit("orderCreated", {
  id: 1,
});
```

Output:

```txt id="jlwm6v"
Send email
Update analytics
```

This is classic pub/sub behavior.

---

# Real-World Example: Order System

---

# Event Bus Module

```js id="eg7p3j"
const EventEmitter = require("events");

class EventBus extends EventEmitter {}

module.exports = new EventBus();
```

---

# Publisher

```js id="rjlwm3"
const bus = require("./bus");

function createOrder(order) {
  console.log("Order created");

  bus.emit("orderCreated", order);
}
```

---

# Subscriber 1

```js id="jlwmfd"
const bus = require("./bus");

bus.on("orderCreated", (order) => {
  console.log("Sending email for", order.id);
});
```

---

# Subscriber 2

```js id="jlwm2m"
const bus = require("./bus");

bus.on("orderCreated", (order) => {
  console.log("Updating inventory");
});
```

This decouples modules cleanly.

---

# Important EventEmitter Methods

| Method                 | Purpose                  |
| ---------------------- | ------------------------ |
| `on()`                 | Add listener             |
| `once()`               | Listen once              |
| `emit()`               | Publish event            |
| `off()`                | Remove listener          |
| `removeListener()`     | Remove specific listener |
| `removeAllListeners()` | Remove all listeners     |

---

# Using `once()`

Listener executes only once.

```js id="jlwmwp"
emitter.once("login", (user) => {
  console.log("First login");
});
```

Useful for:

- Initialization
- One-time events

---

# Removing Listeners

Important for memory management.

```js id="hjlwmz"
function handler(data) {
  console.log(data);
}

emitter.on("data", handler);

emitter.off("data", handler);
```

---

# Async Event Handlers

EventEmitter itself is synchronous, but handlers can be async.

---

# Example

```js id="2jlwmz"
emitter.on("task", async (data) => {
  await saveToDB(data);

  console.log("Saved");
});
```

---

# Important Detail

`emit()` does NOT await async listeners.

```js id="6jlwmq"
emitter.emit("task");
console.log("Done");
```

Output may be:

```txt id="jlwmkv"
Done
Saved
```

Because listeners run asynchronously internally.

---

# Error Handling

Special event:

```js id="jlwm8v"
"error";
```

If emitted without a listener:

- Node.js throws
- Process may crash

---

# Proper Error Handling

```js id="jlwmpt"
emitter.on("error", (err) => {
  console.error(err);
});
```

---

# Example

```js id="jlwm0n"
emitter.emit("error", new Error("Something failed"));
```

---

# EventEmitter Is Synchronous

Very important interview topic.

When you call:

```js id="5jlwmj"
emitter.emit("event");
```

All listeners execute synchronously in registration order.

---

# Example

```js id="jlwm63"
emitter.on("test", () => {
  console.log(1);
});

emitter.on("test", () => {
  console.log(2);
});

emitter.emit("test");

console.log(3);
```

Output:

```txt id="jlwmx0"
1
2
3
```

---

# Making Events Async

Use:

- `setImmediate`
- `process.nextTick`
- Promises

---

# Example

```js id="jlwm0y"
emitter.on("event", (data) => {
  setImmediate(() => {
    console.log(data);
  });
});
```

---

# Event-Driven Architecture Pattern

Common backend architecture:

```txt id="jlwm1d"
API -> Emit Event -> Independent Consumers
```

Benefits:

- Decoupling
- Scalability
- Modularity

---

# Example Use Cases

| Use Case           | Example            |
| ------------------ | ------------------ |
| Logging            | `requestCompleted` |
| Notifications      | `userRegistered`   |
| Analytics          | `purchaseMade`     |
| Cache invalidation | `dataUpdated`      |
| WebSockets         | `messageReceived`  |

---

# Building a Simple Pub/Sub System

---

# Pub/Sub Wrapper

```js id="jlwm4u"
class PubSub extends EventEmitter {
  publish(event, data) {
    this.emit(event, data);
  }

  subscribe(event, listener) {
    this.on(event, listener);
  }
}
```

Usage:

```js id="4jlwmf"
const bus = new PubSub();

bus.subscribe("news", (data) => {
  console.log(data);
});

bus.publish("news", "Breaking News");
```

---

# Memory Leak Warnings

By default:

```txt id="jlwmhm"
MaxListenersExceededWarning
```

Occurs after:

- 10 listeners/event

This warns about possible leaks.

---

# Example Leak

```js id="3jlwm2"
setInterval(() => {
  emitter.on("data", () => {});
}, 1000);
```

Listeners accumulate forever.

---

# Fix

Remove listeners properly.

Or increase limit cautiously:

```js id="7jlwm5"
emitter.setMaxListeners(20);
```

But increasing limits does NOT fix leaks.

---

# EventEmitter vs Message Queues

Important distinction.

---

# EventEmitter

- In-memory
- Single process
- Fast
- No persistence

Best for:

- Internal app events

---

# Message Brokers

Examples:

- RabbitMQ
- Kafka
- Redis Pub/Sub

Features:

- Persistence
- Distributed systems
- Reliability
- Cross-service communication

---

# EventEmitter vs Observer Pattern

`EventEmitter` is essentially Node.js’s implementation of:

- Observer pattern
- Pub/sub pattern

But with:

- Named events
- Multiple listeners
- Event bus semantics

---

# Advanced Pattern: Namespaced Events

```js id="0jlwm8"
emitter.emit("user:created");
emitter.emit("user:deleted");
```

Useful for:

- Large systems
- Event organization

---

# Typed Events (TypeScript)

Common production pattern.

```ts id="jlwmde"
interface Events {
  login: (userId: string) => void;
  logout: () => void;
}
```

Improves:

- Safety
- Autocomplete
- Maintainability

---

# EventEmitter Internals

Internally:

- Listeners stored in hash map
- Event names are keys
- Arrays hold callbacks

Conceptually:

```js id="qjlwm8"
{
  eventName: [listener1, listener2];
}
```

---

# Performance Considerations

EventEmitter is:

- Extremely fast
- Lightweight
- Synchronous

But:

- Large listener counts can hurt performance
- Heavy handlers block event loop

---

# Best Practices

---

# Use Events for Decoupling

Good:

- Notifications
- Hooks
- Async workflows

Bad:

- Core synchronous business logic

---

# Keep Listeners Small

Heavy work should be delegated:

- Queues
- Worker threads
- Async jobs

---

# Always Handle Errors

Especially:

- Async listeners
- Unhandled rejections

---

# Remove Unused Listeners

Prevent memory leaks.

---

# Avoid Event Chains That Become Hard to Trace

Too many emitted events can create:

- Debugging complexity
- Hidden dependencies

---

# Interview-Level Insights

A senior-level answer should mention:

- EventEmitter implements pub/sub
- `emit()` is synchronous
- Listeners can be async
- Memory leaks via listeners
- Error event handling
- Difference from distributed brokers
- Event-driven architecture benefits
- Event loop considerations

---

# Interview Summary

A strong interview answer should explain:

- `EventEmitter` provides pub/sub in Node.js
- Publishers emit events with `emit()`
- Subscribers listen using `on()`
- Multiple listeners can subscribe to one event
- EventEmitter is synchronous internally
- Async listeners require careful handling
- Remove listeners to avoid leaks
- Best for in-process event-driven architecture
- Distributed systems require brokers like Kafka or RabbitMQ

That demonstrates understanding of:

- Node.js internals
- Async architecture
- Design patterns
- Production backend systems.

## Question 7. Difference between EventEmitter and Observables

`EventEmitter` and Observables are both used for handling asynchronous events and reactive programming, but they differ significantly in:

- Data flow model
- Laziness
- Cancellation
- Composition
- Error handling
- Multicasting behavior

In interviews, the concise distinction is:

> `EventEmitter` is a simple push-based event system mainly for pub/sub, while Observables provide a powerful lazy stream abstraction with operators, cancellation, and composability.

---

# High-Level Comparison

| Feature                     | EventEmitter | Observable                   |
| --------------------------- | ------------ | ---------------------------- |
| Pattern                     | Pub/Sub      | Reactive streams             |
| Built into Node.js          | Yes          | No                           |
| Lazy                        | No           | Yes                          |
| Multicast by default        | Yes          | Usually no (cold observable) |
| Operators (`map`, `filter`) | No           | Yes                          |
| Cancellation                | Manual       | Built-in unsubscribe         |
| Error channel               | Limited      | First-class                  |
| Completion support          | No           | Yes                          |
| Async composition           | Limited      | Excellent                    |
| Backpressure support        | Poor         | Better                       |
| Common ecosystem            | Node.js      | RxJS                         |

---

# What Is EventEmitter?

Node.js built-in pub/sub system:

```js id="9jlwm0"
const EventEmitter = require("events");
```

Used for:

- Internal Node.js APIs
- Streams
- HTTP servers
- Custom events

---

# EventEmitter Example

```js id="qjlwm2"
const EventEmitter = require("events");

const emitter = new EventEmitter();

emitter.on("message", (data) => {
  console.log(data);
});

emitter.emit("message", "Hello");
```

Output:

```txt id="jlwmj1"
Hello
```

---

# Characteristics of EventEmitter

## Push-Based

Publisher pushes values immediately.

---

## Eager

Events happen whether listeners exist or not.

```js id="3jlwmc"
emitter.emit("data", 123);
```

If nobody listens:

- Event is lost.

---

## Multicast

Multiple listeners receive same event.

```js id="xjlwmf"
emitter.on("event", listener1);
emitter.on("event", listener2);
```

---

## Synchronous by Default

```js id="wjlwm8"
emitter.emit("event");
```

Listeners run immediately.

---

# What Is an Observable?

An Observable represents:

- A stream of values over time

Popularized by:

[RxJS](https://rxjs.dev?utm_source=chatgpt.com)

---

# Observable Example

```js id="0jlwm9"
const { Observable } = require("rxjs");

const observable = new Observable((subscriber) => {
  subscriber.next("Hello");
  subscriber.complete();
});

observable.subscribe({
  next: (value) => console.log(value),
});
```

Output:

```txt id="7jlwmk"
Hello
```

---

# Observable Core Concepts

Observables provide:

- Values (`next`)
- Errors (`error`)
- Completion (`complete`)

---

# Observable Lifecycle

```txt id="jlwm2d"
subscribe()
   ↓
next()
next()
next()
   ↓
complete()
```

Or:

```txt id="jlwmwy"
error()
```

---

# Key Difference #1: Lazy vs Eager

---

# EventEmitter = Eager

```js id="jlwmp9"
emitter.emit("data");
```

Executes immediately.

---

# Observable = Lazy

Nothing happens until subscription.

```js id="1jlwm7"
const obs = new Observable((sub) => {
  console.log("Executed");
});

obs.subscribe();
```

Only upon `subscribe()`.

---

# Why Laziness Matters

Allows:

- Deferred execution
- Efficient pipelines
- Better resource management

---

# Key Difference #2: Cancellation

---

# EventEmitter

Must manually remove listeners.

```js id="0jlwmu"
emitter.off("event", handler);
```

Easy to leak memory.

---

# Observable

Built-in subscription cleanup.

```js id="4jlwmt"
const subscription = observable.subscribe();

subscription.unsubscribe();
```

Very important in:

- Frontend apps
- Long-running streams
- WebSockets

---

# Key Difference #3: Operators and Composition

Observables support functional reactive programming.

---

# Observable Operators

```js id="5jlwm3"
observable.pipe(
  map((x) => x * 2),
  filter((x) => x > 10),
);
```

Powerful stream transformations.

---

# EventEmitter Lacks This

You must manually implement:

- Filtering
- Mapping
- Buffering
- Debouncing

---

# Key Difference #4: Completion Semantics

---

# Observable

Supports completion:

```js id="9jlwmw"
subscriber.complete();
```

Subscribers know stream ended.

---

# EventEmitter

No built-in completion signal.

You’d need custom events:

```js id="2jlwmq"
emitter.emit("done");
```

---

# Key Difference #5: Error Handling

---

# EventEmitter

Special `'error'` event.

```js id="6jlwm0"
emitter.emit("error", err);
```

If unhandled:

- Process may crash.

---

# Observable

Errors are part of stream lifecycle.

```js id="2jlwm1"
observable.subscribe({
  error: (err) => console.error(err),
});
```

More structured.

---

# Key Difference #6: Single vs Multiple Values

---

# Promise

Single async value.

---

# EventEmitter

Many unrelated events.

---

# Observable

A sequence/stream of values over time.

This makes Observables ideal for:

- Live data
- Streams
- User interactions
- Real-time systems

---

# Converting EventEmitter to Observable

Very common interview topic.

RxJS provides:

```js id="3jlwmn"
fromEvent();
```

---

# Example

```js id="9jlwmr"
const { fromEvent } = require("rxjs");

const clicks = fromEvent(emitter, "click");

clicks.subscribe((data) => {
  console.log(data);
});
```

Now EventEmitter becomes reactive stream.

---

# Cold vs Hot Observables

Advanced interview topic.

---

# Cold Observable

Each subscriber gets independent execution.

```js id="9jlwm4"
const obs = new Observable(...);
```

Like:

- HTTP request
- File read

---

# Hot Observable

Shared producer.

Similar to EventEmitter behavior.

Examples:

- Mouse movement
- WebSocket
- Broadcast events

---

# EventEmitter Is Always Hot

Events occur independently of subscribers.

---

# Backpressure Handling

---

# EventEmitter

Poor backpressure support.

If producer emits too fast:

- Consumers may lag
- Memory may grow

---

# Observables

RxJS supports:

- Buffering
- Throttling
- Debouncing
- Sampling
- Windowing

Example:

```js id="7jlwmg"
stream.pipe(throttleTime(1000));
```

Much better for high-frequency streams.

---

# Async Behavior

---

# EventEmitter

Synchronous by default.

```js id="0jlwm6"
emit();
```

Immediately invokes listeners.

---

# Observables

Can be:

- Sync
- Async
- Scheduled

Using schedulers.

---

# Real-World Use Cases

---

# EventEmitter Best For

- Internal Node.js events
- Lightweight pub/sub
- Streams
- Server events

Examples:

- HTTP server
- File streams
- Socket events

---

# Observables Best For

- Reactive systems
- Complex async flows
- UI interactions
- Real-time data pipelines

Examples:

- WebSocket streams
- Search autocomplete
- Reactive frontend apps
- Event transformations

---

# Example: Debouncing

---

# EventEmitter

Manual implementation required.

---

# Observable

Built-in operator:

```js id="2jlwm7"
stream.pipe(debounceTime(300));
```

Very concise.

---

# Memory Management

---

# EventEmitter

Risk:

- Listener leaks
- `MaxListenersExceededWarning`

---

# Observable

Subscriptions easier to manage:

- `unsubscribe()`
- Cleanup logic

---

# Performance

---

# EventEmitter

- Lightweight
- Fast
- Minimal abstraction

Great for:

- Core Node.js internals

---

# Observables

More powerful but:

- Higher abstraction cost
- More memory/CPU overhead

Trade-off:

- Flexibility vs simplicity

---

# Example Comparison

---

# EventEmitter

```js id="4jlwmh"
emitter.on("data", console.log);

emitter.emit("data", 1);
```

---

# Observable

```js id="8jlwm9"
observable
  .pipe(
    filter((x) => x > 0),
    map((x) => x * 2),
  )
  .subscribe(console.log);
```

Observables are declarative stream pipelines.

---

# Interview-Level Insights

A senior-level answer should mention:

- EventEmitter is pub/sub
- Observable is stream abstraction
- Observables are lazy
- EventEmitter is eager/hot
- Observables support composition/operators
- Better cancellation semantics
- Completion/error channels
- RxJS reactive programming model
- EventEmitter is simpler/lower overhead

---

# Interview Summary

A strong interview answer should explain:

- `EventEmitter` is Node.js’s lightweight pub/sub system
- Observables model async streams over time
- EventEmitter is eager and synchronous
- Observables are lazy and composable
- Observables support operators like `map`, `filter`, `debounce`
- Observables have built-in cancellation and completion
- EventEmitter is ideal for simple internal events
- Observables are better for complex reactive workflows
- RxJS can convert EventEmitters into Observables using `fromEvent()`

That demonstrates understanding of:

- Node.js internals
- Reactive programming
- Async architecture
- Stream abstractions
- Event-driven systems.

## Question 8. How to implement a microservices architecture in Node.js

Implementing a microservices architecture in Node.js involves designing an application as a collection of **small, independently deployable services** that communicate over the network.

Each microservice:

- Owns a specific business capability
- Has its own codebase and often its own database
- Can be deployed/scaled independently
- Communicates via APIs or messaging systems

Node.js is a strong fit for microservices because of:

- Non-blocking I/O
- Fast startup
- Lightweight runtime
- Excellent async/network support

---

# Monolith vs Microservices

## Monolith

```txt id="xjlwm8"
Single Application
 ├── Auth
 ├── Orders
 ├── Payments
 └── Notifications
```

Problems:

- Tight coupling
- Hard scaling
- Large deployments
- Slower development

---

## Microservices

```txt id="cjlwm0"
Auth Service
Order Service
Payment Service
Notification Service
```

Each service runs independently.

---

# Core Principles of Microservices

A good interview answer should mention:

| Principle              | Meaning                         |
| ---------------------- | ------------------------------- |
| Single responsibility  | One business domain per service |
| Loose coupling         | Services independent            |
| Independent deployment | Deploy separately               |
| Decentralized data     | Each service owns data          |
| Fault isolation        | Failures isolated               |
| Scalability            | Scale services independently    |

---

# Typical Node.js Microservices Architecture

```txt id="jlwm62"
Client
   ↓
API Gateway
   ↓
-------------------------
| Auth Service          |
| Order Service         |
| Payment Service       |
| Notification Service  |
-------------------------
   ↓
Message Broker / APIs
```

---

# Communication Patterns

Microservices communicate using:

| Pattern         | Example              |
| --------------- | -------------------- |
| HTTP REST       | Express APIs         |
| gRPC            | High-performance RPC |
| Message queues  | RabbitMQ/Kafka       |
| Event streaming | Kafka                |
| Pub/Sub         | Redis Pub/Sub        |

---

# Step 1: Create Independent Services

---

# Example Folder Structure

```txt id="jlwmvz"
services/
  auth-service/
  order-service/
  payment-service/
```

Each service:

- Has its own package.json
- Own dependencies
- Own DB access

---

# Example Auth Service

Using [Express.js](https://expressjs.com?utm_source=chatgpt.com)

```js id="5jlwmm"
const express = require("express");

const app = express();

app.get("/users/:id", (req, res) => {
  res.json({
    id: req.params.id,
    name: "John",
  });
});

app.listen(3001);
```

---

# Example Order Service

```js id="7jlwmt"
const express = require("express");

const app = express();

app.get("/orders/:id", (req, res) => {
  res.json({
    id: req.params.id,
    total: 100,
  });
});

app.listen(3002);
```

---

# Step 2: Service Communication

---

# Option 1: REST APIs

Order service calls auth service.

```js id="8jlwm6"
const axios = require("axios");

const user = await axios.get("http://auth-service/users/1");
```

Simple but synchronous.

---

# Problems with HTTP Chaining

Can cause:

- Cascading failures
- Increased latency
- Tight runtime coupling

---

# Option 2: Message Queues (Preferred)

Services communicate asynchronously.

Example brokers:

- RabbitMQ
- Kafka
- Redis Pub/Sub

---

# Example Event Flow

```txt id="jlwmn0"
Order Service
   ↓ emits
order.created
   ↓
Notification Service
```

Loose coupling.

---

# RabbitMQ Example

Publisher:

```js id="9jlwmv"
channel.sendToQueue("orders", Buffer.from(JSON.stringify(order)));
```

Consumer:

```js id="3jlwmz"
channel.consume("orders", (msg) => {
  const order = JSON.parse(msg.content);

  console.log(order);
});
```

---

# Step 3: API Gateway

Clients should not directly call all services.

Use an API gateway.

Responsibilities:

- Authentication
- Routing
- Rate limiting
- Aggregation
- Logging

---

# Example

```txt id="1jlwm2"
Client
  ↓
API Gateway
  ↓
Services
```

Popular gateways:

- Kong
- NGINX
- Traefik

---

# Example with Express Gateway

```js id="7jlwmp"
app.use("/auth", authProxy);
app.use("/orders", orderProxy);
```

---

# Step 4: Database Per Service

Critical microservices principle.

---

# BAD

```txt id="4jlwm4"
All services share one DB
```

Causes:

- Tight coupling
- Deployment coordination
- Schema conflicts

---

# GOOD

```txt id="0jlwmu"
Auth Service -> Auth DB
Order Service -> Order DB
```

Each service owns its data.

---

# Handling Distributed Transactions

Hard problem in microservices.

Avoid:

- Cross-service DB transactions

Use:

- Eventual consistency
- Saga pattern

---

# Saga Pattern

Example:

```txt id="9jlwmf"
Create Order
   ↓
Reserve Inventory
   ↓
Charge Payment
   ↓
Send Confirmation
```

If one step fails:

- Compensating actions rollback previous steps.

---

# Step 5: Service Discovery

Services may scale dynamically.

Need discovery mechanism.

---

# Solutions

- Kubernetes DNS
- Consul
- Eureka
- etcd

Example:

```txt id="jlwm3k"
http://auth-service
```

Instead of fixed IPs.

---

# Step 6: Containerization

Microservices are commonly containerized using:

Docker

---

# Dockerfile Example

```dockerfile id="8jlwmk"
FROM node:20

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

CMD ["node", "server.js"]
```

---

# Step 7: Orchestration

Production systems use:

Kubernetes

Features:

- Auto-scaling
- Service discovery
- Rolling deployments
- Self-healing

---

# Example Kubernetes Deployment

```yaml id="6jlwmw"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth-service
```

---

# Step 8: Centralized Logging

Microservices generate distributed logs.

Use:

- ELK stack
- Loki
- Datadog

---

# Logging Best Practices

Include:

- Request IDs
- Correlation IDs
- Trace IDs

---

# Example

```js id="1jlwmx"
logger.info({
  requestId,
  service: "order-service",
});
```

---

# Step 9: Distributed Tracing

Critical for debugging.

Popular tools:

- Jaeger
- Zipkin
- OpenTelemetry

---

# Step 10: Resilience Patterns

Important interview topic.

---

# Circuit Breaker

Prevents repeated failures.

Libraries:

- [Opossum](https://nodeshift.dev/opossum/?utm_source=chatgpt.com)

---

# Retry with Backoff

```js id="8jlwm1"
retry(apiCall, {
  retries: 3,
});
```

---

# Bulkheads

Isolate resources per service.

---

# Timeouts

Always set timeouts for inter-service calls.

---

# Authentication Between Services

Common approaches:

- JWT
- mTLS
- API keys
- OAuth2

---

# Example JWT Verification

```js id="4jlwmn"
jwt.verify(token, secret);
```

---

# Event-Driven Microservices

Very common architecture.

```txt id="9jlwm9"
Service A emits event
        ↓
Message Broker
        ↓
Service B reacts
```

Benefits:

- Loose coupling
- Scalability
- Async processing

---

# Node.js Strengths for Microservices

---

# Excellent I/O Performance

Great for:

- APIs
- Messaging
- Streaming

---

# Lightweight Runtime

Fast startup times.

---

# Huge Ecosystem

Frameworks:

- [NestJS](https://nestjs.com?utm_source=chatgpt.com)
- [Fastify](https://fastify.dev?utm_source=chatgpt.com)
- [Express.js](https://expressjs.com?utm_source=chatgpt.com)

---

# Common Pitfalls

---

# 1. Over-Splitting Services

Too many tiny services:

- Operational complexity
- Network overhead

---

# 2. Distributed Monolith

Services tightly dependent on each other.

---

# 3. Synchronous Communication Everywhere

Causes cascading failures.

Prefer async events where appropriate.

---

# 4. Shared Databases

Breaks service autonomy.

---

# 5. Missing Observability

Distributed debugging becomes impossible.

---

# When NOT to Use Microservices

Avoid if:

- Small team
- Small app
- Simple domain

A monolith may be better initially.

---

# Monolith First Strategy

Very common modern recommendation:

```txt id="8jlwm7"
Start monolith
Extract services later
```

---

# Interview-Level Insights

A senior-level answer should mention:

- Service boundaries
- Independent deployment
- API gateway
- Async messaging
- Event-driven systems
- Distributed tracing
- Resilience patterns
- Database-per-service
- Container orchestration
- Observability

---

# Interview Summary

A strong interview answer should explain:

- Microservices split applications into independent services
- Node.js is ideal due to async I/O and lightweight runtime
- Services communicate via REST, gRPC, or messaging
- Message brokers like Kafka or RabbitMQ enable loose coupling
- Each service should own its database
- Use API gateways for routing/authentication
- Docker + Kubernetes are common deployment choices
- Observability and resilience are critical
- Event-driven architecture improves scalability

That demonstrates understanding of:

- Distributed systems
- Node.js backend architecture
- Scalability
- Production engineering
- Cloud-native design.

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
