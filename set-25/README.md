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

The Factory Pattern is a creational design pattern that provides a centralized way to create objects without exposing the exact instantiation logic to the client.

Instead of using `new` directly everywhere, object creation is delegated to a factory function or factory class.

It helps with:

- encapsulating object creation
- reducing coupling
- improving maintainability
- supporting polymorphism
- simplifying complex initialization logic

---

# Basic Idea

Without factory:

```js id="vqsy2m"
const car = new Car();
const bike = new Bike();
```

With factory:

```js id="v8fby4"
const vehicle = VehicleFactory.create("car");
```

The caller does not need to know how objects are created internally.

---

# Simple Factory Pattern

---

# 1. Basic Factory Function

```js id="oijc2j"
class Car {
  drive() {
    console.log("Driving a car");
  }
}

class Bike {
  ride() {
    console.log("Riding a bike");
  }
}

function vehicleFactory(type) {
  switch (type) {
    case "car":
      return new Car();

    case "bike":
      return new Bike();

    default:
      throw new Error("Unknown vehicle type");
  }
}
```

Usage:

```js id="tz9kgw"
const car = vehicleFactory("car");
car.drive();

const bike = vehicleFactory("bike");
bike.ride();
```

---

# Why Use a Factory?

Without factory:

```js id="r4n6go"
const user = new AdminUser();
```

Application code becomes tightly coupled to concrete classes.

With factory:

```js id="vjlwm0"
const user = UserFactory.create(role);
```

Benefits:

- centralized creation logic
- easier maintenance
- easier extension
- abstraction layer

---

# Real-World Example

---

# Notification Factory

```js id="40cxkl"
class EmailNotification {
  send(message) {
    console.log("Sending Email:", message);
  }
}

class SMSNotification {
  send(message) {
    console.log("Sending SMS:", message);
  }
}

class PushNotification {
  send(message) {
    console.log("Sending Push:", message);
  }
}
```

Factory:

```js id="q2bhgo"
class NotificationFactory {
  static create(type) {
    switch (type) {
      case "email":
        return new EmailNotification();

      case "sms":
        return new SMSNotification();

      case "push":
        return new PushNotification();

      default:
        throw new Error("Invalid notification type");
    }
  }
}
```

Usage:

```js id="r7gbtl"
const notifier = NotificationFactory.create("sms");

notifier.send("Hello");
```

---

# Factory Using Object Mapping

A cleaner modern approach:

```js id="bnnvls"
const factories = {
  car: () => new Car(),
  bike: () => new Bike(),
};

function vehicleFactory(type) {
  const factory = factories[type];

  if (!factory) {
    throw new Error("Unknown type");
  }

  return factory();
}
```

Advantages:

- scalable
- cleaner than switch
- easy extension

---

# Factory with Configuration

Factories can encapsulate complex initialization.

```js id="mfrf6x"
class Database {
  constructor(connectionString) {
    this.connectionString = connectionString;
  }
}
```

Factory:

```js id="qcdjsj"
function createDatabase(env) {
  const configs = {
    development: "localhost:3000",
    production: "prod-db-server",
  };

  return new Database(configs[env]);
}
```

Usage:

```js id="0v8lr9"
const db = createDatabase("production");
```

---

# Factory vs Constructor

---

# Constructor

```js id="5a1c3t"
const user = new User();
```

Direct instantiation.

---

# Factory

```js id="p7d9k7"
const user = UserFactory.create();
```

Creation logic abstracted away.

Factories are better when:

- object creation is complex
- conditional creation exists
- caching/singletons needed
- setup logic required

---

# Factory Pattern with Prototypes

Factories work well with prototype-based JS.

```js id="b9v1n2"
function createUser(name) {
  return {
    name,
    greet() {
      console.log(`Hello ${name}`);
    },
  };
}
```

Usage:

```js id="zc56xg"
const user = createUser("John");
```

This is also considered a factory function.

---

# Factory + Closures

Factories can create private state.

```js id="zsluxl"
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
  };
}
```

Usage:

```js id="ln01rl"
const counter = createCounter();

console.log(counter.increment());
```

This is a powerful JavaScript-specific use case.

---

# Abstract Factory Pattern

A more advanced variation.

Creates families of related objects.

---

# Example

```js id="t7kprm"
class MacButton {}
class WindowsButton {}

class MacCheckbox {}
class WindowsCheckbox {}
```

Factory:

```js id="8wcmw5"
class GUIFactory {
  static createFactory(os) {
    if (os === "mac") {
      return {
        createButton: () => new MacButton(),
        createCheckbox: () => new MacCheckbox(),
      };
    }

    return {
      createButton: () => new WindowsButton(),
      createCheckbox: () => new WindowsCheckbox(),
    };
  }
}
```

