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
