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

## ✅ Short Answer

Both **Cluster** and **Worker Threads** enable parallelism in Node.js, but they solve different problems:

- **Cluster** → Creates **multiple Node.js processes** to utilize multiple CPU cores and scale server workloads.
- **Worker Threads** → Creates **multiple threads within a single process** to perform CPU-intensive JavaScript computations without blocking the main thread.

A common interview answer is:

> Use **Cluster** to scale network servers and handle more requests across CPU cores. Use **Worker Threads** for CPU-bound tasks like image processing, data transformation, encryption, or large calculations.

---

# 🧠 Why They Exist

JavaScript execution in Node.js is single-threaded.

Without additional mechanisms:

```js
while (true) {
  // heavy computation
}
```

This blocks:

- HTTP requests
- Timers
- Promises
- Event loop

Node provides two solutions:

1. **Cluster** → Multiple processes
2. **Worker Threads** → Multiple threads

---

# 🏗️ Cluster Architecture

```txt
                 Master Process
                        |
      ---------------------------------
      |               |              |
   Worker 1       Worker 2      Worker 3
      |               |              |
      -------------------------------
               Shared Port
```

Each worker:

- Is a completely separate process
- Has its own memory
- Has its own V8 instance
- Has its own event loop

Example:

```js
const cluster = require("cluster");
const os = require("os");

if (cluster.isPrimary) {
  for (let i = 0; i < os.cpus().length; i++) {
    cluster.fork();
  }
}
```

---

# 🧵 Worker Threads Architecture

```txt
Node Process
    |
    |-- Main Thread
    |
    |-- Worker Thread A
    |
    |-- Worker Thread B
```

All workers belong to the same process.

Example:

```js
const { Worker } = require("worker_threads");

new Worker("./worker.js");
```

---

# ⚡ Core Difference

## Cluster

Creates:

```txt
Node Process #1
Node Process #2
Node Process #3
Node Process #4
```

## Worker Threads

Creates:

```txt
One Node Process
 ├─ Thread 1
 ├─ Thread 2
 ├─ Thread 3
 └─ Thread 4
```

---

# 📊 Detailed Comparison

| Feature             | Cluster         | Worker Threads                      |
| ------------------- | --------------- | ----------------------------------- |
| Unit of parallelism | Process         | Thread                              |
| Memory              | Separate memory | Shared memory possible              |
| V8 instance         | Separate        | Shared process                      |
| Event loop          | One per process | One per thread                      |
| Communication       | IPC             | Message passing / SharedArrayBuffer |
| Startup cost        | Higher          | Lower                               |
| Memory usage        | Higher          | Lower                               |
| Crash isolation     | Strong          | Weaker                              |
| Best for            | HTTP scaling    | CPU-intensive tasks                 |

---

# 💾 Memory Behavior

## Cluster

Every process has its own memory.

```js
let counter = 0;
```

Worker A:

```txt
counter = 5
```

Worker B:

```txt
counter = 0
```

No sharing.

---

## Worker Threads

Can share memory.

Example:

```js
const sharedBuffer = new SharedArrayBuffer(4);
```

Multiple workers can access it.

---

# 🚀 Performance Characteristics

## Cluster

Excellent for:

- REST APIs
- GraphQL servers
- WebSocket servers
- High-concurrency applications

Reason:

Each worker handles requests independently.

---

## Worker Threads

Excellent for:

- Image resizing
- Video processing
- Data analytics
- Compression
- Large JSON parsing
- Encryption

Reason:

Heavy CPU work moves off the main thread.

---

# Example: Bad Use of Cluster

Suppose you need:

```js
for (let i = 0; i < 1e10; i++) {
  // calculate
}
```

Creating 8 cluster workers:

```txt
Worker 1 → blocked
Worker 2 → blocked
Worker 3 → blocked
...
```

Not ideal.

Worker Threads are better.

---

# Example: Bad Use of Worker Threads

Suppose your API receives:

```txt
10000 requests/sec
```

Creating worker threads for every request is inefficient.

Cluster is the better solution.

---

# Communication Mechanisms

## Cluster

Uses IPC (Inter-Process Communication):

```js
worker.send("hello");
```

```js
process.on("message", (msg) => {
  console.log(msg);
});
```

---

## Worker Threads

Uses message channels:

```js
parentPort.postMessage("done");
```