Usage:

```js id="h7gplj"
const factory = GUIFactory.createFactory("mac");

const button = factory.createButton();
```

Useful in:

- UI frameworks
- theme systems
- cross-platform apps

---

# Factory Pattern in Modern Frameworks

---

# React

React components themselves behave somewhat like factories.

```jsx id="7nupxj"
function Button(props) {
  return <button>{props.label}</button>;
}
```

---

# Redux

Reducers often use factories for store setup.

---

# Node.js

Express middleware factories:

```js id="7w4yga"
function logger(options) {
  return function (req, res, next) {
    console.log(options.prefix);
    next();
  };
}
```

Usage:

```js id="c0uyol"
app.use(logger({ prefix: "[API]" }));
```

---

# Common Pitfalls

---

# 1. Overusing Factories

Factories add abstraction.

For simple objects:

```js id="s7m8me"
const user = { name: "John" };
```

No factory needed.

---

# 2. Large Switch Statements

Bad:

```js id="yx0uxm"
switch (
  type
  // 50 cases
) {
}
```

Prefer registry/object mapping.

---

# 3. Hidden Complexity

Factories can hide expensive operations.

Document behavior clearly.

---

# 4. Returning Inconsistent Interfaces

Bad:

```js id="k4ctzf"
factory("car").drive();
factory("bike").ride();
```

Better:

```js id="4o9gsp"
vehicle.move();
```

Consistent APIs improve polymorphism.

---

# Factory vs Other Patterns

| Pattern              | Purpose                                |
| -------------------- | -------------------------------------- |
| Factory              | Create objects                         |
| Singleton            | Ensure single instance                 |
| Builder              | Construct complex objects step-by-step |
| Prototype            | Clone existing objects                 |
| Dependency Injection | Provide dependencies externally        |

---

# Best Practices

---

# Prefer Factory Functions for Simplicity

Modern JS often prefers:

```js id="lzj5xr"
function createUser() {}
```

over complex class factories.

---

# Use Factories for:

- polymorphic objects
- environment-based creation
- plugin systems
- caching
- encapsulated setup logic

---

# Keep Interfaces Consistent

All produced objects should behave similarly.

---

# Combine with Dependency Injection

Factories + DI work very well together.

---

# Interview Summary

The Factory Pattern centralizes object creation logic and hides implementation details from consumers.

Key points:

- abstracts instantiation
- reduces coupling
- improves scalability
- supports polymorphism
- encapsulates complex setup logic

Common implementations in JavaScript:

- factory functions
- static factory classes
- abstract factories
- closure-based factories

Widely used in:

- React ecosystems
- Node.js middleware
- UI systems
- plugin architectures
- service creation layers

A strong interview answer should also discuss:

- factory vs constructor
- factory vs builder
- closure-based factories
- dependency injection integration
- real-world framework usage

## Question 4. How to implement singleton pattern in Node.js modules

In Node.js, the Singleton pattern is commonly implemented using the module system itself because Node caches modules after the first `require()` or `import()`.

That means every subsequent import receives the same instance automatically.

This makes Node.js modules a natural fit for singleton behavior.

---

# What Is the Singleton Pattern?

Singleton ensures:

- only one instance of an object exists
- a global access point to that instance is provided

Typical use cases:

- database connections
- loggers
- configuration managers
- cache managers
- event buses
- connection pools

---

# Why Node.js Modules Behave Like Singletons

Node.js caches modules internally.

When a module is loaded:

1. module executes once
2. exports are cached
3. future imports reuse cached version

---

# Basic Singleton Example

---

# logger.js

```js id="4rq2g3"
class Logger {
  constructor() {
    this.logs = [];
    console.log("Logger initialized");
  }

  log(message) {
    this.logs.push(message);
    console.log(message);
  }
}

module.exports = new Logger();
```

---

# app.js

```js id="0w2p5s"
const logger1 = require("./logger");
const logger2 = require("./logger");

console.log(logger1 === logger2);
```

Output:

```txt id="6i7hq0"
Logger initialized
true
```

The constructor runs only once.

---

# How Node Module Caching Works

Internally:

```txt id="ihhm5m"
require()
   ↓
Check require.cache
   ↓
If cached → return same exports
Else → execute module + cache it
```

This is why singleton implementation is very easy in Node.js.

---

# Singleton Using a Static Instance

More explicit classical implementation.

---

# database.js

```js id="mly5jc"
class Database {
  constructor() {
    if (Database.instance) {
      return Database.instance;
    }

    console.log("Connecting to database...");

    this.connection = "DB_CONNECTION";

    Database.instance = this;
  }

  query(sql) {
    console.log(`Executing: ${sql}`);
  }
}

module.exports = Database;
```

Usage:

