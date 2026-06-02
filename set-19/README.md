# Set 19

| S.No. | Question                                                                                                                                          |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to implement a factory pattern in JavaScript](#question-1-how-to-implement-a-factory-pattern-in-javascript)                                  |
| 2.    | [How to implement a module pattern in JavaScript](#question-2-how-to-implement-a-module-pattern-in-javascript)                                    |
| 3.    | [How to create a proxy to validate object properties](#question-3-how-to-create-a-proxy-to-validate-object-properties)                            |
| 4.    | [How to use Reflect API to manipulate objects](#question-4-how-to-use-reflect-api-to-manipulate-objects)                                          |
| 5.    | [How to implement a reactive object (like Vue.js reactivity system)](#question-5-how-to-implement-a-reactive-object-like-vuejs-reactivity-system) |
| 6.    | [How to implement observer pattern in JavaScript](#question-6-how-to-implement-observer-pattern-in-javascript)                                    |
| 7.    | [How to use `WeakRef` to prevent memory leaks](#question-7-how-to-use-weakref-to-prevent-memory-leaks)                                            |
| 8.    | [Difference between shallow and deep freezing an object](#question-8-difference-between-shallow-and-deep-freezing-an-object)                      |
| 9.    | [How to implement a read-only object using Proxy](#question-9-how-to-implement-a-read-only-object-using-proxy)                                    |
| 10.   | [How to implement custom iterables using `[Symbol.iterator]`](#question-10-how-to-implement-custom-iterables-using-symboliterator)                |
| 11.   | [How to implement async iterables using `[Symbol.asyncIterator]`](#question-11-how-to-implement-async-iterables-using-symbolasynciterator)        |
| 12.   | [How to implement generators for state machines](#question-12-how-to-implement-generators-for-state-machines)                                     |
| 13.   | [How to implement a scheduler for async tasks](#question-13-how-to-implement-a-scheduler-for-async-tasks)                                         |
| 14.   | [How to implement a task queue to prevent blocking](#question-14-how-to-implement-a-task-queue-to-prevent-blocking)                               |
| 15.   | [How to implement cooperative multitasking in JavaScript](#question-15-how-to-implement-cooperative-multitasking-in-javascript)                   |
| 16.   | [How to implement backpressure for streaming data](#question-16-how-to-implement-backpressure-for-streaming-data)                                 |
| 17.   | [How to implement a simple event loop simulation](#question-17-how-to-implement-a-simple-event-loop-simulation)                                   |
| 18.   | [How to use `requestIdleCallback` for non-urgent tasks](#question-18-how-to-use-requestidlecallback-for-non-urgent-tasks)                         |
| 19.   | [How to implement memoization for expensive functions](#question-19-how-to-implement-memoization-for-expensive-functions)                         |
| 20.   | [How to implement a caching mechanism with TTL (Time-To-Live)](#question-20-how-to-implement-a-caching-mechanism-with-ttl-time-to-live)           |

## Question 1. How to implement a factory pattern in JavaScript

The **Factory Pattern** in JavaScript is a creational design pattern used to create objects without exposing the exact object creation logic to the client. Instead of using `new` directly everywhere, you centralize object creation in a factory function or class.

It helps:

- Encapsulate object creation logic
- Reduce repetitive code
- Improve maintainability
- Support polymorphism and abstraction

### 1. Basic Factory Function

A simple factory returns objects based on input.

```js
function createUser(name, role) {
  return {
    name,
    role,
    describe() {
      console.log(`${this.name} is a ${this.role}`);
    },
  };
}

const admin = createUser("John", "Admin");
const editor = createUser("Sarah", "Editor");

admin.describe();
editor.describe();
```

### Output

```js
John is a Admin
Sarah is a Editor
```

### 2. Why Use Factory Pattern?

Without a factory:

```js
const user1 = {
  name: "John",
  role: "Admin",
};

const user2 = {
  name: "Sarah",
  role: "Editor",
};
```

Problems:

- Duplicate logic
- Hard to maintain
- No abstraction
- Difficult to add validation or conditional behavior

Factory pattern centralizes creation.

### 3. Real Interview-Level Example

Suppose you need different employee types.

```js
function EmployeeFactory(type, name) {
  switch (type) {
    case "developer":
      return {
        name,
        type,
        code() {
          console.log(`${name} writes code`);
        },
      };

    case "designer":
      return {
        name,
        type,
        design() {
          console.log(`${name} creates UI designs`);
        },
      };

    default:
      throw new Error("Unknown employee type");
  }
}

const dev = EmployeeFactory("developer", "Alice");
const designer = EmployeeFactory("designer", "Bob");

dev.code();
designer.design();
```

### 4. Factory Pattern Using Classes

Factories are often implemented with classes in modern JavaScript.

```js
class Car {
  constructor(model) {
    this.model = model;
  }
}

class Bike {
  constructor(model) {
    this.model = model;
  }
}

class VehicleFactory {
  createVehicle(type, model) {
    switch (type) {
      case "car":
        return new Car(model);

      case "bike":
        return new Bike(model);

      default:
        throw new Error("Invalid vehicle type");
    }
  }
}

const factory = new VehicleFactory();

const car = factory.createVehicle("car", "Tesla");
const bike = factory.createVehicle("bike", "Yamaha");

console.log(car);
console.log(bike);
```

### 5. Factory Pattern vs Constructor Function

#### Constructor Function

```js
function User(name) {
  this.name = name;
}

const u1 = new User("John");
```

##### Characteristics

- Requires `new`
- Creates instances from same blueprint
- Uses prototypes

#### Factory Function

```js
function createUser(name) {
  return { name };
}

const u1 = createUser("John");
```

##### Characteristics

- No `new`
- Simpler syntax
- Easier encapsulation
- More flexible

### 6. Factory Pattern + Prototypes (Memory Efficient)

One downside of naive factories:

```js
function createUser(name) {
  return {
    name,
    greet() {
      console.log("Hello");
    },
  };
}
```

Each object gets its own `greet()` copy.

Better approach:

```js
const userMethods = {
  greet() {
    console.log(`Hello ${this.name}`);
  },
};

function createUser(name) {
  const user = Object.create(userMethods);
  user.name = name;
  return user;
}

const u1 = createUser("John");
const u2 = createUser("Sarah");

u1.greet();
```

Now methods are shared through the prototype chain.

### 7. Factory Pattern in Real Applications

Common real-world usage:

#### UI Component Creation

```js
function createButton(type) {
  if (type === "primary") {
    return document.createElement("button");
  }
}
```

#### API Client Creation

```js
function createApiClient(env) {
  const baseUrl =
    env === "production" ? "https://api.prod.com" : "https://api.dev.com";

  return {
    get(path) {
      return fetch(baseUrl + path);
    },
  };
}
```

### 8. Factory Pattern vs Class

| Feature                  | Factory Function | Class       |
| ------------------------ | ---------------- | ----------- |
| Uses `new`               | No               | Yes         |
| Simpler syntax           | Yes              | Moderate    |
| Encapsulation            | Excellent        | Good        |
| Prototype support        | Manual           | Automatic   |
| Private data via closure | Easy             | Harder      |
| Best for                 | Flexible objects | OOP systems |

### 9. Advanced Factory with Private Variables

Factories work very well with closures.

```js
function createCounter() {
  let count = 0;

  return {
    increment() {
      count++;
      return count;
    },

    getCount() {
      return count;
    },
  };
}

const counter = createCounter();

console.log(counter.increment());
console.log(counter.increment());
console.log(counter.getCount());
```

#### Why Important?

`count` is private and inaccessible directly.

```js
console.log(counter.count); // undefined
```

This is a major advantage over plain objects/classes.

### 10. Common Interview Follow-Up Questions

#### Q1: Is Factory Pattern same as Factory Method Pattern?

No.

##### Factory Pattern

Usually a simple function/class creating objects.

##### Factory Method Pattern

A more formal OOP design pattern where subclasses decide object creation.

#### Q2: Why are factory functions popular in modern JS?

Because they:

- Avoid `this` confusion
- Avoid mandatory `new`
- Work naturally with closures
- Are easier in functional programming styles

#### Q3: What are disadvantages?

##### Memory Overhead

Methods recreated per object unless prototypes/shared methods used.

##### Less Clear Type Relationships

`instanceof` may not work as expected.

Example:

```js
function createUser() {
  return {};
}

const u = createUser();

console.log(u instanceof createUser); // false
```

### 11. Best Practices

#### Prefer Factories When

- You need encapsulation
- You need flexible object shapes
- You want private state
- You want composition over inheritance

#### Prefer Classes When

- You need inheritance hierarchies
- You need `instanceof`
- You work in large OOP architectures

### 12. Summary

The Factory Pattern is a creational design pattern that abstracts and centralizes object creation logic. In JavaScript, it is commonly implemented using factory functions or factory classes. It improves maintainability, encapsulation, and flexibility while reducing duplication. Modern JavaScript often favors factory functions because they integrate naturally with closures and functional programming patterns, though classes remain useful for prototype-based inheritance and structured OOP systems.

## Question 2. How to implement a module pattern in JavaScript

The **Module Pattern** in JavaScript is a design pattern used to create **encapsulated, private, and reusable code units**. It helps you avoid polluting the global scope while exposing only a controlled public API.

---

# 1. Direct Answer

The Module Pattern is implemented using **closures** (or ES Modules today) to create private variables/functions and expose only selected members via a returned object or export statement.

Classic implementation:

```js
const Module = (function () {
  // private state
  let count = 0;

  function privateMethod() {
    return "I am private";
  }

  // public API
  return {
    increment() {
      count++;
      return count;
    },

    getCount() {
      return count;
    },
  };
})();

Module.increment();
Module.getCount();
```

---

# 2. Core Idea of Module Pattern

The module pattern is built on:

- **Closures** → to preserve private state
- **IIFE (Immediately Invoked Function Expression)** → to create isolated scope
- **Returned object** → to expose public methods

### Key Goal:

> “Expose only what is necessary, hide everything else.”

---

# 3. Step-by-Step Breakdown

## Step 1: Create Private Scope using IIFE

```js
(function () {
  // everything here is private
})();
```

This runs immediately and creates a **separate scope**.

---

## Step 2: Add Private Variables

```js
const Module = (function () {
  let secret = "hidden data";
})();
```

`secret` cannot be accessed from outside.

---

## Step 3: Return Public API

```js
const Module = (function () {
  let secret = "hidden data";

  return {
    getSecret() {
      return secret;
    },
  };
})();
```

---

# 4. Real Interview Example (Counter Module)

```js
const CounterModule = (function () {
  let count = 0;

  function validate(value) {
    return typeof value === "number";
  }

  return {
    increment() {
      count++;
      return count;
    },

    decrement() {
      count--;
      return count;
    },

    reset() {
      count = 0;
    },

    set(value) {
      if (validate(value)) {
        count = value;
      }
    },

    get() {
      return count;
    },
  };
})();

console.log(CounterModule.increment()); // 1
console.log(CounterModule.increment()); // 2
console.log(CounterModule.get()); // 2
```

### Why this is powerful:

- `count` is private
- `validate` is private helper
- Only controlled access is exposed

---

# 5. Module Pattern Variations

## 5.1 Revealing Module Pattern

Instead of defining functions inside returned object, you map them explicitly.

```js
const UserModule = (function () {
  let name = "John";

  function setName(newName) {
    name = newName;
  }

  function getName() {
    return name;
  }

  return {
    setName,
    getName,
  };
})();
```

### Benefit:

- Cleaner separation of logic and API exposure

---

## 5.2 Augmented Module Pattern

Used when extending existing modules.

```js
const Module = (function (mod) {
  mod.newFeature = function () {
    return "new feature added";
  };

  return mod;
})(Module || {});
```

---

# 6. Module Pattern vs Global Variables

## Without Module Pattern

```js
let count = 0;

function increment() {
  count++;
}
```

### Problems:

- Global pollution
- Risk of accidental modification
- Hard to scale

---

## With Module Pattern

- Encapsulation
- Controlled access
- No global leaks

---

# 7. Module Pattern vs ES Modules (Modern JS)

Today, we mostly use **ES Modules** instead of classic module pattern.

## ES Module Example

```js
// counter.js
let count = 0;

export function increment() {
  return ++count;
}

export function getCount() {
  return count;
}
```

```js
import { increment, getCount } from "./counter.js";
```

---

## Key Differences

| Feature         | Module Pattern | ES Modules      |
| --------------- | -------------- | --------------- |
| Scope isolation | IIFE closure   | Built-in        |
| Loading         | Manual         | Native          |
| Tree shaking    | No             | Yes             |
| Async loading   | No             | Yes             |
| Standardization | Pre-ES6        | Modern standard |

---

# 8. Why Module Pattern Still Asked in Interviews

Even though ES Modules exist, interviewers ask this because it tests:

- Understanding of **closures**
- Scope isolation
- Encapsulation principles
- Pre-ES6 JavaScript design

---

# 9. Common Pitfalls

## ❌ Mistake 1: Forgetting closure retention

```js
const Module = (function () {
  let secret = "data";

  return {
    get: function () {
      return secret;
    },
  };
})();
```

Works fine, but if you reassign incorrectly:

```js
secret = null; // breaks encapsulation if leaked globally
```

---

## ❌ Mistake 2: Mutating exposed objects

```js
const Module = (function () {
  const state = { count: 0 };

  return {
    state, // ❌ exposing internal reference
  };
})();
```

Now external code can mutate it:

```js
Module.state.count = 999;
```

---

## ✔ Fix: return controlled API

```js
return {
  getCount() {
    return state.count;
  },
};
```

---

# 10. Advanced Insight (Senior-Level)

The module pattern is essentially:

> A manually constructed closure-based encapsulation system that mimics private scope in JavaScript before ES6 classes and modules existed.

It demonstrates:

- Lexical scoping
- Closure persistence
- Data hiding (encapsulation principle in OOP)

---

# 11. Interview Summary

The Module Pattern in JavaScript uses IIFEs and closures to create private state and expose only a controlled public API. It prevents global namespace pollution and enables encapsulation. While largely replaced by ES Modules in modern development, it remains a fundamental concept for understanding closures, scope, and pre-ES6 architectural patterns in JavaScript.

## Question 3. How to create a proxy to validate object properties

## Direct answer

You can create a **Proxy** in JavaScript and use the `set` (and optionally `get`) trap to validate property assignments before they are written to the target object.

---

# 1. Basic Proxy for Property Validation

A Proxy lets you intercept operations on an object. For validation, the key trap is:

- `set(target, property, value)` → runs whenever a property is assigned

### Example: simple validation proxy

```js
const user = {
  name: "John",
  age: 25,
};

const validator = {
  set(target, prop, value) {
    if (prop === "age") {
      if (typeof value !== "number" || value < 0) {
        throw new TypeError("Age must be a positive number");
      }
    }

    if (prop === "name") {
      if (typeof value !== "string" || value.length < 2) {
        throw new TypeError("Name must be a valid string");
      }
    }

    target[prop] = value;
    return true; // required for successful assignment
  },
};

const proxiedUser = new Proxy(user, validator);

// Valid
proxiedUser.age = 30;
proxiedUser.name = "Alice";

console.log(proxiedUser);

// Invalid
proxiedUser.age = -5; // throws error
```

---

# 2. How It Works (Interview Explanation)

A Proxy wraps an object and intercepts fundamental operations like:

- Property access (`get`)
- Property assignment (`set`)
- Property deletion (`deleteProperty`)
- Function calls (`apply`)
- Construction (`construct`)

For validation, we primarily use:

### `set(target, prop, value, receiver)`

When you do:

```js
proxiedUser.age = 40;
```

Internally:

1. Proxy intercepts assignment
2. `set()` trap runs
3. Validation logic executes
4. If valid → assign to target
5. If invalid → throw error or reject

---

# 3. Advanced Validation Proxy (Schema-Based)

A more realistic interview-level approach is schema validation.

```js
const schema = {
  name: (v) => typeof v === "string" && v.length > 2,
  age: (v) => typeof v === "number" && v >= 0 && v <= 120,
};

function createValidatedObject(target, schema) {
  return new Proxy(target, {
    set(obj, prop, value) {
      if (schema[prop]) {
        const isValid = schema[prop](value);

        if (!isValid) {
          throw new Error(`Invalid value for ${prop}: ${value}`);
        }
      }

      obj[prop] = value;
      return true;
    },
  });
}

const user = createValidatedObject({}, schema);

user.name = "John"; // OK
user.age = 30; // OK

user.age = -10; // Error
```

---

# 4. Adding Get Validation / Defaults

You can also control reads using `get`.

```js
const safeUser = new Proxy(
  {},
  {
    set(target, prop, value) {
      if (prop === "age" && value < 0) {
        throw new Error("Invalid age");
      }
      target[prop] = value;
      return true;
    },

    get(target, prop) {
      return prop in target ? target[prop] : "NOT FOUND";
    },
  },
);

safeUser.name = "Alice";

console.log(safeUser.name); // Alice
console.log(safeUser.age); // NOT FOUND
```

---

# 5. Real-World Use Cases

Proxies are commonly used for:

### 1. Validation layers

- Form input validation
- API payload validation

### 2. State management (Vue 3 uses Proxy)

- Reactivity tracking

### 3. Security restrictions

- Prevent invalid mutations

### 4. Logging / debugging

```js
const debugProxy = new Proxy(
  {},
  {
    set(target, prop, value) {
      console.log(`Setting ${prop} = ${value}`);
      target[prop] = value;
      return true;
    },
  },
);
```

---

# 6. Important Edge Cases (Interview Traps)

## ❌ Forgetting `return true`

```js
set(target, prop, value) {
  target[prop] = value;
}
```

### Problem:

In strict mode, this may fail silently or throw.

### ✔ Correct:

```js
return true;
```

---

## ❌ Not handling inherited properties

Proxy only affects the wrapped object, not prototypes.

---

## ❌ Performance overhead

Proxies are powerful but:

- Slight runtime cost
- Not ideal for hot loops or high-frequency updates

---

# 7. Proxy vs Object.defineProperty

| Feature              | Proxy     | Object.defineProperty |
| -------------------- | --------- | --------------------- |
| Intercepts all props | ✔         | ❌ (must define each) |
| Dynamic properties   | ✔         | ❌                    |
| Array handling       | Easy      | Complex               |
| Modern usage         | Preferred | Legacy (Vue 2 style)  |

---

# 8. Senior-Level Summary

A Proxy in JavaScript allows you to intercept and redefine fundamental operations on an object. By using the `set` trap, you can enforce validation rules before properties are assigned. This makes Proxy a powerful mechanism for building reactive systems, validation layers, and controlled state management. Unlike `Object.defineProperty`, Proxy works dynamically for all properties, making it more flexible and scalable for modern JavaScript architectures.

## Question 4. How to use Reflect API to manipulate objects

## Direct Answer

The **Reflect API** is a built-in JavaScript object that provides methods for performing object operations (get, set, delete, define properties, construct objects, etc.) in a more consistent and functional way than using operators or `Object` methods directly.

It is especially useful with **Proxies**, where `Reflect` allows you to forward operations to the target object while preserving default behavior.

---

# What is Reflect?

Introduced in **ES6**, `Reflect` is a static object (similar to `Math`) that contains methods corresponding to many JavaScript internal object operations.

```js
console.log(typeof Reflect);
```

Output:

```js
"object";
```

You cannot instantiate it:

```js
new Reflect(); // TypeError
```

---

# Why Was Reflect Introduced?

Before ES6, object operations were performed using:

```js
obj.name = "John";
delete obj.name;

Object.defineProperty(obj, "age", {
  value: 25,
});
```

The APIs were inconsistent:

- Some returned booleans
- Some threw errors
- Some used operators

Reflect provides a unified functional interface:

```js
Reflect.set(obj, "name", "John");
Reflect.deleteProperty(obj, "name");
Reflect.defineProperty(obj, "age", {
  value: 25,
});
```

---

# 1. Reflect.get()

Equivalent to property access.

```js
const user = {
  name: "John",
};

console.log(Reflect.get(user, "name"));
```

Output:

```js
John;
```

Equivalent to:

```js
user.name;
```

---

# 2. Reflect.set()

Sets a property value.

```js
const user = {};

Reflect.set(user, "name", "Alice");

console.log(user);
```

Output:

```js
{
  name: "Alice";
}
```

Equivalent to:

```js
user.name = "Alice";
```

---

# 3. Reflect.has()

Checks property existence.

```js
const user = {
  name: "John",
};

console.log(Reflect.has(user, "name"));
```

Output:

```js
true;
```

Equivalent to:

```js
"name" in user;
```

---

# 4. Reflect.deleteProperty()

Deletes properties safely.

```js
const user = {
  name: "John",
};

Reflect.deleteProperty(user, "name");

console.log(user);
```

Output:

```js
{
}
```

Equivalent to:

```js
delete user.name;
```

---

# 5. Reflect.ownKeys()

Returns all keys.

```js
const obj = {
  a: 1,
  b: 2,
};

console.log(Reflect.ownKeys(obj));
```

Output:

```js
["a", "b"];
```

Unlike:

```js
Object.keys();
```

it also includes:

- Non-enumerable keys
- Symbols

---

# 6. Reflect.defineProperty()

Defines properties.

```js
const obj = {};

Reflect.defineProperty(obj, "id", {
  value: 101,
  writable: false,
});

console.log(obj.id);
```

Output:

```js
101;
```

### Difference from Object.defineProperty

```js
Object.defineProperty(...)
```

returns the object.

```js
Reflect.defineProperty(...)
```

returns a boolean.

```js
const success = Reflect.defineProperty(obj, "x", {
  value: 10,
});

console.log(success);
```

Output:

```js
true;
```

---

# 7. Reflect.getOwnPropertyDescriptor()

```js
const user = {
  name: "John",
};

console.log(Reflect.getOwnPropertyDescriptor(user, "name"));
```

Output:

```js
{
  value: 'John',
  writable: true,
  enumerable: true,
  configurable: true
}
```

---

# 8. Reflect.preventExtensions()

Prevents adding new properties.

```js
const obj = {};

Reflect.preventExtensions(obj);

console.log(Reflect.isExtensible(obj));
```

Output:

```js
false;
```

---

# 9. Reflect.construct()

Programmatic version of `new`.

```js
class User {
  constructor(name) {
    this.name = name;
  }
}

const user = Reflect.construct(User, ["John"]);

console.log(user);
```

Output:

```js
User { name: 'John' }
```

Equivalent to:

```js
new User("John");
```

---

# 10. Reflect.apply()

Programmatic function invocation.

```js
function greet(name) {
  return `Hello ${name}`;
}

const result = Reflect.apply(greet, null, ["John"]);

console.log(result);
```

Output:

```js
Hello John
```

Equivalent to:

```js
greet.call(null, "John");
```

---

# Reflect + Proxy (Most Important Interview Topic)

Reflect is heavily used inside Proxy handlers.

Without Reflect:

```js
const proxy = new Proxy(
  {},
  {
    get(target, prop) {
      console.log(`Reading ${prop}`);
      return target[prop];
    },
  },
);
```

With Reflect:

```js
const proxy = new Proxy(
  {},
  {
    get(target, prop, receiver) {
      console.log(`Reading ${prop}`);

      return Reflect.get(target, prop, receiver);
    },
  },
);
```

### Why?

Reflect preserves JavaScript's default internal behavior.

---

# Example: Validation Proxy

```js
const user = {};

const proxy = new Proxy(user, {
  set(target, prop, value) {
    if (prop === "age" && value < 0) {
      throw new Error("Age cannot be negative");
    }

    return Reflect.set(target, prop, value);
  },
});

proxy.age = 30;

console.log(proxy.age);
```

Output:

```js
30;
```

---

# Common Interview Question

### Why use Reflect inside Proxy traps?

Because:

```js
target[prop] = value;
```

may not preserve all built-in semantics.

Instead:

```js
Reflect.set(target, prop, value);
```

delegates the operation to JavaScript's internal object behavior.

This avoids subtle bugs involving:

- inheritance
- getters/setters
- receivers
- prototype chains

---

# Reflect vs Object Methods

| Operation       | Traditional               | Reflect                    |
| --------------- | ------------------------- | -------------------------- |
| Get property    | `obj.x`                   | `Reflect.get()`            |
| Set property    | `obj.x = 1`               | `Reflect.set()`            |
| Delete property | `delete obj.x`            | `Reflect.deleteProperty()` |
| Check existence | `"x" in obj`              | `Reflect.has()`            |
| Define property | `Object.defineProperty()` | `Reflect.defineProperty()` |
| Create instance | `new Constructor()`       | `Reflect.construct()`      |
| Call function   | `fn.call()`               | `Reflect.apply()`          |

---

# Best Practices

### Use Reflect when:

✅ Writing Proxy handlers

✅ Building frameworks/libraries

✅ Need functional object operations

✅ Need consistent return values

---

### Avoid unnecessary Reflect usage

Instead of:

```js
Reflect.get(user, "name");
```

normal code should usually be:

```js
user.name;
```

Reflect shines in meta-programming, not everyday property access.

---

# Senior-Level Interview Summary

The **Reflect API** is a standardized set of methods that expose JavaScript's internal object operations through a functional interface. It provides consistent behavior for getting, setting, deleting, defining properties, invoking functions, and constructing objects. Its primary use case is **meta-programming**, particularly when combined with **Proxy**, where `Reflect` allows traps to forward operations while preserving JavaScript's default semantics, prototype behavior, getters/setters, and receiver handling. This makes Reflect a fundamental tool for advanced object manipulation and framework development.

## Question 5. How to implement a reactive object (like Vue.js reactivity system)

## Direct Answer

A simple Vue-like reactivity system can be implemented using a **Proxy** to intercept property access (`get`) and updates (`set`), along with a dependency-tracking mechanism that records which functions depend on which properties and re-runs them when those properties change.

Modern **Vue 3** uses `Proxy`-based reactivity internally, while Vue 2 relied on `Object.defineProperty()`.

---

# Core Idea

A reactive system has three parts:

1. **Track** dependencies when properties are read
2. **Trigger** updates when properties change
3. **Effect** functions that automatically re-run when dependencies change

Example:

```js
state.count++;
```

Should automatically update:

```js
console.log(`Count: ${state.count}`);
```

without manually calling the logging function again.

---

# Step 1: Basic Reactive Object with Proxy

Intercept reads and writes:

```js
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      console.log(`Getting ${key}`);
      return Reflect.get(target, key, receiver);
    },

    set(target, key, value, receiver) {
      console.log(`Setting ${key} = ${value}`);
      return Reflect.set(target, key, value, receiver);
    },
  });
}

const state = reactive({
  count: 0,
});

state.count;
state.count = 1;
```

Output:

```js
Getting count
Setting count = 1
```

This intercepts operations but doesn't yet react to changes.

---

# Step 2: Dependency Tracking

We need to know:

> Which function depends on which property?

We'll maintain:

```js
target -> property -> effects
```

Structure:

```js
WeakMap
  └── target
       └── Map
            └── property
                 └── Set(effect functions)
```

---

# Step 3: Implement `track()`

```js
const targetMap = new WeakMap();

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

Whenever a property is read, we'll record the currently running effect.

---

# Step 4: Implement `trigger()`

When a property changes:

```js
function trigger(target, key) {
  const depsMap = targetMap.get(target);

  if (!depsMap) return;

  const effects = depsMap.get(key);

  if (effects) {
    effects.forEach((effect) => effect());
  }
}
```

All dependent functions re-execute.

---

# Step 5: Create Effect Function

```js
function effect(fn) {
  activeEffect = fn;
  fn();
  activeEffect = null;
}
```

This registers the currently running function.

---

# Step 6: Connect Everything

```js
const targetMap = new WeakMap();

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

function trigger(target, key) {
  const depsMap = targetMap.get(target);

  if (!depsMap) return;

  const dep = depsMap.get(key);

  if (dep) {
    dep.forEach((effect) => effect());
  }
}

function effect(fn) {
  activeEffect = fn;
  fn();
  activeEffect = null;
}

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

# Usage Example

```js
const state = reactive({
  count: 0,
});

effect(() => {
  console.log(`Count is ${state.count}`);
});

state.count++;
state.count++;
```

Output:

```js
Count is 0
Count is 1
Count is 2
```

No manual update calls needed.

---

# How Vue 3 Works Internally

Very simplified:

```js
const state = reactive({
  count: 0,
});

effect(() => {
  render(state.count);
});
```

Internally:

```text
effect()
   ↓
track(count)
   ↓
dependency recorded
   ↓
count changes
   ↓
trigger(count)
   ↓
effect reruns
   ↓
UI updates
```

---

# Supporting Nested Objects

Naive implementation:

```js
const state = reactive({
  user: {
    name: "John",
  },
});
```

Problem:

```js
state.user.name = "Alice";
```

won't be reactive because `user` isn't proxied.

### Solution

Recursively wrap objects:

```js
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      track(target, key);

      const value = Reflect.get(target, key, receiver);

      if (value && typeof value === "object") {
        return reactive(value);
      }

      return value;
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

# Implementing Computed Values

A simplified computed:

```js
function computed(getter) {
  let value;

  effect(() => {
    value = getter();
  });

  return {
    get value() {
      return value;
    },
  };
}
```

Usage:

```js
const state = reactive({
  count: 5,
});

const doubled = computed(() => state.count * 2);

console.log(doubled.value);

state.count++;

console.log(doubled.value);
```

Output:

```js
10;
12;
```

---

# Implementing Watchers

```js
function watch(getter, callback) {
  let oldValue = getter();

  effect(() => {
    const newValue = getter();

    if (newValue !== oldValue) {
      callback(newValue, oldValue);
      oldValue = newValue;
    }
  });
}
```

Usage:

```js
watch(
  () => state.count,
  (newVal, oldVal) => {
    console.log(`${oldVal} -> ${newVal}`);
  },
);
```

---

# Common Pitfalls

## 1. Infinite Loops

```js
effect(() => {
  state.count++;
});
```

This updates the same dependency it tracks:

```text
effect
 ↓
count++
 ↓
trigger
 ↓
effect
 ↓
count++
 ...
```

Infinite recursion.

---

## 2. Duplicate Effects

Using arrays:

```js
deps.push(effect);
```

can create duplicates.

Use:

```js
Set;
```

instead.

---

## 3. Memory Leaks

Without cleanup:

```js
effect(() => {
  console.log(state.count);
});
```

Old dependencies may remain forever.

Real frameworks remove stale dependencies during re-execution.

---

## 4. Deep Reactivity Cost

Recursively proxying large objects can be expensive.

Vue uses lazy proxy creation and caching to optimize this.

---

# Vue 2 vs Vue 3 Reactivity

| Feature            | Vue 2                   | Vue 3   |
| ------------------ | ----------------------- | ------- |
| Core API           | `Object.defineProperty` | `Proxy` |
| Detect new props   | ❌                      | ✅      |
| Array interception | Complex                 | Simple  |
| Performance        | Good                    | Better  |
| Nested handling    | Manual limitations      | Natural |

---

# Senior-Level Interview Summary

A Vue-like reactivity system is built around **dependency tracking** and **change notification**. Using a `Proxy`, property reads are intercepted to **track** active effects, while property writes **trigger** those effects to re-run. Internally, dependencies are typically stored in a `WeakMap → Map → Set` structure. Modern frameworks such as **Vue 3** use this pattern to implement reactive state, computed properties, watchers, and automatic UI updates while preserving performance through lazy tracking, batching, and dependency cleanup.

## Question 6. How to implement observer pattern in JavaScript

## Direct Answer

The **Observer Pattern** is a behavioral design pattern where one object (the **Subject**) maintains a list of dependent objects (**Observers**) and automatically notifies them whenever its state changes.

In JavaScript, it is commonly implemented using:

- `subscribe()` → register observers
- `unsubscribe()` → remove observers
- `notify()` → inform all observers of changes

---

# Real-World Analogy

Think of a YouTube channel:

- **Channel** = Subject
- **Subscribers** = Observers
- **New Video Upload** = Event
- **Notification** = Observer update

```text
Subject
   ↓
Observer A
Observer B
Observer C
```

When the subject changes, all observers are notified.

---

# 1. Basic Observer Pattern Implementation

```js
class Subject {
  constructor() {
    this.observers = [];
  }

  subscribe(observer) {
    this.observers.push(observer);
  }

  unsubscribe(observer) {
    this.observers = this.observers.filter((obs) => obs !== observer);
  }

  notify(data) {
    this.observers.forEach((observer) => observer(data));
  }
}
```

---

# Usage

```js
const subject = new Subject();

function observer1(data) {
  console.log("Observer 1:", data);
}

function observer2(data) {
  console.log("Observer 2:", data);
}

subject.subscribe(observer1);
subject.subscribe(observer2);

subject.notify("Hello Observers!");
```

Output:

```js
Observer 1: Hello Observers!
Observer 2: Hello Observers!
```

---

# 2. Observer Pattern with State

A more realistic implementation:

```js
class Subject {
  constructor() {
    this.observers = [];
    this.state = null;
  }

  subscribe(observer) {
    this.observers.push(observer);
  }

  unsubscribe(observer) {
    this.observers = this.observers.filter((obs) => obs !== observer);
  }

  setState(newState) {
    this.state = newState;
    this.notify();
  }

  notify() {
    this.observers.forEach((observer) => observer(this.state));
  }
}
```

Usage:

```js
const store = new Subject();

store.subscribe((state) => {
  console.log("Component A:", state);
});

store.subscribe((state) => {
  console.log("Component B:", state);
});

store.setState({
  user: "John",
});
```

Output:

```js
Component A: { user: "John" }
Component B: { user: "John" }
```

---

# 3. Object-Oriented Observer Pattern

Instead of functions, observers can be objects.

```js
class Observer {
  constructor(name) {
    this.name = name;
  }

  update(data) {
    console.log(`${this.name} received: ${data}`);
  }
}
```

Subject:

```js
class Subject {
  constructor() {
    this.observers = [];
  }

  subscribe(observer) {
    this.observers.push(observer);
  }

  notify(data) {
    this.observers.forEach((observer) => observer.update(data));
  }
}
```

Usage:

```js
const subject = new Subject();

const obs1 = new Observer("Alice");
const obs2 = new Observer("Bob");

subject.subscribe(obs1);
subject.subscribe(obs2);

subject.notify("New message");
```

Output:

```js
Alice received: New message
Bob received: New message
```

---

# 4. Event Emitter Style (Node.js)

Node.js internally uses an Observer-like pattern.

Simple implementation:

```js
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, listener) {
    if (!this.events[event]) {
      this.events[event] = [];
    }

    this.events[event].push(listener);
  }

  emit(event, data) {
    if (!this.events[event]) return;

    this.events[event].forEach((listener) => listener(data));
  }

  off(event, listener) {
    if (!this.events[event]) return;

    this.events[event] = this.events[event].filter((l) => l !== listener);
  }
}
```

Usage:

```js
const emitter = new EventEmitter();

emitter.on("login", (user) => {
  console.log(`${user} logged in`);
});

emitter.emit("login", "John");
```

Output:

```js
John logged in
```

---

# 5. Observer Pattern vs Pub/Sub Pattern

Interviewers frequently ask this.

## Observer Pattern

```text
Observer → Subject
```

Observers directly know the subject.

```js
subject.subscribe(observer);
```

---

## Publish/Subscribe Pattern

```text
Publisher
    ↓
 Event Bus
    ↓
Subscribers
```

Publisher and subscribers don't know each other.

```js
eventBus.publish("login");
eventBus.subscribe("login");
```

---

### Comparison

| Feature             | Observer         | Pub/Sub         |
| ------------------- | ---------------- | --------------- |
| Direct relationship | Yes              | No              |
| Mediator            | No               | Yes             |
| Coupling            | Higher           | Lower           |
| Scalability         | Moderate         | High            |
| Example             | UI state changes | Message brokers |

---

# 6. Real-World JavaScript Uses

### DOM Events

```js
button.addEventListener("click", handleClick);
```

- Button = Subject
- Handler = Observer

---

### Redux Store

```js
store.subscribe(() => {
  console.log(store.getState());
});
```

Observer pattern in action.

---

### React State Updates

```js
useEffect(() => {
  console.log(count);
}, [count]);
```

Conceptually similar dependency observation.

---

### Vue Reactivity

```js
watch(() => state.count, callback);
```

Observer-based change notification.

---

# Common Pitfalls

## 1. Memory Leaks

```js
subject.subscribe(observer);
```

If observers are never removed:

```js
subject.unsubscribe(observer);
```

they remain in memory.

---

## 2. Duplicate Subscriptions

```js
subject.subscribe(fn);
subject.subscribe(fn);
```

Now notifications occur twice.

Possible fix:

```js
this.observers = new Set();
```

instead of an array.

---

## 3. Slow Notifications

```js
notify() {
  observers.forEach(...)
}
```

Thousands of observers can cause performance issues.

Real frameworks often batch updates.

---

## 4. Observer Errors

One observer throwing:

```js
observer();
throw new Error();
```

can stop notification flow.

Safer:

```js
notify(data) {
  this.observers.forEach(obs => {
    try {
      obs(data);
    } catch (err) {
      console.error(err);
    }
  });
}
```

---

# Advanced Version Using Set

```js
class Subject {
  constructor() {
    this.observers = new Set();
  }

  subscribe(fn) {
    this.observers.add(fn);
  }

  unsubscribe(fn) {
    this.observers.delete(fn);
  }

  notify(data) {
    for (const fn of this.observers) {
      fn(data);
    }
  }
}
```

Benefits:

- No duplicates
- Faster removal
- Cleaner implementation

---

# Senior-Level Interview Summary

The **Observer Pattern** defines a one-to-many dependency between objects so that when a subject changes state, all registered observers are automatically notified. In JavaScript, it is typically implemented with `subscribe`, `unsubscribe`, and `notify` methods. The pattern underpins many modern APIs and frameworks, including DOM events, Redux subscriptions, Vue watchers, and Node.js event emitters. While simple and powerful, production-grade implementations must handle duplicate listeners, memory leaks, error isolation, and performance concerns through cleanup, batching, and efficient data structures such as `Set`.

## Question 7. How to use `WeakRef` to prevent memory leaks

## Direct Answer

`WeakRef` allows you to hold a **weak reference** to an object without preventing it from being garbage collected. It is useful when you want to reference an object (e.g., cache entries, listeners, large DOM-related objects) but don't want that reference alone to keep the object alive and cause memory leaks.

```js
const obj = { data: "large object" };

const weakRef = new WeakRef(obj);

// Later
const value = weakRef.deref();

if (value) {
  console.log(value.data);
} else {
  console.log("Object was garbage collected");
}
```

---

# What Problem Does WeakRef Solve?

Normally, references are **strong references**:

```js
let user = {
  name: "John",
};

const cache = {
  user,
};
```

Memory graph:

```text
cache
  ↓
 user object
```

As long as `cache.user` exists, the object cannot be garbage collected.

This can cause memory leaks in:

- Caches
- Observer systems
- Event listeners
- Large object registries
- DOM-related data structures

---

# Strong Reference vs Weak Reference

## Strong Reference

```js
const cache = new Map();

cache.set("user", {
  name: "John",
});
```

The object stays alive because the Map references it.

---

## Weak Reference

```js
const user = {
  name: "John",
};

const weakRef = new WeakRef(user);
```

The JavaScript engine may garbage collect `user` when no strong references remain.

---

# How `WeakRef` Works

Create:

```js
const ref = new WeakRef(obj);
```

Read:

```js
const value = ref.deref();
```

`deref()` returns:

```js
Object;
```

or

```js
undefined;
```

if the object was collected.

Example:

```js
const user = {
  name: "Alice",
};

const ref = new WeakRef(user);

console.log(ref.deref());
```

Output:

```js
{
  name: "Alice";
}
```

---

# Example: Weak Cache

Without WeakRef:

```js
const cache = new Map();

function store(id, data) {
  cache.set(id, data);
}
```

Problem:

```text
Map
 ↓
Objects remain forever
```

Potential memory leak.

---

With WeakRef:

```js
const cache = new Map();

function store(id, value) {
  cache.set(id, new WeakRef(value));
}

function get(id) {
  const ref = cache.get(id);

  return ref?.deref();
}
```

Usage:

```js
let user = {
  name: "John",
};

store("u1", user);

console.log(get("u1"));

user = null;
```

Now the object becomes eligible for garbage collection.

---

# Example: Observer Pattern Without Leaks

A common issue:

```js
class Subject {
  constructor() {
    this.observers = [];
  }

  subscribe(observer) {
    this.observers.push(observer);
  }
}
```

Problem:

```text
Subject
 ↓
Observer
```

Observers stay alive forever.

---

Using WeakRef:

```js
class Subject {
  constructor() {
    this.observers = [];
  }

  subscribe(observer) {
    this.observers.push(new WeakRef(observer));
  }

  notify(message) {
    this.observers = this.observers.filter((ref) => {
      const observer = ref.deref();

      if (!observer) {
        return false;
      }

      observer.update(message);
      return true;
    });
  }
}
```

Benefits:

- Dead observers disappear naturally
- Less risk of memory leaks

---

# WeakRef + FinalizationRegistry

`WeakRef` is often paired with `FinalizationRegistry`.

It allows cleanup when an object is garbage collected.

```js
const registry = new FinalizationRegistry((key) => {
  console.log(`Cleaning cache entry ${key}`);
});
```

Register:

```js
let user = {
  name: "John",
};

registry.register(user, "user-1");
```

When the object is collected:

```text
Cleaning cache entry user-1
```

may eventually run.

---

# Complete Cache Example

```js
const cache = new Map();

const registry = new FinalizationRegistry((key) => {
  cache.delete(key);
});

function add(key, obj) {
  cache.set(key, new WeakRef(obj));

  registry.register(obj, key);
}
```

This prevents stale cache entries from accumulating.

---

# Why Not Just Use WeakMap?

Interviewers often ask this.

## WeakMap

```js
const map = new WeakMap();

map.set(obj, data);
```

Characteristics:

- Keys must be objects
- Values are strong references
- Automatic cleanup

---

## WeakRef

```js
const ref = new WeakRef(obj);
```

Characteristics:

- Weakly references the object itself
- Can be stored anywhere
- More flexible

---

### Comparison

| Feature            | WeakMap   | WeakRef                                |
| ------------------ | --------- | -------------------------------------- |
| Weak reference     | Key only  | Entire object                          |
| Manual dereference | No        | Yes                                    |
| Cache use          | Limited   | Excellent                              |
| Cleanup support    | Automatic | Often paired with FinalizationRegistry |

---

# Important Caveats

## 1. Never Depend on GC Timing

Bad:

```js
user = null;

console.log(ref.deref());
```

You cannot assume:

```js
undefined;
```

immediately.

Garbage collection timing is non-deterministic.

---

## 2. Object May Disappear Anytime

```js
const value = ref.deref();
```

Always check:

```js
if (value) {
  // use safely
}
```

Never assume it exists.

---

## 3. Avoid Overusing WeakRef

Most applications do **not** need it.

Use it only when:

- Memory retention is a proven issue
- Large caches exist
- Observer/listener systems are long-lived
- Object lifetimes are difficult to manage manually

---

# Real-World Uses

### Browser Frameworks

Framework internals may use weak references for:

- Component registries
- Metadata storage
- Virtual DOM caches

---

### Large Data Caches

```js
Image cache
Query cache
Compiled template cache
```

---

### Observer Systems

```js
Subscribers
Event listeners
Plugin systems
```

---

### IDEs and DevTools

Large object graphs that should not remain permanently in memory.

---

# Common Interview Questions

### Is WeakRef guaranteed to release memory?

No.

It only allows garbage collection.

The engine decides when (or if) collection occurs.

---

### Can primitives be used?

No.

```js
new WeakRef(42);
```

Throws:

```text
TypeError
```

Only objects are allowed.

---

### Does deref() always return the object?

No.

```js
ref.deref();
```

returns:

```js
object;
```

or

```js
undefined;
```

---

# Senior-Level Interview Summary

`WeakRef` provides a weak reference to an object, allowing it to be garbage collected when no strong references remain. It is primarily used in advanced scenarios such as caches, observer registries, metadata stores, and framework internals where retaining objects unnecessarily can cause memory leaks. Because garbage collection is non-deterministic, `WeakRef` should be treated as an optimization tool rather than a core application mechanism. In production systems, it is often combined with `FinalizationRegistry` to clean up associated resources when objects are eventually collected.

## Question 8. Difference between shallow and deep freezing an object

## Direct Answer

- **Shallow freeze** freezes only the top-level properties of an object; nested objects can still be modified.
- **Deep freeze** recursively freezes the entire object graph, making the object and all nested objects completely immutable.

---

# 1. What is `Object.freeze()` (Shallow Freeze)

In JavaScript, `Object.freeze()` performs a **shallow freeze**.

It prevents:

- Adding new properties
- Removing properties
- Modifying existing top-level properties

But it does **NOT freeze nested objects**.

---

## Example: Shallow Freeze

```js id="k8f1qv"
const user = {
  name: "John",
  address: {
    city: "Delhi",
  },
};

Object.freeze(user);

user.name = "Alice"; // ❌ ignored (or fails silently in non-strict mode)
user.age = 30; // ❌ not added

user.address.city = "Mumbai"; // ✅ allowed!

console.log(user.address.city);
```

### Output:

```js id="z9kq2x"
Mumbai;
```

👉 Even though `user` is frozen, `address` is still mutable.

---

# 2. Why is it called "shallow"?

Because it only freezes **one level deep**:

```text id="sh1f3z"
user (frozen)
 ├── name (frozen)
 ├── address (NOT frozen object)
        └── city (still mutable)
```

So only the **references at the top level are protected**, not the objects they point to.

---

# 3. Deep Freeze (Recursive Freezing)

Deep freezing means recursively freezing all nested objects.

---

## Example: Deep Freeze Implementation

```js id="d7k3lz"
function deepFreeze(obj) {
  Object.keys(obj).forEach((key) => {
    const value = obj[key];

    if (value && typeof value === "object") {
      deepFreeze(value);
    }
  });

  return Object.freeze(obj);
}
```

---

## Usage

```js id="v4n8qp"
const user = {
  name: "John",
  address: {
    city: "Delhi",
    pin: {
      code: 110001,
    },
  },
};

deepFreeze(user);

user.address.city = "Mumbai"; // ❌ ignored
user.address.pin.code = 999999; // ❌ ignored

console.log(user);
```

Now everything is immutable.

---

# 4. Key Difference Summary

| Feature                      | Shallow Freeze      | Deep Freeze         |
| ---------------------------- | ------------------- | ------------------- |
| Freezes top-level properties | ✅                  | ✅                  |
| Freezes nested objects       | ❌                  | ✅                  |
| Recursion                    | No                  | Yes                 |
| Performance                  | Faster              | Slower              |
| Use case                     | Simple immutability | Strict immutability |

---

# 5. Memory Structure Explanation

## Shallow freeze

```text id="m8nq0c"
Object.freeze(user)
   ↓
user object is immutable
   ↓
BUT nested objects still live independently
```

---

## Deep freeze

```text id="q1xw7p"
user
 ├── frozen
 ├── address
 │     ├── frozen
 │     └── pin
 │          └── frozen
```

---

# 6. Important Edge Cases

## 1. Cyclic objects (⚠️ problem)

```js id="c2v9kf"
const obj = {};
obj.self = obj;

deepFreeze(obj); // ❌ infinite recursion risk
```

### Fix using WeakSet:

```js id="w1z8qp"
function deepFreeze(obj, seen = new WeakSet()) {
  if (seen.has(obj)) return obj;
  seen.add(obj);

  Object.keys(obj).forEach((key) => {
    const value = obj[key];

    if (value && typeof value === "object") {
      deepFreeze(value, seen);
    }
  });

  return Object.freeze(obj);
}
```

---

## 2. Arrays are objects too

```js id="a9x2ld"
const arr = [1, 2, 3];

Object.freeze(arr);

arr.push(4); // ❌ fails
arr[0] = 100; // ❌ fails
```

But nested objects inside arrays are still mutable unless deep frozen.

---

## 3. Strict mode behavior

In strict mode:

```js id="s1k8dn"
"use strict";

Object.freeze(obj);

obj.x = 10; // ❌ TypeError
```

In non-strict mode:

- fails silently

---

# 7. Real-World Use Cases

## Shallow Freeze

Used when:

- You only need top-level immutability
- Config objects
- Constants

```js id="c7m0qp"
const config = Object.freeze({
  API_URL: "https://api.com",
});
```

---

## Deep Freeze

Used when:

- Redux-like state immutability
- Preventing accidental mutation in shared state
- Security-critical configuration objects

```js id="r9q2ls"
const state = deepFreeze({
  user: {
    name: "John",
  },
});
```

---

# 8. Performance Considerations

## Shallow freeze

- O(1)
- Very fast
- No recursion

## Deep freeze

- O(n)
- Traverses entire object graph
- Can be expensive for large data

👉 Trade-off: **safety vs performance**

---

# 9. Alternative Approaches

Instead of deep freezing, modern JS often uses:

### 1. Immutable data structures

- Immer.js
- Immutable.js

### 2. Copy-on-write updates

```js id="p4t7sk"
const newState = {
  ...state,
  user: {
    ...state.user,
    name: "Alice",
  },
};
```

---

# 10. Interview Summary

Shallow freezing using `Object.freeze()` prevents modification of an object’s immediate properties but does not affect nested objects, making them still mutable. Deep freezing recursively applies `Object.freeze()` to all nested objects, ensuring complete immutability of the entire object graph. While shallow freeze is fast and suitable for simple configuration objects, deep freeze provides stronger guarantees at the cost of performance and recursion complexity. In modern applications, deep freezing is often replaced with immutable update patterns or specialized libraries for better scalability and efficiency.

## Question 9. How to implement a read-only object using Proxy

## Direct Answer

You can implement a **read-only object** using a **Proxy** by intercepting mutation operations (`set`, `deleteProperty`, `defineProperty`, etc.) and preventing them from modifying the target object.

```js
const obj = {
  name: "John",
  age: 30,
};

const readOnly = new Proxy(obj, {
  set() {
    throw new Error("Object is read-only");
  },
});

readOnly.name = "Alice"; // Error
```

Unlike `Object.freeze()`, a Proxy can provide **custom behavior, error messages, logging, and deep read-only protection**.

---

# 1. Basic Read-Only Proxy

The simplest implementation blocks property assignments.

```js
const user = {
  name: "John",
  age: 25,
};

const readOnlyUser = new Proxy(user, {
  get(target, prop, receiver) {
    return Reflect.get(target, prop, receiver);
  },

  set(target, prop, value) {
    throw new Error(`Cannot modify '${String(prop)}'. Object is read-only.`);
  },
});

console.log(readOnlyUser.name);

readOnlyUser.age = 30; // Error
```

Output:

```text
John
Error: Cannot modify 'age'. Object is read-only.
```

---

# 2. Prevent Property Deletion

Without handling `deleteProperty`, users could still remove properties.

```js
const readOnly = new Proxy(user, {
  set() {
    throw new Error("Read-only");
  },

  deleteProperty() {
    throw new Error("Cannot delete properties");
  },
});
```

Example:

```js
delete readOnly.name;
```

Output:

```text
Error: Cannot delete properties
```

---

# 3. Prevent Defining New Properties

Users can also modify objects via:

```js
Object.defineProperty(obj, "role", {
  value: "admin",
});
```

Block it:

```js
const readOnly = new Proxy(user, {
  set() {
    throw new Error("Read-only");
  },

  deleteProperty() {
    throw new Error("Read-only");
  },

  defineProperty() {
    throw new Error("Cannot define properties");
  },
});
```

---

# 4. Complete Read-Only Proxy

```js
function createReadOnly(obj) {
  return new Proxy(obj, {
    get(target, prop, receiver) {
      return Reflect.get(target, prop, receiver);
    },

    set() {
      throw new Error("Object is read-only");
    },

    deleteProperty() {
      throw new Error("Object is read-only");
    },

    defineProperty() {
      throw new Error("Object is read-only");
    },
  });
}
```

Usage:

```js
const user = createReadOnly({
  name: "John",
});

console.log(user.name);

user.name = "Alice"; // Error
```

---

# 5. Problem: Nested Objects Are Still Mutable

Consider:

```js
const user = {
  name: "John",
  address: {
    city: "Delhi",
  },
};
```

Using the previous Proxy:

```js
user.address.city = "Mumbai";
```

still works because only the top-level object is protected.

---

# 6. Deep Read-Only Proxy

A common interview follow-up is implementing recursive protection.

```js
function createReadOnly(obj) {
  return new Proxy(obj, {
    get(target, prop, receiver) {
      const value = Reflect.get(target, prop, receiver);

      if (value && typeof value === "object") {
        return createReadOnly(value);
      }

      return value;
    },

    set() {
      throw new Error("Object is read-only");
    },

    deleteProperty() {
      throw new Error("Object is read-only");
    },

    defineProperty() {
      throw new Error("Object is read-only");
    },
  });
}
```

Usage:

```js
const state = createReadOnly({
  user: {
    profile: {
      name: "John",
    },
  },
});

state.user.profile.name = "Alice";
```

Output:

```text
Error: Object is read-only
```

---

# 7. Avoid Creating Multiple Proxies

The recursive version creates a new Proxy every time a nested object is accessed.

Optimization:

```js
const cache = new WeakMap();

function createReadOnly(obj) {
  if (cache.has(obj)) {
    return cache.get(obj);
  }

  const proxy = new Proxy(obj, {
    get(target, prop, receiver) {
      const value = Reflect.get(target, prop, receiver);

      if (value && typeof value === "object") {
        return createReadOnly(value);
      }

      return value;
    },

    set() {
      throw new Error("Read-only");
    },
  });

  cache.set(obj, proxy);

  return proxy;
}
```

This is similar to techniques used in reactive frameworks.

---

# 8. Proxy Read-Only vs Object.freeze()

## `Object.freeze()`

```js
Object.freeze(user);
```

Pros:

- Native
- Fast
- Simple

Cons:

- Shallow
- No custom behavior
- Silent failures (outside strict mode)

---

## Proxy Read-Only

```js
const readOnly = new Proxy(user, handler);
```

Pros:

- Custom errors
- Deep protection
- Logging
- Validation
- Dynamic behavior

Cons:

- Slight runtime overhead
- More complex

---

# Comparison

| Feature          | Object.freeze | Proxy           |
| ---------------- | ------------- | --------------- |
| Read-only        | ✅            | ✅              |
| Custom errors    | ❌            | ✅              |
| Deep protection  | ❌            | ✅              |
| Logging          | ❌            | ✅              |
| Dynamic behavior | ❌            | ✅              |
| Performance      | Faster        | Slightly slower |

---

# 9. Real-World Uses

### Redux Dev Tools

Prevent accidental state mutations.

```js
state.user.name = "Alice";
```

Throw immediately.

---

### Configuration Objects

```js
const config = createReadOnly({
  API_URL: "https://api.example.com",
});
```

---

### Library APIs

Expose internal state safely.

```js
return createReadOnly(internalState);
```

Consumers can inspect but not modify.

---

# Common Interview Questions

### Why use `Reflect.get()`?

Instead of:

```js
return target[prop];
```

use:

```js
return Reflect.get(target, prop, receiver);
```

because it preserves:

- getters
- prototype chains
- correct `this` binding

---

### Can Proxy make an object truly immutable?

Not completely.

If code still has access to the original target:

```js
obj.name = "Alice";
```

it can bypass the Proxy.

To enforce immutability, expose only the Proxy and keep the target private.

---

# Senior-Level Interview Summary

A read-only object can be implemented using a `Proxy` by intercepting mutation operations such as `set`, `deleteProperty`, and `defineProperty` and rejecting them. Compared to `Object.freeze()`, a Proxy offers greater flexibility, including custom error handling, deep recursive protection, logging, and runtime validation. Production-quality implementations typically combine recursive proxying with `WeakMap` caching to efficiently provide deep read-only views of complex object graphs while preserving normal property access through `Reflect`.

## Question 10. How to implement custom iterables using `[Symbol.iterator]`

## Direct Answer

You implement a **custom iterable in JavaScript** by defining a method on an object using:

```js
[Symbol.iterator]();
```

This method must return an **iterator object** with a `next()` function that returns:

```js
{ value: any, done: boolean }
```

---

# 1. Basic Custom Iterable

### Example: Iterating over a custom range object

```js id="q7v3xk"
const range = {
  start: 1,
  end: 5,

  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;

    return {
      next() {
        if (current <= end) {
          return { value: current++, done: false };
        }
        return { value: undefined, done: true };
      },
    };
  },
};
```

### Usage:

```js id="m1c9zq"
for (const num of range) {
  console.log(num);
}
```

### Output:

```js id="k8d2lp"
1;
2;
3;
4;
5;
```

---

# 2. How It Works (Interview Explanation)

When you use:

```js id="p3k8lm"
for (const x of range)
```

JavaScript internally does:

```js id="v0n8qz"
const iterator = range[Symbol.iterator]();

let result = iterator.next();

while (!result.done) {
  console.log(result.value);
  result = iterator.next();
}
```

So the iterable protocol is:

### Iterable:

Object with `[Symbol.iterator]`

### Iterator:

Object with `.next()`

---

# 3. Custom Iterable Using Class

More structured approach:

```js id="t6r9qp"
class Range {
  constructor(start, end) {
    this.start = start;
    this.end = end;
  }

  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;

    return {
      next() {
        if (current <= end) {
          return { value: current++, done: false };
        }
        return { done: true };
      },
    };
  }
}
```

### Usage:

```js id="x2m9qv"
const r = new Range(3, 7);

for (const n of r) {
  console.log(n);
}
```

---

# 4. Iterable Using Generator (Best Approach)

Generators automatically implement iterators.

```js id="g8k3lp"
function* range(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}
```

### Usage:

```js id="h4n8qz"
for (const num of range(1, 5)) {
  console.log(num);
}
```

### Output:

```js id="l9p3wx"
1;
2;
3;
4;
5;
```

### Why generators are better:

- Less boilerplate
- Automatic iterator creation
- Easier state management

---

# 5. Custom Iterable Over Object Properties

Example: iterate object values

```js id="o2k9xz"
const user = {
  name: "John",
  age: 30,
  city: "Delhi",

  [Symbol.iterator]() {
    const values = Object.values(this);
    let index = 0;

    return {
      next() {
        if (index < values.length) {
          return { value: values[index++], done: false };
        }
        return { done: true };
      },
    };
  },
};
```

### Usage:

```js id="c7m1qz"
for (const value of user) {
  console.log(value);
}
```

---

# 6. Iterating Keys, Values, Entries (Advanced Pattern)

### Values iterator:

```js id="k9x2pl"
[Symbol.iterator]() {
  return Object.values(this)[Symbol.iterator]();
}
```

### Keys iterator:

```js id="z8m1qv"
[Symbol.iterator]() {
  return Object.keys(this)[Symbol.iterator]();
}
```

### Entries iterator:

```js id="n3p8xq"
[Symbol.iterator]() {
  return Object.entries(this)[Symbol.iterator]();
}
```

---

# 7. Real-World Example: Paginated API Iterable

```js id="r5k2xv"
class PaginatedData {
  constructor(fetchPage) {
    this.fetchPage = fetchPage;
    this.page = 1;
    this.hasMore = true;
  }

  [Symbol.iterator]() {
    return {
      next: async () => {
        if (!this.hasMore) {
          return { done: true };
        }

        const data = await this.fetchPage(this.page);

        if (data.length === 0) {
          this.hasMore = false;
          return { done: true };
        }

        this.page++;

        return { value: data, done: false };
      },
    };
  }
}
```

👉 Note: This is conceptual; async iterables should use `Symbol.asyncIterator`.

---

# 8. Async Iterables (Important Interview Topic)

For asynchronous iteration:

```js id="u8v3qp"
const asyncRange = {
  start: 1,
  end: 3,

  [Symbol.asyncIterator]() {
    let current = this.start;

    return {
      async next() {
        if (current <= this.end) {
          return {
            value: await Promise.resolve(current++),
            done: false,
          };
        }

        return { done: true };
      },
    };
  },
};
```

### Usage:

```js id="m0q8xz"
(async () => {
  for await (const num of asyncRange) {
    console.log(num);
  }
})();
```

---

# 9. Iterable Protocol vs Iterator Protocol

| Concept  | Meaning                   |
| -------- | ------------------------- |
| Iterable | Has `[Symbol.iterator]()` |
| Iterator | Has `.next()` method      |

---

# 10. Common Pitfalls

## ❌ Returning wrong structure

```js id="p9x2mq"
next() {
  return current++; // wrong
}
```

Must be:

```js id="x7q1mz"
next() {
  return { value: current++, done: false };
}
```

---

## ❌ Forgetting `Symbol.iterator`

```js id="t1p9xz"
const obj = {
  next() {},
};
```

This is NOT iterable.

---

## ❌ Sharing iterator state incorrectly

Bad:

```js id="a3k8qp"
[Symbol.iterator]() {
  let i = 0;

  return this; // ❌ wrong
}
```

Each iteration must return a **fresh iterator**.

---

# 11. Real-World Uses

Custom iterables are used in:

### 1. Arrays (built-in)

```js id="z9x2qp"
[1, 2, 3][Symbol.iterator]();
```

### 2. Strings

```js id="m7p3xz"
for (const char of "hello") {
}
```

### 3. DOM collections

```js id="k4x9qp"
document.querySelectorAll("div");
```

### 4. Framework internals

- React fiber traversal
- Vue reactivity iteration
- Graph traversal systems

---

# 12. Senior-Level Interview Summary

Custom iterables in JavaScript are implemented using the `[Symbol.iterator]` method, which returns an iterator object with a `next()` function following the iterator protocol. This allows objects to be used with `for...of`, spread syntax, and other iteration-based constructs. While manual iterator implementation provides full control over iteration behavior, generator functions offer a cleaner and more maintainable alternative by automatically handling state and iteration logic. For asynchronous data sources, `Symbol.asyncIterator` enables asynchronous iteration using `for await...of`, making iterables a foundational concept in modern JavaScript architecture and data flow design.

## Question 11. How to implement async iterables using `[Symbol.asyncIterator]`

## Direct Answer

You implement an **async iterable** in JavaScript by defining the `[Symbol.asyncIterator]()` method on an object. This method must return an **async iterator**, where the `next()` method returns a **Promise** that resolves to:

```js
{ value: any, done: boolean }
```

This allows the object to be consumed using:

```js
for await (const item of iterable)
```

---

# 1. Basic Async Iterable

### Simple example: async number range

```js id="k2v8qz"
const asyncRange = {
  start: 1,
  end: 3,

  [Symbol.asyncIterator]() {
    let current = this.start;

    return {
      async next() {
        if (current <= this.end) {
          return {
            value: await Promise.resolve(current++),
            done: false,
          };
        }

        return { done: true };
      },
    };
  },
};
```

### Usage:

```js id="m9x2qp"
(async () => {
  for await (const num of asyncRange) {
    console.log(num);
  }
})();
```

### Output:

```js id="a8q1lz"
1;
2;
3;
```

---

# 2. How Async Iteration Works Internally

When you write:

```js id="p3k8mz"
for await (const value of asyncIterable)
```

JavaScript internally does:

```js id="x7q2mz"
const iterator = asyncIterable[Symbol.asyncIterator]();

while (true) {
  const result = await iterator.next();

  if (result.done) break;

  console.log(result.value);
}
```

So async iteration is basically:

> Iterator + Promises + await loop

---

# 3. Async Iterable with Delays

### Example: simulating streaming data

```js id="v1k8qz"
const stream = {
  data: ["A", "B", "C"],

  [Symbol.asyncIterator]() {
    let index = 0;

    return {
      async next() {
        if (index < stream.data.length) {
          await new Promise((resolve) => setTimeout(resolve, 1000));

          return {
            value: stream.data[index++],
            done: false,
          };
        }

        return { done: true };
      },
    };
  },
};
```

### Usage:

```js id="z9k3qp"
(async () => {
  for await (const item of stream) {
    console.log(item);
  }
})();
```

---

# 4. Async Iterable Using Async Generator (Best Practice)

Instead of manually writing `next()`, use **async generators**.

### Cleaner implementation:

```js id="g7x9qp"
async function* asyncRange(start, end) {
  for (let i = start; i <= end; i++) {
    await new Promise((resolve) => setTimeout(resolve, 500));

    yield i;
  }
}
```

### Usage:

```js id="h2m8qp"
(async () => {
  for await (const num of asyncRange(1, 3)) {
    console.log(num);
  }
})();
```

---

# 5. Async Iterable Over API Calls (Real-World Example)

### Simulating paginated API

```js id="r8k3mz"
function createPaginatedFetcher(fetchPage) {
  return {
    page: 1,
    hasMore: true,

    [Symbol.asyncIterator]() {
      return {
        next: async () => {
          if (!this.hasMore) {
            return { done: true };
          }

          const data = await fetchPage(this.page);

          if (!data.length) {
            this.hasMore = false;
            return { done: true };
          }

          this.page++;

          return {
            value: data,
            done: false,
          };
        },
      };
    },
  };
}
```

### Usage:

```js id="n4q8xz"
const fetchPage = async (page) => {
  return page <= 3 ? [`page-${page}`] : [];
};

(async () => {
  const iterable = createPaginatedFetcher(fetchPage);

  for await (const page of iterable) {
    console.log(page);
  }
})();
```

---

# 6. Async Iterable vs Sync Iterable

| Feature       | Iterable            | Async Iterable             |
| ------------- | ------------------- | -------------------------- |
| Method        | `[Symbol.iterator]` | `[Symbol.asyncIterator]`   |
| next() return | `{ value, done }`   | `Promise<{ value, done }>` |
| Consumption   | `for...of`          | `for await...of`           |
| Use case      | Sync data           | Async data (API, streams)  |

---

# 7. Common Pitfalls

## ❌ Forgetting `await` in loop

```js id="p1x7mz"
for (const item of asyncIterable) {
} // ❌ wrong
```

Must be:

```js id="k8q2mz"
for await (const item of asyncIterable) {
}
```

---

## ❌ Returning non-Promise in async iterator

```js id="v4m9qp"
next() {
  return { value: 1, done: false }; // works but defeats async purpose
}
```

Better:

```js id="x9k3qp"
async next() {
  return { value: 1, done: false };
}
```

---

## ❌ Sharing iterator state incorrectly

```js id="b2q8mz"
[Symbol.asyncIterator]() {
  return this; // ❌ invalid
}
```

Must return a fresh iterator each time.

---

# 8. Advanced Pattern: Async Queue Iterator

Useful in event systems:

```js id="t7k3qp"
class AsyncQueue {
  constructor() {
    this.queue = [];
    this.resolvers = [];
  }

  push(value) {
    if (this.resolvers.length) {
      this.resolvers.shift()(value);
    } else {
      this.queue.push(value);
    }
  }

  [Symbol.asyncIterator]() {
    return {
      next: () => {
        if (this.queue.length) {
          return Promise.resolve({
            value: this.queue.shift(),
            done: false,
          });
        }

        return new Promise((resolve) => {
          this.resolvers.push(resolve);
        }).then((value) => ({
          value,
          done: false,
        }));
      },
    };
  }
}
```

### Usage:

```js id="m8x2qp"
const q = new AsyncQueue();

setTimeout(() => q.push(1), 500);
setTimeout(() => q.push(2), 1000);

(async () => {
  for await (const item of q) {
    console.log(item);
  }
})();
```

---

# 9. Real-World Use Cases

Async iterables are used in:

### 1. Streams

- Node.js streams
- Web Streams API

### 2. API pagination

- Fetching large datasets page-by-page

### 3. Event streams

- WebSocket messages
- Kafka-like systems

### 4. File processing

- Reading large files chunk by chunk

---

# 10. Senior-Level Interview Insight

Async iterables combine:

- Iterator protocol
- Promises
- Event-driven programming

They enable **pull-based asynchronous data consumption**, where data is fetched only when requested, unlike push-based event emitters.

Modern JavaScript frameworks and runtimes use async iterables to model streaming data efficiently, making them a foundational abstraction for scalable I/O and reactive systems.

---

# 11. Interview Summary

An async iterable is an object that implements `[Symbol.asyncIterator]`, returning an async iterator whose `next()` method returns a Promise resolving to `{ value, done }`. It enables consumption of asynchronous data sequences using `for await...of`. While manual implementations provide full control, async generator functions are the preferred approach due to simplicity and readability. Async iterables are widely used in streams, pagination, and real-time data systems, making them a key abstraction for modern asynchronous programming in JavaScript.

## Question 12. How to implement generators for state machines

## Question 13. How to implement a scheduler for async tasks

## Question 14. How to implement a task queue to prevent blocking

## Question 15. How to implement cooperative multitasking in JavaScript

## Question 16. How to implement backpressure for streaming data

## Question 17. How to implement a simple event loop simulation

## Question 18. How to use `requestIdleCallback` for non-urgent tasks

## Question 19. How to implement memoization for expensive functions

## Question 20. How to implement a caching mechanism with TTL (Time-To-Live)
