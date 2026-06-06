# Set 10

| S.No. | Question                                                                                                                                                                          |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [Explain Proxy traps in JavaScript](#question-1-explain-proxy-traps-in-javascript)                                                                                                |
| 2.    | [What are revocable proxies?](#question-2-what-are-revocable-proxies)                                                                                                             |
| 3.    | [How to observe object property changes using Proxy?](#question-3-how-to-observe-object-property-changes-using-proxy)                                                             |
| 4.    | [Difference between WeakMap and Map](#question-4-difference-between-weakmap-and-map)                                                                                              |
| 5.    | [How to implement private variables using closures?](#question-5-how-to-implement-private-variables-using-closures)                                                               |
| 6.    | [Explain JavaScript memory model: stack vs heap](#question-6-explain-javascript-memory-model-stack-vs-heap)                                                                       |
| 7.    | [How to handle circular references in objects?](#question-7-how-to-handle-circular-references-in-objects)                                                                         |
| 8.    | [What are ES6 modules vs CommonJS modules?](#question-8-what-are-es6-modules-vs-commonjs-modules)                                                                                 |
| 9.    | [Explain code splitting and lazy loading in JS](#question-9-explain-code-splitting-and-lazy-loading-in-js)                                                                        |
| 10.   | [Difference between mutable and immutable operations on arrays and objects](#question-10-difference-between-mutable-and-immutable-operations-on-arrays-and-objects)               |
| 11.   | [How to prevent race conditions in asynchronous code?](#question-11-how-to-prevent-race-conditions-in-asynchronous-code)                                                          |
| 12.   | [Explain service workers and caching in JavaScript](#question-12-explain-service-workers-and-caching-in-javascript)                                                               |
| 13.   | [Difference between synchronous and asynchronous script loading](#question-13-difference-between-synchronous-and-asynchronous-script-loading)                                     |
| 14.   | [How to throttle API requests in JavaScript?](#question-14-how-to-throttle-api-requests-in-javascript)                                                                            |
| 15.   | [How to implement a debounce function manually?](#question-15-how-to-implement-a-debounce-function-manually)                                                                      |
| 16.   | [What are SharedArrayBuffers?](#question-16-what-are-sharedarraybuffers)                                                                                                          |
| 17.   | [Explain how JavaScript handles event delegation internally](#question-17-explain-how-javascript-handles-event-delegation-internally)                                             |
| 18.   | [Difference between classical inheritance and mixins in JavaScript](#question-18-difference-between-classical-inheritance-and-mixins-in-javascript)                               |
| 19.   | [How to optimize performance for heavy computations in JS (e.g., using Web Workers)?](#question-19-how-to-optimize-performance-for-heavy-computations-in-js-eg-using-web-workers) |

## Question 1. Explain Proxy traps in JavaScript

A **Proxy** in JavaScript allows you to intercept and customize operations performed on an object.
The interception points are called **traps**.

Think of a Proxy as a wrapper around an object that lets you control behaviors like:

- reading properties
- writing properties
- deleting properties
- function calls
- object construction
- iteration
- property checks (`in`)
- and more

A trap is simply a method inside the proxy handler object.

### Basic Syntax

```js
const target = {
  name: "John",
};

const handler = {
  get(target, property) {
    return target[property];
  },
};

const proxy = new Proxy(target, handler);

console.log(proxy.name); // John
```

Here:

- `target` → original object
- `handler` → object containing traps
- `get` → trap that intercepts property access

### Why Proxy Traps Are Useful

Proxy traps are commonly used for:

- validation
- logging
- reactive frameworks (Vue uses proxies)
- access control
- default values
- data binding
- API mocking
- debugging
- virtualization

### Most Important Proxy Traps

#### 1. `get` Trap

Intercepts property access.

```js
const user = {
  name: "Alice",
};

const proxy = new Proxy(user, {
  get(target, prop) {
    console.log(`Getting ${prop}`);

    return target[prop];
  },
});

console.log(proxy.name);
```

Output:

```txt
Getting name
Alice
```

##### Returning Default Values

```js
const proxy = new Proxy(
  {},
  {
    get(target, prop) {
      return prop in target ? target[prop] : "Not Found";
    },
  },
);

console.log(proxy.age); // Not Found
```

#### 2. `set` Trap

Intercepts property assignment.

```js
const user = {};

const proxy = new Proxy(user, {
  set(target, prop, value) {
    if (typeof value !== "string") {
      throw new Error("Only strings allowed");
    }

    target[prop] = value;

    return true;
  },
});

proxy.name = "John";
console.log(proxy.name);

proxy.age = 25; // Error
```

##### Important

The `set` trap should return `true` if assignment succeeds.

Returning `false` may throw errors in strict mode.

#### 3. `has` Trap

Intercepts the `in` operator.

```js
const proxy = new Proxy(
  {},
  {
    has(target, prop) {
      return prop === "secret";
    },
  },
);

console.log("secret" in proxy); // true
console.log("name" in proxy); // false
```

#### 4. `deleteProperty` Trap

Intercepts property deletion.

```js
const user = {
  name: "John",
};

const proxy = new Proxy(user, {
  deleteProperty(target, prop) {
    console.log(`Deleting ${prop}`);

    delete target[prop];

    return true;
  },
});

delete proxy.name;
```

#### 5. `ownKeys` Trap

Intercepts:

- `Object.keys()`
- `Object.getOwnPropertyNames()`
- `Reflect.ownKeys()`

```js
const proxy = new Proxy(
  {},
  {
    ownKeys() {
      return ["a", "b", "c"];
    },
  },
);

console.log(Object.keys(proxy));
```

#### 6. `apply` Trap

Intercepts function calls.

```js
function sum(a, b) {
  return a + b;
}

const proxy = new Proxy(sum, {
  apply(target, thisArg, args) {
    console.log("Function called");

    return target(...args);
  },
});

console.log(proxy(2, 3));
```

Output:

```txt
Function called
5
```

#### 7. `construct` Trap

Intercepts `new` operator calls.

```js
function User(name) {
  this.name = name;
}

const proxy = new Proxy(User, {
  construct(target, args) {
    console.log("Constructor called");

    return new target(...args);
  },
});

const u = new proxy("John");
```

#### 8. `defineProperty` Trap

Intercepts `Object.defineProperty()`.

```js
const proxy = new Proxy(
  {},
  {
    defineProperty(target, prop, descriptor) {
      console.log(`Defining ${prop}`);

      return Reflect.defineProperty(target, prop, descriptor);
    },
  },
);

Object.defineProperty(proxy, "x", {
  value: 10,
});
```

#### 9. `getOwnPropertyDescriptor` Trap

Intercepts property descriptor access.

```js
const proxy = new Proxy(
  {},
  {
    getOwnPropertyDescriptor(target, prop) {
      return {
        configurable: true,
        enumerable: true,
        value: 42,
      };
    },
  },
);

console.log(Object.getOwnPropertyDescriptor(proxy, "x"));
```

#### 10. `preventExtensions` Trap

Intercepts `Object.preventExtensions()`.

```js
const proxy = new Proxy(
  {},
  {
    preventExtensions(target) {
      console.log("Preventing extensions");

      Object.preventExtensions(target);

      return true;
    },
  },
);

Object.preventExtensions(proxy);
```

#### 11. `isExtensible` Trap

Intercepts `Object.isExtensible()`.

```js
const proxy = new Proxy(
  {},
  {
    isExtensible(target) {
      return false;
    },
  },
);

console.log(Object.isExtensible(proxy));
```

#### 12. `getPrototypeOf` Trap

Intercepts prototype access.

```js
const proxy = new Proxy(
  {},
  {
    getPrototypeOf() {
      return Array.prototype;
    },
  },
);

console.log(Array.isArray(proxy)); // false
```

#### 13. `setPrototypeOf` Trap

Intercepts prototype changes.

```js
const proxy = new Proxy(
  {},
  {
    setPrototypeOf(target, proto) {
      console.log("Changing prototype");

      return Reflect.setPrototypeOf(target, proto);
    },
  },
);
```

### Full List of Proxy Traps

| Trap                       | Intercepts                 |
| -------------------------- | -------------------------- |
| `get`                      | Property access            |
| `set`                      | Property assignment        |
| `has`                      | `in` operator              |
| `deleteProperty`           | `delete`                   |
| `ownKeys`                  | Object key listing         |
| `apply`                    | Function call              |
| `construct`                | `new`                      |
| `defineProperty`           | `Object.defineProperty`    |
| `getOwnPropertyDescriptor` | Descriptor access          |
| `preventExtensions`        | `Object.preventExtensions` |
| `isExtensible`             | `Object.isExtensible`      |
| `getPrototypeOf`           | `Object.getPrototypeOf`    |
| `setPrototypeOf`           | `Object.setPrototypeOf`    |

### Using Reflect with Proxy

Best practice is to use the `Reflect` API inside traps.

Example:

```js
const proxy = new Proxy(obj, {
  get(target, prop, receiver) {
    return Reflect.get(target, prop, receiver);
  },
});
```

Why?

Because `Reflect` performs the default behavior safely and correctly.

#### Example: Validation Proxy

```js
const user = {};

const validator = new Proxy(user, {
  set(target, prop, value) {
    if (prop === "age" && value < 0) {
      throw new Error("Age cannot be negative");
    }

    return Reflect.set(target, prop, value);
  },
});

validator.age = 20;
console.log(validator.age);

validator.age = -5; // Error
```

#### Example: Logging Changes

```js
const data = {
  count: 0,
};

const proxy = new Proxy(data, {
  set(target, prop, value) {
    console.log(`${prop} changed from ${target[prop]} to ${value}`);

    return Reflect.set(target, prop, value);
  },
});

proxy.count = 10;
```

### Important Proxy Rules (Invariants)

JavaScript enforces internal consistency rules called **invariants**.

Example:

You cannot report a non-configurable property as deleted.

Breaking invariants causes a `TypeError`.

```js
const obj = {};

Object.defineProperty(obj, "id", {
  value: 1,
  configurable: false,
});

const proxy = new Proxy(obj, {
  deleteProperty() {
    return true;
  },
});

delete proxy.id; // TypeError
```

### Performance Considerations

Proxies are slower than normal object access because every operation can be intercepted.

Avoid using them in:

- hot loops
- performance-critical rendering
- low-level optimized code

### Proxy vs Object.defineProperty

| Proxy                       | defineProperty                 |
| --------------------------- | ------------------------------ |
| Intercepts many operations  | Only property access           |
| Works on dynamic properties | Requires predefined properties |
| More powerful               | Simpler                        |
| Used by modern frameworks   | Older reactivity systems       |

Vue 2 used `Object.defineProperty`.

Vue 3 uses `Proxy`.

### Common Interview Questions

#### Q1: Difference between `Proxy` and `Reflect`?

- `Proxy` intercepts operations
- `Reflect` performs default object behavior

They are commonly used together.

#### Q2: Can Proxy observe array changes?

Yes.

```js
const arr = new Proxy([], {
  set(target, prop, value) {
    console.log(`Index ${prop} updated`);

    return Reflect.set(target, prop, value);
  },
});

arr.push(10);
```

#### Q3: Can Proxy intercept private fields?

No.

Private fields (`#field`) are not accessible through Proxy traps.

### Best Practices

- Prefer `Reflect` inside traps
- Keep traps lightweight
- Avoid violating invariants
- Use proxies for cross-cutting concerns
- Avoid overengineering with proxies

### Summary

Proxy traps let you intercept and customize JavaScript object behavior.

Key traps:

- `get`
- `set`
- `apply`
- `construct`
- `deleteProperty`
- `has`
- `ownKeys`

They are powerful tools behind:

- reactivity systems
- validation
- access control
- debugging
- metaprogramming

Modern frameworks and advanced JavaScript libraries rely heavily on Proxy-based architectures.

## Question 2. What are revocable proxies?

# What Are Revocable Proxies in JavaScript?

**Revocable proxies** are special Proxy objects that can be **disabled (revoked) later**. Once revoked, any operation on the proxy throws a `TypeError`.

JavaScript provides them through `Proxy.revocable()`.

This is useful when you want to grant temporary access to an object and later invalidate that access.

---

# Syntax

```js
const { proxy, revoke } = Proxy.revocable(target, handler);
```

- `proxy` → the Proxy object
- `revoke()` → function that permanently disables the proxy

---

# Basic Example

```js
const target = {
  name: "John",
};

const { proxy, revoke } = Proxy.revocable(target, {});

console.log(proxy.name); // John

revoke();

console.log(proxy.name); // TypeError
```

After calling `revoke()`, the proxy becomes unusable.

---

# How It Works

```js
const user = {
  name: "Alice",
};

const { proxy, revoke } = Proxy.revocable(user, {
  get(target, prop) {
    console.log(`Accessing ${prop}`);
    return target[prop];
  },
});

console.log(proxy.name);

revoke();

console.log(proxy.name);
```

Output:

```txt
Accessing name
Alice

TypeError: Cannot perform 'get' on a proxy that has been revoked
```

---

# Why Use Revocable Proxies?

They are useful when access should be temporary.

Common scenarios:

- Security-sensitive applications
- Session-based access
- Resource cleanup
- Plugin systems
- Sandboxed environments
- Temporary API exposure

---

# Example: Temporary Access Control

```js
function createSession(user) {
  const { proxy, revoke } = Proxy.revocable(user, {});

  return {
    user: proxy,
    logout: revoke,
  };
}

const session = createSession({
  name: "John",
});

console.log(session.user.name);

session.logout();

console.log(session.user.name); // TypeError
```

Once the user logs out, access is revoked.

---

# Example: Plugin Sandbox

```js
const config = {
  apiKey: "secret-key",
};

const { proxy, revoke } = Proxy.revocable(config, {});

function runPlugin(settings) {
  console.log(settings.apiKey);
}

runPlugin(proxy);

revoke();

// Plugin can no longer access config
```

This prevents plugins from holding permanent references.

---

# Revocation Is Permanent

A revoked proxy cannot be re-enabled.

```js
const { proxy, revoke } = Proxy.revocable({}, {});

revoke();

// No way to undo this
```

To regain access, you must create a new proxy.

---

# What Operations Fail After Revocation?

After revoking:

```js
proxy.name;
proxy.name = "John";
delete proxy.name;
"name" in proxy;
Object.keys(proxy);
```

All throw:

```txt
TypeError
```

because the proxy is disconnected from its target.

---

# Relationship with Garbage Collection

One common use case is resource management.

```js
let { proxy, revoke } = Proxy.revocable(largeObject, {});
```

After revocation and removing references:

```js
revoke();
proxy = null;
```

the proxy and associated resources become eligible for garbage collection (assuming no other references exist).

---

# Revocable Proxy vs Normal Proxy

| Feature                  | Proxy | Revocable Proxy |
| ------------------------ | ----- | --------------- |
| Intercepts operations    | ✅    | ✅              |
| Uses handler traps       | ✅    | ✅              |
| Can be disabled          | ❌    | ✅              |
| Has revoke function      | ❌    | ✅              |
| Temporary access control | ❌    | ✅              |

---

# Practical Interview Example

Imagine a file access system:

```js
function openFile(file) {
  const { proxy, revoke } = Proxy.revocable(file, {});

  return {
    file: proxy,
    close: revoke,
  };
}

const doc = openFile({
  content: "Hello World",
});

console.log(doc.file.content);

doc.close();

console.log(doc.file.content);
```

After `close()`, attempting to access the file throws an error, similar to accessing a closed resource.

---

# Common Interview Questions

### Q1: What is the difference between `new Proxy()` and `Proxy.revocable()`?

```js
new Proxy(target, handler);
```

Creates a permanent proxy.

```js
Proxy.revocable(target, handler);
```

Creates a proxy that can later be disabled.

---

### Q2: Can a revoked proxy be restored?

No.

Revocation is permanent.

```js
revoke();

// Cannot undo
```

You must create a new proxy.

---

### Q3: Does revoking affect the original target object?

No.

```js
const target = {
  name: "John",
};

const { proxy, revoke } = Proxy.revocable(target, {});

revoke();

console.log(target.name); // Works
```

Only the proxy becomes unusable; the target object remains intact.

---

# Best Practices

- Use revocable proxies when access should be temporary.
- Revoke access during logout, cleanup, or resource disposal.
- Combine with Proxy traps for validation and auditing.
- Do not rely on them as the sole security mechanism; they are primarily an object-access control tool within JavaScript code.

---

# Interview Summary

**Revocable proxies** are proxies created using `Proxy.revocable()` that can be permanently disabled through a `revoke()` function.

```js
const { proxy, revoke } = Proxy.revocable(target, handler);
```

After calling:

```js
revoke();
```

any operation on the proxy throws a `TypeError`.

They are commonly used for **temporary access, session management, plugin isolation, sandboxing, and resource cleanup**.

## Question 3. How to observe object property changes using Proxy?

## Short Answer

You observe object property changes using a **Proxy with the `set` trap**, which intercepts every property assignment. By comparing old and new values inside this trap, you can detect and react to changes.

---

## Interview-Ready Explanation

In JavaScript, there is no built-in “object observer” API for plain objects. However, **Proxy** provides a powerful way to intercept and track mutations. The key mechanism is the **`set` trap**, which runs whenever a property is written.

A Proxy allows you to:

- Detect when a property is added
- Detect when a property is updated
- Detect when a property is deleted (via `deleteProperty` trap)
- React to changes in real time (logging, UI updates, reactivity systems)

This is the foundation of modern reactivity systems like **Vue 3**.

---

# Core Idea: Using the `set` Trap

```js
const target = {
  name: "John",
  age: 25,
};

const observer = new Proxy(target, {
  set(target, property, value, receiver) {
    console.log(`Property "${property}" changed to`, value);

    return Reflect.set(target, property, value, receiver);
  },
});

observer.name = "Alice";
observer.age = 30;
```

### Output:

```
Property "name" changed to Alice
Property "age" changed to 30
```

---

# Detecting Old vs New Values (Important in Interviews)

A common requirement is to compare previous and new values.

```js
const target = {
  count: 0,
};

const proxy = new Proxy(target, {
  set(target, prop, value) {
    const oldValue = target[prop];

    if (oldValue !== value) {
      console.log(`${prop} changed: ${oldValue} → ${value}`);
    }

    return Reflect.set(target, prop, value);
  },
});

proxy.count = 1;
proxy.count = 2;
```

---

# Detecting Add vs Update

You can distinguish between **new properties** and **existing updates**:

```js
const obj = {};

const proxy = new Proxy(obj, {
  set(target, prop, value) {
    const isNew = !(prop in target);

    console.log(isNew ? `New property: ${prop}` : `Updated property: ${prop}`);

    return Reflect.set(target, prop, value);
  },
});

proxy.name = "John"; // New property
proxy.name = "Alice"; // Updated property
```

---

# Detecting Deletions

To fully observe object mutations, you also need `deleteProperty`:

```js
const obj = { name: "John" };

const proxy = new Proxy(obj, {
  set(target, prop, value) {
    console.log(`Set ${prop}`);
    return Reflect.set(target, prop, value);
  },

  deleteProperty(target, prop) {
    console.log(`Deleted ${prop}`);
    return Reflect.deleteProperty(target, prop);
  },
});

proxy.name = "Alice";
delete proxy.name;
```

---

# Real-World Pattern: Observer System

A reusable observer function:

```js
function createObserver(target, callback) {
  return new Proxy(target, {
    set(obj, prop, value) {
      const oldValue = obj[prop];

      obj[prop] = value;

      callback({
        type: "set",
        property: prop,
        oldValue,
        newValue: value,
      });

      return true;
    },

    deleteProperty(obj, prop) {
      const oldValue = obj[prop];

      delete obj[prop];

      callback({
        type: "delete",
        property: prop,
        oldValue,
      });

      return true;
    },
  });
}
```

### Usage:

```js
const state = createObserver({ count: 0 }, (change) => console.log(change));

state.count = 10;
delete state.count;
```

---

# Deep Observation (Nested Objects)

A key interview follow-up: Proxy does NOT automatically observe nested objects.

### Problem:

```js
const obj = {
  user: {
    name: "John",
  },
};
```

Changing `obj.user.name` will NOT trigger outer proxy unless nested proxies are created.

---

## Solution: Recursive Proxy (Deep Observe)

```js
function deepProxy(obj, callback) {
  if (typeof obj !== "object" || obj === null) {
    return obj;
  }

  return new Proxy(obj, {
    set(target, prop, value) {
      const oldValue = target[prop];

      target[prop] = deepProxy(value, callback);

      callback(prop, oldValue, value);

      return true;
    },

    get(target, prop) {
      return deepProxy(target[prop], callback);
    },
  });
}
```

---

# Vue-like Reactivity Example

```js
const state = deepProxy({ user: { name: "John" } }, (prop, oldVal, newVal) => {
  console.log(`${prop} changed`);
});

state.user.name = "Alice";
```

---

# Important Edge Cases (Interview Gold)

### 1. Assignment must return `true`

If `set` returns `false`, strict mode throws.

```js
set() {
  return true; // required
}
```

---

### 2. Use `Reflect` for correctness

Preferred approach:

```js
Reflect.set(target, prop, value);
```

It preserves default JS semantics.

---

### 3. Non-configurable properties

You cannot violate invariants:

- cannot change writability rules incorrectly
- cannot lie about property existence

---

### 4. Performance cost

Proxies add overhead:

- each property access is intercepted
- deep proxies increase memory usage

---

# Proxy Observation vs Alternatives

| Approach              | Pros                      | Cons                     |
| --------------------- | ------------------------- | ------------------------ |
| Proxy                 | Powerful, dynamic, modern | Performance overhead     |
| Object.defineProperty | Works in old browsers     | Limited, manual setup    |
| MutationObserver      | DOM only                  | Not for objects          |
| Immutable patterns    | Predictable               | Requires rewriting logic |

---

# Real-World Usage

Proxy-based observation is used in:

- Vue 3 reactivity system
- MobX (optional proxy mode)
- state management libraries
- form validation systems
- logging/debugging tools
- sandboxing environments

---

# Interview Summary

To observe object property changes:

- Use a **Proxy**
- Implement the **`set` trap**
- Optionally use:
  - `deleteProperty` for deletions
  - `get` for lazy tracking

- Compare old vs new values using `target[prop]`
- Use `Reflect.set` for correctness

---

## One-Line Answer (Interview Version)

> You can observe object property changes in JavaScript by wrapping the object in a Proxy and using the `set` trap to intercept and react to property assignments, optionally comparing old and new values and handling deletions via `deleteProperty`.

## Question 4. Difference between WeakMap and Map

# Difference Between WeakMap and Map in JavaScript

## Short Answer

Both **Map** and **WeakMap** store key-value pairs, but the key difference is:

- **Map** can use **any value** as a key and holds **strong references** to keys.
- **WeakMap** only allows **objects as keys** and holds **weak references**, allowing garbage collection when no other references exist.

A `WeakMap` is mainly used for **private data, metadata storage, and memory-leak prevention**.

---

## Basic Syntax

### Map

```js
const map = new Map();

map.set("name", "John");
map.set(1, "number");
map.set(true, "boolean");

console.log(map.get("name"));
```

### WeakMap

```js
const weakMap = new WeakMap();

const user = {};

weakMap.set(user, "John");

console.log(weakMap.get(user));
```

---

# Key Differences

| Feature                    | Map                        | WeakMap                 |
| -------------------------- | -------------------------- | ----------------------- |
| Key types                  | Any value                  | Objects only            |
| Primitive keys             | ✅                         | ❌                      |
| Iterable                   | ✅                         | ❌                      |
| size property              | ✅                         | ❌                      |
| keys()                     | ✅                         | ❌                      |
| values()                   | ✅                         | ❌                      |
| entries()                  | ✅                         | ❌                      |
| forEach()                  | ✅                         | ❌                      |
| Garbage collection of keys | No (strong refs)           | Yes (weak refs)         |
| Use case                   | General-purpose collection | Private data / metadata |

---

# 1. Key Types

## Map

Any value can be a key.

```js
const map = new Map();

map.set("name", "John");
map.set(42, "number");
map.set(true, "boolean");

console.log(map.size); // 3
```

---

## WeakMap

Only objects are allowed.

```js
const weakMap = new WeakMap();

const obj = {};

weakMap.set(obj, "value");
```

Primitive keys throw an error:

```js
weakMap.set("name", "John");
```

Output:

```txt
TypeError: Invalid value used as weak map key
```

---

# 2. Garbage Collection

This is the most important interview point.

## Map Holds Strong References

```js
let user = {
  name: "John",
};

const map = new Map();

map.set(user, "data");

user = null;
```

The object still exists because Map maintains a reference.

```js
console.log(map.size); // 1
```

The key cannot be garbage collected.

---

## WeakMap Holds Weak References

```js
let user = {
  name: "John",
};

const weakMap = new WeakMap();

weakMap.set(user, "data");

user = null;
```

Now the object can be garbage collected.

Because the key is weakly referenced, WeakMap does not keep it alive.

---

# Why Is This Useful?

Suppose you're attaching metadata to DOM elements:

```js
const metadata = new WeakMap();

function attachData(element) {
  metadata.set(element, {
    clicked: false,
  });
}
```

When the DOM element is removed:

```js
element = null;
```

The metadata automatically becomes collectible.

No manual cleanup required.

This prevents memory leaks.

---

# 3. Iteration

## Map Is Iterable

```js
const map = new Map([
  ["a", 1],
  ["b", 2],
]);

for (const [key, value] of map) {
  console.log(key, value);
}
```

Output:

```txt
a 1
b 2
```

---

## WeakMap Is Not Iterable

```js
const wm = new WeakMap();

for (const item of wm) {
}
```

Output:

```txt
TypeError
```

---

# Why Can't WeakMap Be Iterated?

Imagine:

```js
const obj = {};

wm.set(obj, "data");
```

The engine may garbage collect `obj` at any moment.

Therefore:

```js
wm.keys();
```

would be unpredictable.

Because keys can disappear at any time, iteration is forbidden.

---

# 4. Size Property

## Map

```js
const map = new Map();

map.set("a", 1);
map.set("b", 2);

console.log(map.size);
```

Output:

```txt
2
```

---

## WeakMap

```js
const wm = new WeakMap();

console.log(wm.size);
```

Output:

```txt
undefined
```

No size tracking exists.

---

# 5. Available Methods

## Map

```js
map.set();
map.get();
map.has();
map.delete();
map.clear();

map.keys();
map.values();
map.entries();
map.forEach();
```

---

## WeakMap

```js
wm.set();
wm.get();
wm.has();
wm.delete();
```

Only four methods.

---

# Common Interview Use Case: Private Data

Before private fields (`#`) existed, WeakMap was commonly used to simulate private members.

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

const user = new User("John");

console.log(user.getName());
```

Outside access:

```js
console.log(user.name);
```

Output:

```txt
undefined
```

---

# Modern Alternative: Private Fields

Today we usually write:

```js
class User {
  #name;

  constructor(name) {
    this.#name = name;
  }

  getName() {
    return this.#name;
  }
}
```

But WeakMap remains useful for attaching metadata to external objects.

---

# Memory Leak Example

### Problem

```js
const cache = new Map();

function process(obj) {
  cache.set(obj, expensiveResult(obj));
}
```

Thousands of objects enter the cache.

Even after objects are no longer used:

```js
obj = null;
```

Map still retains them.

Memory grows indefinitely.

---

### Solution

```js
const cache = new WeakMap();

function process(obj) {
  cache.set(obj, expensiveResult(obj));
}
```

Unused keys can now be collected automatically.

---

# Tricky Interview Questions

## Q1

```js
const wm = new WeakMap();

wm.set({}, "A");

console.log(wm);
```

Can you retrieve the value later?

**No.**

The key object reference is lost.

```js
wm.get({});
```

returns:

```txt
undefined
```

because it's a different object.

---

## Q2

```js
const map = new Map();

map.set({}, "A");

console.log(map.get({}));
```

Output?

```txt
undefined
```

Objects are compared by reference, not structure.

---

## Q3

```js
const obj = {};

const wm = new WeakMap();

wm.set(obj, 123);

console.log(wm.has(obj));
```

Output:

```txt
true
```

---

## Q4

```js
const wm = new WeakMap();

wm.set("name", "John");
```

Output?

```txt
TypeError
```

WeakMap keys must be objects.

---

# Map vs WeakMap Use Cases

### Use Map When

- Need iteration
- Need size
- Need primitive keys
- Need a general collection

```js
const cache = new Map();
```

---

### Use WeakMap When

- Keys are objects
- Metadata attached to objects
- Need automatic cleanup
- Want to avoid memory leaks

```js
const metadata = new WeakMap();
```

---

# Interview Summary

> A `Map` stores key-value pairs with strong references and supports any key type, iteration, and size tracking. A `WeakMap` only allows object keys and holds weak references, enabling automatic garbage collection when the key object becomes unreachable. Because keys can disappear at any time, WeakMaps are not iterable and do not expose size information.

### Remember This Interview One-Liner

> **Map = iterable + any key type + strong references.**
> **WeakMap = object keys only + non-iterable + automatic garbage collection.**

## Question 5. How to implement private variables using closures?

# How to Implement Private Variables Using Closures?

## Short Answer

Private variables can be implemented using **closures** by defining variables inside a function and returning methods that access them. Since the variables are not exposed outside the function, they remain private.

```js
function createCounter() {
  let count = 0; // private variable

  return {
    increment() {
      count++;
    },

    getCount() {
      return count;
    },
  };
}

const counter = createCounter();

counter.increment();
console.log(counter.getCount()); // 1

console.log(counter.count); // undefined
```

---

# What Is a Closure?

A **closure** is created when a function remembers and can access variables from its lexical scope even after the outer function has finished executing.

```js
function outer() {
  let message = "Hello";

  return function inner() {
    console.log(message);
  };
}

const fn = outer();
fn(); // Hello
```

Even though `outer()` has completed, `inner()` still has access to `message`.

This ability is what enables private variables.

---

# Basic Private Variable Example

```js
function createUser(name) {
  let username = name; // private

  return {
    getName() {
      return username;
    },

    setName(newName) {
      username = newName;
    },
  };
}

const user = createUser("John");

console.log(user.getName()); // John

user.setName("Alice");

console.log(user.getName()); // Alice
console.log(user.username); // undefined
```

### Why is `username` private?

Because it exists only inside `createUser()`'s scope.

External code cannot access it directly.

---

# Interview Favorite: Counter Example

```js
function createCounter() {
  let count = 0;

  return {
    increment() {
      count++;
      return count;
    },

    decrement() {
      count--;
      return count;
    },

    value() {
      return count;
    },
  };
}

const counter = createCounter();

console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.decrement()); // 1
```

Here:

- `count` is private
- Only returned methods can modify it
- External code cannot change it directly

---

# Multiple Instances Have Separate Private State

```js
function createCounter() {
  let count = 0;

  return {
    increment() {
      return ++count;
    },
  };
}

const c1 = createCounter();
const c2 = createCounter();

console.log(c1.increment()); // 1
console.log(c1.increment()); // 2

console.log(c2.increment()); // 1
```

Each closure gets its own copy of `count`.

---

# Module Pattern (Classic JavaScript)

Before ES Modules, closures were commonly used for encapsulation.

```js
const Calculator = (function () {
  let result = 0;

  return {
    add(num) {
      result += num;
    },

    subtract(num) {
      result -= num;
    },

    getResult() {
      return result;
    },
  };
})();

Calculator.add(10);
Calculator.add(5);

console.log(Calculator.getResult()); // 15
```

The variable `result` is completely private.

---

# Simulating Private Methods

Closures can hide both data and helper functions.

```js
function createBankAccount(balance) {
  function validate(amount) {
    return amount > 0;
  }

  return {
    deposit(amount) {
      if (validate(amount)) {
        balance += amount;
      }
    },

    getBalance() {
      return balance;
    },
  };
}
```

Both:

```js
balance;
validate();
```

are private.

---

# Why Closures Work for Privacy

When a function returns another function:

```js
function outer() {
  let secret = "hidden";

  return function () {
    return secret;
  };
}
```

JavaScript's garbage collector keeps `secret` alive because the inner function still references it.

This preserved lexical environment is the closure.

---

# Common Pitfall

Developers sometimes think this is private:

```js
function User(name) {
  this.name = name;
}

const user = new User("John");

console.log(user.name);
```

It is not private.

Anyone can do:

```js
user.name = "Hacked";
```

Closures provide true encapsulation.

---

# Closure-Based Privacy vs `#private` Fields

Modern JavaScript supports private class fields:

```js
class User {
  #name;

  constructor(name) {
    this.#name = name;
  }

  getName() {
    return this.#name;
  }
}
```

---

## Closure Approach

```js
function createUser(name) {
  let username = name;

  return {
    getName() {
      return username;
    },
  };
}
```

---

## Comparison

| Feature                             | Closures      | `#private` Fields |
| ----------------------------------- | ------------- | ----------------- |
| Truly private                       | ✅            | ✅                |
| Works in functions                  | ✅            | ❌                |
| Works in classes                    | Possible      | ✅                |
| Memory efficient for many instances | ❌            | ✅                |
| Modern standard                     | Older pattern | ✅                |

---

# Performance Consideration

With closures:

```js
function createUser(name) {
  return {
    getName() {
      return name;
    },
  };
}
```

Every instance creates new function objects.

```js
const u1 = createUser("A");
const u2 = createUser("B");
```

Each instance gets its own `getName()` function.

With classes:

```js
class User {
  getName() {}
}
```

Methods are shared via the prototype, making them more memory-efficient for many instances.

---

# Real-World Example: Secure Token Storage

```js
function createAuth() {
  let token = null;

  return {
    login(newToken) {
      token = newToken;
    },

    getToken() {
      return token;
    },

    logout() {
      token = null;
    },
  };
}

const auth = createAuth();

auth.login("abc123");

console.log(auth.getToken()); // abc123
console.log(auth.token); // undefined
```

The token cannot be accessed directly from outside.

---

# Interview Summary

> Private variables can be implemented using closures by defining variables inside an outer function and returning inner functions that access those variables. The returned functions retain access to the outer scope through lexical scoping, while external code cannot access the variables directly.

```js
function createCounter() {
  let count = 0;

  return {
    increment() {
      count++;
    },

    getCount() {
      return count;
    },
  };
}
```

### Key Interview Takeaways

- Closures preserve access to outer-scope variables.
- Private variables are inaccessible from outside the closure.
- Each function invocation gets its own private state.
- This pattern was widely used before `#private` class fields.
- Modern JavaScript often prefers `#private` fields for class-based designs, but closures remain fundamental and are frequently asked in interviews.

## Question 6. Explain JavaScript memory model: stack vs heap

# JavaScript Memory Model: Stack vs Heap

## Short Answer

JavaScript memory is broadly divided into:

- **Stack Memory** → Stores primitive values and function execution contexts.
- **Heap Memory** → Stores objects, arrays, functions, and other reference types.

Primitive values are usually copied by value, while objects are accessed through references that point to heap memory.

---

# High-Level Overview

When JavaScript code runs, the engine (e.g., V8) manages memory using:

```txt
+----------------+
| Call Stack     |
+----------------+
| Execution Info |
| Primitive Data |
| References     |
+----------------+

+----------------+
| Heap Memory    |
+----------------+
| Objects        |
| Arrays         |
| Functions      |
| Closures       |
+----------------+
```

Think of:

- **Stack** = fast, organized memory for execution.
- **Heap** = large, dynamic memory for complex data.

---

# Stack Memory

The stack stores:

- Function calls
- Local variables
- Primitive values
- References to heap objects

Example:

```js
let name = "John";
let age = 25;
```

Conceptually:

```txt
STACK

name → "John"
age  → 25
```

Primitives are stored directly.

---

# Primitive Types

These are typically stack-allocated values:

```js
String;
Number;
Boolean;
Undefined;
Null;
BigInt;
Symbol;
```

Example:

```js
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20
```

Why?

Because primitives are copied by value.

```txt
STACK

a → 10
b → 10

After b = 20

a → 10
b → 20
```

Each variable has its own independent value.

---

# Heap Memory

The heap stores:

- Objects
- Arrays
- Functions
- Dates
- Maps
- Sets
- Class instances

Example:

```js
const user = {
  name: "John",
};
```

Conceptually:

```txt
STACK

user ───────┐
            │
            ▼

HEAP

{
  name: "John"
}
```

The variable holds a reference (memory address) to the object.

---

# Reference Behavior

```js
const user1 = {
  name: "John",
};

const user2 = user1;

user2.name = "Alice";

console.log(user1.name);
```

Output:

```txt
Alice
```

Memory:

```txt
STACK

user1 ──────┐
            │
user2 ──────┘

             ▼

HEAP

{
  name: "Alice"
}
```

Both variables point to the same object.

---

# Function Execution Context and the Stack

Every function call creates a new stack frame.

```js
function first() {
  second();
}

function second() {
  third();
}

function third() {
  console.log("Hello");
}

first();
```

Call Stack:

```txt
third()
second()
first()
global()
```

Execution occurs from top to bottom.

After completion:

```txt
global()
```

Frames are popped off the stack.

---

# Stack Overflow

Because the stack is finite:

```js
function recurse() {
  recurse();
}

recurse();
```

Output:

```txt
RangeError: Maximum call stack size exceeded
```

Too many stack frames cause stack overflow.

---

# Objects Inside Functions

```js
function createUser() {
  const user = {
    name: "John",
  };

  return user;
}
```

Memory:

```txt
STACK

user ──────┐
           │
           ▼

HEAP

{
  name: "John"
}
```

The reference lives on the stack.

The actual object lives on the heap.

---

# Arrays in Heap

```js
const arr = [1, 2, 3];
```

```txt
STACK

arr ──────┐
          │
          ▼

HEAP

[1,2,3]
```

Arrays are objects internally.

Therefore they live in heap memory.

---

# Functions in Heap

Functions are objects too.

```js
function greet() {
  console.log("Hello");
}
```

Conceptually:

```txt
STACK

greet ──────┐
            │
            ▼

HEAP

Function Object
```

---

# Closures and Memory

One of the most important interview topics.

```js
function counter() {
  let count = 0;

  return function () {
    count++;
    return count;
  };
}

const increment = counter();
```

Normally:

```txt
counter() frame removed
```

But closure variables survive.

```txt
HEAP

Closure Environment

count = 0
```

Because the returned function still references `count`.

---

# Garbage Collection

JavaScript automatically frees unused heap memory.

Example:

```js
let user = {
  name: "John",
};

user = null;
```

Now:

```txt
Object unreachable
```

Garbage collector eventually removes it.

---

# Reachability Principle

Objects remain alive while reachable.

```js
let user = {
  name: "John",
};

const admin = user;
```

Memory:

```txt
admin ──┐
        │
user ───┘

   ▼

Object
```

Even if:

```js
user = null;
```

the object survives because:

```js
admin;
```

still references it.

---

# Memory Leak Example

```js
let cache = [];

function store() {
  const bigData = new Array(1000000);

  cache.push(bigData);
}
```

Every call keeps references alive.

```txt
cache → bigData1
cache → bigData2
cache → bigData3
```

Garbage collector cannot reclaim them.

This is a memory leak.

---

# Stack vs Heap Summary

| Feature      | Stack                                      | Heap                       |
| ------------ | ------------------------------------------ | -------------------------- |
| Stores       | Primitives, execution contexts, references | Objects, arrays, functions |
| Access Speed | Very fast                                  | Slower                     |
| Size         | Small                                      | Large                      |
| Allocation   | Automatic                                  | Dynamic                    |
| Cleanup      | Automatic when function exits              | Garbage collection         |
| Structure    | LIFO (Last In First Out)                   | Unstructured memory pool   |

---

# Common Interview Questions

## Q1: Where are objects stored?

```js
const obj = {};
```

- Object → Heap
- Reference → Stack

---

## Q2: Why does this happen?

```js
const a = { x: 1 };
const b = a;

b.x = 2;

console.log(a.x);
```

Output:

```txt
2
```

Because both variables reference the same heap object.

---

## Q3: Why doesn't this happen with primitives?

```js
let a = 10;
let b = a;

b = 20;

console.log(a);
```

Output:

```txt
10
```

Primitives are copied by value.

---

## Q4: Where are closures stored?

Closure variables that outlive their function execution are typically retained in heap memory so they remain accessible to inner functions.

---

## Q5: What causes stack overflow?

Too many nested function calls:

```js
function f() {
  f();
}
```

Eventually:

```txt
Maximum call stack size exceeded
```

---

# Modern Engine Nuance

For interview purposes:

- **Primitives → Stack**
- **Objects → Heap**

is the correct mental model.

However, modern engines such as V8 perform many optimizations:

- Escape analysis
- Inline caching
- Hidden classes
- Stack allocation of some objects

So the actual implementation may differ internally.

In interviews, focus on the conceptual model rather than engine-specific optimizations.

---

# Interview Summary

> JavaScript uses a memory model consisting of a **stack** and a **heap**. The stack stores execution contexts, primitive values, and references, while the heap stores objects, arrays, functions, and closures. Primitive values are copied by value, whereas objects are accessed through references. Memory in the heap is automatically managed by the garbage collector using reachability analysis.

```js
const user = { name: "John" };
```

```txt
Stack: user → reference
Heap : { name: "John" }
```

This distinction explains object mutation behavior, closures, garbage collection, memory leaks, and stack overflow errors—all common JavaScript interview topics.

## Question 7. How to handle circular references in objects?

# How to Handle Circular References in JavaScript Objects?

## Short Answer

A **circular reference** occurs when an object directly or indirectly references itself.

```js
const obj = {};
obj.self = obj;
```

Circular references can cause problems with:

- `JSON.stringify()`
- Deep cloning
- Recursive traversal
- Serialization

Common solutions include:

- Using a `WeakSet` to track visited objects
- Custom `JSON.stringify` replacers
- Using `structuredClone()`
- Specialized libraries like Lodash

---

# What Is a Circular Reference?

## Direct Circular Reference

```js
const obj = {
  name: "John",
};

obj.self = obj;
```

Visualization:

```txt
obj
 ├─ name: "John"
 └─ self ─────┐
              │
              └──> obj
```

The object points back to itself.

---

## Indirect Circular Reference

```js
const user = {};
const profile = {};

user.profile = profile;
profile.user = user;
```

Visualization:

```txt
user ───► profile
  ▲         │
  │         ▼
  └─────────┘
```

Neither object references itself directly, but together they form a cycle.

---

# Problem 1: JSON.stringify Fails

```js
const obj = {};
obj.self = obj;

JSON.stringify(obj);
```

Output:

```txt
TypeError: Converting circular structure to JSON
```

Why?

Because the serializer enters an infinite loop:

```txt
obj
 └─ self
      └─ self
           └─ self
                ...
```

---

# Solution 1: Custom JSON.stringify Replacer

A common interview solution is tracking visited objects with a `WeakSet`.

```js
function safeStringify(obj) {
  const seen = new WeakSet();

  return JSON.stringify(obj, (key, value) => {
    if (typeof value === "object" && value !== null) {
      if (seen.has(value)) {
        return "[Circular]";
      }

      seen.add(value);
    }

    return value;
  });
}
```

Usage:

```js
const obj = {};
obj.self = obj;

console.log(safeStringify(obj));
```

Output:

```json
{
  "self": "[Circular]"
}
```

---

# Why WeakSet?

A `WeakSet`:

- Stores object references
- Doesn't prevent garbage collection
- Is ideal for tracking visited objects

```js
const visited = new WeakSet();
```

This is a very common senior-level interview answer.

---

# Solution 2: Recursive Traversal with Cycle Detection

Suppose you're writing your own object walker.

Bad:

```js
function traverse(obj) {
  for (const key in obj) {
    traverse(obj[key]);
  }
}
```

Infinite recursion occurs with circular structures.

---

## Safe Version

```js
function traverse(obj, visited = new WeakSet()) {
  if (typeof obj !== "object" || obj === null) {
    return;
  }

  if (visited.has(obj)) {
    return;
  }

  visited.add(obj);

  for (const key in obj) {
    traverse(obj[key], visited);
  }
}
```

Now cycles are safely skipped.

---

# Solution 3: Deep Clone with Circular References

Naive deep clone:

```js
function clone(obj) {
  if (typeof obj !== "object") {
    return obj;
  }

  const copy = {};

  for (const key in obj) {
    copy[key] = clone(obj[key]);
  }

  return copy;
}
```

Fails on circular references.

---

## Circular-Safe Clone

Using a `WeakMap`:

```js
function deepClone(obj, visited = new WeakMap()) {
  if (typeof obj !== "object" || obj === null) {
    return obj;
  }

  if (visited.has(obj)) {
    return visited.get(obj);
  }

  const copy = Array.isArray(obj) ? [] : {};

  visited.set(obj, copy);

  for (const key in obj) {
    copy[key] = deepClone(obj[key], visited);
  }

  return copy;
}
```

Example:

```js
const obj = {};
obj.self = obj;

const cloned = deepClone(obj);

console.log(cloned.self === cloned);
```

Output:

```txt
true
```

The circular relationship is preserved.

---

# Solution 4: Use structuredClone()

Modern JavaScript provides:

```js
const cloned = structuredClone(obj);
```

Example:

```js
const obj = {};
obj.self = obj;

const copy = structuredClone(obj);

console.log(copy.self === copy);
```

Output:

```txt
true
```

Advantages:

- Handles circular references automatically
- Supports Maps
- Supports Sets
- Supports Dates
- Supports TypedArrays

Modern browsers and Node.js support it.

---

# WeakMap vs WeakSet

Interviewers often ask which to use.

### WeakSet

Use when you only need to know:

```js
Have I visited this object?
```

Example:

```js
visited.add(obj);
visited.has(obj);
```

---

### WeakMap

Use when you need a mapping:

```js
Original Object -> Cloned Object
```

Example:

```js
visited.set(original, clone);
```

Common in deep cloning algorithms.

---

# Real-World Example: Graph Structures

```js
const nodeA = {};
const nodeB = {};

nodeA.next = nodeB;
nodeB.next = nodeA;
```

Graphs naturally contain cycles.

Algorithms traversing graphs must maintain a visited collection.

```js
const visited = new WeakSet();
```

Otherwise recursion never terminates.

---

# Common Interview Pitfalls

## Pitfall 1

```js
JSON.stringify(obj);
```

Assuming it works for every object.

It fails for circular references.

---

## Pitfall 2

```js
const copy = JSON.parse(JSON.stringify(obj));
```

This also fails with circular structures.

---

## Pitfall 3

Writing recursive functions without tracking visited nodes.

```js
walk(obj);
```

Can cause:

```txt
RangeError:
Maximum call stack size exceeded
```

---

# Modern Best Practice

### Serialization

```js
safeStringify(obj);
```

or a library like:

```js
flatted;
```

---

### Cloning

```js
structuredClone(obj);
```

Preferred in modern JavaScript.

---

### Traversal

```js
const visited = new WeakSet();
```

Track processed objects.

---

# Interview Summary

> A circular reference occurs when an object directly or indirectly references itself. Circular structures can break serialization, deep cloning, and recursive traversal. The standard solution is to track visited objects using a `WeakSet` or `WeakMap` to prevent infinite recursion and duplicate processing.

### Example

```js
const obj = {};
obj.self = obj;
```

Safe traversal:

```js
const visited = new WeakSet();

if (visited.has(obj)) {
  return;
}

visited.add(obj);
```

### Key Interview Takeaways

- `JSON.stringify()` throws on circular references.
- Use `WeakSet` for cycle detection.
- Use `WeakMap` for deep cloning.
- `structuredClone()` handles circular references automatically.
- Circular references are common in graphs, trees with parent links, DOM-like structures, and complex application state.

## Question 8. What are ES6 modules vs CommonJS modules?

## Question 9. Explain code splitting and lazy loading in JS

## Question 10. Difference between mutable and immutable operations on arrays and objects

## Question 11. How to prevent race conditions in asynchronous code?

## Question 12. Explain service workers and caching in JavaScript

## Question 13. Difference between synchronous and asynchronous script loading

## Question 14. How to throttle API requests in JavaScript?

## Question 15. How to implement a debounce function manually?

## Question 16. What are SharedArrayBuffers?

## Question 17. Explain how JavaScript handles event delegation internally

## Question 18. Difference between classical inheritance and mixins in JavaScript

## Question 19. How to optimize performance for heavy computations in JS (e.g., using Web Workers)?