```js id="uux3zh"
const Database = require("./database");

const db1 = new Database();
const db2 = new Database();

console.log(db1 === db2);
```

Output:

```txt id="n4xrw8"
Connecting to database...
true
```

---

# Modern Singleton with ES Modules

---

# logger.js

```js id="s4vvk0"
class Logger {
  constructor() {
    console.log("Logger created");
  }

  log(msg) {
    console.log(msg);
  }
}

const logger = new Logger();

export default logger;
```

Usage:

```js id="wqzbry"
import logger1 from "./logger.js";
import logger2 from "./logger.js";

console.log(logger1 === logger2);
```

Still singleton because ES modules are cached too.

---

# Singleton Database Connection Example

Very common interview example.

---

# db.js

```js id="lrpl6h"
const mongoose = require("mongoose");

class Database {
  constructor() {
    if (!Database.instance) {
      this.connect();
      Database.instance = this;
    }

    return Database.instance;
  }

  connect() {
    console.log("Connecting to MongoDB...");
  }
}

module.exports = new Database();
```

Why useful?

Avoids:

- multiple DB connections
- connection duplication
- resource waste

---

# Closure-Based Singleton

JavaScript closures make elegant singleton implementations possible.

```js id="4ewb2l"
const Database = (function () {
  let instance;

  function createInstance() {
    return {
      connect() {
        console.log("Connected");
      },
    };
  }

  return {
    getInstance() {
      if (!instance) {
        instance = createInstance();
      }

      return instance;
    },
  };
})();
```

Usage:

```js id="y81b3g"
const db1 = Database.getInstance();
const db2 = Database.getInstance();

console.log(db1 === db2);
```

---

# Real-World Singleton Examples in Node.js

---

# 1. Database Pools

```js id="1r3l9t"
module.exports = mysql.createPool(config);
```

Single shared pool.

---

# 2. Winston/Pino Loggers

```js id="fq0m31"
module.exports = createLogger({...});
```

Single logger instance.

---

# 3. Redis Clients

```js id="xajxsn"
module.exports = redis.createClient();
```

Shared connection.

---

# 4. Config Managers

```js id="p0rzv4"
module.exports = {
  port: process.env.PORT,
};
```

Single configuration source.

---

# Common Pitfalls

---

# 1. Shared Mutable State

Singletons are globally shared.

Bad:

```js id="8w57sj"
singleton.user = "John";
```

Can create hidden side effects.

---

# 2. Harder Unit Testing

Global state complicates test isolation.

Solution:

- reset singleton
- dependency injection
- mocks/stubs

---

# 3. Memory Retention

Singletons live entire process lifetime.

Large cached objects may cause leaks.

---

# 4. Concurrency Misunderstanding

Node.js is single-threaded per process, but:

- clusters
- worker threads
- serverless environments

may create multiple instances.

Singleton is usually process-scoped, not system-wide.

---

# Singleton vs Module Pattern

Interview nuance.

---

# Module Pattern

Encapsulation + private state.

```js id="st8tx6"
const counter = (() => {
  let count = 0;

  return {
    increment() {
      count++;
    },
  };
})();
```

---

# Singleton Pattern

Ensures single instance.

Node modules often combine both.

---

# Best Practices

---

# Prefer Module-Level Singleton

Simplest and idiomatic Node.js approach:

```js id="d9gch0"
module.exports = new Service();
```

Usually sufficient.

---

# Avoid Excessive Global State

Keep singleton responsibilities focused.

---

# Combine with Dependency Injection

For testability:

```js id="6kq6h5"
function createService(logger) {
  return new Service(logger);
}
```

---

# Use Lazy Initialization When Expensive

Initialize only when needed.

```js id="vzt1qh"
if (!instance) {
  instance = create();
}
```

---

# Interview Summary

In Node.js, singleton pattern is commonly implemented using the built-in module cache system.

Key points:

- modules execute once
- exports are cached
- subsequent imports reuse same instance

Common implementations:

- exporting instantiated object
- static instance class
- closure-based singleton

Common real-world uses:

- database connections
- loggers
- Redis clients
- config managers
- connection pools

Important interview discussion areas:

- module caching
- ES modules vs CommonJS
- shared mutable state
- testing challenges
- process-level limitations
- singleton vs module pattern

## Question 5. How to implement a strategy pattern in JavaScript

The Strategy Pattern is a behavioral design pattern that allows you to define a family of algorithms, encapsulate each one, and make them interchangeable at runtime.

Instead of hardcoding logic using large `if/else` or `switch` statements, you delegate behavior to separate strategy objects or functions.

It helps with:

- reducing conditional complexity
- improving extensibility
- following the Open/Closed Principle
- enabling runtime behavior changes

---

# Core Idea

Instead of:

