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

## ✅ Short Answer

To stream large files without blocking memory in Node.js, use **streams** (`fs.createReadStream()` and `fs.createWriteStream()`) instead of reading the entire file into memory with `fs.readFile()`.

Streams process data **chunk by chunk**, keeping memory usage low and allowing Node.js to remain responsive.

```js
const fs = require("fs");

const readStream = fs.createReadStream("large-video.mp4");

readStream.on("data", (chunk) => {
  console.log(`Received ${chunk.length} bytes`);
});
```

This approach can handle files that are much larger than the available RAM.

---

# 🧠 Why Not Use `fs.readFile()`?

A common mistake is:

```js
const fs = require("fs");

fs.readFile("large-video.mp4", (err, data) => {
  console.log(data.length);
});
```

### Problem

`fs.readFile()`:

1. Reads the **entire file**
2. Stores it in memory
3. Then invokes the callback

For a 5 GB file:

```txt
File Size = 5 GB
RAM Usage ≈ 5 GB
```

This can cause:

- High memory consumption
- Garbage collection pressure
- Process crashes
- Poor scalability

---

# How Streams Solve the Problem

Streams process data incrementally:

```txt
Large File
    │
    ▼
Chunk 1 (64 KB)
Chunk 2 (64 KB)
Chunk 3 (64 KB)
...
```

Only a small portion of the file is held in memory at any time.

Memory remains nearly constant:

```txt
File Size: 10 GB
Memory Usage: ~64 KB–a few MB
```

---

# Reading a Large File

```js
const fs = require("fs");

const stream = fs.createReadStream("large.log");

stream.on("data", (chunk) => {
  console.log("Chunk received:", chunk.length);
});

stream.on("end", () => {
  console.log("Finished");
});
```

### Flow

```txt
Disk
 ↓
Read Stream
 ↓
Chunk
 ↓
Application
```

---

# Writing Large Files

```js
const fs = require("fs");

const writeStream = fs.createWriteStream("output.txt");

writeStream.write("Hello\n");
writeStream.write("World\n");

writeStream.end();
```

---

# Copying Large Files Efficiently

### ❌ Bad

```js
fs.readFile("big.zip", (err, data) => {
  fs.writeFile("copy.zip", data, () => {});
});
```

Loads entire file into RAM.

---

### ✅ Good

```js
const fs = require("fs");

const readStream = fs.createReadStream("big.zip");
const writeStream = fs.createWriteStream("copy.zip");

readStream.pipe(writeStream);
```

### Internal Flow

```txt
Read Stream
     │
     ▼
   pipe()
     │
     ▼
Write Stream
```

This is the preferred production approach.

---

# Streaming Files Over HTTP

A very common interview question.

### ❌ Bad

```js
const fs = require("fs");

app.get("/video", (req, res) => {
  fs.readFile("movie.mp4", (err, data) => {
    res.send(data);
  });
});
```

Entire file enters memory.

---

### ✅ Good

```js
const fs = require("fs");

app.get("/video", (req, res) => {
  const stream = fs.createReadStream("movie.mp4");

  stream.pipe(res);
});
```

Now:

```txt
Disk
 ↓
Read Stream
 ↓
HTTP Response
 ↓
Browser
```

Memory usage stays low.

---

# Using `pipeline()` (Best Practice)

Modern Node.js provides `stream.pipeline()`.

```js
const fs = require("fs");
const { pipeline } = require("stream");

pipeline(
  fs.createReadStream("input.mp4"),
  fs.createWriteStream("output.mp4"),
  (err) => {
    if (err) {
      console.error(err);
    } else {
      console.log("Done");
    }
  },
);
```

### Benefits

- Automatic error handling
- Cleans up streams
- Prevents resource leaks

---

# Backpressure (Senior-Level Interview Topic)

## The Problem

Suppose:

```txt
Disk Read Speed = 500 MB/s
Network Speed = 20 MB/s
```

Data arrives faster than it can be written.

Without control:

```txt
Memory grows indefinitely
```

---

## Solution: Backpressure

Streams automatically pause and resume.

```txt
Readable Stream
      ↓
Write Buffer Full
      ↓
Pause Reading
      ↓
Buffer Drained
      ↓
Resume Reading
```

This prevents memory explosions.

---

# Example of Manual Backpressure Handling

```js
const fs = require("fs");

const readable = fs.createReadStream("big.txt");
const writable = fs.createWriteStream("copy.txt");

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

Normally `pipe()` handles this automatically.

---

# Streaming Uploads

Instead of:

```txt
Upload
 ↓
Memory
 ↓
Disk
```

Use:

```txt
Upload
 ↓
Stream
 ↓
Disk
```

Example:

```js
req.pipe(fs.createWriteStream("uploaded.bin"));
```

This scales much better for large uploads.

---

# Tuning Stream Performance

Default chunk size:

```txt
64 KB
```

Can be customized:

```js
const stream = fs.createReadStream("big.dat", {
  highWaterMark: 1024 * 1024,
});
```

1 MB chunks.

### Trade-off

Larger chunks:

✅ Fewer I/O operations

❌ More memory

Smaller chunks:

✅ Lower memory

❌ More overhead

---

# Async Iteration with Streams

Modern Node.js supports:

```js
const fs = require("fs");

