# Set 23

| S.No. | Question                                                                                                                                                                   |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How does Node.js handle I/O asynchronously using the libuv thread pool?](#question-1-how-does-nodejs-handle-io-asynchronously-using-the-libuv-thread-pool)                |
| 2.    | [Difference between `process.nextTick`, `setImmediate`, and `setTimeout` in Node.js](#question-2-difference-between-processnexttick-setimmediate-and-settimeout-in-nodejs) |
| 3.    | [How to implement clustering in Node.js](#question-3-how-to-implement-clustering-in-nodejs)                                                                                |
| 4.    | [Difference between cluster and worker threads in Node.js](#question-4-difference-between-cluster-and-worker-threads-in-nodejs)                                            |
| 5.    | [How to handle file uploads in Node.js efficiently](#question-5-how-to-handle-file-uploads-in-nodejs-efficiently)                                                          |
| 6.    | [How to implement streaming large files without blocking memory](#question-6-how-to-implement-streaming-large-files-without-blocking-memory)                               |
| 7.    | [Difference between Buffer and Stream in Node.js](#question-7-difference-between-buffer-and-stream-in-nodejs)                                                              |
| 8.    | [How to implement rate limiting in Node.js APIs](#question-8-how-to-implement-rate-limiting-in-nodejs-apis)                                                                |
| 9.    | [How to prevent event loop blocking for CPU-intensive tasks](#question-9-how-to-prevent-event-loop-blocking-for-cpu-intensive-tasks)                                       |
| 10.   | [How to implement caching in Node.js with LRU cache](#question-10-how-to-implement-caching-in-nodejs-with-lru-cache)                                                       |
| 11.   | [Difference between callback-based APIs and promise-based APIs in Node.js](#question-11-difference-between-callback-based-apis-and-promise-based-apis-in-nodejs)           |
| 12.   | [How to handle uncaught exceptions gracefully](#question-12-how-to-handle-uncaught-exceptions-gracefully)                                                                  |
| 13.   | [How to implement JWT authentication in Node.js](#question-13-how-to-implement-jwt-authentication-in-nodejs)                                                               |
| 14.   | [How to implement role-based access control in Node.js](#question-14-how-to-implement-role-based-access-control-in-nodejs)                                                 |
| 15.   | [How to implement API versioning in Node.js applications](#question-15-how-to-implement-api-versioning-in-nodejs-applications)                                             |
| 16.   | [How to handle database connection pooling efficiently](#question-16-how-to-handle-database-connection-pooling-efficiently)                                                |
| 17.   | [Difference between synchronous and asynchronous file operations](#question-17-difference-between-synchronous-and-asynchronous-file-operations)                            |
| 18.   | [How to stream logs in Node.js](#question-18-how-to-stream-logs-in-nodejs)                                                                                                 |
| 19.   | [How to implement WebSocket server and handle concurrent clients](#question-19-how-to-implement-websocket-server-and-handle-concurrent-clients)                            |
| 20.   | [How to implement server-sent events (SSE) in Node.js](#question-20-how-to-implement-server-sent-events-sse-in-nodejs)                                                     |

## Question 1. How does Node.js handle I/O asynchronously using the libuv thread pool?

> Node.js handles asynchronous I/O primarily through the **event loop** and the **libuv library**. For operations that the operating system cannot perform asynchronously natively (like file system access, DNS lookups, compression, and some crypto tasks), libuv uses a **thread pool** behind the scenes.

In short:

- JavaScript runs on a **single main thread**.
- Expensive or blocking I/O tasks are delegated to libuv.
- libuv either:
  - Uses the OS’s async APIs (preferred), or
  - Uses a **thread pool** to avoid blocking the event loop.

- Once the operation completes, the callback/promise resolution is queued back to the event loop.

### High-Level Architecture

Node.js async execution involves:

1. **JavaScript Call Stack**
2. **Node.js APIs**
3. **libuv**
4. **Thread Pool**
5. **Event Loop**
6. **Callback Queue / Microtask Queue**

### What is libuv?

libuv is a C library used internally by Node.js to provide:

- Event loop
- Asynchronous I/O
- Thread pool
- Cross-platform abstractions
- Timers
- Networking

It allows Node.js to remain non-blocking even though JavaScript itself runs on one thread.

### Important Concept: Not All Async I/O Uses the Thread Pool

This is one of the most important point.

#### Operations Using OS Async APIs (No Thread Pool)

Networking operations usually rely on the operating system’s async capabilities:

- TCP sockets
- HTTP requests
- WebSockets

These are handled efficiently by:

- epoll (Linux)
- kqueue (macOS)
- IOCP (Windows)

No extra thread is needed.

Example:

```js
const http = require("http");

http.get("http://example.com", (res) => {
  console.log("Response received");
});
```

The OS notifies libuv when the socket is ready.

#### Operations That Use the libuv Thread Pool

Some operations are blocking at the OS level, so libuv offloads them to worker threads.

Common examples:

| Operation                 | Uses Thread Pool? |
| ------------------------- | ----------------- |
| fs.readFile               | Yes               |
| fs.writeFile              | Yes               |
| crypto.pbkdf2             | Yes               |
| bcrypt hashing            | Yes               |
| zlib compression          | Yes               |
| DNS lookup (`dns.lookup`) | Yes               |

Example:

```js
const fs = require("fs");

console.log("Start");

fs.readFile("large.txt", "utf8", (err, data) => {
  console.log("File read complete");
});

console.log("End");
```

Output:

```txt
Start
End
File read complete
```

Why?

Because `fs.readFile()` gets delegated to the thread pool.

### How the Thread Pool Works

libuv maintains a pool of worker threads.

Default size:

```txt
4 threads
```

Configurable via:

```bash
UV_THREADPOOL_SIZE=8
```

Example:

```bash
UV_THREADPOOL_SIZE=8 node app.js
```

Maximum allowed:

```txt
1024
```

(though very large values are rarely useful)

### Step-by-Step Flow of Async File Read

Consider:

```js
const fs = require("fs");

fs.readFile("data.txt", () => {
  console.log("Done");
});
```

Execution flow:

#### 1. JavaScript Calls fs.readFile()

The main thread invokes the Node.js API.

#### 2. Node Delegates to libuv

libuv receives the request.

#### 3. libuv Assigns Work to Thread Pool

One worker thread picks up the file read operation.

Meanwhile:

- Main thread becomes free
- Event loop continues processing other tasks

#### 4. Worker Thread Performs Blocking I/O

The worker thread executes the actual filesystem call.

#### 5. Completion Notification

After reading completes:

- Worker thread informs libuv
- libuv queues the callback

#### 6. Event Loop Executes Callback

When the call stack is free:

```js
console.log("Done");
```

gets executed on the main thread.

### Visualization

```txt
Main Thread
    |
    |---- fs.readFile()
    |
    +--> libuv
            |
            +--> Thread Pool Worker
                    |
                    +--> Read File
                    |
                    +--> Notify Completion
            |
            +--> Event Loop Queue
                    |
                    +--> Callback Executed
```

### Why Node.js is Fast

Node.js achieves scalability because:

- Main thread does not block
- One process can handle thousands of connections
- I/O waits happen outside the main thread
- Event loop stays responsive

This is ideal for:

- APIs
- Chat apps
- Streaming
- Real-time systems

#### Example: Thread Pool Saturation

```js
const crypto = require("crypto");

for (let i = 0; i < 8; i++) {
  crypto.pbkdf2("password", "salt", 100000, 64, "sha512", () => {
    console.log(`Task ${i} done`);
  });
}
```

Default thread pool size is 4.

Result:

- First 4 tasks run immediately
- Remaining 4 wait in queue

This demonstrates that thread pool resources are limited.

### Increasing Thread Pool Size

```bash
UV_THREADPOOL_SIZE=8 node app.js
```

Useful when:

- Heavy filesystem work
- Crypto-intensive workloads
- Compression tasks

But increasing blindly can hurt performance because:

- More context switching
- Higher memory usage
- CPU contention

### Event Loop vs Thread Pool

Interviewers often ask this distinction.

| Feature    | Event Loop                 | Thread Pool             |
| ---------- | -------------------------- | ----------------------- |
| Purpose    | Schedule async callbacks   | Execute blocking tasks  |
| Runs JS?   | Yes                        | No                      |
| Threads    | Single thread              | Multiple worker threads |
| Managed by | libuv                      | libuv                   |
| Handles    | Timers, callbacks, sockets | FS, crypto, DNS         |

### Common Misconception

#### “Node.js is single-threaded”

Partially true.

##### JavaScript execution

✅ Single-threaded

#### Runtime internals

❌ Not single-threaded

Node.js internally uses:

- Thread pool
- OS threads
- Background workers

Only the JavaScript execution model is single-threaded.

#### Microtasks and Callback Ordering

After async work completes:

```js
fs.readFile("a.txt", () => {
  console.log("file");

  Promise.resolve().then(() => {
    console.log("microtask");
  });
});

console.log("sync");
```

Output:

```txt
sync
file
microtask
```

Because:

1. Callback executes in event loop phase
2. Promise microtasks execute immediately after callback

This demonstrates interaction between:

- libuv event loop
- microtask queue
- JavaScript runtime

### Real-World Best Practices

#### 1. Avoid Blocking APIs

Bad:

```js
fs.readFileSync("large.txt");
```

This blocks the entire event loop.

Prefer:

```js
fs.readFile();
```

#### 2. Avoid CPU-Heavy Work on Main Thread

Heavy computations block the event loop.

Use:

- Worker Threads
- Child Processes
- Queues

#### 3. Understand Thread Pool Limits

Too many crypto/fs tasks can create bottlenecks.

Monitor:

- Event loop lag
- Queue delays
- CPU usage

### Worker Threads vs libuv Thread Pool

Important interview distinction:

| Feature        | libuv Thread Pool  | Worker Threads          |
| -------------- | ------------------ | ----------------------- |
| Executes JS?   | No                 | Yes                     |
| Purpose        | Native async tasks | Parallel JS computation |
| Suitable for   | FS, crypto, DNS    | CPU-intensive JS        |
| Access from JS | Indirect           | Direct                  |

Example:

- `fs.readFile()` → libuv thread pool
- Image processing algorithm → Worker Threads

### Summary

Node.js uses libuv to provide asynchronous I/O. Operations like networking rely on OS-level non-blocking APIs, while operations that are inherently blocking—such as filesystem access, DNS lookup, crypto, and compression—are delegated to a libuv-managed thread pool. The main JavaScript thread remains free to continue executing the event loop. Once the background task completes, libuv pushes the callback into the event loop queue, where it is eventually executed on the main thread. This architecture allows Node.js to efficiently handle large numbers of concurrent I/O operations without blocking.

## Question 2. Difference between `process.nextTick`, `setImmediate`, and `setTimeout` in Node.js

## ✅ Short Answer

In Node.js:

- **`process.nextTick()`** → runs **immediately after текущ call stack**, before the event loop continues (highest priority).
- **`setTimeout(fn, 0)`** → runs in the **timers phase**, after a minimum delay (not truly “immediate”).
- **`setImmediate()`** → runs in the **check phase**, after I/O callbacks.

**Priority order (most important):**

```
process.nextTick → Promise microtasks → setTimeout → setImmediate
```

---

# 🧠 Deep Interview-Grade Explanation

To understand these properly, you must understand the **Node.js event loop phases**.

Node.js uses libuv to manage its event loop inside Node.js.

---

# 🔁 Event Loop Phases (simplified)

1. **Timers phase** → `setTimeout`, `setInterval`
2. **Pending callbacks**
3. **Idle / prepare**
4. **Poll phase** → I/O callbacks
5. **Check phase** → `setImmediate`
6. **Close callbacks**

Between these phases, Node also flushes:

- `process.nextTick()` queue
- Microtask queue (Promises)

---

# ⚡ 1. `process.nextTick()`

## ✔️ What it does

Executes callbacks **before the event loop continues**, even before Promises.

### Key idea:

👉 It runs **after current function finishes but before any I/O or timers**

---

## Example

```js
console.log("start");

process.nextTick(() => {
  console.log("nextTick");
});

console.log("end");
```

### Output:

```
start
end
nextTick
```

---

## 🧠 Why it behaves this way

Node maintains a **special nextTick queue** that is drained:

- AFTER current call stack
- BEFORE event loop continues

---

## ⚠️ Pitfall (very important in interviews)

You can **starve the event loop**:

```js
function loop() {
  process.nextTick(loop);
}

loop();
```

❌ This blocks everything else (no I/O, no timers).

---

## Use cases

- Deferring execution until after current function
- Ensuring async behavior in APIs
- Error handling in initialization logic

---

# ⏱ 2. `setTimeout(fn, 0)`

## ✔️ What it does

Schedules a callback in the **timers phase** after a minimum delay.

Even `0ms` is NOT immediate.

---

## Example

```js
console.log("start");

setTimeout(() => {
  console.log("timeout");
}, 0);

console.log("end");
```

### Output:

```
start
end
timeout
```

---

## 🧠 Important detail

Even with `0ms`:

- Node enforces a **minimum delay (~1ms or more depending on system load)**
- It executes in the **next timers phase**

---

## Use cases

- Scheduling delayed execution
- Breaking long synchronous tasks
- Deferring work to avoid blocking

---

# 🚀 3. `setImmediate()`

## ✔️ What it does

Executes callbacks in the **check phase**, after I/O events.

---

## Example

```js
console.log("start");

setImmediate(() => {
  console.log("immediate");
});

console.log("end");
```

### Output:

```
start
end
immediate
```

---

## 🧠 Key behavior

- Runs **after poll phase**
- Designed for **I/O completion callbacks**

---

## Special case (very important interview trap)

Order depends on context:

```js
const fs = require("fs");

fs.readFile(__filename, () => {
  setTimeout(() => console.log("timeout"), 0);
  setImmediate(() => console.log("immediate"));
});
```

### Output:

```
immediate
timeout
```

### Why?

Inside I/O callback:

- Event loop is in **poll phase**
- Next phase is **check → setImmediate runs first**
- Then timers phase → setTimeout runs later

---

# ⚖️ Comparison Table

| Feature         | `process.nextTick`          | `setTimeout(fn, 0)` | `setImmediate` |
| --------------- | --------------------------- | ------------------- | -------------- |
| Phase           | Before event loop continues | Timers phase        | Check phase    |
| Priority        | Highest                     | Low                 | Medium         |
| Delay           | Immediate (after stack)     | Minimum delay       | After I/O      |
| Starvation risk | Yes                         | No                  | No             |
| Use case        | Internal deferral           | Scheduling delay    | I/O completion |

---

# 🔥 Execution Order Example (Classic Interview Question)

```js
console.log("A");

setTimeout(() => console.log("B"), 0);

setImmediate(() => console.log("C"));

process.nextTick(() => console.log("D"));

Promise.resolve().then(() => console.log("E"));

console.log("F");
```

## ✅ Output:

```
A
F
D
E
B
C
```

---

## 🧠 Reasoning:

1. Sync code runs first → `A F`
2. `process.nextTick` → `D`
3. Microtasks (Promises) → `E`
4. Timers phase → `B`
5. Check phase → `C`

---

# ⚠️ Common Interview Pitfalls

## 1. Thinking `setTimeout(0)` is immediate

❌ Wrong
✔ It is scheduled for next timers phase

---

## 2. Confusing `setImmediate` and `setTimeout`

- `setImmediate` → after I/O
- `setTimeout` → after timer delay

---

## 3. Overusing `process.nextTick`

Can freeze event loop if abused.

---

# 🧠 Mental Model (Best way to remember)

Think of execution priority like this:

```
SYNC CODE
   ↓
process.nextTick queue
   ↓
Promise microtasks
   ↓
Timers (setTimeout)
   ↓
I/O callbacks
   ↓
setImmediate
```

---

# 🚀 Interview Summary (Strong Answer)

> In Node.js, `process.nextTick` executes immediately after the current call stack and before the event loop continues, making it the highest priority queue. `setTimeout(fn, 0)` schedules a callback in the timers phase after a minimum delay, so it is not truly immediate. `setImmediate` executes in the check phase, typically after I/O callbacks. The execution order depends on the event loop phases managed by libuv, and understanding this ordering is crucial for writing non-blocking and predictable asynchronous code in Node.js.

## Question 3. How to implement clustering in Node.js

## ✅ Short Answer

Clustering in Node.js is implemented using the built-in **`cluster` module**, which allows you to spawn multiple **worker processes** that share the same server port. This enables Node.js to fully utilize multi-core CPUs and improve concurrency.

Each worker is an independent process, and a **master process (primary)** manages them.

---

# 🧠 Interview-Grade Explanation

Node.js is single-threaded at the JavaScript level, but the runtime (Node.js) supports scaling via the **cluster model**, built on top of OS-level process forking.

Clustering uses:

- The **master process (primary)**
- Multiple **worker processes**
- Shared server port via load balancing

Internally, it leverages OS capabilities (not libuv threads) and spreads incoming connections across workers.

---

# 🏗️ How Node.js Clustering Works

When you create a cluster:

1. Master process starts
2. It forks multiple worker processes
3. Each worker runs a copy of your app
4. Incoming requests are distributed among workers
5. If a worker crashes, master can restart it

---

# ⚙️ Basic Cluster Implementation

```js id="c1k8xq"
const cluster = require("cluster");
const http = require("http");
const os = require("os");

const numCPUs = os.cpus().length;

if (cluster.isPrimary) {
  console.log(`Master ${process.pid} is running`);

  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // Restart worker if it dies
  cluster.on("exit", (worker) => {
    console.log(`Worker ${worker.process.pid} died. Restarting...`);
    cluster.fork();
  });
} else {
  // Worker processes share same server port
  http
    .createServer((req, res) => {
      res.writeHead(200);
      res.end(`Handled by worker ${process.pid}`);
    })
    .listen(3000);

  console.log(`Worker ${process.pid} started`);
}
```

---

# 🧠 Key Concepts

## 1. Primary (Master) Process

- Does NOT handle requests
- Manages workers
- Handles lifecycle (fork, restart, monitoring)

---

## 2. Worker Process

- Independent Node.js process
- Runs full app instance
- Handles HTTP requests

---

## 3. Load Distribution

Node uses OS-level load balancing:

- Round-robin (default on most systems)
- Or OS kernel-level scheduling

---

# ⚡ Why Clustering Improves Performance

Without cluster:

- Single CPU core used
- One event loop
- Limited throughput

With cluster:

- Multiple processes
- Multiple event loops
- Parallel request handling
- Better CPU utilization

---

# 🧵 Important Distinction (Interview Favorite)

| Feature         | Cluster               | Worker Threads            |
| --------------- | --------------------- | ------------------------- |
| Unit            | Process               | Thread                    |
| Memory          | Separate              | Shared memory possible    |
| Use case        | HTTP scaling          | CPU-heavy tasks           |
| Communication   | IPC (message passing) | Shared memory / messaging |
| Crash isolation | High                  | Medium                    |

---

# 🔁 Inter-Process Communication (IPC)

Workers can communicate with master:

```js id="k8m2px"
if (cluster.isPrimary) {
  const worker = cluster.fork();

  worker.on("message", (msg) => {
    console.log("From worker:", msg);
  });
} else {
  process.send("Hello from worker");
}
```

---

# ⚠️ Common Pitfalls

## 1. Not sharing state properly

Each worker has its own memory:

- No shared variables
- No shared cache

👉 Solution: Use Redis / external store

---

## 2. Sticky sessions problem

If using sessions stored in memory:

- Requests may hit different workers
- Session inconsistency occurs

👉 Fix:

- Use Redis session store
- Or sticky session load balancer (NGINX)

---

## 3. Over-forking workers

Too many workers cause:

- High memory usage
- Context switching overhead

Rule of thumb:

```txt id="m2x9qf"
Number of workers ≈ number of CPU cores
```

---

## 4. Not handling worker crashes

Always listen for:

```js id="r8k3dd"
cluster.on("exit", (worker) => {
  cluster.fork();
});
```

---

# 🚀 Modern Alternative (Important in Interviews)

In real production systems, clustering is often replaced by:

- PM2 process manager
- Docker containers
- Kubernetes scaling

Example with PM2:

```bash id="p0m3x1"
pm2 start app.js -i max
```

This automatically:

- Clusters app
- Restarts crashed processes
- Balances load

---

# 🧠 When to Use Cluster

Use clustering when:

✔ CPU cores are underutilized
✔ Building HTTP API servers
✔ High concurrency traffic apps

Avoid when:

❌ Heavy shared state required
❌ Already using container orchestration
❌ CPU-bound tasks (use Worker Threads instead)

---

# ⚡ Mental Model (Interview Ready)

Think of clustering as:

```txt id="x1c9aa"
One app → multiple independent Node.js processes → same port → OS distributes traffic
```

---

# 🎯 Interview Summary

> Node.js clustering is implemented using the built-in cluster module, which allows a master process to fork multiple worker processes. Each worker runs its own event loop and handles incoming requests independently while sharing the same server port. This enables Node.js to utilize multi-core systems efficiently. Communication between workers and the master happens via IPC. Clustering improves scalability for I/O-heavy applications, but state management must be externalized since each worker has isolated memory.

## Question 4. Difference between cluster and worker threads in Node.js

## Question 5. How to handle file uploads in Node.js efficiently

## Question 6. How to implement streaming large files without blocking memory

## Question 7. Difference between Buffer and Stream in Node.js

## Question 8. How to implement rate limiting in Node.js APIs

## Question 9. How to prevent event loop blocking for CPU-intensive tasks

## Question 10. How to implement caching in Node.js with LRU cache

## Question 11. Difference between callback-based APIs and promise-based APIs in Node.js

## Question 12. How to handle uncaught exceptions gracefully

## Question 13. How to implement JWT authentication in Node.js

## Question 14. How to implement role-based access control in Node.js

## Question 15. How to implement API versioning in Node.js applications

## Question 16. How to handle database connection pooling efficiently

## Question 17. Difference between synchronous and asynchronous file operations

## Question 18. How to stream logs in Node.js

## Question 19. How to implement WebSocket server and handle concurrent clients

## Question 20. How to implement server-sent events (SSE) in Node.js
