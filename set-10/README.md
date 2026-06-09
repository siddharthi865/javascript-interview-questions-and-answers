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

## Question 5. How to implement private variables using closures?

## Question 6. Explain JavaScript memory model: stack vs heap

## Question 7. How to handle circular references in objects?

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