async function readFile() {
  const stream = fs.createReadStream("large.txt");

  for await (const chunk of stream) {
    console.log(chunk.length);
  }
}

readFile();
```

This is often considered cleaner than event handlers.

---

# Common Interview Pitfalls

### ❌ Using `readFile()` for large files

```js
fs.readFile(...)
```

Loads everything into RAM.

---

### ❌ Ignoring Stream Errors

Always handle:

```js
stream.on("error", (err) => {
  console.error(err);
});
```

Or use `pipeline()`.

---

### ❌ Forgetting Backpressure

Writing custom stream logic without pause/resume can exhaust memory.

---

### ❌ Buffering Uploads

Avoid:

```js
let data = [];

req.on("data", (chunk) => {
  data.push(chunk);
});
```

For large uploads.

---

# `pipe()` vs `pipeline()`

| Feature                    | `pipe()` | `pipeline()` |
| -------------------------- | -------- | ------------ |
| Easy to use                | ✅       | ✅           |
| Handles backpressure       | ✅       | ✅           |
| Automatic cleanup          | ❌       | ✅           |
| Centralized error handling | ❌       | ✅           |
| Recommended today          | Good     | Best         |

---

# 🚀 Interview Summary

> To stream large files efficiently in Node.js, use streams such as `fs.createReadStream()` and `fs.createWriteStream()` rather than loading the entire file into memory with `fs.readFile()`. Streams process data in chunks, keeping memory usage constant regardless of file size. Using `pipe()` or, preferably, `stream.pipeline()` allows data to flow efficiently between sources and destinations while automatically handling backpressure. This makes streams the standard solution for large file transfers, uploads, downloads, and media streaming in production Node.js applications.

## Question 7. Difference between Buffer and Stream in Node.js

## ✅ Short Answer

**Buffer** and **Stream** are both used for handling binary data in Node.js, but they work differently:

- **Buffer** → Holds the **entire data in memory**.
- **Stream** → Processes data **chunk by chunk** without loading everything into memory.

### Simple Analogy

Imagine a 5 GB movie file:

- **Buffer** = Download the entire movie before watching.
- **Stream** = Watch the movie while it is downloading.

For large files and network operations, streams are generally more memory-efficient and scalable.

---

# 🧠 What is a Buffer?

A **Buffer** is a fixed-size chunk of memory used to store raw binary data.

JavaScript traditionally handles strings as UTF-16, but files, network packets, images, videos, and compressed data are binary. Node.js provides the `Buffer` class to work with such data efficiently.

Example:

```js
const buffer = Buffer.from("Hello");

console.log(buffer);
```

Output:

```txt
<Buffer 48 65 6c 6c 6f>
```

Each byte represents part of the string.

---

# Creating Buffers

```js
const buf1 = Buffer.alloc(10);

const buf2 = Buffer.from("Node.js");

const buf3 = Buffer.from([65, 66, 67]);
```

Output:

```txt
ABC
```

for `buf3.toString()`.

---

# Buffer Characteristics

### Entire Data Lives in Memory

```js
const fs = require("fs");

fs.readFile("large-video.mp4", (err, data) => {
  console.log(data instanceof Buffer); // true
});
```

Here:

```txt
File
 ↓
Entire File Loaded
 ↓
Buffer
 ↓
Callback
```

---

### Advantages

- Fast access
- Easy manipulation
- Good for small/medium data

---

### Disadvantages

- High memory consumption
- Not suitable for very large files
- Can cause memory pressure

---

# What is a Stream?

A **Stream** is an abstraction for reading or writing data incrementally.

Instead of loading everything into memory:

```txt
File
 ↓
Chunk 1
Chunk 2
Chunk 3
 ↓
Application
```

Data flows continuously.

---

# Reading with a Stream

```js
const fs = require("fs");

const stream = fs.createReadStream("large.log");

stream.on("data", (chunk) => {
  console.log(chunk.length);
});
```

Each `chunk` is actually a Buffer:

```txt
Stream
 ├─ Buffer Chunk #1
 ├─ Buffer Chunk #2
 ├─ Buffer Chunk #3
```

This is an important interview point:

> Streams internally emit Buffer chunks by default.

---

# Key Difference

## Buffer

```txt
Entire File
     ↓
  Buffer
     ↓
 Process
```

---

## Stream

```txt
Chunk
 ↓
Process
 ↓
Chunk
 ↓
Process
```

---

# Memory Comparison

Suppose:

```txt
File Size = 2 GB
```

### Buffer Approach

```js
fs.readFile("big.zip");
```

Memory usage:

```txt
~2 GB RAM
```

---

### Stream Approach

```js
fs.createReadStream("big.zip");
```

Memory usage:

```txt
64 KB – few MB
```

(depending on chunk size)

---

# Example: Reading a File

## Buffer-Based

```js
const fs = require("fs");

