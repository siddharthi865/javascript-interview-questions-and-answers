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
