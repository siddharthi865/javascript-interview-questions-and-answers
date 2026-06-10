# Set 1

| S.No. | Question                                                                                                                                           |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What are the main features of JavaScript?](#question-1-what-are-the-main-features-of-javascript)                                                  |
| 2.    | [Difference between JavaScript, Java, and ECMAScript](#question-2-difference-between-javascript-java-and-ecmascript)                               |
| 3.    | [Explain var, let, and const with examples](#question-3-explain-var-let-and-const-with-examples)                                                   |
| 4.    | [What are data types in JavaScript?](#question-4-what-are-data-types-in-javascript)                                                                |
| 5.    | [Difference between null and undefined](#question-5-difference-between-null-and-undefined)                                                         |
| 6.    | [Explain type coercion in JavaScript](#question-6-explain-type-coercion-in-javascript)                                                             |
| 7.    | [What is the difference between == and ===?](#question-7-what-is-the-difference-between--and-)                                                     |
| 8.    | [What are JavaScript functions? How to declare them?](#question-8-what-are-javascript-functions-how-to-declare-them)                               |
| 9.    | [Difference between function declaration and function expression](#question-9-difference-between-function-declaration-and-function-expression)     |
| 10.   | [What are arrow functions? Give examples](#question-10-what-are-arrow-functions-give-examples)                                                     |
| 11.   | [What are template literals in JavaScript?](#question-11-what-are-template-literals-in-javascript)                                                 |
| 12.   | [How do you add comments in JavaScript?](#question-12-how-do-you-add-comments-in-javascript)                                                       |
| 13.   | [Difference between global and local scope](#question-13-difference-between-global-and-local-scope)                                                |
| 14.   | [What is hoisting in JavaScript?](#question-14-what-is-hoisting-in-javascript)                                                                     |
| 15.   | [Difference between function scope and block scope](#question-15-difference-between-function-scope-and-block-scope)                                |
| 16.   | [Explain closures in simple words](#question-16-explain-closures-in-simple-words)                                                                  |
| 17.   | [What is the difference between synchronous and asynchronous code?](#question-17-what-is-the-difference-between-synchronous-and-asynchronous-code) |
| 18.   | [What is an event in JavaScript?](#question-18-what-is-an-event-in-javascript)                                                                     |
| 19.   | [Explain event bubbling and event capturing](#question-19-explain-event-bubbling-and-event-capturing)                                              |
| 20.   | [What is the DOM? How do you access it in JavaScript?](#question-20-what-is-the-dom-how-do-you-access-it-in-javascript)                            |

## Question 1. What are the main features of JavaScript?

> JavaScript is a high-level, interpreted, dynamically typed, object-based scripting language used mainly for web development. Its main features include dynamic typing, first-class functions, prototype-based inheritance, asynchronous programming, and cross-platform support.

### Detailed Explanation

JavaScript is one of the core technologies of the web (along with HTML and CSS). It runs in browsers and also on servers (via Node.js). Its design makes it flexible, dynamic, and well-suited for both frontend and backend development.

Here are the **main features of JavaScript** explained in an interview-friendly way:

#### 1. Lightweight and Interpreted Language

JavaScript is not compiled ahead of time like Java or C++.

- It is interpreted (or JIT-compiled in modern engines like V8).
- Runs directly in the browser or runtime environments.

```js
console.log("Hello, JavaScript!");
```

Benefit: Fast development and instant execution in browsers.

#### 2. Dynamic Typing

Variables are not bound to a fixed type.

```js
let value = 10; // number
value = "hello"; // now string
```

Benefit:

- Flexible and fast to write
- Less boilerplate

Pitfall:

- Can lead to runtime type errors

#### 3. Object-Based (Prototype-Based Inheritance)

JavaScript uses prototypes instead of classical inheritance.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  return `Hello, ${this.name}`;
};

const p1 = new Person("Alice");
console.log(p1.greet());
```

Key concept:

- Objects inherit directly from other objects via prototypes
- ES6 classes are syntactic sugar over prototypes

#### 4. First-Class and Higher-Order Functions

Functions are treated like variables.

```js
function greet(name) {
  return `Hello ${name}`;
}

function process(fn) {
  return fn("John");
}

console.log(process(greet));
```

Enables:

- Functional programming
- Callbacks
- Higher-order utilities like `map`, `filter`, `reduce`

#### 5. Asynchronous & Non-blocking Behavior

JavaScript can handle asynchronous operations efficiently using:

- Callbacks
- Promises
- async/await
- Event loop

```js
console.log("Start");

setTimeout(() => {
  console.log("Async task");
}, 1000);

console.log("End");
```

Output:

```terminal
Start
End
Async task
```

Key concept:

- Event loop handles async tasks without blocking main thread

#### 6. Cross-Platform Compatibility

JavaScript runs in:

- Browsers (Chrome, Firefox, Safari, etc.)
- Servers (Node.js)
- Mobile apps (React Native)
- Desktop apps (Electron)

Benefit: One language everywhere (full-stack capability)

#### 7. Event-Driven Programming

JavaScript reacts to user actions and events.

```js
button.addEventListener("click", () => {
  console.log("Button clicked!");
});
```

Core idea:

- Everything is driven by events (clicks, inputs, timers)

#### 8. Rich Ecosystem & Libraries

JavaScript has a massive ecosystem:

- React, Angular, Vue (frontend)
- Express, NestJS (backend)
- npm ecosystem (largest package registry)

#### 9. Single-Threaded but Concurrency-Friendly

JavaScript runs on a single thread but handles concurrency using:

- Event loop
- Callback queue
- Microtasks (Promises)

Benefit:

- Simplified execution model
- Efficient handling of I/O operations

#### 10. Modern ES6+ Features

Modern JavaScript includes powerful features:

- let/const
- Arrow functions
- Classes
- Modules
- Destructuring
- Spread/rest operators

```js
const user = { name: "John", age: 25 };
const { name } = user;
```

### Common Pitfalls (Interview Insight)

- Type coercion surprises:

```js
console.log("5" + 2); // "52"
console.log("5" - 2); // 3
```

- Hoisting confusion
- `this` binding issues
- Callback hell (before Promises)

### Summary

JavaScript is:

- Flexible (dynamic typing, first-class functions)
- Powerful (async + event-driven model)
- Universal (runs everywhere)
- Prototype-based (not class-based OOP internally)
- Highly ecosystem-driven

## Question 2. Difference between JavaScript, Java, and ECMAScript

**Short answer:**
JavaScript is a programming language used to build web applications, Java is a general-purpose object-oriented programming language, and ECMAScript is the standardized specification that defines how JavaScript should behave.

---

# Detailed Explanation

These three terms are often confused in interviews, but they refer to **different layers of the language ecosystem**.

---

# 1. ☕ Java

## What it is:

Java is a **statically typed, object-oriented, compiled programming language** developed by Sun Microsystems (now Oracle).

## Key characteristics:

- Compiled to bytecode and runs on JVM (Java Virtual Machine)
- Strict OOP (everything revolves around classes)
- Strongly typed
- Platform-independent via “Write Once, Run Anywhere”

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello Java");
    }
}
```

## Key use cases:

- Enterprise applications
- Android development
- Backend systems (Spring Boot)

## Key points:

- Class-based OOP
- Compile-time type checking
- Verbose syntax compared to JavaScript

---

# 2. 🌐 JavaScript

## What it is:

JavaScript is a **dynamic, interpreted (or JIT-compiled), prototype-based scripting language** mainly used for web development.

## Key characteristics:

- Runs in browsers and Node.js
- Dynamically typed
- Supports functional + object-oriented styles
- Event-driven and asynchronous

```js
console.log("Hello JavaScript");
```

## Key use cases:

- Frontend development (React, Angular, Vue)
- Backend development (Node.js)
- Full-stack applications

## Key points:

- Prototype-based inheritance (not class-based internally)
- Flexible and loosely typed
- Built for interactivity on the web

---

# 3. 📜 ECMAScript (ES)

## What it is:

ECMAScript is a **language specification (standard)** created by ECMA International that defines the rules, syntax, and features of scripting languages like JavaScript.

👉 JavaScript is an **implementation of ECMAScript**

## Think of it like this:

- ECMAScript = Blueprint / Standard
- JavaScript = Real-world implementation of that blueprint

## Example:

ES6 (ECMAScript 2015) introduced:

- `let`, `const`
- Arrow functions
- Classes
- Modules
- Promises

```js
const add = (a, b) => a + b;
```

---

# 🧠 Relationship Between Them

## Simple analogy:

- ECMAScript → Rules of the language (like grammar rules)
- JavaScript → Language built using those rules
- Java → Completely different language with its own ecosystem

---

# 📊 Key Differences Table

| Feature   | Java                              | JavaScript                          | ECMAScript                |
| --------- | --------------------------------- | ----------------------------------- | ------------------------- |
| Type      | Programming language              | Programming language                | Specification (standard)  |
| Typing    | Static                            | Dynamic                             | N/A                       |
| Execution | JVM (compiled bytecode)           | Browser / Node.js                   | N/A                       |
| OOP model | Class-based                       | Prototype-based                     | Defines language features |
| Purpose   | General-purpose, backend, Android | Web + full-stack                    | Standard for JS language  |
| Ownership | Oracle                            | Multiple engines (V8, SpiderMonkey) | ECMA International        |

---

# ⚠️ Common Interview Confusions

## 1. Java vs JavaScript

👉 They are **completely different languages**

- Java ≠ JavaScript
- Similar name is just marketing history (JavaScript was originally called “LiveScript”)

---

## 2. JavaScript vs ECMAScript

👉 ECMAScript defines the **standard**
👉 JavaScript is the **implementation**

- Chrome’s V8 engine implements ECMAScript
- Firefox’s SpiderMonkey also implements ECMAScript

---

## 3. Why ECMAScript matters

- Ensures all browsers follow the same rules
- Introduces new features in versions (ES5, ES6, ES2020, etc.)

---

# 🚀 Final Summary

- **Java** → Statically typed, class-based, JVM language used for enterprise and Android.
- **JavaScript** → Dynamic, prototype-based scripting language for web and server-side development.
- **ECMAScript** → The official specification that defines how JavaScript should work.

---

If you want, I can next explain **how JavaScript engines (like V8) implement ECMAScript step by step**, which is a very common senior-level interview question.

## Question 3. Explain var, let, and const with examples

## Question 4. What are data types in JavaScript?

## Question 5. Difference between null and undefined

## Question 6. Explain type coercion in JavaScript

## Question 7. What is the difference between == and ===?

## Question 8. What are JavaScript functions? How to declare them?

## Question 9. Difference between function declaration and function expression

## Question 10. What are arrow functions? Give examples

## Question 11. What are template literals in JavaScript?

## Question 12. How do you add comments in JavaScript?

## Question 13. Difference between global and local scope

## Question 14. What is hoisting in JavaScript?

## Question 15. Difference between function scope and block scope

## Question 16. Explain closures in simple words

## Question 17. What is the difference between synchronous and asynchronous code?

## Question 18. What is an event in JavaScript?

## Question 19. Explain event bubbling and event capturing

## Question 20. What is the DOM? How do you access it in JavaScript?