fs.readFile("data.txt", (err, data) => {
  console.log(data.toString());
});
```

Waits until entire file is loaded.

---

## Stream-Based

```js
const fs = require("fs");

const stream = fs.createReadStream("data.txt");

stream.on("data", (chunk) => {
  console.log(chunk.toString());
});
```

Processes data immediately.

---

# Performance Comparison

| Feature       | Buffer             | Stream    |
| ------------- | ------------------ | --------- |
| Memory Usage  | High               | Low       |
| Startup Time  | Wait for full load | Immediate |
| Large Files   | Poor               | Excellent |
| Random Access | Easy               | Harder    |
| Scalability   | Lower              | Higher    |

---

# Buffer and Stream Relationship

A common misconception:

> "Buffer and Stream are competing concepts."

Not exactly.

Streams often use Buffers internally.

Example:

```js
stream.on("data", (chunk) => {
  console.log(Buffer.isBuffer(chunk));
});
```

Output:

```txt
true
```

The stream emits Buffer objects.

---

# Types of Streams

Node.js has four stream types:

### Readable

```js
fs.createReadStream();
```

Reads data.

---

### Writable

```js
fs.createWriteStream();
```

Writes data.

---

### Duplex

```js
net.Socket;
```

Read and write.

---

### Transform

```js
zlib.createGzip();
```

Read, modify, write.

---

# Practical Example: File Copy

## Buffer Approach

```js
fs.readFile("source.zip", (err, data) => {
  fs.writeFile("dest.zip", data, () => {});
});
```

Memory:

```txt
source.zip → RAM → dest.zip
```

Not ideal for huge files.

---

## Stream Approach

```js
fs.createReadStream("source.zip").pipe(fs.createWriteStream("dest.zip"));
```

Memory remains low.

---

# Backpressure Support

Buffers alone do not provide backpressure.

Streams do.

Example:

```txt
Disk → 500 MB/s
Network → 20 MB/s
```

Streams automatically:

```txt
Pause
Resume
Throttle
```

to avoid memory overload.

This is one reason streams are preferred for network applications.

---

# When to Use Buffer

Use Buffer when:

✅ Data is relatively small
✅ Need random access
✅ Binary manipulation is required
✅ Working with cryptography or protocols

Example:

```js
const hash = crypto
  .createHash("sha256")
  .update(Buffer.from("secret"))
  .digest("hex");
```

---

# When to Use Stream

Use Stream when:

✅ Large files
✅ Uploads/downloads
✅ Video/audio processing
✅ Network communication
✅ Compression pipelines

Example:

```js
fs.createReadStream("video.mp4").pipe(res);
```

---

# Common Interview Pitfalls

### ❌ "Streams don't use memory"

Wrong.

Streams use memory for chunks.

The difference is:

```txt
Buffer → Entire dataset
Stream → Small chunks
```

---

### ❌ "Buffers are slower"

Not necessarily.

For small data:

```txt
Buffer can be faster and simpler.
```

---

### ❌ "Stream data is not a Buffer"

Wrong.

By default:

```js
stream.on("data", (chunk) => {
  console.log(Buffer.isBuffer(chunk));
});
```

returns:

```txt
true
```

---

# Real-World Example

Serving a video:

### Buffer

```js
const data = await fs.promises.readFile("movie.mp4");

res.end(data);
```

Consumes memory proportional to file size.

---

### Stream

```js
fs.createReadStream("movie.mp4").pipe(res);
```

Consumes only chunk-sized memory.

This is how production servers typically serve large media files.

---

# 🎯 Interview Summary

> A Buffer is a memory structure used to store raw binary data in Node.js, while a Stream is an abstraction that processes data incrementally in chunks. Buffers require the entire dataset to be loaded into memory, making them suitable for smaller amounts of data or situations requiring random access. Streams are designed for large files and network operations because they process data piece by piece, support backpressure, and maintain low memory usage. Internally, streams often emit Buffer objects, so streams and buffers complement each other rather than being competing technologies.

## Question 8. How to implement rate limiting in Node.js APIs

## ✅ Short Answer

Rate limiting restricts how many requests a client can make within a given time window. It protects Node.js APIs from:

- Abuse and brute-force attacks
- DDoS-like traffic spikes
- Excessive resource consumption
- API quota violations

The most common approaches are:

1. **Fixed Window Counter**
2. **Sliding Window Log**
3. **Sliding Window Counter**
4. **Token Bucket**
5. **Leaky Bucket**

In production, rate limits are often implemented using **Redis** and middleware such as express-rate-limit.

---

# Why Rate Limiting Matters

Without rate limiting:

```txt
Client
  ↓
100,000 requests/sec
  ↓
API Server
  ↓
CPU / Memory Exhaustion
```

With rate limiting:

```txt
Client
  ↓
100 requests/minute
  ↓
Allowed

101st request
  ↓
429 Too Many Requests
```

---

# HTTP Response for Rate Limit Exceeded

Standard response:

```http
HTTP/1.1 429 Too Many Requests
```

Example:

```json
{
  "error": "Rate limit exceeded"
}
```

---

# Option 1: Using express-rate-limit (Most Common)

Install:

```bash
npm install express-rate-limit
```

Example:

```js
const express = require("express");
const rateLimit = require("express-rate-limit");

