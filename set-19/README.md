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

## Question 3. How to create a proxy to validate object properties

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