```js id="2aqy95"
if (paymentType === "paypal") {
  // PayPal logic
} else if (paymentType === "stripe") {
  // Stripe logic
}
```

Use interchangeable strategies:

```js id="6w6p7g"
paymentStrategy.pay(amount);
```

---

# Basic Strategy Pattern Structure

```txt id="yz17dx"
Context
   ↓ uses
Strategy Interface
   ↓
Concrete Strategies
```

---

# Simple Example

---

# Without Strategy Pattern

```js id="bl37jd"
function calculatePrice(type, price) {
  if (type === "regular") {
    return price;
  }

  if (type === "premium") {
    return price * 0.8;
  }

  if (type === "vip") {
    return price * 0.6;
  }
}
```

Problems:

- difficult to extend
- violates Open/Closed Principle
- large conditionals grow over time

---

# With Strategy Pattern

---

# 1. Define Strategies

```js id="jlwmg6"
class RegularPricing {
  calculate(price) {
    return price;
  }
}

class PremiumPricing {
  calculate(price) {
    return price * 0.8;
  }
}

class VIPPricing {
  calculate(price) {
    return price * 0.6;
  }
}
```

---

# 2. Context Class

```js id="d8tz7m"
class PriceCalculator {
  constructor(strategy) {
    this.strategy = strategy;
  }

  setStrategy(strategy) {
    this.strategy = strategy;
  }

  calculate(price) {
    return this.strategy.calculate(price);
  }
}
```

---

# 3. Usage

```js id="0g94of"
const calculator = new PriceCalculator(new PremiumPricing());

console.log(calculator.calculate(100));
```

Output:

```txt id="p6xrfm"
80
```

Switch strategy dynamically:

```js id="g5mdx9"
calculator.setStrategy(new VIPPricing());

console.log(calculator.calculate(100));
```

---

# JavaScript Functional Approach (Common Modern Style)

Because functions are first-class citizens, JavaScript often implements strategy pattern using functions instead of classes.

---

# Functional Strategy Example

```js id="3ln1py"
const strategies = {
  regular: (price) => price,

  premium: (price) => price * 0.8,

  vip: (price) => price * 0.6,
};
```

Usage:

```js id="sjhv1f"
function calculatePrice(type, price) {
  return strategies[type](price);
}

console.log(calculatePrice("vip", 100));
```

This is the most common real-world JS implementation.

---

# Real-World Example: Payment Processing

---

# Strategies

```js id="d7fy81"
class PayPalPayment {
  pay(amount) {
    console.log(`Paid ${amount} using PayPal`);
  }
}

class StripePayment {
  pay(amount) {
    console.log(`Paid ${amount} using Stripe`);
  }
}

class CryptoPayment {
  pay(amount) {
    console.log(`Paid ${amount} using Crypto`);
  }
}
```

---

# Context

```js id="8r4xj6"
class PaymentProcessor {
  constructor(strategy) {
    this.strategy = strategy;
  }

  process(amount) {
    this.strategy.pay(amount);
  }
}
```

Usage:

```js id="zqjlwm"
const payment = new PaymentProcessor(new StripePayment());

payment.process(500);
```

---

# Strategy Pattern with Configuration Objects

Modern frontend apps often use object maps.

```js id="x4d5g8"
const renderers = {
  json: (data) => JSON.stringify(data),

  html: (data) => `<div>${data}</div>`,

  text: (data) => String(data),
};
```

Usage:

```js id="kbb8hc"
function render(type, data) {
  return renderers[type](data);
}
```

Very common in:

- React apps
- middleware systems
- plugin architectures

---

# Strategy Pattern in React

---

# Dynamic Component Strategy

```jsx id="zjzccl"
const strategies = {
  admin: AdminDashboard,
  user: UserDashboard,
};

function Dashboard({ role }) {
  const Component = strategies[role];

  return <Component />;
}
```

This is strategy pattern in UI rendering.

---

# Strategy Pattern in Express Middleware

```js id="n3uzmy"
const authStrategies = {
  jwt: jwtAuth,
  oauth: oauthAuth,
};

app.use(authStrategies[type]);
```

---

# Strategy Pattern in Validation Systems

```js id="4wjv6m"
const validators = {
  email: (value) => value.includes("@"),

  password: (value) => value.length >= 8,
};
```

Usage:

```js id="3nzw1m"
validators.email("test@example.com");
```

---

# Advantages of Strategy Pattern

| Benefit                       | Explanation                              |
| ----------------------------- | ---------------------------------------- |
| Eliminates large conditionals | Cleaner code                             |
| Easy to extend                | Add strategies without modifying context |
| Runtime flexibility           | Switch behavior dynamically              |
| Better separation             | Each algorithm isolated                  |
| Easier testing                | Strategies test independently            |

