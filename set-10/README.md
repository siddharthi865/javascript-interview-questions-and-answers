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

## Question 3. How to observe object property changes using Proxy?

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