const app = express();

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: "Too many requests",
});

app.use(limiter);

app.get("/", (req, res) => {
  res.send("Hello");
});

app.listen(3000);
```

Meaning:

```txt
100 requests
per
15 minutes
per IP
```

---

# How It Works Internally

Each client IP gets a counter:

```txt
192.168.1.1 → 42 requests

192.168.1.2 → 17 requests
```

When:

```txt
count > limit
```

return:

```txt
429 Too Many Requests
```

---

# Fixed Window Algorithm

The simplest algorithm.

Example:

```txt
Window = 1 minute
Limit = 100 requests
```

Requests:

```txt
10:00 → counter starts

Request #101
↓
Blocked
```

Implementation:

```js
const requests = new Map();

function rateLimit(req, res, next) {
  const ip = req.ip;

  const current = requests.get(ip) || 0;

  if (current >= 100) {
    return res.status(429).send("Too many requests");
  }

  requests.set(ip, current + 1);

  next();
}
```

---

## Problem

Burst issue:

```txt
10:00:59 → 100 requests

10:01:00 → 100 requests
```

User effectively sends:

```txt
200 requests in 2 seconds
```

---

# Sliding Window Log

Store timestamps:

```txt
[
  10:01:01,
  10:01:05,
  10:01:12
]
```

For each request:

1. Remove expired timestamps
2. Count remaining requests
3. Reject if limit exceeded

Example:

```js
const logs = new Map();

function limiter(req, res, next) {
  const now = Date.now();
  const ip = req.ip;

  const timestamps = logs.get(ip) || [];

  const valid = timestamps.filter((ts) => now - ts < 60000);

  if (valid.length >= 100) {
    return res.status(429).send("Too many requests");
  }

  valid.push(now);

  logs.set(ip, valid);

  next();
}
```

---

## Pros

More accurate.

## Cons

Memory-intensive for large traffic volumes.

---

# Token Bucket Algorithm

A favorite in system-design interviews.

Imagine:

```txt
Bucket Capacity = 100 tokens
```

Each request:

```txt
Consumes 1 token
```

Tokens regenerate over time:

```txt
+1 token/sec
```

Example:

```txt
100 tokens available
↓
User sends 50 requests
↓
50 tokens remain
```

Allows bursts while maintaining average rate.

---

# Token Bucket Visualization

```txt
Bucket = 10 tokens

Request
 ↓
Take token

No token?
 ↓
Reject
```

Widely used in:

- API gateways
- Cloud services
- Reverse proxies

---

# Leaky Bucket Algorithm

Think:

```txt
Requests enter bucket
 ↓
Processed at constant rate
```

Example:

```txt
Incoming:
100 requests/sec

Outgoing:
10 requests/sec
```

Smooths traffic spikes.

---

# Redis-Based Distributed Rate Limiting

A critical production topic.

Problem:

```txt
Server A
Server B
Server C
```

If counters are stored in memory:

```txt
IP count differs per server
```

Rate limits become inaccurate.

---

## Solution

Store counters in Redis.

Architecture:

```txt
Client
  ↓
API Servers
  ↓
Redis
```

All servers share the same counter.

---

# Redis Example

```js
const count = await redis.incr(`rate:${ip}`);
```

Set expiration:

```js
await redis.expire(`rate:${ip}`, 60);
```

Logic:

```txt
Count > Limit
 ↓
429
```

---

# Production-Grade Example

```js
const limiter = rateLimit({
  windowMs: 60 * 1000,
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
});
```

Headers returned:

```http
RateLimit-Limit: 100
RateLimit-Remaining: 75
RateLimit-Reset: 45
```

Useful for API consumers.

---

# Different Limits Per Route

Example:

```txt
/login
↓
5 requests/min

/api/products
↓
500 requests/min
```

Implementation:

```js
app.use("/login", loginLimiter);
app.use("/api", apiLimiter);
```

---

# Rate Limiting Authentication Endpoints

Common practice:

```txt
POST /login
```

Limit:

```txt
5 attempts
per 15 minutes
```

Protects against brute-force attacks.

---

# Rate Limiting by API Key

Instead of IP:

```txt
API Key
↓
Request Count
```

Useful for SaaS APIs.

Example:

```js
const apiKey = req.headers["x-api-key"];
```

Track usage by key.

---

# Common Pitfalls

## ❌ In-Memory Storage in Production

```js
const requests = new Map();
```

Fails across multiple servers.

Use Redis.

---

## ❌ Trusting `req.ip` Behind Proxies

If using:

- NGINX
- Load balancers
- Cloudflare

Configure:

```js
app.set("trust proxy", true);
```

Otherwise rate limits may not work correctly.

---

## ❌ One Global Limit

Bad:

```txt
All endpoints:
100 req/min
```

Different endpoints need different policies.

---

## ❌ No Retry Information

Return useful headers:

```http
Retry-After: 60
```

---

# Rate Limiting vs Throttling

Often confused.

### Rate Limiting

```txt
100 requests/minute
```

Reject excess requests.

---

### Throttling

```txt
Slow down requests
```

Instead of rejecting them.

---

# Real Production Architecture

```txt
Client
   ↓
