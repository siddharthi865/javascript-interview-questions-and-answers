# Set 15

| S.No. | Question                                                                                                                                                          |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [Difference between Proxy and Reflect API](#question-1-difference-between-proxy-and-reflect-api)                                                                  |
| 2.    | [How to create a revocable Proxy](#question-2-how-to-create-a-revocable-proxy)                                                                                    |
| 3.    | [How to validate object property access using Proxy](#question-3-how-to-validate-object-property-access-using-proxy)                                              |
| 4.    | [Difference between WeakMap and Map in garbage collection](#question-4-difference-between-weakmap-and-map-in-garbage-collection)                                  |
| 5.    | [How to implement memoization for expensive function calls](#question-5-how-to-implement-memoization-for-expensive-function-calls)                                |
| 6.    | [Difference between synchronous and asynchronous iterables](#question-6-difference-between-synchronous-and-asynchronous-iterables)                                |
| 7.    | [How to use Symbol.iterator to make an object iterable](#question-7-how-to-use-symboliterator-to-make-an-object-iterable)                                         |
| 8.    | [How to implement a reactive object (like Vue's reactivity system)](#question-8-how-to-implement-a-reactive-object-like-vues-reactivity-system)                   |
| 9.    | [How to implement a simple pub/sub system in JavaScript](#question-9-how-to-implement-a-simple-pubsub-system-in-javascript)                                       |
| 10.   | [Difference between mutable and immutable operations on arrays/objects](#question-10-difference-between-mutable-and-immutable-operations-on-arraysobjects)        |
| 11.   | [Difference between deep equality and shallow equality](#question-11-difference-between-deep-equality-and-shallow-equality)                                       |
| 12.   | [How to avoid race conditions in async JavaScript](#question-12-how-to-avoid-race-conditions-in-async-javascript)                                                 |
| 13.   | [Difference between Web Workers, Service Workers, and Shared Workers](#question-13-difference-between-web-workers-service-workers-and-shared-workers)             |
| 14.   | [How to use Transferable objects with Web Workers](#question-14-how-to-use-transferable-objects-with-web-workers)                                                 |
| 15.   | [How to implement a queue with asynchronous tasks](#question-15-how-to-implement-a-queue-with-asynchronous-tasks)                                                 |
| 16.   | [Difference between synchronous and asynchronous script loading](#question-16-difference-between-synchronous-and-asynchronous-script-loading)                     |
| 17.   | [How to handle large datasets efficiently in JavaScript](#question-17-how-to-handle-large-datasets-efficiently-in-javascript)                                     |
| 18.   | [Difference between `requestAnimationFrame` and `setTimeout` for animations](#question-18-difference-between-requestanimationframe-and-settimeout-for-animations) |
| 19.   | [How to implement a simple scheduler for throttling async requests](#question-19-how-to-implement-a-simple-scheduler-for-throttling-async-requests)               |
| 20.   | [How to optimize performance for a large number of DOM updates](#question-20-how-to-optimize-performance-for-a-large-number-of-dom-updates)                       |

## Question 1. Difference between Proxy and Reflect API

`Proxy` and `Reflect` are related but serve different purposes in JavaScript:

- **Proxy** lets you intercept and customize operations on objects (like property access, assignment, function calls, etc.).
- **Reflect** provides standard built-in methods for performing those same object operations programmatically.

They are often used together:

- `Proxy` intercepts the operation.
- `Reflect` performs the default behavior safely and consistently.

### Detailed Explanation

#### 1. What is a Proxy?

A `Proxy` wraps an object and allows you to define custom behavior for fundamental operations.

##### Syntax

```js
const proxy = new Proxy(target, handler);
```

- `target` → original object
- `handler` → object containing traps (interceptors)

##### Example: Intercept Property Access

```js
const user = {
  name: "John",
};

const proxyUser = new Proxy(user, {
  get(target, property) {
    console.log(`Accessing ${property}`);
    return target[property];
  },
});

console.log(proxyUser.name);
```

##### Output

```js
Accessing name
John
```

The `get` trap intercepts property reads.

#### 2. What is Reflect?

`Reflect` is a built-in object containing methods that mirror JavaScript internal object operations.

It provides cleaner and more reliable alternatives to operators like:

- `obj[prop]`
- `delete obj[prop]`
- `Object.defineProperty()`
- `new`

##### Example

```js
const user = {
  name: "John",
};

console.log(Reflect.get(user, "name"));
```

##### Output

```js
John;
```

#### 3. Why Proxy and Reflect Are Used Together

Inside Proxy traps, using `Reflect` helps preserve default behavior.

##### Example Without Reflect

```js
const proxy = new Proxy(user, {
  get(target, prop) {
    return target[prop];
  },
});
```

This works, but can break behavior in edge cases involving:

- inheritance
- getters/setters
- `this` binding

##### Better Version Using Reflect

```js
const proxy = new Proxy(user, {
  get(target, prop, receiver) {
    return Reflect.get(target, prop, receiver);
  },
});
```

This forwards the operation exactly as JavaScript normally would.

#### 4. Key Differences

| Feature            | Proxy                           | Reflect                          |
| ------------------ | ------------------------------- | -------------------------------- |
| Purpose            | Intercept operations            | Perform operations               |
| Type               | Constructor                     | Built-in object                  |
| Used for           | Custom behavior                 | Standardized object manipulation |
| Can intercept?     | Yes                             | No                               |
| Modifies behavior? | Yes                             | No                               |
| Common usage       | Validation, logging, reactivity | Safe forwarding/default behavior |

#### 5. Common Proxy Traps and Matching Reflect Methods

| Proxy Trap         | Reflect Equivalent         |
| ------------------ | -------------------------- |
| `get()`            | `Reflect.get()`            |
| `set()`            | `Reflect.set()`            |
| `deleteProperty()` | `Reflect.deleteProperty()` |
| `has()`            | `Reflect.has()`            |
| `ownKeys()`        | `Reflect.ownKeys()`        |
| `apply()`          | `Reflect.apply()`          |
| `construct()`      | `Reflect.construct()`      |

#### 6. Real Example

##### Validation Using Proxy + Reflect

```js
const user = {};

const proxyUser = new Proxy(user, {
  set(target, prop, value) {
    if (prop === "age" && value < 0) {
      throw new Error("Age cannot be negative");
    }

    return Reflect.set(target, prop, value);
  },
});

proxyUser.age = 25;

console.log(proxyUser.age);
```

##### Output

```js
25;
```

##### Invalid Value

```js
proxyUser.age = -5;
```

##### Output

```js
Error: Age cannot be negative
```

#### 7. Important Interview Concepts

##### Proxy Changes Object Behavior

A Proxy can:

- validate data
- log access
- create reactive systems
- implement access control
- create virtual properties

Frameworks like:

- Vue.js
- MobX

use Proxies internally for reactivity.

##### Reflect Does NOT Intercept

`Reflect` simply performs operations.

Think of it as:

> “Functional versions of object operations.”

Example:

```js
Reflect.deleteProperty(obj, "name");
```

instead of:

```js
delete obj.name;
```

#### 8. Why Reflect is Better Than Direct Operations

Reflect methods:

- return predictable boolean values
- work better inside proxies
- avoid some syntax inconsistencies

Example:

```js
const success = Reflect.set(obj, "x", 10);

console.log(success);
```

Returns:

- `true`
- `false`

instead of silently failing sometimes.

#### 9. Common Pitfalls

##### Infinite Recursion

Wrong:

```js
const proxy = new Proxy(obj, {
  get(target, prop) {
    return proxy[prop];
  },
});
```

This recursively calls the trap forever.

Correct:

```js
return Reflect.get(target, prop);
```

##### Forgetting Return in `set`

The `set` trap should return `true`.

```js
set(target, prop, value) {
  target[prop] = value;
  return true;
}
```

Better:

```js
return Reflect.set(target, prop, value);
```

#### 10. Advanced Example: Default Values

```js
const settings = {};

const proxy = new Proxy(settings, {
  get(target, prop) {
    return prop in target ? Reflect.get(target, prop) : "Default Value";
  },
});

console.log(proxy.theme);
```

##### Output

```js
Default Value
```

#### 11. Best Practices

##### Use Proxy When

- intercepting object behavior
- implementing validation
- creating reactive systems
- logging/debugging

##### Use Reflect When

- forwarding default behavior in Proxy traps
- performing safe object operations
- avoiding low-level operator inconsistencies

#### Summary

A strong interview answer would be:

> “Proxy is used to intercept and customize operations on objects, while Reflect provides built-in methods to perform those operations programmatically. They are commonly used together because Proxy traps can delegate default behavior to Reflect methods, ensuring consistent behavior with JavaScript’s internal object mechanics.”

## Question 2. How to create a revocable Proxy

## Concise Answer

You create a **revocable Proxy** using:

```js
Proxy.revocable(target, handler);
```

It returns an object containing:

- `proxy` → the Proxy instance
- `revoke` → a function that disables the proxy permanently

Once revoked, any operation on the proxy throws a **TypeError**.

---

# Detailed Explanation (Interview-Friendly)

## 1. What is a Revocable Proxy?

A **revocable Proxy** is a special form of `Proxy` that can be **turned off at runtime**.

Unlike a normal Proxy:

- You can explicitly destroy its access
- After revocation, it becomes unusable

This is useful for:

- security controls
- temporary access
- sandboxing
- resource cleanup

---

## 2. Syntax

```js
const { proxy, revoke } = Proxy.revocable(target, handler);
```

- `proxy` → behaves like a normal Proxy
- `revoke()` → disables it permanently

---

## 3. Basic Example

```js
const user = {
  name: "Alice",
};

const { proxy, revoke } = Proxy.revocable(user, {
  get(target, prop) {
    return target[prop];
  },
});

console.log(proxy.name); // Alice

revoke();

console.log(proxy.name); // TypeError
```

---

## 4. What Happens After Revocation?

Once `revoke()` is called:

- Any `get`, `set`, or operation on proxy throws error

```js
TypeError: Cannot perform 'get' on a proxy that has been revoked
```

---

## 5. Internal Behavior Concept

Think of it like:

- Proxy = controlled gateway to object
- revoke() = closing the gateway permanently

After revocation, the internal target binding is invalidated.

---

## 6. Practical Use Case: Temporary Access Control

### Example: Secure API access

```js
function createSecureObject(data) {
  const { proxy, revoke } = Proxy.revocable(data, {
    get(target, prop) {
      if (prop.startsWith("_")) {
        throw new Error("Access denied");
      }
      return target[prop];
    },
  });

  return { proxy, revoke };
}

const { proxy, revoke } = createSecureObject({
  name: "John",
  _secret: "12345",
});

console.log(proxy.name); // John
// console.log(proxy._secret); // Error

revoke();
```

---

## 7. Real-World Use Cases

### 1. Sandboxing

Used in environments like:

- plugin systems
- iframe-like isolation
- untrusted code execution

### 2. Resource Cleanup

Prevent memory leaks by disabling references:

```js
let { proxy, revoke } = Proxy.revocable(obj, handler);

// later
revoke();
```

### 3. Temporary Permissions

Give time-limited access to an object.

---

## 8. Key Differences: Proxy vs Revocable Proxy

| Feature          | Proxy         | Revocable Proxy     |
| ---------------- | ------------- | ------------------- |
| Can be disabled  | ❌ No         | ✅ Yes              |
| Created using    | `new Proxy()` | `Proxy.revocable()` |
| Lifetime control | Permanent     | Temporary           |
| Security use     | Medium        | High                |

---

## 9. Important Pitfalls

### ❌ Forgetting to store revoke function

```js
const { proxy } = Proxy.revocable(obj, handler);
// no way to revoke later
```

---

### ❌ Using proxy after revoke

```js
revoke();
proxy.name; // Runtime TypeError
```

Always ensure lifecycle control.

---

## 10. Advanced Insight (Interview Gold)

Internally:

- `Proxy.revocable()` creates a Proxy with an internal “revocation slot”
- After `revoke()`:
  - internal target reference is invalidated
  - all traps immediately fail

This is enforced by the JS engine (not user-land logic).

---

## 11. Best Practice Pattern

```js
function createRevocableUser(user) {
  const { proxy, revoke } = Proxy.revocable(user, {
    get(target, prop, receiver) {
      return Reflect.get(target, prop, receiver);
    },
  });

  return { userProxy: proxy, revoke };
}
```

---

## 12. Interview Summary

A strong answer:

> “A revocable Proxy is created using `Proxy.revocable()`, which returns a proxy and a revoke function. Once revoke is called, the proxy becomes permanently unusable and any access throws a TypeError. It is useful for temporary access control, sandboxing, and secure resource management.”

## Question 3. How to validate object property access using Proxy

## Concise Answer

You validate object property access using a `Proxy` by intercepting the **`get`** (and often `set`) traps and applying rules before allowing access or modification. If validation fails, you can **throw an error**, **return a default value**, or **block the operation**.

---

# Detailed Explanation (Interview-Ready)

## 1. Core Idea

A `Proxy` lets you intercept property access like:

- reading (`get`)
- writing (`set`)
- checking existence (`has`)
- deleting (`deleteProperty`)

For validation, the most important traps are:

- `get` → validate read access
- `set` → validate writes

---

# 2. Basic Validation Using `get`

### Example: Restrict access to sensitive properties

```js id="v1g3z8"
const user = {
  name: "Alice",
  password: "12345",
};

const proxy = new Proxy(user, {
  get(target, prop) {
    if (prop === "password") {
      throw new Error("Access denied to password");
    }
    return target[prop];
  },
});

console.log(proxy.name);
console.log(proxy.password); // Error
```

---

### Output

```
Alice
Error: Access denied to password
```

---

## 3. Validation Using `set` (Most Common in Real Apps)

### Example: Type + business rule validation

```js id="k8x2q1"
const user = {};

const proxy = new Proxy(user, {
  set(target, prop, value) {
    if (prop === "age") {
      if (typeof value !== "number") {
        throw new TypeError("Age must be a number");
      }
      if (value < 0 || value > 120) {
        throw new RangeError("Invalid age range");
      }
    }

    target[prop] = value;
    return true;
  },
});

proxy.age = 25; // OK
proxy.age = -5; // Error
```

---

## 4. Best Practice: Use `Reflect`

Instead of directly mutating the object, use `Reflect` to preserve native behavior:

```js id="y3qv9m"
const proxy = new Proxy(user, {
  set(target, prop, value, receiver) {
    if (prop === "age" && value < 0) {
      throw new Error("Invalid age");
    }

    return Reflect.set(target, prop, value, receiver);
  },
});
```

### Why this is better:

- handles prototype chain correctly
- respects getters/setters
- consistent with JS internal semantics

---

## 5. Advanced Validation: Whitelisting Properties

### Only allow predefined keys

```js id="w7r0p2"
const allowedKeys = ["name", "age"];

const user = {};

const proxy = new Proxy(user, {
  set(target, prop, value) {
    if (!allowedKeys.includes(prop)) {
      throw new Error(`Property "${prop}" is not allowed`);
    }

    return Reflect.set(target, prop, value);
  },
});

proxy.name = "John"; // OK
proxy.age = 30; // OK
proxy.password = "x"; // Error
```

---

## 6. Validation on Property Existence (`has` trap)

Used when you want to control `in` operator:

```js id="c9p2k4"
const user = { name: "Alice" };

const proxy = new Proxy(user, {
  has(target, prop) {
    if (prop === "password") return false;
    return prop in target;
  },
});

console.log("name" in proxy); // true
console.log("password" in proxy); // false
```

---

## 7. Read + Write Protection Together (Real-world pattern)

```js id="t6m8z1"
function createSecureObject(obj) {
  return new Proxy(obj, {
    get(target, prop) {
      if (prop.startsWith("_")) {
        throw new Error("Private property access denied");
      }
      return Reflect.get(target, prop);
    },

    set(target, prop, value) {
      if (prop.startsWith("_")) {
        throw new Error("Cannot modify private property");
      }
      return Reflect.set(target, prop, value);
    },
  });
}

const user = createSecureObject({
  name: "Alice",
  _secret: "hidden",
});

console.log(user.name); // Alice
// user._secret ❌ error
```

---

## 8. Common Pitfalls (Interview Favorites)

### ❌ Forgetting return in `set`

```js
set(target, prop, value) {
  target[prop] = value;
  // missing return true ❌
}
```

👉 Can break strict mode behavior.

---

### ❌ Using Proxy for heavy validation everywhere

Proxies are powerful but:

- add runtime overhead
- harder to debug
- can break performance-sensitive loops

---

### ❌ Not using `Reflect`

Without `Reflect`, you may break:

- prototype chain
- setters/getters
- inheritance behavior

---

## 9. When to Use Proxy Validation

### Good use cases:

- form validation (React/Vue state)
- API response sanitization
- secure object wrappers
- logging/debugging access
- enforcing schema rules dynamically

---

## 10. Interview Summary (Strong Answer)

> “We validate object property access using a Proxy by intercepting `get` and `set` traps. In the `get` trap, we can restrict or transform property reads, and in the `set` trap, we enforce rules like type checking, whitelisting, or range validation. Using `Reflect` ensures default JavaScript behavior is preserved, especially with inheritance and property descriptors.”

## Question 4. Difference between WeakMap and Map in garbage collection

## Concise Answer

The key difference is:

- **`Map` holds strong references to its keys**, so keys cannot be garbage-collected while they remain in the map.
- **`WeakMap` holds weak references to its keys**, so if there are no other references to a key object, it can be garbage-collected automatically, and its associated entry is removed.

This makes `WeakMap` useful for storing metadata, private data, caches, and DOM-related information without causing memory leaks.

---

# Interview-Friendly Explanation

## How Garbage Collection Works

JavaScript's garbage collector frees memory occupied by objects that are no longer reachable from the application.

Example:

```js
let user = {
  name: "John",
};

user = null;
```

Since nothing references the original object anymore, it becomes eligible for garbage collection.

---

# Map and Garbage Collection

A `Map` maintains a **strong reference** to its keys.

```js
let user = {
  name: "John",
};

const map = new Map();

map.set(user, "Some metadata");

user = null;
```

Many developers assume the object can now be collected.

### Reality

The object is still referenced by the `Map`:

```js
map
  ↓
{ {name:"John"} => "Some metadata" }
```

Therefore:

- Object remains in memory
- Entry remains in the map
- Garbage collector cannot remove it

This can cause memory leaks if entries aren't manually removed.

---

# WeakMap and Garbage Collection

A `WeakMap` stores keys weakly.

```js
let user = {
  name: "John",
};

const weakMap = new WeakMap();

weakMap.set(user, "Some metadata");

user = null;
```

Now:

- No strong references to the object exist
- Object becomes eligible for garbage collection
- WeakMap entry disappears automatically

Conceptually:

```js
WeakMap
  ↓
weak reference
```

The WeakMap does not keep the object alive.

---

# Visual Comparison

## Map

```text
Map
 ↓
Object Key
```

Strong reference:

```text
Map ─────► Object
```

Object cannot be collected.

---

## WeakMap

```text
WeakMap
 ~~~► Object
```

Weak reference:

```text
WeakMap ~~~► Object
```

If no other references exist:

```text
Object removed by GC
Entry removed automatically
```

---

# Why WeakMap Keys Must Be Objects

This is a common interview question.

Valid:

```js
const wm = new WeakMap();

const obj = {};

wm.set(obj, "data");
```

Invalid:

```js
wm.set("name", "John");
```

Output:

```js
TypeError;
```

### Why?

Primitive values:

- strings
- numbers
- booleans
- symbols (non-registered)

cannot be garbage-collected in the same way as objects.

Weak references only make sense for objects.

---

# Why WeakMap Has No Size Property

Another popular interview question.

`Map`:

```js
console.log(map.size);
```

Works.

---

`WeakMap`:

```js
console.log(weakMap.size);
```

Undefined.

### Why?

Garbage collection is non-deterministic.

The engine may remove entries at any time.

Therefore JavaScript cannot reliably tell you:

- current size
- keys
- values
- entries

---

# Why WeakMap Is Not Iterable

`Map`:

```js
for (const [key, value] of map) {
  console.log(key, value);
}
```

Works.

---

`WeakMap`:

```js
for (const item of weakMap) {
}
```

Throws.

### Reason

Imagine:

```js
for (...) {
   // GC runs here
}
```

Entries could disappear during iteration.

To avoid inconsistent behavior:

- no iteration
- no keys()
- no values()
- no entries()
- no size

---

# Real-World Example: DOM Metadata

Memory leak with Map:

```js
const map = new Map();

let button = document.querySelector("button");

map.set(button, {
  clicks: 0,
});

button = null;
```

The DOM element remains in memory because `Map` still references it.

---

Better:

```js
const weakMap = new WeakMap();

let button = document.querySelector("button");

weakMap.set(button, {
  clicks: 0,
});

button = null;
```

When the DOM node is removed and no other references exist:

- node can be garbage-collected
- metadata disappears automatically

---

# Private Data Pattern (Before `#private` Fields)

```js
const privateData = new WeakMap();

class User {
  constructor(name) {
    privateData.set(this, {
      name,
    });
  }

  getName() {
    return privateData.get(this).name;
  }
}
```

Benefits:

- data hidden from outside
- automatically cleaned when instance is collected

---

# Feature Comparison

| Feature                | Map       | WeakMap      |
| ---------------------- | --------- | ------------ |
| Key type               | Any value | Objects only |
| Strong references      | ✅ Yes    | ❌ No        |
| Weak references        | ❌ No     | ✅ Yes       |
| Prevents GC            | ✅ Yes    | ❌ No        |
| Iterable               | ✅ Yes    | ❌ No        |
| size property          | ✅ Yes    | ❌ No        |
| keys()                 | ✅ Yes    | ❌ No        |
| values()               | ✅ Yes    | ❌ No        |
| entries()              | ✅ Yes    | ❌ No        |
| Good for caches        | Limited   | Excellent    |
| Memory leak prevention | ❌ No     | ✅ Yes       |

---

# Common Interview Questions

### Q: Can you force garbage collection to test WeakMap?

No.

JavaScript engines decide when GC runs.

You cannot rely on immediate cleanup.

---

### Q: Can WeakMap keys be strings?

No.

```js
weakMap.set("name", "John");
```

Throws a `TypeError`.

Only objects are allowed as keys.

---

### Q: When should you choose WeakMap?

Use `WeakMap` when:

- Keys are objects
- You don't want the map to keep objects alive
- Building caches
- Storing metadata
- Associating data with DOM nodes
- Implementing private object state

---

# Interview Summary

> `Map` keeps strong references to keys, so objects stored as keys remain in memory until explicitly removed. `WeakMap` keeps weak references to object keys, allowing them to be garbage-collected when no other references exist. This makes `WeakMap` ideal for metadata, caches, and preventing memory leaks, but it cannot be iterated and only accepts object keys.

## Question 5. How to implement memoization for expensive function calls

## Concise Answer

**Memoization** is an optimization technique where you cache the result of an expensive function call and return the cached result when the same inputs occur again.

In JavaScript, memoization is commonly implemented using a `Map`, `WeakMap`, or a closure-based cache.

```js
function memoize(fn) {
  const cache = new Map();

  return function (...args) {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      return cache.get(key);
    }

    const result = fn.apply(this, args);
    cache.set(key, result);

    return result;
  };
}
```

---

# What Problem Does Memoization Solve?

Consider an expensive computation:

```js
function slowSquare(n) {
  console.log("Computing...");
  return n * n;
}

console.log(slowSquare(5));
console.log(slowSquare(5));
```

Output:

```js
Computing...
25

Computing...
25
```

The same work is repeated unnecessarily.

Memoization avoids this.

---

# Basic Memoization Implementation

```js
function memoize(fn) {
  const cache = new Map();

  return function (...args) {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      console.log("Cache hit");
      return cache.get(key);
    }

    console.log("Computing");
    const result = fn.apply(this, args);

    cache.set(key, result);

    return result;
  };
}
```

Usage:

```js
function add(a, b) {
  return a + b;
}

const memoizedAdd = memoize(add);

console.log(memoizedAdd(2, 3));
console.log(memoizedAdd(2, 3));
```

Output:

```js
Computing
5

Cache hit
5
```

---

# Memoization and Closures

A common interview point:

```js
function memoize(fn) {
  const cache = new Map();

  return function (...args) {
    // cache survives because of closure
  };
}
```

The returned function closes over `cache`.

Even after `memoize()` finishes execution, the cache remains accessible.

This is a classic practical use of **closures**.

---

# Memoizing Recursive Functions

A famous interview example is Fibonacci.

### Without Memoization

```js
function fib(n) {
  if (n <= 1) return n;

  return fib(n - 1) + fib(n - 2);
}
```

Time Complexity:

```text
O(2^n)
```

Huge amount of repeated work.

---

### With Memoization

```js
function memoizedFib() {
  const cache = {};

  function fib(n) {
    if (n in cache) {
      return cache[n];
    }

    if (n <= 1) {
      return n;
    }

    cache[n] = fib(n - 1) + fib(n - 2);

    return cache[n];
  }

  return fib;
}

const fib = memoizedFib();

console.log(fib(40));
```

Complexity becomes:

```text
O(n)
```

instead of:

```text
O(2^n)
```

---

# Memoizing Async Functions

Very common in modern frontend applications.

```js
function memoizeAsync(fn) {
  const cache = new Map();

  return async function (...args) {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      return cache.get(key);
    }

    const promise = fn(...args);

    cache.set(key, promise);

    return promise;
  };
}
```

Example:

```js
const getUser = memoizeAsync(async (id) => {
  const response = await fetch(`/users/${id}`);
  return response.json();
});
```

Benefits:

- prevents duplicate API calls
- reuses in-flight requests
- reduces server load

---

# Using WeakMap for Object Arguments

If function arguments are objects:

```js
const cache = new WeakMap();
```

Example:

```js
function memoizeObject(fn) {
  const cache = new WeakMap();

  return function (obj) {
    if (cache.has(obj)) {
      return cache.get(obj);
    }

    const result = fn(obj);

    cache.set(obj, result);

    return result;
  };
}
```

---

## Why WeakMap?

```js
let user = { id: 1 };

memoizedFn(user);

user = null;
```

With `WeakMap`:

- object can be garbage collected
- cache entry disappears automatically

With `Map`:

- cache keeps object alive
- potential memory leak

---

# Common Pitfalls

## 1. JSON.stringify Limitations

```js
memoizedFn({ a: 1, b: 2 });
memoizedFn({ b: 2, a: 1 });
```

Different strings:

```js
'{"a":1,"b":2}';
'{"b":2,"a":1}';
```

Cache miss occurs.

---

## 2. Functions as Arguments

```js
JSON.stringify(() => {});
```

Produces:

```js
undefined;
```

Bad cache key.

---

## 3. Unbounded Cache Growth

```js
const cache = new Map();
```

Over time:

```text
1M entries
2M entries
5M entries
```

Memory usage grows continuously.

Solutions:

- LRU cache
- TTL expiration
- WeakMap for object keys

---

# Production-Grade Memoization

```js
function memoize(fn) {
  const cache = new Map();

  return function (...args) {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      return cache.get(key);
    }

    const result = fn.apply(this, args);

    cache.set(key, result);

    return result;
  };
}
```

Key features:

- supports multiple arguments
- preserves `this`
- uses closure-based cache

---

# Time Complexity

Without memoization:

```text
Repeated calls:
O(expensive computation)
```

With memoization:

```text
Cache hit:
O(1)
```

Map lookup is approximately constant time.

---

# Interview Follow-up: Memoization vs Caching

### Memoization

```text
Function-level optimization
Input → Output cache
Usually automatic
```

Example:

```js
memoizedFib(40);
```

---

### Caching

```text
Broader concept
Can cache:
- API responses
- Files
- Database queries
- Computations
```

Memoization is a specific type of caching.

---

# Interview Summary

> Memoization is a technique that caches function results based on input arguments. When the same inputs are provided again, the cached result is returned instead of recomputing. In JavaScript, it is typically implemented using closures and a `Map`, while `WeakMap` is preferred when object arguments are involved to avoid memory leaks. Memoization can dramatically improve performance for expensive computations and repeated API requests.

## Question 6. Difference between synchronous and asynchronous iterables

## Concise Answer

The difference is:

- **Synchronous iterables** produce values immediately and are consumed using `for...of`.
- **Asynchronous iterables** produce values over time (often involving Promises) and are consumed using `for await...of`.

JavaScript uses different protocols for each:

| Type                  | Protocol Method        |
| --------------------- | ---------------------- |
| Synchronous Iterable  | `Symbol.iterator`      |
| Asynchronous Iterable | `Symbol.asyncIterator` |

---

# Understanding Iterables

An iterable is any object that can provide values one at a time.

Examples:

```js
Array;
String;
Map;
Set;
Generator;
```

All implement the iterable protocol.

---

# 1. Synchronous Iterables

A synchronous iterable returns values immediately.

## Example

```js
const numbers = [1, 2, 3];

for (const num of numbers) {
  console.log(num);
}
```

Output:

```js
1;
2;
3;
```

---

## Internal Protocol

A synchronous iterable implements:

```js
Symbol.iterator;
```

Example:

```js
const obj = {
  *[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
  },
};

for (const value of obj) {
  console.log(value);
}
```

Output:

```js
1;
2;
3;
```

---

## What Happens Internally?

```js
const iterator = obj[Symbol.iterator]();

iterator.next();
iterator.next();
iterator.next();
```

Returns:

```js
{ value: 1, done: false }
{ value: 2, done: false }
{ value: 3, done: false }
```

Everything is available immediately.

---

# 2. Asynchronous Iterables

An asynchronous iterable can produce values later.

Useful for:

- API streams
- File streams
- Database cursors
- Web Streams
- Real-time data

---

## Protocol

Uses:

```js
Symbol.asyncIterator;
```

instead of:

```js
Symbol.iterator;
```

---

## Example

```js
const asyncNumbers = {
  async *[Symbol.asyncIterator]() {
    yield 1;
    yield 2;
    yield 3;
  },
};

(async () => {
  for await (const num of asyncNumbers) {
    console.log(num);
  }
})();
```

Output:

```js
1;
2;
3;
```

---

# Why `for await...of`?

Because each iteration may involve a Promise.

Consider:

```js
async function getData() {
  return 42;
}
```

You can't do:

```js
for (const value of asyncIterable)
```

because values may not exist yet.

Instead:

```js
for await (const value of asyncIterable)
```

which automatically awaits each value.

---

# Example with Delay

```js
const asyncCounter = {
  async *[Symbol.asyncIterator]() {
    for (let i = 1; i <= 3; i++) {
      await new Promise((resolve) => setTimeout(resolve, 1000));

      yield i;
    }
  },
};

(async () => {
  for await (const value of asyncCounter) {
    console.log(value);
  }
})();
```

Output:

```js
(after 1 sec) 1
(after 1 sec) 2
(after 1 sec) 3
```

---

# Generator vs Async Generator

## Synchronous Generator

```js
function* generator() {
  yield 1;
  yield 2;
}
```

Consumption:

```js
for (const x of generator()) {
  console.log(x);
}
```

---

## Async Generator

```js
async function* generator() {
  yield 1;
  yield 2;
}
```

Consumption:

```js
for await (const x of generator()) {
  console.log(x);
}
```

---

# Internal Difference

## Synchronous Iterator

```js
iterator.next();
```

Returns:

```js
{
  value: 1,
  done: false
}
```

---

## Async Iterator

```js
iterator.next();
```

Returns:

```js
Promise<{
  value: 1,
  done: false
}>
```

Notice:

```js
Promise;
```

is the key difference.

---

# Interview Trick Question

## What happens here?

```js
async function* gen() {
  yield 1;
}

for (const x of gen()) {
  console.log(x);
}
```

### Answer

```js
TypeError;
```

Because:

```js
gen();
```

returns an **async iterable**, not a synchronous iterable.

Must use:

```js
for await (const x of gen())
```

---

# Another Trick

```js
for await (const x of [1, 2, 3]) {
  console.log(x);
}
```

### Does it work?

✅ Yes.

Output:

```js
1;
2;
3;
```

Why?

`for await...of` can consume both:

- async iterables
- sync iterables

It automatically wraps sync values in resolved Promises.

---

# Real-World Use Cases

## Synchronous Iterables

When data is already available:

```js
const users = [ ... ];
const map = new Map();
const set = new Set();
```

---

## Asynchronous Iterables

When data arrives over time:

```js
API pagination
WebSocket messages
Node.js streams
Database cursors
File streaming
Large datasets
```

Example:

```js
for await (const chunk of readableStream) {
  process(chunk);
}
```

---

# Event Loop Connection

Async iterables integrate with:

- Promises
- async/await
- microtask queue

Each iteration can pause:

```js
await iterator.next();
```

allowing other tasks to run.

Synchronous iterables block until iteration finishes.

---

# Comparison Table

| Feature            | Synchronous Iterable | Asynchronous Iterable  |
| ------------------ | -------------------- | ---------------------- |
| Protocol           | `Symbol.iterator`    | `Symbol.asyncIterator` |
| Loop               | `for...of`           | `for await...of`       |
| Value availability | Immediate            | Future/async           |
| `next()` returns   | Object               | Promise                |
| Supports `await`   | No                   | Yes                    |
| Generator type     | `function*`          | `async function*`      |
| Common use         | Arrays, Sets, Maps   | Streams, APIs, cursors |

---

# Interview Summary

> A synchronous iterable implements `Symbol.iterator` and produces values immediately, making it consumable with `for...of`. An asynchronous iterable implements `Symbol.asyncIterator` and produces values asynchronously, often involving Promises, so it must be consumed with `for await...of`. Internally, a synchronous iterator's `next()` returns an object, while an async iterator's `next()` returns a Promise that resolves to that object. This makes async iterables ideal for streams, paginated APIs, and other data sources that arrive over time.

## Question 7. How to use Symbol.iterator to make an object iterable

## Concise Answer

To make a custom object iterable, implement the **`Symbol.iterator`** method. This method must return an **iterator object** that has a `next()` method returning:

```js
{
  value: ...,
  done: true | false
}
```

Once implemented, the object can be used with:

- `for...of`
- spread operator (`...`)
- `Array.from()`
- destructuring

---

# What Is `Symbol.iterator`?

JavaScript's iteration protocol is based on:

```js
Symbol.iterator;
```

When you write:

```js
for (const item of iterable) {
  console.log(item);
}
```

JavaScript internally does:

```js
const iterator = iterable[Symbol.iterator]();
```

and repeatedly calls:

```js
iterator.next();
```

---

# Example 1: Manual Iterator Implementation

```js
const range = {
  start: 1,
  end: 3,

  [Symbol.iterator]() {
    let current = this.start;
    let end = this.end;

    return {
      next() {
        if (current <= end) {
          return {
            value: current++,
            done: false,
          };
        }

        return {
          done: true,
        };
      },
    };
  },
};

for (const num of range) {
  console.log(num);
}
```

### Output

```js
1;
2;
3;
```

---

# How It Works Internally

```js
const iterator = range[Symbol.iterator]();

console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

Output:

```js
{ value: 1, done: false }
{ value: 2, done: false }
{ value: 3, done: false }
{ done: true }
```

---

# Example 2: Using a Generator (Recommended)

Most modern code uses generators because they automatically create iterators.

```js
const range = {
  start: 1,
  end: 3,

  *[Symbol.iterator]() {
    for (let i = this.start; i <= this.end; i++) {
      yield i;
    }
  },
};

for (const num of range) {
  console.log(num);
}
```

Output:

```js
1;
2;
3;
```

---

# Why Generators Are Better

Without generator:

```js
return {
  next() {
    ...
  }
};
```

With generator:

```js
yield value;
```

The JavaScript engine automatically:

- creates the iterator
- manages state
- handles `done`
- remembers execution position

---

# Example 3: Making a Custom Collection Iterable

```js
const team = {
  members: ["Alice", "Bob", "Charlie"],

  *[Symbol.iterator]() {
    for (const member of this.members) {
      yield member;
    }
  },
};

for (const member of team) {
  console.log(member);
}
```

Output:

```js
Alice;
Bob;
Charlie;
```

---

# Spread Operator Support

Once iterable:

```js
const range = {
  *[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
  },
};

console.log([...range]);
```

Output:

```js
[1, 2, 3];
```

Because spread internally uses:

```js
Symbol.iterator;
```

---

# Array.from Support

```js
const iterable = {
  *[Symbol.iterator]() {
    yield "A";
    yield "B";
    yield "C";
  },
};

console.log(Array.from(iterable));
```

Output:

```js
["A", "B", "C"];
```

---

# Destructuring Support

```js
const numbers = {
  *[Symbol.iterator]() {
    yield 10;
    yield 20;
    yield 30;
  },
};

const [a, b] = numbers;

console.log(a, b);
```

Output:

```js
10 20
```

Destructuring also relies on the iterable protocol.

---

# Interview Trick Question

## Why Doesn't This Work?

```js
const obj = {
  a: 1,
  b: 2,
};

for (const item of obj) {
  console.log(item);
}
```

Output:

```js
TypeError: obj is not iterable
```

### Why?

Plain objects do **not** implement:

```js
Symbol.iterator;
```

Only built-in iterables like:

- Arrays
- Strings
- Maps
- Sets

have it by default.

---

# Making a Plain Object Iterable

```js
const obj = {
  a: 1,
  b: 2,

  *[Symbol.iterator]() {
    for (const key of Object.keys(this)) {
      if (key !== Symbol.iterator) {
        yield [key, this[key]];
      }
    }
  },
};

for (const [key, value] of obj) {
  console.log(key, value);
}
```

Output:

```js
a 1
b 2
```

A cleaner version:

```js
const obj = {
  a: 1,
  b: 2,

  *[Symbol.iterator]() {
    yield* Object.entries(this);
  },
};
```

---

# Iterator vs Iterable

A very common interview question.

## Iterable

Has:

```js
Symbol.iterator;
```

Example:

```js
const arr = [1, 2, 3];
```

---

## Iterator

Has:

```js
next();
```

Example:

```js
const iterator = arr[Symbol.iterator]();
```

Then:

```js
iterator.next();
```

returns:

```js
{
  value: 1,
  done: false
}
```

---

# Advanced: Iterable Iterator

Generators return objects that are both:

- iterable
- iterator

```js
function* gen() {
  yield 1;
}
```

```js
const g = gen();
```

This is valid:

```js
g[Symbol.iterator]() === g;
```

Result:

```js
true;
```

---

# Common Pitfalls

### ❌ Returning an array instead of an iterator

Wrong:

```js
[Symbol.iterator]() {
  return [1,2,3];
}
```

Must return an iterator.

---

### ❌ Forgetting `done`

Wrong:

```js
next() {
  return { value: 1 };
}
```

Can cause infinite iteration.

Correct:

```js
{
  value,
  done: false
}
```

and eventually:

```js
{
  done: true;
}
```

---

### ❌ Using Arrow Functions Incorrectly

```js
[Symbol.iterator]: () => {
  console.log(this);
}
```

`this` won't refer to the object.

Prefer:

```js
[Symbol.iterator]() {
}
```

or a generator:

```js
*[Symbol.iterator]() {
}
```

---

# Best Practice

For modern JavaScript, use generators:

```js
const range = {
  start: 1,
  end: 5,

  *[Symbol.iterator]() {
    for (let i = this.start; i <= this.end; i++) {
      yield i;
    }
  },
};
```

It's concise, readable, and less error-prone.

---

# Interview Summary

> To make an object iterable, implement the `Symbol.iterator` method. This method must return an iterator with a `next()` method that yields `{ value, done }` objects. Once implemented, the object can be used with `for...of`, spread syntax, destructuring, and `Array.from()`. In modern JavaScript, generators (`*[Symbol.iterator](){}`) are the preferred way to create custom iterables because they automatically manage iterator state and completion.

## Question 8. How to implement a reactive object (like Vue's reactivity system)

## Concise Answer

You can implement a **basic reactive object (Vue-like reactivity)** in JavaScript using a **`Proxy`** that intercepts `get` and `set` operations, combined with a **dependency tracking system (effect functions + subscribers)**.

Core idea:

- `get` → collect dependencies (track who is using the value)
- `set` → trigger updates (re-run dependent functions)

---

# 1. Core Concept of Reactivity

A reactive system is built on:

### 🔹 Dependency Tracking

When a function reads a property → we “remember” it depends on that property.

### 🔹 Change Notification

When a property changes → we re-run all dependent functions.

---

# 2. Minimal Reactive System (Vue-like)

## Step 1: Dependency Map

We use:

- `WeakMap` → target object → property map
- `Map` → property → set of effects

```js id="rq2x7c"
const targetMap = new WeakMap();
```

---

## Step 2: Track Dependencies

```js id="p7k9sa"
let activeEffect = null;

function track(target, key) {
  if (!activeEffect) return;

  let depsMap = targetMap.get(target);
  if (!depsMap) {
    depsMap = new Map();
    targetMap.set(target, depsMap);
  }

  let dep = depsMap.get(key);
  if (!dep) {
    dep = new Set();
    depsMap.set(key, dep);
  }

  dep.add(activeEffect);
}
```

---

## Step 3: Trigger Updates

```js id="x1m8qv"
function trigger(target, key) {
  const depsMap = targetMap.get(target);
  if (!depsMap) return;

  const dep = depsMap.get(key);
  if (!dep) return;

  dep.forEach((effect) => effect());
}
```

---

## Step 4: Effect Runner

```js id="v9t3lm"
function effect(fn) {
  activeEffect = fn;
  fn(); // run once to collect dependencies
  activeEffect = null;
}
```

---

## Step 5: Reactive Proxy

```js id="c4n8zx"
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      track(target, key);
      return Reflect.get(target, key, receiver);
    },

    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      trigger(target, key);
      return result;
    },
  });
}
```

---

# 3. Example Usage

```js id="m2p7kd"
const state = reactive({ count: 0 });

effect(() => {
  console.log("Count changed:", state.count);
});

state.count = 1;
state.count = 2;
```

---

## Output

```
Count changed: 0
Count changed: 1
Count changed: 2
```

---

# 4. What Is Happening Internally?

### Step-by-step

### 1. Initial run

```js
effect(() => console.log(state.count));
```

- `activeEffect = fn`
- `get` is triggered → track dependency
- function runs once

---

### 2. Read access

```js
state.count;
```

Triggers:

```js
track(target, "count");
```

We store:

```
count → [effect]
```

---

### 3. Write access

```js
state.count = 1;
```

Triggers:

```js
trigger(target, "count");
```

Which re-runs all effects.

---

# 5. WeakMap Structure (Important Interview Concept)

Memory structure looks like:

```text
WeakMap (targetMap)
  └── state object
        └── Map (depsMap)
              └── "count"
                    └── Set (effects)
```

---

# 6. Why WeakMap is Important

We use:

```js
WeakMap;
```

because:

- If object is garbage collected → dependencies also disappear
- Prevents memory leaks
- Vue 3 uses same concept

---

# 7. Handling Multiple Effects

```js id="d7kq1z"
effect(() => console.log(state.count));
effect(() => console.log(state.count * 2));
```

Now dependency set:

```
count → [effect1, effect2]
```

Both re-run when `count` changes.

---

# 8. Avoid Infinite Loops (Important Pitfall)

Bad:

```js id="p1x8lm"
effect(() => {
  state.count++;
});
```

This causes infinite re-triggering.

### Why?

- `get` → track
- `set` → trigger
- triggers itself repeatedly

---

# 9. Optimized Version (Batching idea)

Real frameworks use:

- microtask queue
- scheduler
- batching updates

Simple idea:

```js id="z3q9np"
const queue = new Set();
let isFlushing = false;

function queueEffect(effect) {
  queue.add(effect);

  if (!isFlushing) {
    isFlushing = true;

    queueMicrotask(() => {
      queue.forEach((fn) => fn());
      queue.clear();
      isFlushing = false;
    });
  }
}
```

---

# 10. Real Vue-like Architecture

Vue 3 uses:

- `Proxy` → reactivity core
- `WeakMap` → dependency storage
- `effect()` → reactive runner
- scheduler → batching updates
- computed properties → cached effects

---

# 11. Advanced Enhancements

## Computed properties

```js id="x8m2qa"
function computed(getter) {
  let value;
  let dirty = true;

  const runner = effect(() => {
    value = getter();
  });

  return {
    get value() {
      if (dirty) {
        runner();
        dirty = false;
      }
      return value;
    },
  };
}
```

---

## Deep reactivity

Extend Proxy:

- nested objects → recursively wrap with `reactive()`

---

# 12. Common Interview Pitfalls

### ❌ Not tracking dependencies correctly

→ effects never re-run

---

### ❌ Using Map instead of WeakMap

→ memory leaks

---

### ❌ Missing cleanup

→ stale subscriptions accumulate

---

### ❌ Triggering same effect infinitely

→ recursion loop

---

# 13. One-Line Mental Model

> Reactivity = Proxy (detect access/change) + Dependency graph (track effects) + Scheduler (run updates efficiently)

---

# 14. Interview Summary

> A reactive system like Vue is built using a Proxy to intercept `get` and `set` operations. On `get`, dependencies (effect functions) are tracked using a WeakMap-based structure. On `set`, all dependent effects are triggered to re-run. This creates a dependency graph between state and side effects, enabling automatic UI updates. Advanced implementations also include batching, computed caching, and cleanup mechanisms for performance optimization.

## Question 9. How to implement a simple pub/sub system in JavaScript

## Concise Answer

A **Pub/Sub (Publish-Subscribe)** system is a messaging pattern where:

- **Publishers** emit events/messages.
- **Subscribers** listen for specific events.
- Neither knows about the other directly.

A simple implementation uses an object (or `Map`) to store event names and their subscribed callbacks.

```js
class EventBus {
  constructor() {
    this.events = {};
  }

  subscribe(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }

    this.events[event].push(callback);
  }

  publish(event, data) {
    if (!this.events[event]) return;

    this.events[event].forEach((callback) => {
      callback(data);
    });
  }
}
```

---

# What Problem Does Pub/Sub Solve?

Without Pub/Sub:

```js
function saveUser(user) {
  updateUI(user);
  sendAnalytics(user);
  notifyAdmin(user);
}
```

The function becomes tightly coupled to multiple systems.

With Pub/Sub:

```js
eventBus.publish("user:saved", user);
```

Any number of subscribers can react independently.

---

# Basic Implementation

## Step 1: Create Event Storage

```js
class EventBus {
  constructor() {
    this.events = {};
  }
}
```

Structure:

```js
{
  "user:login": [
    callback1,
    callback2
  ],

  "order:created": [
    callback3
  ]
}
```

---

## Step 2: Subscribe

```js
subscribe(event, callback) {
  if (!this.events[event]) {
    this.events[event] = [];
  }

  this.events[event].push(callback);
}
```

Usage:

```js
bus.subscribe("login", (user) => {
  console.log("Welcome", user);
});
```

---

## Step 3: Publish

```js
publish(event, data) {
  if (!this.events[event]) return;

  this.events[event].forEach(callback => {
    callback(data);
  });
}
```

Usage:

```js
bus.publish("login", "John");
```

Output:

```js
Welcome John
```

---

# Complete Example

```js
class EventBus {
  constructor() {
    this.events = {};
  }

  subscribe(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }

    this.events[event].push(callback);
  }

  publish(event, data) {
    if (!this.events[event]) return;

    this.events[event].forEach((callback) => {
      callback(data);
    });
  }
}

const bus = new EventBus();

bus.subscribe("userCreated", (user) => {
  console.log("Email sent to", user.name);
});

bus.subscribe("userCreated", (user) => {
  console.log("Analytics tracked", user.name);
});

bus.publish("userCreated", {
  name: "Alice",
});
```

Output:

```js
Email sent to Alice
Analytics tracked Alice
```

---

# Adding Unsubscribe Support

Interviewers almost always ask for this.

```js
class EventBus {
  constructor() {
    this.events = {};
  }

  subscribe(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }

    this.events[event].push(callback);

    return () => {
      this.events[event] = this.events[event].filter((cb) => cb !== callback);
    };
  }

  publish(event, data) {
    if (!this.events[event]) return;

    this.events[event].forEach((cb) => cb(data));
  }
}
```

Usage:

```js
const unsubscribe = bus.subscribe("message", (msg) => console.log(msg));

bus.publish("message", "Hello");

unsubscribe();

bus.publish("message", "World");
```

Output:

```js
Hello;
```

`World` is not printed because the listener was removed.

---

# Using Map Instead of Object

A more robust implementation:

```js
class EventBus {
  constructor() {
    this.events = new Map();
  }

  subscribe(event, callback) {
    if (!this.events.has(event)) {
      this.events.set(event, new Set());
    }

    this.events.get(event).add(callback);

    return () => {
      this.events.get(event)?.delete(callback);
    };
  }

  publish(event, data) {
    this.events.get(event)?.forEach((cb) => cb(data));
  }
}
```

### Why `Map + Set`?

Benefits:

- no prototype collisions
- faster lookups
- automatic duplicate prevention (`Set`)
- cleaner API

---

# Implementing `once()`

Execute a subscriber only one time.

```js
once(event, callback) {
  const unsubscribe = this.subscribe(event, data => {
    callback(data);
    unsubscribe();
  });
}
```

Usage:

```js
bus.once("ready", () => {
  console.log("Runs once");
});
```

---

# Async Pub/Sub

Sometimes subscribers are async.

```js
async publish(event, data) {
  const callbacks = this.events.get(event);

  if (!callbacks) return;

  await Promise.all(
    [...callbacks].map(cb => cb(data))
  );
}
```

Usage:

```js
bus.subscribe("fetch", async () => {
  await fetch("/api");
});
```

---

# Pub/Sub vs Observer Pattern

A very common interview question.

| Observer                | Pub/Sub                            |
| ----------------------- | ---------------------------------- |
| Subject knows observers | Publisher doesn't know subscribers |
| Direct relationship     | Indirect relationship              |
| Tighter coupling        | Looser coupling                    |
| Usually one subject     | Central event broker               |

Observer:

```js
subject.attach(observer);
```

Pub/Sub:

```js
eventBus.publish("event");
```

---

# Real-World Examples

### Browser Events

```js
button.addEventListener("click", handler);
```

Similar to Pub/Sub:

- browser publishes click
- handlers subscribe

---

### Node.js EventEmitter

```js
emitter.on("data", callback);

emitter.emit("data", payload);
```

Node's `EventEmitter` is essentially a Pub/Sub implementation.

---

### Redux

```js
store.subscribe(listener);
```

Uses a related publish/subscribe concept.

---

# Common Pitfalls

## ❌ Memory Leaks

```js
bus.subscribe("event", callback);
```

If never removed:

```text
subscriber remains in memory
```

Always provide unsubscribe support.

---

## ❌ One Subscriber Crashes All

Bad:

```js
callbacks.forEach((cb) => cb(data));
```

If one callback throws:

```js
throw Error();
```

Remaining subscribers won't run.

Better:

```js
callbacks.forEach((cb) => {
  try {
    cb(data);
  } catch (err) {
    console.error(err);
  }
});
```

---

## ❌ Duplicate Subscriptions

```js
bus.subscribe("login", cb);
bus.subscribe("login", cb);
```

Using `Set` instead of arrays prevents duplicates.

---

# Time Complexity

Assume `n` subscribers.

### Subscribe

```text
O(1)
```

### Unsubscribe

```text
O(1) with Set
O(n) with Array
```

### Publish

```text
O(n)
```

Must notify all subscribers.

---

# Interview Summary

> A Pub/Sub system decouples publishers from subscribers using an event broker. Publishers emit events, subscribers register callbacks, and the broker routes messages between them. A typical JavaScript implementation stores event names and callbacks in a `Map` or object, supports `subscribe`, `publish`, and `unsubscribe`, and is commonly used in event-driven architectures, UI frameworks, and systems like Node.js `EventEmitter`.

## Question 10. Difference between mutable and immutable operations on arrays/objects

## Concise Answer

- **Mutable operations** modify the original array or object directly.
- **Immutable operations** create and return a new array or object without changing the original.

Understanding this distinction is important for:

- React state updates
- Redux and state management
- Predictable code behavior
- Avoiding unintended side effects

---

# What is Mutation?

Mutation means changing an existing object in memory.

```js
const arr = [1, 2, 3];

arr.push(4);

console.log(arr);
```

Output:

```js
[1, 2, 3, 4];
```

The original array was modified.

---

# Mutable Array Operations

These methods change the original array.

| Method       | Mutates? |
| ------------ | -------- |
| push()       | ✅       |
| pop()        | ✅       |
| shift()      | ✅       |
| unshift()    | ✅       |
| splice()     | ✅       |
| sort()       | ✅       |
| reverse()    | ✅       |
| fill()       | ✅       |
| copyWithin() | ✅       |

---

## Example: push()

```js
const arr = [1, 2];

arr.push(3);

console.log(arr);
```

Output:

```js
[1, 2, 3];
```

---

## Example: sort()

```js
const numbers = [3, 1, 2];

numbers.sort();

console.log(numbers);
```

Output:

```js
[1, 2, 3];
```

The original array is changed.

---

# Immutable Array Operations

These return a new array.

| Method       | Mutates? |
| ------------ | -------- |
| map()        | ❌       |
| filter()     | ❌       |
| reduce()     | ❌       |
| slice()      | ❌       |
| concat()     | ❌       |
| toSorted()   | ❌       |
| toReversed() | ❌       |
| toSpliced()  | ❌       |
| with()       | ❌       |

---

## Example: filter()

```js
const arr = [1, 2, 3, 4];

const result = arr.filter((x) => x > 2);

console.log(arr);
console.log(result);
```

Output:

```js
[1, 2, 3, 4][(3, 4)];
```

Original array remains unchanged.

---

## Modern Immutable Alternative

Instead of:

```js
arr.sort();
```

Use:

```js
const sorted = arr.toSorted();
```

Introduced in modern JavaScript.

```js
console.log(arr); // original
console.log(sorted); // new array
```

---

# Mutable Object Operations

Objects are mutable by default.

```js
const user = {
  name: "John",
};

user.name = "Alice";
```

Output:

```js
{
  name: "Alice";
}
```

The original object changes.

---

## Adding Properties

```js
const user = {
  name: "John",
};

user.age = 25;
```

Mutates original object.

---

## Deleting Properties

```js
delete user.age;
```

Also mutates.

---

# Immutable Object Operations

Usually done using:

- Spread operator (`...`)
- Object.assign()

---

## Using Spread

```js
const user = {
  name: "John",
};

const updatedUser = {
  ...user,
  age: 25,
};
```

Result:

```js
user;
// { name: "John" }

updatedUser;
// { name: "John", age: 25 }
```

Original remains unchanged.

---

## Using Object.assign()

```js
const updated = Object.assign({}, user, {
  age: 25,
});
```

Creates a new object.

---

# Why Immutability Matters

Consider:

```js
const user = {
  name: "John",
};

const anotherRef = user;

anotherRef.name = "Alice";

console.log(user.name);
```

Output:

```js
Alice;
```

Because both variables point to the same object.

---

# Visualizing References

```text
user ─────┐
          ▼
      { name: "John" }
          ▲
anotherRef┘
```

Mutating through either reference affects the same object.

---

# Immutable Update

```js
const user = {
  name: "John",
};

const updatedUser = {
  ...user,
  name: "Alice",
};
```

Memory:

```text
user ─────────► Object A
updatedUser ──► Object B
```

No shared mutation.

---

# Shallow vs Deep Immutability

Interviewers often ask this.

## Shallow Copy

```js
const user = {
  name: "John",
  address: {
    city: "NY",
  },
};

const copy = { ...user };
```

Looks immutable, but:

```js
copy.address.city = "LA";
```

Now:

```js
console.log(user.address.city);
```

Output:

```js
LA;
```

Because nested objects are shared.

---

## Deep Copy

Modern solution:

```js
const copy = structuredClone(user);
```

Now nested objects are independent.

---

# React Interview Example

Wrong:

```js
state.users.push(newUser);

setState(state);
```

Mutates state directly.

---

Correct:

```js
setState({
  ...state,
  users: [...state.users, newUser],
});
```

Creates new references.

React can detect changes properly.

---

# Freezing Objects

To prevent mutation:

```js
const user = Object.freeze({
  name: "John",
});

user.name = "Alice";
```

Fails (or throws in strict mode).

---

## Deep Freeze Caveat

```js
Object.freeze(obj);
```

Only freezes top level.

Nested objects remain mutable.

---

# Performance Trade-Offs

## Mutable Operations

Advantages:

- Faster
- Less memory allocation

```js
arr.push(item);
```

---

Disadvantages:

- Side effects
- Harder debugging
- State management issues

---

## Immutable Operations

Advantages:

- Predictable state
- Easier debugging
- Change detection
- Functional programming style

---

Disadvantages:

- More allocations
- Potentially higher memory usage

---

# Common Interview Traps

## Trap 1

```js
const arr = [1, 2];

const copy = arr;

copy.push(3);

console.log(arr);
```

Output:

```js
[1, 2, 3];
```

Because `copy` references the same array.

---

## Trap 2

```js
const arr = [1, 2, 3];

const copy = [...arr];

copy.push(4);

console.log(arr);
```

Output:

```js
[1, 2, 3];
```

Different arrays.

---

## Trap 3

```js
const obj = {
  nested: {
    value: 1,
  },
};

const copy = { ...obj };

copy.nested.value = 99;

console.log(obj.nested.value);
```

Output:

```js
99;
```

Spread performs a shallow copy.

---

# Interview Summary

> Mutable operations modify the original array or object directly, such as `push()`, `splice()`, property assignment, or `delete`. Immutable operations return new arrays or objects without changing the original, using methods like `map()`, `filter()`, spread syntax, or `Object.assign()`. Immutability improves predictability, simplifies state management in frameworks like React, and avoids unintended side effects, while mutation is often more memory-efficient and faster.

## Question 11. Difference between deep equality and shallow equality

## Concise Answer

- **Shallow equality** checks only the **first-level references or values** of an object/array.
- **Deep equality** recursively compares **all nested structures** to ensure complete structural equality.

So:

- Shallow = “same top-level reference/values”
- Deep = “same entire structure”

---

# 1. What is Shallow Equality?

Shallow equality compares only the **outer layer** of data.

### Example (Objects)

```js id="v3k9q1"
const obj1 = { a: 1, b: { c: 2 } };
const obj2 = { a: 1, b: { c: 2 } };

console.log(obj1 === obj2);
```

### Output:

```js id="q8m1z2"
false;
```

Even though values look identical, references differ.

---

## Shallow Comparison Logic

A shallow check typically does:

```js id="shallow1"
function shallowEqual(a, b) {
  if (a === b) return true;

  if (typeof a !== "object" || typeof b !== "object") {
    return false;
  }

  const keysA = Object.keys(a);
  const keysB = Object.keys(b);

  if (keysA.length !== keysB.length) return false;

  for (let key of keysA) {
    if (a[key] !== b[key]) {
      return false;
    }
  }

  return true;
}
```

---

## Shallow Equality Example

```js id="shallow2"
const a = { x: 1, y: { z: 2 } };
const b = { x: 1, y: { z: 2 } };

console.log(shallowEqual(a, b));
```

### Output:

```js id="shallow3"
false;
```

Because:

```text id="shallow4"
a.y !== b.y  (different references)
```

---

# 2. What is Deep Equality?

Deep equality compares **all nested levels recursively**.

### Example:

```js id="deep1"
const a = { x: 1, y: { z: 2 } };
const b = { x: 1, y: { z: 2 } };

console.log(deepEqual(a, b));
```

### Output:

```js id="deep2"
true;
```

---

## Deep Equality Implementation (Simplified)

```js id="deep3"
function deepEqual(a, b) {
  if (a === b) return true;

  if (
    typeof a !== "object" ||
    typeof b !== "object" ||
    a === null ||
    b === null
  ) {
    return false;
  }

  const keysA = Object.keys(a);
  const keysB = Object.keys(b);

  if (keysA.length !== keysB.length) return false;

  for (let key of keysA) {
    if (!keysB.includes(key)) return false;

    if (!deepEqual(a[key], b[key])) {
      return false;
    }
  }

  return true;
}
```

---

# 3. Key Differences

| Feature          | Shallow Equality             | Deep Equality            |
| ---------------- | ---------------------------- | ------------------------ |
| Comparison depth | 1 level                      | Recursive (all levels)   |
| Performance      | Fast                         | Slower                   |
| Complexity       | O(n)                         | O(n × depth)             |
| Nested objects   | Compared by reference        | Compared by value        |
| Use cases        | UI optimization, React props | Testing, data validation |

---

# 4. Real Example Difference

```js id="example1"
const obj1 = {
  name: "John",
  address: { city: "NY" },
};

const obj2 = {
  name: "John",
  address: { city: "NY" },
};
```

---

## Shallow comparison:

```js id="example2"
obj1.address === obj2.address;
```

### Result:

```js id="example3"
false;
```

So shallow equality fails.

---

## Deep comparison:

```js id="example4"
deepEqual(obj1, obj2);
```

### Result:

```js id="example5"
true;
```

---

# 5. Where Shallow Equality is Used

## React Example

```js id="react1"
prevProps === nextProps;
```

React uses **shallow comparison** for:

- `React.memo`
- `PureComponent`
- `useEffect` dependency checks

### Why shallow?

- Performance critical
- Deep comparison is expensive

---

## React Pitfall

```js id="react2"
const obj = { a: { b: 1 } };

setState(obj);
```

Even if inner data changes, React only sees reference.

---

# 6. Where Deep Equality is Used

## Use cases:

- Unit testing
- State verification
- Data syncing
- API response validation

Example:

```js id="deepuse1"
expect(actualData).toEqual(expectedData);
```

(Jest uses deep equality internally)

---

# 7. Performance Comparison

## Shallow Equality

```text id="perf1"
O(1) to O(n)
```

Only checks top-level keys.

---

## Deep Equality

```text id="perf2"
O(n × depth)
```

Traverses entire structure.

---

# 8. Edge Cases (Interview Favorites)

## Circular references

```js id="cycle1"
const a = {};
a.self = a;
```

Deep equality must handle cycles:

- Use `WeakMap` to track visited nodes
- Prevent infinite recursion

---

## Different object types

```js id="type1"
deepEqual(new Date(), new Date());
deepEqual(/abc/, /abc/);
```

Need special handling:

- Date → `.getTime()`
- RegExp → `.source` + `.flags`

---

## NaN comparison

```js id="nan1"
NaN === NaN; // false
```

Deep equality often treats:

```js id="nan2"
Number.isNaN(a) && Number.isNaN(b);
```

as equal.

---

# 9. One-Line Mental Model

> Shallow equality checks only top-level references, while deep equality recursively compares all nested values and structures.

---

# 10. Interview Summary

> Shallow equality compares only the first level of an object or array and checks references of nested values, making it fast but limited. Deep equality recursively compares all nested structures to ensure complete structural equivalence, making it accurate but more expensive. Shallow equality is commonly used in performance-sensitive scenarios like React rendering, while deep equality is used in testing and validation where correctness is more important than speed.

## Question 12. How to avoid race conditions in async JavaScript

## Question 13. Difference between Web Workers, Service Workers, and Shared Workers

## Question 14. How to use Transferable objects with Web Workers

## Question 15. How to implement a queue with asynchronous tasks

## Question 16. Difference between synchronous and asynchronous script loading

## Question 17. How to handle large datasets efficiently in JavaScript

## Question 18. Difference between `requestAnimationFrame` and `setTimeout` for animations

## Question 19. How to implement a simple scheduler for throttling async requests

## Question 20. How to optimize performance for a large number of DOM updates