```js
worker.on("message", (msg) => {
  console.log(msg);
});
```

---

# Fault Tolerance

## Cluster

If worker crashes:

```txt
Worker 3 crashed
```

Other workers continue serving requests.

Master can restart it.

```js
cluster.on("exit", () => {
  cluster.fork();
});
```

Very resilient.

---

## Worker Threads

If a worker thread crashes:

```txt
Entire process may be affected
```

Isolation is weaker than separate processes.

---

# Real Production Architecture

A common architecture:

```txt
NGINX
   |
Cluster Workers
   |
Worker Threads
```

Example:

```txt
HTTP Request
    ↓
Cluster Worker
    ↓
Worker Thread
    ↓
Heavy Image Processing
```

Cluster handles scalability.

Worker thread handles CPU work.

---

# Common Interview Question

## "Does Cluster Make JavaScript Multi-Threaded?"

No.

Each cluster worker:

```txt
Single-threaded JavaScript
```

But there are multiple processes:

```txt
Process A
Process B
Process C
```

running in parallel.

---

# Common Interview Question

## "Does Worker Thread Have Its Own Event Loop?"

Yes.

Every worker thread has:

- Separate V8 isolate
- Separate event loop
- Separate call stack

But remains inside the same OS process.

---

# Best Practice

### Use Cluster For

✅ APIs
✅ Web servers
✅ Microservices
✅ WebSockets

---

### Use Worker Threads For

✅ Image processing
✅ Compression
✅ Encryption
✅ Machine learning inference
✅ Large computations

---

# Interview Summary

> The Cluster module creates multiple Node.js processes, each with its own memory space, V8 instance, and event loop. It is primarily used to scale servers across CPU cores and improve request throughput. Worker Threads, on the other hand, create multiple threads within a single process and are designed for CPU-intensive JavaScript tasks. Worker Threads have lower memory overhead and can share memory, while Cluster provides stronger isolation and fault tolerance. In practice, Cluster is used for horizontal scaling of server workloads, whereas Worker Threads are used to offload expensive computations from the main event loop.

## Question 5. How to handle file uploads in Node.js efficiently

## ✅ Short Answer

To handle file uploads efficiently in Node.js:

1. **Stream files instead of buffering them in memory.**
2. Use middleware like **Multer**, **Busboy**, or **Formidable**.
3. Store large files directly in cloud storage (e.g., S3) when possible.
4. Enforce file size/type limits.
5. Validate and sanitize uploads.
6. Process files asynchronously to avoid blocking the event loop.

The key principle is:

> **Never load large files entirely into memory if you can stream them.**

---

# 🧠 Understanding the Challenge

When a user uploads a file:

```txt
Browser
   ↓
Node.js Server
   ↓
Storage (Disk/S3/Database)
```

A naive implementation might read the entire file into memory:

```js
// Bad idea for large files
const chunks = [];

req.on("data", (chunk) => {
  chunks.push(chunk);
});
```

Problems:

- High memory usage
- Potential crashes
- Poor scalability
- Event loop pressure

---

# Why Streams Are Important

Node.js streams process data chunk-by-chunk.

```txt
File
 ↓
Chunk 1
Chunk 2
Chunk 3
 ↓
Destination
```

Benefits:

- Constant memory usage
- Better performance
- Suitable for very large files

---

# Option 1: Using Multer (Most Common)

Multer is the most common middleware for handling multipart/form-data uploads in Express.

Install:

```bash
npm install multer
```

---

## Basic Example

```js
const express = require("express");
const multer = require("multer");

const app = express();

const upload = multer({
  dest: "uploads/",
});

app.post("/upload", upload.single("file"), (req, res) => {
  res.json({
    filename: req.file.filename,
  });
});

app.listen(3000);
```

HTML:

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
  <input type="file" name="file" />
  <button>Upload</button>
</form>
```

---

# Option 2: Streaming with Busboy (More Efficient)

For very large uploads, Busboy is often preferred because it streams directly.

```js
const Busboy = require("busboy");
const fs = require("fs");

app.post("/upload", (req, res) => {
  const busboy = Busboy({ headers: req.headers });

  busboy.on("file", (name, file, info) => {
    const writeStream = fs.createWriteStream(`uploads/${info.filename}`);

    file.pipe(writeStream);
  });

  busboy.on("finish", () => {
    res.send("Uploaded");
  });

  req.pipe(busboy);
});
```

---

# Streaming Directly to Storage

Instead of:

```txt
Client
 ↓