---

# Common Pitfalls

---

# 1. Too Many Tiny Classes

In JavaScript, class-heavy strategies can become verbose.

Prefer functions when possible.

---

# 2. Overengineering

Simple logic may not need full strategy abstraction.

Bad:

```js id="d75f72"
if (x > 5)
```

No strategy needed.

---

# 3. Inconsistent Interfaces

All strategies should expose the same API.

Bad:

```js id="n5c08u"
paypal.pay();
stripe.process();
crypto.send();
```

Good:

```js id="3tgu95"
strategy.execute();
```

---

# 4. Hidden Runtime Errors

If strategy missing:

```js id="u9u0j5"
strategies[type]();
```

Can throw:

```txt id="8fy0vb"
TypeError: undefined is not a function
```

Safer:

```js id="qfjlwm"
if (!strategies[type]) {
  throw new Error("Invalid strategy");
}
```

---

# Strategy vs State Pattern

Interviewers often ask this.

---

# Strategy Pattern

- algorithms are interchangeable
- chosen externally
- behavior selected intentionally

Example:

```txt id="njlwmw"
Payment strategy
Sorting strategy
Validation strategy
```

---

# State Pattern

- behavior changes automatically based on internal state

Example:

```txt id="kg7flg"
Traffic light states
Game character states
```

Structure looks similar, intent differs.

---

# Strategy vs Factory Pattern

| Strategy                    | Factory                          |
| --------------------------- | -------------------------------- |
| Chooses behavior            | Creates objects                  |
| Runtime algorithm selection | Object instantiation abstraction |

Often used together.

---

# Best Practices

---

# Prefer Functional Strategies in JS

Very idiomatic:

```js id="pjb4kp"
const strategies = {
  a: fn1,
  b: fn2,
};
```

---

# Keep Interfaces Consistent

Every strategy should behave predictably.

---

# Combine with Dependency Injection

Inject strategy externally:

```js id="v5qbtv"
new PaymentProcessor(strategy);
```

---

# Avoid Massive Strategy Registries

Too many strategies may indicate architectural issues.

---

# Interview Summary

The Strategy Pattern encapsulates interchangeable algorithms and allows behavior to be selected dynamically at runtime.

Key concepts:

- separates algorithms from consumers
- eliminates large conditional logic
- promotes extensibility
- follows Open/Closed Principle

Common JavaScript implementations:

- class-based strategies
- function-based strategies
- object maps
- configuration-driven handlers

Widely used in:

- payment systems
- validation engines
- middleware selection
- React rendering
- sorting/filtering systems
- authentication providers

A strong interview answer should also discuss:

- runtime behavior switching
- functional strategies in JS
- strategy vs state pattern
- strategy vs factory
- avoiding overengineering
- consistent interfaces and error handling

## Question 6. How to implement command pattern in JavaScript

The Command Pattern is a behavioral design pattern that encapsulates a request or operation as an object, allowing you to:

- parameterize actions
- queue operations
- support undo/redo
- log commands
- decouple senders from receivers

Instead of calling methods directly, you wrap actions inside command objects.

---

# Core Idea

Without command pattern:

```js id="exigj8"
button.onclick = () => {
  editor.copy();
};
```

With command pattern:

```js id="bnvjlwm"
button.onclick = () => {
  command.execute();
};
```

The invoker does not know the implementation details.

---

# Main Participants

```txt id="pxh5qs"
Invoker → Command → Receiver
```

| Component | Responsibility        |
| --------- | --------------------- |
| Command   | Encapsulates action   |
| Receiver  | Performs actual work  |
| Invoker   | Triggers command      |
| Client    | Configures everything |

---

# Basic Command Pattern Example

---

# 1. Receiver

The object that performs the real action.

```js id="z8nccq"
class Light {
  turnOn() {
    console.log("Light ON");
  }

  turnOff() {
    console.log("Light OFF");
  }
}
```

---

# 2. Command Classes

```js id="9jlwmr"
class TurnOnCommand {
  constructor(light) {
    this.light = light;
  }

  execute() {
    this.light.turnOn();
  }
}

class TurnOffCommand {
  constructor(light) {
    this.light = light;
  }

  execute() {
    this.light.turnOff();
  }
}
```

---

# 3. Invoker

```js id="7jw3ht"
class RemoteControl {
  submit(command) {
    command.execute();
  }
}
```

---

# 4. Usage

```js id="3j4tf7"
const light = new Light();

const onCommand = new TurnOnCommand(light);
const offCommand = new TurnOffCommand(light);

const remote = new RemoteControl();

remote.submit(onCommand);
remote.submit(offCommand);
```

Output:

```txt id="q7pjlwm"
Light ON
Light OFF
```

---

# Why Use Command Pattern?