Cloudflare
   ↓
NGINX
   ↓
Node.js API
   ↓
Redis Rate Limiter
   ↓
Database
```

Multiple layers provide protection.

---

# 🎯 Interview Summary

> Rate limiting in Node.js APIs is used to control how many requests a client can make within a specific time window. Common algorithms include Fixed Window, Sliding Window, Token Bucket, and Leaky Bucket. For Express applications, middleware such as express-rate-limit provides an easy implementation. In production environments, counters should be stored in Redis so limits remain consistent across multiple server instances. Rate limiting is especially important for authentication endpoints, public APIs, and services that need protection against abuse, brute-force attacks, and resource exhaustion.

## Question 9. How to prevent event loop blocking for CPU-intensive tasks

## ✅ Short Answer

To prevent **event loop blocking** in Node.js for CPU-intensive tasks, you should **move heavy computation off the main thread** using one of these approaches:

1. **Worker Threads (best for CPU-heavy JS tasks)**
2. **Child Processes (for isolation / external programs)**
3. **Clustering (for scaling, not CPU splitting)**
4. **Queues + Background Workers (for async job processing)**
5. **Native addons (for extreme performance cases)**

The core principle:

> Never run CPU-heavy work on the main event loop thread.

---

# 🧠 Why Event Loop Blocking Happens

Node.js runs JavaScript on a **single thread** inside Node.js.

If you run heavy computation:

```js
function heavyTask() {
  while (true) {}
}
```

Then:

```txt
Event Loop
   ↓
Blocked ❌
   ↓
No I/O
No HTTP responses
No timers
```

Even simple API requests stop responding.

---

# 🚨 Example of Blocking Code

```js id="b9x1kz"
app.get("/compute", (req, res) => {
  let sum = 0;

  for (let i = 0; i < 1e10; i++) {
    sum += i;
  }

  res.send({ sum });
});
```

### Problem:

- Blocks entire server
- Blocks all other requests
- Breaks scalability

---

# ✅ Solution 1: Worker Threads (Best Practice)

Worker Threads allow CPU-heavy tasks in parallel threads.

---

## Example: Offload CPU Task

### main.js

```js id="c1k9qz"
const { Worker } = require("worker_threads");

app.get("/compute", (req, res) => {
  const worker = new Worker("./worker.js", {
    workerData: 1000000000,
  });

  worker.on("message", (result) => {
    res.send({ result });
  });

  worker.on("error", (err) => {
    res.status(500).send(err.message);
  });
});
```

---

### worker.js

```js id="h3m9vp"
const { parentPort, workerData } = require("worker_threads");

let sum = 0;

for (let i = 0; i < workerData; i++) {
  sum += i;
}

parentPort.postMessage(sum);
```

---

## Why it works

```txt
Main Thread → stays free
Worker Thread → handles CPU work
```

No blocking occurs.

---

# ⚙️ Solution 2: Child Processes

Use when:

- Running separate programs
- Python scripts
- Heavy isolation needed

```js id="w0k7dp"
const { spawn } = require("child_process");

const child = spawn("node", ["heavy.js"]);

child.stdout.on("data", (data) => {
  console.log(data.toString());
});
```

### Pros:

- Strong isolation
- Separate memory

### Cons:

- Higher overhead than worker threads

---

# 🚀 Solution 3: Job Queues (Production Standard)

Use background processing systems:

- Redis queues
- BullMQ
- RabbitMQ

Example architecture:

```txt id="z3p7qk"
API Server
   ↓
Queue (Redis)
   ↓
Worker Process
   ↓
