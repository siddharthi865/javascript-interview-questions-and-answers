# Set 25

| S.No. | Question                                                                                                                                                  |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to implement dependency injection in JavaScript](#question-1-how-to-implement-dependency-injection-in-javascript)                                    |
| 2.    | [How to implement observer/observable pattern in JS frameworks](#question-2-how-to-implement-observerobservable-pattern-in-js-frameworks)                 |
| 3.    | [How to implement factory pattern in JavaScript](#question-3-how-to-implement-factory-pattern-in-javascript)                                              |
| 4.    | [How to implement singleton pattern in Node.js modules](#question-4-how-to-implement-singleton-pattern-in-nodejs-modules)                                 |
| 5.    | [How to implement a strategy pattern in JavaScript](#question-5-how-to-implement-a-strategy-pattern-in-javascript)                                        |
| 6.    | [How to implement command pattern in JavaScript](#question-6-how-to-implement-command-pattern-in-javascript)                                              |
| 7.    | [How to implement middleware pattern in frontend or backend](#question-7-how-to-implement-middleware-pattern-in-frontend-or-backend)                      |
| 8.    | [How to implement a decorator pattern using higher-order functions](#question-8-how-to-implement-a-decorator-pattern-using-higher-order-functions)        |
| 9.    | [How to implement chain of responsibility pattern in JS](#question-9-how-to-implement-chain-of-responsibility-pattern-in-js)                              |
| 10.   | [How to handle circular dependencies in modules](#question-10-how-to-handle-circular-dependencies-in-modules)                                             |
| 11.   | [How to optimize memory usage in JavaScript applications](#question-11-how-to-optimize-memory-usage-in-javascript-applications)                           |
| 12.   | [How to implement immutable data structures efficiently](#question-12-how-to-implement-immutable-data-structures-efficiently)                             |
| 13.   | [How to implement a virtual DOM diffing algorithm](#question-13-how-to-implement-a-virtual-dom-diffing-algorithm)                                         |
| 14.   | [How to implement reactive programming using proxies](#question-14-how-to-implement-reactive-programming-using-proxies)                                   |
| 15.   | [How to implement a simple state management library](#question-15-how-to-implement-a-simple-state-management-library)                                     |
| 16.   | [How to prevent event handler memory leaks in SPA frameworks](#question-16-how-to-prevent-event-handler-memory-leaks-in-spa-frameworks)                   |
| 17.   | [How to implement lazy evaluation in JavaScript](#question-17-how-to-implement-lazy-evaluation-in-javascript)                                             |
| 18.   | [How to implement a simple logging utility with levels and filters](#question-18-how-to-implement-a-simple-logging-utility-with-levels-and-filters)       |
| 19.   | [How to implement function throttling using `requestAnimationFrame`](#question-19-how-to-implement-function-throttling-using-requestanimationframe)       |
| 20.   | [How to implement a secure frontend-backend token authentication flow](#question-20-how-to-implement-a-secure-frontend-backend-token-authentication-flow) |

## Question 1. How to implement dependency injection in JavaScript

> Dependency Injection (DI) in JavaScript is a design pattern where dependencies are provided to a class or function from the outside instead of being created internally. This improves modularity, testability, maintainability, and flexibility.

### What Is Dependency Injection?

Without DI, a class creates its own dependencies:

```js
class UserService {
  constructor() {
    this.database = new Database();
  }

  getUsers() {
    return this.database.findAll();
  }
}
```

Problem:

- `UserService` is tightly coupled to `Database`
- Hard to test
- Hard to swap implementations

With DI:

```js
class UserService {
  constructor(database) {
    this.database = database;
  }

  getUsers() {
    return this.database.findAll();
  }
}
```

Usage:

```js
const db = new Database();
const service = new UserService(db);
```

Now the dependency is injected externally.

### Why Dependency Injection Matters

DI provides:

| Benefit                | Explanation                                     |
| ---------------------- | ----------------------------------------------- |
| Loose coupling         | Components depend on abstractions               |
| Easier testing         | Mock dependencies easily                        |
| Better maintainability | Swap implementations without changing consumers |
| Reusability            | Components become independent                   |
| Scalability            | Cleaner architecture in large apps              |

### Types of Dependency Injection

#### 1. Constructor Injection (Most Common)

Dependencies are passed via constructor.

```js
class Logger {
  log(message) {
    console.log(message);
  }
}

class AuthService {
  constructor(logger) {
    this.logger = logger;
  }

  login(user) {
    this.logger.log(`User logged in: ${user}`);
  }
}

const logger = new Logger();
const auth = new AuthService(logger);

auth.login("John");
```

##### Why preferred?

- Dependencies are explicit
- Object cannot exist without required dependencies
- Easy to test

#### 2. Setter Injection

Dependencies are set after object creation.

```js
class UserService {
  setDatabase(database) {
    this.database = database;
  }
}

const service = new UserService();
service.setDatabase(new Database());
```

Useful when dependencies are optional.

#### 3. Method Injection

Dependency passed directly to a method.

```js
class ReportService {
  generate(reportGenerator) {
    return reportGenerator.create();
  }
}
```

Good for short-lived dependencies.

### Real-World Example

#### Without DI

```js
class EmailService {
  send(message) {
    console.log("Sending email:", message);
  }
}

class Notification {
  constructor() {
    this.emailService = new EmailService();
  }

  notify(msg) {
    this.emailService.send(msg);
  }
}
```

Hardcoded dependency.

#### With DI

```js
class Notification {
  constructor(service) {
    this.service = service;
  }

  notify(msg) {
    this.service.send(msg);
  }
}
```

Now inject different services:

```js
class SMSService {
  send(msg) {
    console.log("Sending SMS:", msg);
  }
}

const sms = new SMSService();

const notification = new Notification(sms);

notification.notify("Hello");
```

### Dependency Injection and Testing

DI is extremely useful for unit testing.

#### Without DI

Hard to mock internal dependencies.

#### With DI

Easy to inject fake implementations.

```js
class FakeDatabase {
  findAll() {
    return ["test-user"];
  }
}

const service = new UserService(new FakeDatabase());

console.log(service.getUsers());
```

This is a major interview point.

### Dependency Injection Container

In large applications, manually wiring dependencies becomes difficult.

A DI container manages object creation automatically.

### Simple DI Container Example

```js
class Container {
  constructor() {
    this.services = {};
  }

  register(name, dependency) {
    this.services[name] = dependency;
  }

  resolve(name) {
    return this.services[name];
  }
}

const container = new Container();

container.register("logger", new Logger());

const logger = container.resolve("logger");
```

### More Advanced Factory-Based Container

```js
class Container {
  constructor() {
    this.dependencies = new Map();
  }

  register(name, factory) {
    this.dependencies.set(name, factory);
  }

  resolve(name) {
    const factory = this.dependencies.get(name);

    if (!factory) {
      throw new Error(`Dependency ${name} not found`);
    }

    return factory(this);
  }
}
```

Usage:

```js
container.register("logger", () => new Logger());

container.register(
  "authService",
  (container) => new AuthService(container.resolve("logger")),
);

const authService = container.resolve("authService");
```

### Dependency Injection in Popular Frameworks

#### Angular

Angular has built-in DI.

```ts
constructor(private http: HttpClient) {}
```

Angular injector automatically provides dependencies.

#### NestJS

Uses decorators + reflection-based DI.

```ts
@Injectable()
class UserService {}
```

#### React

React doesn't have traditional DI but uses:

- Context API
- Hooks
- Provider pattern

### Dependency Injection vs Service Locator

Interviewers sometimes ask this.

#### DI

Consumer receives dependencies externally.

```js
new UserService(db);
```

#### Service Locator

Consumer fetches dependencies itself.

```js
const db = container.resolve("db");
```

DI is generally preferred because dependencies remain explicit.

### Common Pitfalls

#### 1. Overengineering Small Apps

DI containers can add unnecessary complexity.

Small applications may not need a full DI framework.

#### 2. Hidden Dependencies

Avoid global containers everywhere.

Bad:

```js
const logger = globalContainer.resolve("logger");
```

This hides dependencies.

#### 3. Circular Dependencies

Example:

```txt
A depends on B
B depends on A
```

Can cause initialization issues.

### Best Practices

#### Prefer Constructor Injection

Most predictable and testable.

#### Depend on Abstractions

Inject interfaces/contracts instead of concrete implementations.

JavaScript example:

```js
class Storage {
  save() {}
}
```

#### Keep Dependencies Explicit

Avoid hidden globals.

#### Use DI Containers Carefully

Useful in:

- Enterprise apps
- Backend frameworks
- Large architectures

May be unnecessary in simple scripts.

### Summary

Dependency Injection is a pattern where dependencies are supplied externally rather than created inside a component.

Key points:

- Promotes loose coupling
- Improves testability
- Makes code modular and maintainable
- Constructor injection is most common
- DI containers automate dependency management
- Widely used in Angular, NestJS, and enterprise Node.js apps

## Question 2. How to implement observer/observable pattern in JS frameworks

The Observer pattern is a behavioral design pattern where an object (called the **subject** or **observable**) maintains a list of dependents (**observers**) and automatically notifies them when its state changes.

In JavaScript frameworks, this pattern is fundamental to:

- React state updates
- Vue reactivity
- Angular RxJS Observables
- Event emitters in Node.js
- Redux subscriptions
- Signals/reactive systems

It is one of the core patterns behind modern frontend reactivity systems.

---

# Core Idea

```txt
Observable (Subject)
    ↓ notifies
Observers (Subscribers)
```

Observers subscribe to changes and react automatically.

---

# Basic Observer Pattern Implementation

---

# 1. Simple Observable Class

```js id="0yp2nm"
class Observable {
  constructor() {
    this.observers = [];
  }

  subscribe(fn) {
    this.observers.push(fn);

    // Return unsubscribe function
    return () => {
      this.observers = this.observers.filter((observer) => observer !== fn);
    };
  }

  notify(data) {
    this.observers.forEach((observer) => observer(data));
  }
}
```

Usage:

```js id="4oijzt"
const observable = new Observable();

const unsubscribe = observable.subscribe((data) => {
  console.log("Observer 1:", data);
});

observable.subscribe((data) => {
  console.log("Observer 2:", data);
});

observable.notify("Hello World");
```

Output:

```txt id="sm23pw"
Observer 1: Hello World
Observer 2: Hello World
```

---

# 2. Unsubscribing

```js id="jtr7r5"
unsubscribe();

observable.notify("Second Event");
```

Only remaining observers receive updates.

---

# Real-World Example: Event System

```js id="ddwkdn"
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
    if (this.events[event]) {
      this.events[event].forEach((listener) => {
        listener(data);
      });
    }
  }

  off(event, listenerToRemove) {
    this.events[event] = this.events[event].filter(
      (listener) => listener !== listenerToRemove,
    );
  }
}
```

Usage:

```js id="4p2uv4"
const emitter = new EventEmitter();

function onLogin(user) {
  console.log(`${user} logged in`);
}

emitter.on("login", onLogin);

emitter.emit("login", "John");

emitter.off("login", onLogin);
```

This is essentially how:

- DOM events work
- Node.js `EventEmitter`
- many framework event systems work internally

---

# Observable Pattern in Modern Frameworks

---

# React

React itself is not a classical observable framework, but state changes behave similarly.

Example:

```jsx id="nlp9rr"
function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

Internally:

- component subscribes to state
- state changes trigger re-render
- React reconciles UI updates

React libraries using observables:

- MobX
- Redux store subscriptions
- Zustand
- RxJS integrations

---

# Vue Reactivity System

Vue uses a true dependency-tracking observable system.

Example:

```js id="t5vg5t"
const state = reactive({
  count: 0,
});

watch(
  () => state.count,
  (newValue) => {
    console.log("Count changed:", newValue);
  },
);
```

Internally Vue:

1. Tracks dependencies
2. Registers observers
3. Re-runs effects automatically

This is an advanced observer implementation using proxies.

---

# Angular + RxJS

Angular heavily uses RxJS Observables.

```ts id="x27b0l"
import { Observable } from "rxjs";

const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.complete();
});

observable.subscribe({
  next(value) {
    console.log(value);
  },
});
```

RxJS extends observer pattern with:

- async streams
- operators
- cancellation
- multicasting
- transformation pipelines

---

# Implementing Reactive Observable with Proxy

Modern frameworks use `Proxy` for reactivity.

---

# Simple Reactive System

```js id="x19zfw"
function reactive(obj) {
  const observers = new Set();

  return {
    observe(fn) {
      observers.add(fn);
    },

    proxy: new Proxy(obj, {
      set(target, key, value) {
        target[key] = value;

        observers.forEach((fn) => fn());

        return true;
      },
    }),
  };
}
```

Usage:

```js id="e1e3hn"
const state = reactive({
  count: 0,
});

state.observe(() => {
  console.log("State changed");
});

state.proxy.count++;
```

---

# Advanced Reactive Dependency Tracking

Frameworks like Vue/SolidJS implement:

- dependency collection
- fine-grained subscriptions
- computed properties
- lazy evaluation
- batching
- scheduler queues

Conceptually:

```txt id="qphz8n"
Effect runs
   ↓
Tracks accessed properties
   ↓
Registers dependency
   ↓
Property changes
   ↓
Only affected effects rerun
```

---

# Push vs Pull Model

## Push-Based (Observer)

Observable pushes updates.

```txt id="v6mh9j"
observable -> observers
```

Example:

- events
- RxJS
- subscriptions

---

## Pull-Based

Observer checks manually.

```txt id="1c8y7d"
observer asks for latest state
```

Example:

- polling
- manual state reads

Modern frameworks mostly use push-based reactivity.

---

# Common Pitfalls

---

# 1. Memory Leaks

Observers that are never removed remain in memory.

Bad:

```js id="qk5x7s"
observable.subscribe(fn);
```

Without cleanup.

Good:

```js id="5c4skq"
const unsubscribe = observable.subscribe(fn);

unsubscribe();
```

This is extremely important in React effects and Node.js servers.

---

# 2. Infinite Loops

Observers triggering state changes recursively.

```js id="zv2jtw"
observer(() => {
  state.count++;
});
```

Can create endless updates.

Frameworks solve this using:

- batching
- schedulers
- dependency graphs

---

# 3. Too Many Re-renders

Naive observable systems rerender everything.

Modern frameworks optimize using:

- virtual DOM
- fine-grained reactivity
- memoization

---

# Observer Pattern vs Pub/Sub

Interviewers often ask this distinction.

---

# Observer Pattern

- Direct relationship
- Subject knows observers

```txt id="8jj8vd"
Subject -> Observers
```

---

# Pub/Sub Pattern

Uses message broker/event bus.

```txt id="m7f4ku"
Publisher -> Event Bus -> Subscribers
```

More decoupled.

Example:

- Redis Pub/Sub
- Kafka
- Event buses

---

# Best Practices

---

# Use Weak References When Needed

Prevent leaks in long-running apps.

---

# Always Support Unsubscribe

Critical for cleanup.

---

# Avoid Synchronous Notification Storms

Use batching/microtasks if necessary.

Example:

```js id="y7mjlwm"
queueMicrotask(() => {
  notifyObservers();
});
```

---

# Keep Observers Pure

Avoid mutating shared state unpredictably.

---

# Interview Summary

The Observer pattern allows objects to subscribe to state changes and receive automatic notifications.

Key ideas:

- Observable maintains subscribers
- Observers react to updates
- Core foundation of frontend reactivity
- Used in React, Vue, Angular, RxJS, MobX, Redux

Modern frameworks extend it using:

- Proxy
- dependency tracking
- schedulers
- batching
- async streams

A strong interview answer should connect observer pattern to:

- reactive programming
- event-driven systems
- UI rendering
- RxJS observables
- framework reactivity internals
- memory management and cleanup

## Question 3. How to implement factory pattern in JavaScript

## Question 4. How to implement singleton pattern in Node.js modules

## Question 5. How to implement a strategy pattern in JavaScript

## Question 6. How to implement command pattern in JavaScript

## Question 7. How to implement middleware pattern in frontend or backend

## Question 8. How to implement a decorator pattern using higher-order functions

## Question 9. How to implement chain of responsibility pattern in JS

## Question 10. How to handle circular dependencies in modules

## Question 11. How to optimize memory usage in JavaScript applications

## Question 12. How to implement immutable data structures efficiently

## Question 13. How to implement a virtual DOM diffing algorithm

## Question 14. How to implement reactive programming using proxies

## Question 15. How to implement a simple state management library

## Question 16. How to prevent event handler memory leaks in SPA frameworks

## Question 17. How to implement lazy evaluation in JavaScript

## Question 18. How to implement a simple logging utility with levels and filters

## Question 19. How to implement function throttling using `requestAnimationFrame`

## Question 20. How to implement a secure frontend-backend token authentication flow
