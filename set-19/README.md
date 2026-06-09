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

## Question 5. How to implement a reactive object (like Vue.js reactivity system)

## Question 6. How to implement observer pattern in JavaScript

## Question 7. How to use `WeakRef` to prevent memory leaks

## Question 8. Difference between shallow and deep freezing an object

## Question 9. How to implement a read-only object using Proxy

## Question 10. How to implement custom iterables using `[Symbol.iterator]`

## Question 11. How to implement async iterables using `[Symbol.asyncIterator]`

## Question 12. How to implement generators for state machines

## Question 13. How to implement a scheduler for async tasks

## Question 14. How to implement a task queue to prevent blocking

## Question 15. How to implement cooperative multitasking in JavaScript

## Question 16. How to implement backpressure for streaming data

## Question 17. How to implement a simple event loop simulation

## Question 18. How to use `requestIdleCallback` for non-urgent tasks

## Question 19. How to implement memoization for expensive functions

## Question 20. How to implement a caching mechanism with TTL (Time-To-Live)