CPU-heavy task
```

---

## Example with BullMQ

```js id="v5q2mn"
queue.add("image-processing", {
  imageId: 123,
});
```

Worker:

```js id="l9d0rx"
queue.process(async (job) => {
  await heavyImageProcessing(job.data);
});
```

---

## Why queues are powerful

- Scalable horizontally
- Retry support
- Rate control
- Fault tolerance

---

# ⚖️ Solution 4: Cluster (NOT for CPU tasks)

Clustering uses multiple processes:

cluster module

```txt
Worker 1 → event loop
Worker 2 → event loop
Worker 3 → event loop
```

### But:

❌ Does NOT split a single CPU task
✔ Only distributes requests

---

## Wrong expectation:

```txt
"One request → split across workers"
```

Not true.

Each request still runs on one worker.

---

# ⚡ Solution 5: Break Tasks into Chunks (Cooperative Scheduling)

Instead of blocking:

```js
for (let i = 0; i < 1e9; i++) {}
```

Split work:

```js id="k4z1wv"
function chunkedTask(i = 0) {
  const limit = i + 1e6;

  for (; i < limit; i++) {
    // small work
  }

  if (i < 1e9) {
    setImmediate(() => chunkedTask(i));
  }
}
```

### Benefit:

- Keeps event loop responsive
- No extra threads needed

---

# ⚡ Solution 6: WebAssembly (Advanced)

For compute-heavy tasks:

- Image processing
- Crypto
- ML inference

WASM can run faster than JS in some cases.

---

# 📊 Comparison

| Method          | Best For          | Blocking Risk     | Complexity |
| --------------- | ----------------- | ----------------- | ---------- |
| Worker Threads  | CPU-heavy JS      | ❌ No             | Medium     |
| Child Processes | External programs | ❌ No             | High       |
| Queues          | Background jobs   | ❌ No             | High       |
| Chunking        | Small CPU tasks   | ⚠️ Low            | Low        |
| Cluster         | HTTP scaling      | ❌ No CPU offload | Low        |

---

# 🧠 Key Interview Insight

> Node.js is single-threaded for JavaScript execution, so any CPU-intensive operation blocks the event loop unless explicitly moved to another thread, process, or asynchronous job system.

---

# 🚨 Common Mistakes

## ❌ Using setTimeout for CPU tasks

```js
setTimeout(() => heavyTask(), 0);
```

Does NOT solve blocking.

---

## ❌ Using cluster instead of worker threads

Cluster helps concurrency, not computation splitting.

---

## ❌ Running heavy loops in request handlers

```js
app.get("/", () => {
  heavyLoop(); // bad
});
```

---

# 🧠 Mental Model

Think of Node.js like this:

```txt
Event Loop = Single cashier
CPU task = Long transaction
```

If cashier is busy:

- Everyone waits ❌

So you either:

- Hire assistants (worker threads)
- Open multiple counters (cluster)
- Offload work (queues)

---

# 🎯 Interview Summary

> To prevent event loop blocking in Node.js, CPU-intensive tasks should be offloaded from the main thread. The best approach is using Worker Threads, which allow parallel execution of JavaScript in separate threads within the same process. Alternatively, Child Processes can be used for isolated execution, and job queues like BullMQ are commonly used in production systems for asynchronous background processing. Clustering helps scale request handling but does not split CPU work. For small tasks, chunking using setImmediate or setTimeout can maintain responsiveness. The key principle is to ensure the event loop remains free to handle I/O operations efficiently.

## Question 10. How to implement caching in Node.js with LRU cache

## ✅ Short Answer

To implement caching in Node.js using an **LRU (Least Recently Used) cache**, you typically use an in-memory data structure that:

- Stores a fixed number of items
- Automatically removes the **least recently accessed item** when the limit is exceeded

The most common approach is using the `lru-cache` package or implementing it with a `Map`.

---

# 🧠 What is LRU Cache?

An LRU cache evicts the **least recently used item first** when it reaches capacity.

### Example:

```txt id="xk2q9a"
Capacity = 3

Access order:
A → B → C

Add D → A gets removed (least recently used)
```

---

# Why LRU Cache is Useful in Node.js

In Node.js applications:

- Reduces database load
- Improves API response time
- Stores frequently accessed computations
- Useful for rate-heavy or repeated queries

---

# ⚙️ Option 1: Using `lru-cache` (Recommended)

## Install

```bash id="9qv8mf"
npm install lru-cache
```

---

## Basic Example

```js id="l2x9qv"
const LRUCache = require("lru-cache");

const cache = new LRUCache({
  max: 3, // max items
  ttl: 1000 * 60 * 5, // optional: 5 minutes
});

cache.set("a", 1);
cache.set("b", 2);
cache.set("c", 3);

console.log(cache.get("a")); // 1

cache.set("d", 4); // removes "b" (least recently used)
```

---

# 🧠 How It Works Internally

LRU cache is typically implemented using:

- A **Map** (for O(1) lookup)
- A **doubly linked list** (for tracking usage order)

---

### Structure:

```txt id="m1k7xp"
Most Recently Used → Head
Least Recently Used → Tail
```

On access:

```txt id="8kq2nm"
Move item to head
```

On overflow:

```txt id="z9p3tw"
Remove tail node
```

---

# ⚙️ Option 2: Custom LRU Cache Implementation

This is a common **FAANG interview question**.

---

## Implementation using Map (simplified)

```js id="k8x2qv"
class LRUCache {
  constructor(limit = 3) {
    this.limit = limit;
    this.cache = new Map();
  }

  get(key) {
    if (!this.cache.has(key)) return -1;

    const value = this.cache.get(key);

    // refresh key (mark as recently used)
    this.cache.delete(key);
    this.cache.set(key, value);

    return value;
  }

  set(key, value) {
    if (this.cache.has(key)) {
      this.cache.delete(key);
    }

    this.cache.set(key, value);

    // evict least recently used
    if (this.cache.size > this.limit) {
      const oldestKey = this.cache.keys().next().value;
      this.cache.delete(oldestKey);
    }
  }
}
```

---

## Usage:

```js id="x9m2qa"
const cache = new LRUCache(3);

cache.set("a", 1);
cache.set("b", 2);
cache.set("c", 3);

cache.get("a"); // refreshes "a"