Server Disk
 ↓
Cloud Storage
```

Use:

```txt
Client
 ↓
Node Stream
 ↓
S3
```

Example:

```js
fileStream.pipe(uploadStream);
```

Benefits:

- No temporary files
- Reduced disk usage
- Better scalability

---

# File Size Limits

Always enforce limits.

Multer example:

```js
const upload = multer({
  limits: {
    fileSize: 10 * 1024 * 1024,
  },
});
```

10 MB limit.

Without limits:

```txt
User uploads 5 GB file
```

Your server may run out of resources.

---

# File Type Validation

Never trust file extensions.

Bad:

```txt
virus.jpg.exe
```

Better:

```js
const upload = multer({
  fileFilter(req, file, cb) {
    const allowed = ["image/jpeg", "image/png"];

    cb(null, allowed.includes(file.mimetype));
  },
});
```

For stronger validation, inspect file signatures ("magic bytes").

---

# Efficient Image Processing

Bad:

```txt
Upload image
↓
Process on main thread
↓
Event loop blocked
```

Better:

```txt
Upload
↓
Queue Job
↓
Worker Thread
↓
Image Processing
```

Example libraries:

- Sharp
- Worker Threads
- Background job queues

---

# Handling Large Files

For multi-GB uploads:

Use streams:

```js
const fs = require("fs");

req.pipe(fs.createWriteStream("video.mp4"));
```

Memory remains nearly constant regardless of file size.

---

# Backpressure (Advanced Interview Topic)

Streams automatically handle backpressure.

Without backpressure:

```txt
Incoming data > Write speed
```

Memory grows uncontrollably.

With streams:

```txt
Readable pauses
↓
Writable catches up
↓
Readable resumes
```

Node's stream API manages this automatically.

---

# Security Best Practices

### 1. Validate File Types

```js
file.mimetype;
```

and ideally inspect actual file content.

---

### 2. Limit File Size

```js
limits: {
  fileSize: 5 * 1024 * 1024;
}
```

---

### 3. Rename Uploaded Files

Avoid:

```txt
../../../etc/passwd
```

Use generated names:

```js
crypto.randomUUID();
```

---

### 4. Scan Uploads

For public-facing systems:

```txt
Upload
 ↓
Virus Scan
 ↓
Store
```

---

### 5. Avoid Executable Uploads

Block:

```txt
.exe
.bat
.sh
.php
```

unless explicitly required.

---

# Multer Memory Storage vs Disk Storage

### Memory Storage

```js
multer.memoryStorage();
```

Pros:

- Fast

Cons:

- Entire file in RAM

Bad for large uploads.

---

### Disk Storage

```js
multer.diskStorage();
```

Pros:

- Lower memory usage

Cons:

- Disk I/O

Usually preferred.

---

# Handling Multiple Files

```js
app.post("/upload", upload.array("photos", 10), (req, res) => {
  res.send("Uploaded");
});
```

Maximum 10 files.

---

# Common Interview Pitfalls

## ❌ Reading Entire File into Memory

```js
fs.readFile(...)
```

For large uploads this is inefficient.

Use streams.

---

## ❌ No Upload Limits

Can lead to denial-of-service attacks.

---

## ❌ Trusting File Extensions

Always validate MIME type and file content.

---

## ❌ Processing Images on Main Thread

Use Worker Threads or background jobs.

---

# Real-World Production Flow

```txt
Client
   ↓
Load Balancer
   ↓
Node.js API
   ↓
Streaming Upload
   ↓
S3 Storage
   ↓
Queue
   ↓
Worker Threads
   ↓
Image Processing
```

This architecture scales well and keeps the event loop responsive.

---

# Interview Summary

> Efficient file uploads in Node.js are typically implemented using streams to avoid loading entire files into memory. Libraries such as Multer, Busboy, and Formidable help parse multipart form data. For large files, streaming directly to disk or cloud storage is preferred. Production systems should enforce file size limits, validate MIME types, sanitize filenames, and offload expensive processing tasks to Worker Threads or background workers. Streams are especially important because they provide backpressure handling and maintain low memory usage regardless of file size.

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
