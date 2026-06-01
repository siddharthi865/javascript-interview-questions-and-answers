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

## Question 3. How to validate object property access using Proxy

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