cache.set("d", 4); // removes "b"
```

---

# 🧠 Why Map Works Well

In modern JS:

- `Map` preserves insertion order
- `delete + set` updates recency

So we can simulate LRU behavior efficiently.

---

# ⚡ Option 3: LRU Cache in Express API

Example caching database results:

```js id="p4q8xv"
const LRUCache = require("lru-cache");

const cache = new LRUCache({ max: 100 });

app.get("/user/:id", async (req, res) => {
  const id = req.params.id;

  // 1. Check cache
  if (cache.has(id)) {
    return res.json({
      source: "cache",
      data: cache.get(id),
    });
  }

  // 2. Simulate DB call
  const user = await db.findUser(id);

  // 3. Store in cache
  cache.set(id, user);

  res.json({
    source: "db",
    data: user,
  });
});
```

---

# 📊 Cache Flow

```txt id="z1k9qp"
Request
  ↓
Check LRU Cache
  ↓ yes
Return cached response

  ↓ no
Fetch DB
  ↓
Store in cache
  ↓
Return response
```

---

# ⏱️ TTL (Time-Based Expiration)

LRU alone is not enough; add TTL:

```js id="v7k1mz"
const cache = new LRUCache({
  max: 100,
  ttl: 1000 * 60, // 1 minute
});
```

### Why TTL matters:

- Prevents stale data
- Automatically refreshes old entries

---

# ⚖️ LRU vs Other Caching Strategies

| Strategy | Behavior                      | Use Case             |
| -------- | ----------------------------- | -------------------- |
| LRU      | Removes least recently used   | General API caching  |
| FIFO     | Removes oldest inserted       | Queue-like systems   |
| LFU      | Removes least frequently used | Long-term hot data   |
| TTL      | Removes based on time         | Expiry-based caching |

---

# 🚨 Common Pitfalls

## ❌ Using unlimited cache

```js
const cache = new Map();
```

Risk:

- Memory leaks
- Process crash

---

## ❌ Caching everything

Not all data should be cached:

- Sensitive data ❌
- Frequently changing data ❌

---

## ❌ No invalidation strategy

Bad:

```txt id="a2m8qz"
DB updated → cache still stale
```

---

## ❌ Using in-memory cache in distributed systems

Problem:

```txt id="v8k2xm"
Server A cache ≠ Server B cache
```

---

# 🌐 Production Alternative: Redis LRU-like caching

In distributed systems, use:

Redis

Redis supports:

- TTL
- Eviction policies (LRU, LFU)
- Shared cache across servers

---

# 🚀 Best Practices

## 1. Set max size

```js
max: 1000;
```

Prevents memory overload.

---

## 2. Use TTL + LRU together

Best combination:

```js
ttl + max;
```

---

## 3. Cache only expensive operations

Good:

- DB queries
- API responses
- Computed results

Bad:

- Small static values
- Highly dynamic data

---

## 4. Monitor cache hit ratio

```txt id="k9x2ab"
cache hits / total requests
```

---

# 🧠 Mental Model

Think of LRU cache like:

```txt id="m3x8qp"
Small desk:
- Frequently used papers stay on top
- Old papers fall off automatically
```

---

# 🎯 Interview Summary

> LRU cache in Node.js is a caching mechanism that stores a limited number of items and evicts the least recently used entry when the capacity is exceeded. It is commonly implemented using a Map or dedicated libraries like lru-cache. LRU improves performance by reducing database and computation load for frequently accessed data. In production systems, it is often combined with TTL for expiration and replaced or complemented by distributed caching systems like Redis to handle multi-instance environments.

## Question 11. Difference between callback-based APIs and promise-based APIs in Node.js

## ✅ Short Answer

In Node.js:

- **Callback-based APIs** use functions passed as arguments and follow an error-first pattern.
- **Promise-based APIs** return a Promise that represents the eventual completion (or failure) of an async operation.

👉 Key difference:

> Callbacks are function-based and harder to compose; Promises are object-based and easier to chain, handle errors, and integrate with `async/await`.

---

# 🧠 Callback-Based APIs

Callback APIs were the original async pattern in Node.js.

### Example:

```js id="c1a9xq"
const fs = require("fs");

fs.readFile("file.txt", "utf8", (err, data) => {
  if (err) {
    console.error(err);
    return;
  }

  console.log(data);
});
```

---

## 🔑 Key Characteristics

### 1. Error-first convention

```js
(err, result) => {};
```

- First argument → error
- Second → result

---

### 2. Inversion of control

You pass control to the function:

```txt id="k9m2wp"
You → give callback → Node API controls execution
```

---

### 3. Nested callbacks (callback hell)

```js id="d8x1qp"
fs.readFile("a.txt", (err, a) => {
  fs.readFile("b.txt", (err, b) => {
    fs.readFile("c.txt", (err, c) => {
      console.log(a, b, c);
    });
  });
});
```

---

## ❌ Problems with Callbacks

### 1. Callback hell

Hard to read and maintain.

---

### 2. Poor error handling

Each level must handle errors manually.

---

### 3. No composability

Hard to combine multiple async operations.

---

### 4. Difficult flow control

Parallel vs sequential execution is messy.

---

# 🧠 Promise-Based APIs

Promises represent a value that will be available in the future.

Example:

```js id="p3x7qv"
const fs = require("fs/promises");