Without command pattern:

```js id="3ldokf"
button.onclick = saveFile;
```

Tightly coupled.

With command pattern:

```js id="s6q1zz"
button.onclick = () => command.execute();
```

Benefits:

- loose coupling
- centralized action handling
- undo/redo support
- command queues
- macro commands

---

# Functional Command Pattern (Modern JS)

Because functions are first-class citizens, JavaScript often uses functions directly as commands.

```js id="k8gc6n"
const turnOn = () => console.log("ON");
const turnOff = () => console.log("OFF");

function invoke(command) {
  command();
}

invoke(turnOn);
invoke(turnOff);
```

This is the most common lightweight implementation in modern JS apps.

---

# Undo/Redo Example

A classic interview scenario.

---

# Text Editor Receiver

```js id="hmjlwm"
class TextEditor {
  constructor() {
    this.text = "";
  }

  write(text) {
    this.text += text;
  }

  delete(length) {
    this.text = this.text.slice(0, -length);
  }
}
```

---

# Command with Undo

```js id="0wghrm"
class WriteCommand {
  constructor(editor, text) {
    this.editor = editor;
    this.text = text;
  }

  execute() {
    this.editor.write(this.text);
  }

  undo() {
    this.editor.delete(this.text.length);
  }
}
```

---

# Command Manager

```js id="dhjlwm"
class CommandManager {
  constructor() {
    this.history = [];
  }

  execute(command) {
    command.execute();
    this.history.push(command);
  }

  undo() {
    const command = this.history.pop();

    if (command) {
      command.undo();
    }
  }
}
```

Usage:

```js id="78r3ae"
const editor = new TextEditor();
const manager = new CommandManager();

const command = new WriteCommand(editor, "Hello");

manager.execute(command);

console.log(editor.text);

manager.undo();

console.log(editor.text);
```

Output:

```txt id="4jlwm8"
Hello
""
```

---

# Macro Commands

Combine multiple commands.

```js id="3xjlwm"
class MacroCommand {
  constructor(commands) {
    this.commands = commands;
  }

  execute() {
    this.commands.forEach((command) => {
      command.execute();
    });
  }
}
```

Usage:

```js id="xjlwm9"
const macro = new MacroCommand([onCommand, offCommand]);

macro.execute();
```

---

# Queueing Commands

Very common in async systems.

```js id="7zjlwm"
class CommandQueue {
  constructor() {
    this.queue = [];
  }

  add(command) {
    this.queue.push(command);
  }

  process() {
    while (this.queue.length) {
      const command = this.queue.shift();
      command.execute();
    }
  }
}
```

Useful for:

- job queues
- background tasks
- retries
- transactional systems

---

# Command Pattern in Real Frameworks

---

# Redux

Redux actions resemble commands.

```js id="l7jlwm"
dispatch({
  type: "ADD_TODO",
  payload: todo,
});
```

Action objects encapsulate operations.

---

# VSCode Command System

```js id="q0jlwm"
commands.registerCommand("extension.sayHello", () => {});
```

---

# CLI Tools

Each CLI operation acts like a command.

---

# Game Development

Used heavily for:

- undo systems
- replay systems
- AI action queues

---

# Event Sourcing

Commands are often persisted:

```txt id="6jlwm8"
CreateOrderCommand
CancelOrderCommand
UpdateProfileCommand
```

Very common in enterprise architecture.

---

# Advantages

| Benefit       | Explanation                    |
| ------------- | ------------------------------ |
| Decoupling    | Sender separated from receiver |
| Undo/Redo     | Easy command reversal          |
| Queuing       | Commands can execute later     |
| Logging       | Store commands for auditing    |
| Composition   | Combine commands               |
| Extensibility | Add new commands easily        |

---

# Common Pitfalls

---

# 1. Too Many Command Classes

In JavaScript, excessive classes can become verbose.

Prefer function commands for simple cases.

---

# 2. Overengineering

Simple button handlers do not always need commands.

---

# 3. Undo Complexity

Some operations are difficult to reverse safely.

Example:

```txt id="jlwm77"
network requests
external APIs
database deletes
```

---

# 4. Memory Usage

Command histories can grow large.

Need cleanup strategies.

---

# Function-Based Command Pattern

Very idiomatic modern JS approach.

```js id="jlwm21"
function createCommand(execute, undo) {
  return {
    execute,
    undo,
  };
}
```

Usage:

```js id="jlwm22"
const command = createCommand(
  () => console.log("Execute"),
  () => console.log("Undo"),
);
```

---

# Command vs Strategy Pattern

Interviewers often ask this.

| Command                     | Strategy                         |
| --------------------------- | -------------------------------- |
| Encapsulates request/action | Encapsulates algorithm           |
| Often supports undo/queue   | Chooses behavior                 |
| Represents operation        | Represents interchangeable logic |

