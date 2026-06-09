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

## Question 5. How to implement memoization for expensive function calls

## Question 6. Difference between synchronous and asynchronous iterables

## Question 7. How to use Symbol.iterator to make an object iterable

## Question 8. How to implement a reactive object (like Vue's reactivity system)

## Question 9. How to implement a simple pub/sub system in JavaScript

## Question 10. Difference between mutable and immutable operations on arrays/objects

## Question 11. Difference between deep equality and shallow equality

## Question 12. How to avoid race conditions in async JavaScript

## Question 13. Difference between Web Workers, Service Workers, and Shared Workers

## Question 14. How to use Transferable objects with Web Workers

## Question 15. How to implement a queue with asynchronous tasks

## Question 16. Difference between synchronous and asynchronous script loading

## Question 17. How to handle large datasets efficiently in JavaScript

## Question 18. Difference between `requestAnimationFrame` and `setTimeout` for animations

## Question 19. How to implement a simple scheduler for throttling async requests

## Question 20. How to optimize performance for a large number of DOM updates