fs.readFile("file.txt", "utf8")
  .then((data) => {
    console.log(data);
  })
  .catch((err) => {
    console.error(err);
  });
```

---

## 🔑 Key Characteristics

### 1. Return value instead of callback

```js
function readFile() {
  return Promise;
}
```

---

### 2. Chainable flow

```js id="m2k9xp"
doA().then(doB).then(doC).catch(handleError);
```

---

### 3. Centralized error handling

One `.catch()` handles everything.

---

### 4. Better readability

Linear flow instead of nested callbacks.

---

# ⚡ async/await (built on Promises)

Modern syntax over Promises:

```js id="a8q1wd"
const fs = require("fs/promises");

async function read() {
  try {
    const data = await fs.readFile("file.txt", "utf8");
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

---

# 📊 Callback vs Promise Comparison

| Feature        | Callback-based     | Promise-based          |
| -------------- | ------------------ | ---------------------- |
| Style          | Function passed in | Object returned        |
| Readability    | Poor (nested)      | Good (linear)          |
| Error handling | Manual per level   | Centralized `.catch()` |
| Composition    | Hard               | Easy                   |
| Async control  | Manual             | Built-in chaining      |
| Debugging      | Difficult          | Easier                 |
| Modern usage   | Legacy             | Standard               |

---

# 🧠 Execution Model Difference

## Callback

```txt id="x9k2qp"
Function A → executes → calls callback → continues
```

Control is inverted.

---

## Promise

```txt id="z1m8qv"
Function A returns Promise
   ↓
.then() decides next step
```

Control is retained.

---

# ⚙️ Under the Hood Difference

### Callback-based APIs

- Node APIs directly invoke your function
- You are notified when done

---

### Promise-based APIs

Promises are:

- Objects with internal state:
  - pending
  - fulfilled
  - rejected

They schedule handlers via microtask queue.

---

# 🚨 Important Interview Insight

Promises use:

```txt id="q7x1mp"
Microtask Queue (higher priority than setTimeout)
```

So:

```js id="b2k9qv"
setTimeout(() => console.log("timeout"), 0);

Promise.resolve().then(() => console.log("promise"));
```

Output:

```txt id="n4x7qp"
promise
timeout
```

---

# 🔥 Example: Multiple Async Tasks

## ❌ Callback style

```js id="c9x7wp"
getUser(id, (err, user) => {
  getOrders(user.id, (err, orders) => {
    getPayments(orders, (err, payments) => {
      console.log(payments);
    });
  });
});
```

---

## ✅ Promise style

```js id="p7m2qp"
getUser(id)
  .then((user) => getOrders(user.id))
  .then((orders) => getPayments(orders))
  .then((payments) => {
    console.log(payments);
  })
  .catch(console.error);
```

---

## ✅ async/await style

```js id="a9x3qp"
async function run(id) {
  try {
    const user = await getUser(id);
    const orders = await getOrders(user.id);
    const payments = await getPayments(orders);

    console.log(payments);
  } catch (err) {
    console.error(err);
  }
}
```

---

# 🧠 Common Pitfalls

## ❌ Mixing callbacks and promises

```js id="k1x8qp"
fs.readFile("file", (err, data) => {
  return Promise.resolve(data);
});
```

Leads to confusion.

---

## ❌ Not handling Promise rejections

```js id="m8q1xp"
fetchData().then((data) => {
  console.log(data);
});
```

Missing `.catch()` → silent failures.

---

## ❌ Callback hell in modern code

Still seen in legacy systems.

---

# ⚖️ When to Use What

## Callbacks (rare today)

- Legacy Node APIs
- Low-level libraries
- Event emitters

---

## Promises (modern standard)

- APIs
- DB calls
- HTTP requests

---

## async/await (preferred)

- Cleanest syntax
- Production standard

---

# 🧠 Mental Model

Think of it like:

```txt id="x3k9qp"
Callback → "I'll call you later"
Promise → "I will give you a result object later"
async/await → "I will wait for the result"
```

---

# 🎯 Interview Summary

> Callback-based APIs in Node.js use functions passed as arguments and follow an error-first pattern, but they often lead to nested code and poor composability. Promise-based APIs return a Promise object representing the eventual result of an async operation, allowing chaining, centralized error handling, and better readability. Promises also integrate with the microtask queue and support async/await syntax, making them the modern standard for handling asynchronous operations in Node.js applications.

## Question 12. How to handle uncaught exceptions gracefully

## Question 13. How to implement JWT authentication in Node.js

## Question 14. How to implement role-based access control in Node.js

## Question 15. How to implement API versioning in Node.js applications

## Question 16. How to handle database connection pooling efficiently

## Question 17. Difference between synchronous and asynchronous file operations

## Question 18. How to stream logs in Node.js

## Question 19. How to implement WebSocket server and handle concurrent clients

## Question 20. How to implement server-sent events (SSE) in Node.js