---

# Command vs Observer Pattern

| Command             | Observer                  |
| ------------------- | ------------------------- |
| One-to-one action   | One-to-many notifications |
| Explicit invocation | Event-driven updates      |

---

# Best Practices

---

# Prefer Functional Commands in JS

Simpler and more idiomatic.

---

# Keep Commands Small

Single responsibility per command.

---

# Add Undo Only When Needed

Not every command requires reversal.

---

# Separate Receiver Logic

Command should delegate actual work.

---

# Interview Summary

The Command Pattern encapsulates operations as objects or functions, allowing actions to be executed, queued, logged, undone, or composed independently from the sender.

Key concepts:

- decouples invoker from receiver
- encapsulates actions
- supports undo/redo
- enables queuing and macros

Common JavaScript implementations:

- class-based commands
- function commands
- Redux-like action objects
- async job queues

Widely used in:

- editors
- Redux
- CLI tools
- game engines
- task queues
- workflow systems

A strong interview answer should also discuss:

- undo/redo systems
- macro commands
- command queues
- function-based implementations
- command vs strategy
- command vs observer
- practical JS simplifications

## Question 7. How to implement middleware pattern in frontend or backend

The Middleware Pattern is a design pattern where requests, events, or data pass through a chain of processing functions before reaching the final handler.

Each middleware can:

- inspect data
- modify data
- stop execution
- pass control to the next middleware
- handle errors
- add cross-cutting concerns

Middleware is heavily used in:

- Express.js
- Koa
- Redux
- Next.js
- NestJS
- React state management
- API clients
- authentication pipelines

---

# Core Idea

```txt id="9n2g6w"
Request
   ↓
Middleware 1
   ↓
Middleware 2
   ↓
Middleware 3
   ↓
Final Handler
```

Each middleware decides whether to continue.

---

# Simple Middleware Implementation

---

# Basic Middleware Engine

```js id="w93s7k"
class MiddlewarePipeline {
  constructor() {
    this.middlewares = [];
  }

  use(fn) {
    this.middlewares.push(fn);
  }

  execute(context) {
    const dispatch = (index) => {
      if (index === this.middlewares.length) {
        return;
      }

      const middleware = this.middlewares[index];

      middleware(context, () => {
        dispatch(index + 1);
      });
    };

    dispatch(0);
  }
}
```

Usage:

```js id="8kjlwm"
const app = new MiddlewarePipeline();

app.use((ctx, next) => {
  console.log("Middleware 1");

  next();
});

app.use((ctx, next) => {
  console.log("Middleware 2");

  next();
});

app.execute({});
```

Output:

```txt id="2jlwm8"
Middleware 1
Middleware 2
```

---

# Understanding `next()`

`next()` passes control to the next middleware.

```txt id="jlwm4r"
middleware A
   ↓ next()
middleware B
   ↓ next()
middleware C
```

Without `next()`, the chain stops.

---

# Express.js Middleware Example

The most common real-world implementation.

```js id="0jlwm2"
app.use((req, res, next) => {
  console.log("Request received");

  next();
});
```

Authentication middleware:

```js id="9jlwm1"
app.use((req, res, next) => {
  if (!req.user) {
    return res.status(401).send("Unauthorized");
  }

  next();
});
```

Error middleware:

```js id="7jlwm0"
app.use((err, req, res, next) => {
  console.error(err);

  res.status(500).send("Server Error");
});
```

---

# Koa-Style Async Middleware

Koa introduced async middleware composition.

---

# Async Middleware Engine

```js id="6jlwm9"
class App {
  constructor() {
    this.middlewares = [];
  }

  use(fn) {
    this.middlewares.push(fn);
  }

  compose(context) {
    const dispatch = (index) => {
      if (index >= this.middlewares.length) {
        return Promise.resolve();
      }

      const middleware = this.middlewares[index];

      return Promise.resolve(middleware(context, () => dispatch(index + 1)));
    };

    return dispatch(0);
  }
}
```

Usage:

```js id="5jlwm8"
const app = new App();

app.use(async (ctx, next) => {
  console.log("Start 1");

  await next();

  console.log("End 1");
});

app.use(async (ctx, next) => {
  console.log("Start 2");

  await next();

  console.log("End 2");
});

app.compose({});
```

Output:

```txt id="4jlwm7"
Start 1
Start 2
End 2
End 1
```

This "onion model" is a key Koa concept.

---

# Frontend Middleware Example (Redux)

Redux middleware intercepts actions.

```js id="3jlwm6"
const loggerMiddleware = (store) => (next) => (action) => {
  console.log("Dispatching:", action);

  return next(action);
};
```

Usage:

```js id="2jlwm5"
const store = createStore(reducer, applyMiddleware(loggerMiddleware));
```

Middleware can:

- log actions
- modify actions
- delay actions
- dispatch async actions

---

# Implementing Redux-Like Middleware

---

# Store

```js id="1jlwm4"
function createStore(reducer, middlewares = []) {
  let state;

  const store = {
    getState: () => state,

    dispatch: (action) => {
      state = reducer(state, action);
    },
  };

  let dispatch = store.dispatch;

  middlewares.reverse().forEach((middleware) => {
    dispatch = middleware(store)(dispatch);
  });

  store.dispatch = dispatch;

  return store;
}
```

---

# Logger Middleware

```js id="0jlwm3"
const logger = (store) => (next) => (action) => {
  console.log("Before:", store.getState());

  next(action);

  console.log("After:", store.getState());
};
```

---

# Axios Interceptors (Middleware Concept)

Frontend HTTP clients also use middleware concepts.

```js id="zjlwm2"
axios.interceptors.request.use((config) => {
  config.headers.Authorization = "token";

  return config;
});
```

Response middleware:

```js id="yjlwm1"
axios.interceptors.response.use((response) => {
  return response.data;
});
```

---

# Real-World Middleware Use Cases

| Use Case       | Example                   |
| -------------- | ------------------------- |
| Authentication | JWT validation            |
| Logging        | Request logs              |
| Error handling | Global exception handling |
| Rate limiting  | API throttling            |
| Validation     | Input validation          |
| Caching        | Response cache            |
| Analytics      | Event tracking            |
| Compression    | Gzip middleware           |
| Security       | Helmet/CORS               |

---

# Middleware Composition

Middleware forms a pipeline.

```txt id="xjlwm0"
Request
 ↓
Auth
 ↓
Validation
 ↓
Logger
 ↓
Controller
```

Each layer has one responsibility.

---

# Short-Circuiting Middleware

Middleware can stop execution.

```js id="wjlwm9"
app.use((req, res, next) => {
  if (!req.user) {
    return res.status(401).send("Unauthorized");
  }

  next();
});
```

This is extremely common.

---

# Error Handling Middleware

---

# Custom Error Middleware

```js id="vjlwm8"
function errorMiddleware(err, req, res, next) {
  console.error(err);

  res.status(500).json({
    error: err.message,
  });
}
```

Centralized error management is a major benefit.

---

# Middleware vs Decorator Pattern

Interview nuance.

| Middleware          | Decorator                  |
| ------------------- | -------------------------- |
| Pipeline processing | Enhances object behavior   |
| Sequential chain    | Object wrapping            |
| Request lifecycle   | Class/function enhancement |

---

# Middleware vs Chain of Responsibility

Middleware is essentially a specialized form of Chain of Responsibility.

```txt id="ujlwm7"
Handler → Handler → Handler
```

Difference:

- middleware usually modifies request/response lifecycle
- chain-of-responsibility may route requests differently

---

# Common Pitfalls

---

# 1. Forgetting `next()`

Bad:

```js id="tjlwm6"
app.use((req, res, next) => {
  console.log("Oops");
});
```

Request hangs forever.

---

# 2. Calling `next()` Multiple Times

Can cause:

```txt id="sjlwm5"
Headers already sent
```

errors.

---

# 3. Shared Mutable State

Modifying shared objects carelessly can create bugs.

---

# 4. Deep Middleware Stacks

Too many layers can hurt:

- readability
- debugging
- performance

---

# 5. Async Error Handling

Forgotten `await` or uncaught promise rejections are common issues.

---

# Best Practices

---

# Keep Middleware Focused

One responsibility per middleware.

Good:

```txt id="rjlwm4"
authMiddleware
loggerMiddleware
cacheMiddleware
```

---

# Use Async/Await Carefully

Always handle errors properly.

---

# Order Matters

Middleware execution order is critical.

```txt id="qjlwm3"
Auth before protected routes
Logger before handlers
Error middleware last
```

---

# Avoid Business Logic in Middleware

Middleware should handle cross-cutting concerns.

---

# Interview Summary

The Middleware Pattern processes requests or data through a chain of handlers where each middleware can inspect, modify, terminate, or pass control forward.

Key concepts:

- chain-based processing
- `next()` delegation
- request/response lifecycle
- interception and transformation
- cross-cutting concerns

Common JavaScript implementations:

- Express middleware
- Koa async middleware
- Redux middleware
- Axios interceptors

Widely used for:

- authentication
- logging
- validation
- caching
- error handling
- analytics
- security

A strong interview answer should also discuss:

- middleware composition
- async middleware
- onion model
- short-circuiting
- middleware vs chain-of-responsibility
- common `next()` pitfalls
- request lifecycle management

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
