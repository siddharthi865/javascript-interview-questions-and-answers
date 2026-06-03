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

## Question 3. Explain var, let, and const with examples

**Short answer:**
`var`, `let`, and `const` are used to declare variables in JavaScript, but they differ in **scope, hoisting behavior, and reassignability**.

- `var` → function-scoped, hoisted, can be redeclared and reassigned
- `let` → block-scoped, hoisted but in Temporal Dead Zone, can be reassigned but not redeclared
- `const` → block-scoped, hoisted but in Temporal Dead Zone, cannot be reassigned or redeclared

---

# 🔍 Detailed Interview Explanation

To understand `var`, `let`, and `const`, you must understand three key concepts:

- **Scope (function vs block)**
- **Hoisting**
- **Reassignment & redeclaration rules**

---

# 1. 🟢 var

## Key characteristics:

- Function-scoped
- Hoisted (initialized as `undefined`)
- Can be redeclared and reassigned

---

## Example:

```js
function test() {
  console.log(a); // undefined (hoisting)
  var a = 10;
  console.log(a); // 10
}
test();
```

### What actually happens (hoisting):

```js
var a; // hoisted
console.log(a); // undefined
a = 10;
```

---

## Scope example:

```js
if (true) {
  var x = 100;
}
console.log(x); // 100 ❗ (not block scoped)
```

---

## Problems with var:

- Leaks outside block scope
- Can be redeclared accidentally

```js
var y = 10;
var y = 20; // allowed ❗
console.log(y); // 20
```

---

# 2. 🟡 let

## Key characteristics:

- Block-scoped (`{}`)
- Hoisted but not initialized (Temporal Dead Zone)
- Cannot be redeclared in same scope
- Can be reassigned

---

## Example:

```js
let a = 10;
a = 20; // allowed
console.log(a); // 20
```

---

## Block scope example:

```js
if (true) {
  let x = 100;
}
console.log(x); // ReferenceError ❗
```

---

## Temporal Dead Zone (TDZ):

```js
console.log(a); // ReferenceError
let a = 10;
```

👉 Even though `let` is hoisted, it cannot be accessed before initialization.

---

## No redeclaration:

```js
let b = 10;
let b = 20; // SyntaxError ❗
```

---

# 3. 🔵 const

## Key characteristics:

- Block-scoped
- Must be initialized at declaration
- Cannot be reassigned or redeclared
- Also has TDZ

---

## Example:

```js
const PI = 3.14;
console.log(PI);
```

---

## Reassignment not allowed:

```js
const a = 10;
a = 20; // TypeError ❗
```

---

## Must initialize:

```js
const b; // SyntaxError ❗
b = 10;
```

---

## ⚠️ Important: Objects & arrays with const

`const` prevents reassignment, not mutation.

```js
const user = { name: "John" };

user.name = "Doe"; // allowed ✅
console.log(user); // { name: "Doe" }

user = {}; // ❌ not allowed
```

Same with arrays:

```js
const arr = [1, 2, 3];
arr.push(4); // allowed
console.log(arr); // [1, 2, 3, 4]
```

---

# 📊 Comparison Table

| Feature         | var             | let          | const        |
| --------------- | --------------- | ------------ | ------------ |
| Scope           | Function        | Block        | Block        |
| Hoisting        | Yes (undefined) | Yes (TDZ)    | Yes (TDZ)    |
| Redeclare       | Yes             | No           | No           |
| Reassign        | Yes             | Yes          | No           |
| Must initialize | No              | No           | Yes          |
| Modern usage    | ❌ avoid        | ✅ preferred | ✅ preferred |

---

# ⚠️ Common Pitfalls

## 1. Hoisting confusion

```js
console.log(a);
var a = 5; // undefined, not error
```

---

## 2. TDZ with let/const

```js
console.log(a);
let a = 5; // ReferenceError
```

---

## 3. const misconception

- Many think `const` means immutable ❌
- It only means **binding is constant**, not object content

---

# 🚀 Best Practices (Interview-ready)

- Avoid `var` in modern JavaScript
- Use `const` by default
- Use `let` only when reassignment is needed
- Never rely on hoisting behavior in production code

---

# 🎯 Final Summary

- **var** → old, function-scoped, hoisted with `undefined`, avoid using
- **let** → modern, block-scoped, safe for variables that change
- **const** → modern, block-scoped, safest default choice

## Question 4. What are data types in JavaScript?

**Short answer:**
JavaScript has two main categories of data types: **Primitive types** and **Reference (Non-primitive) types**. Primitives store single values, while reference types store complex objects and are accessed by reference.

---

# 🔍 Detailed Interview Explanation

JavaScript is a **dynamically typed language**, meaning variables are not bound to a specific type at compile time. The type is determined at runtime based on the value assigned.

---

# 1. 🧱 Primitive Data Types

Primitive types are **immutable (cannot be changed directly)** and stored by **value**.

## 📌 Types of Primitive Data Types:

### 1. Number

Represents both integers and floating-point numbers.

```js id="num1"
let age = 25;
let price = 99.99;
```

👉 Special cases:

```js id="num2"
console.log(10 / 0); // Infinity
console.log("abc" * 2); // NaN
```

---

### 2. String

Represents text data.

```js id="str1"
let name = "John";
let greeting = `Hello ${name}`;
```

---

### 3. Boolean

Represents true or false.

```js id="bool1"
let isLoggedIn = true;
```

---

### 4. Undefined

A variable declared but not assigned a value.

```js id="undef1"
let a;
console.log(a); // undefined
```

---

### 5. Null

Represents intentional absence of value.

```js id="null1"
let data = null;
```

⚠️ Important:

```js id="null2"
console.log(typeof null); // "object" ❗ (historical bug)
```

---

### 6. Symbol (ES6)

Used to create unique identifiers.

```js id="sym1"
let id = Symbol("id");
let id2 = Symbol("id");

console.log(id === id2); // false
```

👉 Use case: Avoid property name collisions in objects.

---

### 7. BigInt (ES2020)

Used for very large integers beyond Number limit.

```js id="big1"
let big = 123456789012345678901234567890n;
```

---

# 2. 🧩 Non-Primitive (Reference) Data Types

These store **references to memory locations**, not actual values.

---

## 📌 Object

Collection of key-value pairs.

```js id="obj1"
let user = {
  name: "John",
  age: 30,
};
```

---

## 📌 Array (special type of object)

Ordered list of values.

```js id="arr1"
let numbers = [1, 2, 3, 4];
```

---

## 📌 Function (first-class object)

Functions are also objects in JavaScript.

```js id="fn1"
function greet() {
  return "Hello";
}
```

---

# 🧠 Key Concept: Value vs Reference

## Primitive (copied by value)

```js id="val1"
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20
```

---

## Reference type (copied by reference)

```js id="ref1"
let obj1 = { name: "Alice" };
let obj2 = obj1;

obj2.name = "Bob";

console.log(obj1.name); // Bob ❗
```

👉 Both variables point to the same memory location.

---

# 📊 Data Types Summary Table

| Category  | Type      | Example   | Mutability |
| --------- | --------- | --------- | ---------- |
| Primitive | Number    | 10        | Immutable  |
| Primitive | String    | "hello"   | Immutable  |
| Primitive | Boolean   | true      | Immutable  |
| Primitive | Undefined | undefined | Immutable  |
| Primitive | Null      | null      | Immutable  |
| Primitive | Symbol    | Symbol()  | Immutable  |
| Primitive | BigInt    | 10n       | Immutable  |
| Reference | Object    | {a:1}     | Mutable    |
| Reference | Array     | [1,2,3]   | Mutable    |
| Reference | Function  | fn()      | Mutable    |

---

# ⚠️ Common Interview Pitfalls

## 1. typeof null bug

```js id="pit1"
typeof null; // "object" ❗
```

---

## 2. Arrays are objects

```js id="pit2"
typeof []; // "object"
```

---

## 3. Reference confusion

```js id="pit3"
let a = { x: 1 };
let b = a;

b.x = 99;
console.log(a.x); // 99
```

---

# 🚀 Best Practices

- Use primitives for simple values
- Use objects for structured data
- Be careful with reference copying (use spread operator for cloning)

```js id="bp1"
let obj1 = { a: 1 };
let obj2 = { ...obj1 }; // shallow copy
```

---

# 🎯 Final Summary

- JavaScript has **two categories of data types**
  - **Primitive** → number, string, boolean, null, undefined, symbol, bigint
  - **Reference** → object, array, function

- Primitives are **value-based and immutable**
- Reference types are **reference-based and mutable**
- Understanding this difference is critical for avoiding bugs in JavaScript

## Question 5. Difference between null and undefined

**Short answer:**
`undefined` means a variable has been declared but not assigned a value, while `null` is an intentional assignment representing “no value” or “empty value”.

---

# 🔍 Detailed Interview Explanation

In JavaScript, both `null` and `undefined` represent absence of value, but they are used in **different contexts and have different meanings**.

---

# 1. 🟡 undefined

## 📌 Meaning:

A variable is `undefined` when:

- It is declared but not initialized
- A function does not return a value
- A missing object property is accessed

---

## Example 1: Declared but not assigned

```js id="u1"
let a;
console.log(a); // undefined
```

---

## Example 2: Missing return value

```js id="u2"
function test() {}
console.log(test()); // undefined
```

---

## Example 3: Missing object property

```js id="u3"
const user = { name: "John" };
console.log(user.age); // undefined
```

---

## Key idea:

👉 JavaScript engine assigns `undefined` automatically

---

# 2. 🔴 null

## 📌 Meaning:

`null` is a **manually assigned value** representing “no value” or “empty”.

---

## Example:

```js id="n1"
let user = null;
console.log(user); // null
```

---

## Key idea:

👉 Developer explicitly sets `null` to indicate intentional absence of value

---

# 🧠 Core Difference Concept

| Feature       | undefined         | null                |
| ------------- | ----------------- | ------------------- |
| Type          | Primitive         | Primitive           |
| Meaning       | Not assigned      | Intentionally empty |
| Assigned by   | JavaScript engine | Developer           |
| Default value | Yes               | No                  |
| Usage         | Implicit          | Explicit            |

---

# ⚙️ typeof Behavior (Important Interview Point)

```js id="t1"
console.log(typeof undefined); // "undefined"
console.log(typeof null); // "object" ❗ (historical bug)
```

👉 This is one of the most famous JavaScript quirks.

---

# 🔄 Equality Comparison

## Loose equality (==)

```js id="c1"
console.log(null == undefined); // true
```

👉 They are considered equal in loose comparison.

---

## Strict equality (===)

```js id="c2"
console.log(null === undefined); // false
```

👉 Because type is different.

---

# 🧪 Practical Example

## Case 1: API response handling

```js id="api1"
let data = null; // no data yet (intentional)
```

## Case 2: Missing value from system

```js id="api2"
let data;
console.log(data); // undefined (system assigned)
```

---

# ⚠️ Common Interview Pitfalls

## 1. Confusing meaning

- ❌ “Both mean same thing” → incorrect
- ✔️ undefined = system missing value
- ✔️ null = intentional absence

---

## 2. typeof null confusion

```js id="pit1"
typeof null; // "object"
```

👉 Interview trick question

---

## 3. Loose equality confusion

```js id="pit2"
null == undefined; // true
```

👉 But they are NOT strictly equal

---

# 🚀 Best Practices

- Use `undefined` as default state (system-level absence)
- Use `null` when you want to explicitly clear a variable
- Avoid relying on `==` comparisons
- Prefer strict equality `===`

---

# 🎯 Final Summary

- **undefined** → variable exists but has no value assigned (automatic)
- **null** → intentional empty value assigned by developer
- Both represent “no value” but differ in **intent and usage**
- `typeof null` returning `"object"` is a known historical bug
- `null == undefined` is `true`, but `null === undefined` is `false`

## Question 6. Explain type coercion in JavaScript

**Short answer:**
Type coercion in JavaScript is the automatic or implicit conversion of values from one data type to another (e.g., string → number, number → boolean) during operations like comparisons, arithmetic, or logical expressions.

---

# 🔍 Detailed Interview Explanation

JavaScript is a **dynamically typed language**, so when different data types interact, the engine often converts them internally to make the operation work. This behavior is called **type coercion**.

It happens mainly in two ways:

- **Implicit coercion** (automatic, by JavaScript engine)
- **Explicit coercion** (manual, by developer)

---

# 1. 🔄 Implicit Type Coercion

This happens automatically when JavaScript tries to “help” by converting types.

---

## 📌 String + Number (Concatenation wins)

```js id="c1"
console.log("5" + 2);
// "52"
```

👉 Rule:

- If `+` sees a string, it converts everything to string

---

## 📌 Other arithmetic operators convert to number

```js id="c2"
console.log("5" - 2); // 3
console.log("10" * "2"); // 20
console.log("10" / "2"); // 5
```

👉 Rule:

- `- * /` force numeric conversion

---

## 📌 Boolean coercion in numbers

```js id="c3"
console.log(true + 1); // 2
console.log(false + 1); // 1
```

👉 Rule:

- `true → 1`, `false → 0`

---

# 2. ⚖️ Equality Coercion (== vs ===)

## 📌 Loose equality (==) → performs coercion

```js id="e1"
console.log(5 == "5"); // true
```

👉 JavaScript converts string → number

---

## 📌 Strict equality (===) → no coercion

```js id="e2"
console.log(5 === "5"); // false
```

---

## ⚠️ Famous tricky cases

```js id="e3"
console.log(null == undefined); // true
console.log(0 == false); // true
console.log("" == false); // true
```

👉 These happen because JS tries to normalize types before comparison.

---

# 3. 🧠 Truthy and Falsy Coercion

JavaScript converts values to boolean in conditions.

---

## 📌 Falsy values:

Only these are falsy:

```js id="f1"
false;
0 - 0;
0n;
("");
null;
undefined;
NaN;
```

---

## 📌 Example:

```js id="f2"
if ("") {
  console.log("Won't run");
} else {
  console.log("Falsy value");
}
```

---

## 📌 Truthy values:

Everything else:

```js id="f3"
if ("hello") {
  console.log("Truthy");
}
```

---

# 4. 🔥 Classic Interview Tricky Examples

---

## Example 1: String + Array

```js id="t1"
console.log([] + []);
// ""
```

👉 Explanation:

- Arrays convert to string → ""
- "" + "" = ""

---

## Example 2: Array + Object

```js id="t2"
console.log([] + {});
// "[object Object]"
```

---

## Example 3: Object + Array

```js id="t3"
console.log({} + []);
// 0 or "[object Object]" depending on context (quirk)
```

👉 This depends on parsing rules (block vs expression context)

---

## Example 4: Double negation (!!)

```js id="t4"
console.log(!!"hello"); // true
console.log(!!0); // false
```

👉 Converts any value to boolean

---

# 5. 🔧 Explicit Type Coercion

Done intentionally by developer.

---

## 📌 String conversion

```js id="x1"
String(123); // "123"
(123).toString(); // "123"
```

---

## 📌 Number conversion

```js id="x2"
Number("123"); // 123
parseInt("123px"); // 123
```

---

## 📌 Boolean conversion

```js id="x3"
Boolean(0); // false
Boolean("hello"); // true
```

---

# ⚠️ Common Pitfalls (Very Important in Interviews)

## 1. Confusing == and ===

```js id="p1"
"5" == 5; // true (coercion)
"5" === 5; // false (no coercion)
```

---

## 2. Unexpected arithmetic behavior

```js id="p2"
"10" - 1; // 9
"10" + 1; // "101"
```

---

## 3. Falsy confusion

```js id="p3"
[] == false; // true
```

---

# 📊 Summary Table

| Case        | Result | Reason                  |
| ----------- | ------ | ----------------------- |
| "5" + 2     | "52"   | string concatenation    |
| "5" - 2     | 3      | numeric coercion        |
| true + 1    | 2      | boolean → number        |
| "5" == 5    | true   | loose equality coercion |
| "5" === 5   | false  | no coercion             |
| [] == false | true   | both coerced            |

---

# 🎯 Final Summary

- Type coercion = automatic type conversion in JavaScript
- Happens in:
  - Arithmetic operations
  - Comparisons (==)
  - Logical conditions (if, while)

- Two types:
  - Implicit (automatic)
  - Explicit (manual using Number/String/Boolean)

- `===` avoids coercion and is safer
- Many JS interview trick questions come from coercion rules

## Question 7. What is the difference between == and ===?

**Short answer:**
`==` checks equality with **type coercion**, while `===` checks equality without type coercion (strict equality).

---

# 🔍 Detailed Interview Explanation

In JavaScript, `==` and `===` are comparison operators, but they behave very differently because of **type coercion rules**.

---

# 1. ⚖️ Loose Equality (==)

## 📌 What it does:

- Compares values **after converting them to a common type**
- Performs **implicit type coercion**

---

## Example:

```js id="eq1"
console.log(5 == "5"); // true
```

👉 JavaScript converts `"5"` → `5` before comparison

---

## More examples:

```js id="eq2"
console.log(0 == false); // true
console.log("" == false); // true
console.log(null == undefined); // true
```

---

## ⚠️ Key behavior:

- Can produce **unexpected results**
- Follows complex coercion rules internally

---

# 2. 🔒 Strict Equality (===)

## 📌 What it does:

- Compares **both value and type**
- No type conversion happens

---

## Example:

```js id="eq3"
console.log(5 === "5"); // false
```

👉 Different types: number vs string

---

## More examples:

```js id="eq4"
console.log(0 === false); // false
console.log("" === false); // false
console.log(null === undefined); // false
```

---

## Exception:

```js id="eq5"
console.log(NaN === NaN); // false ❗
```

👉 In JavaScript, NaN is not equal to itself

Correct way:

```js id="eq6"
console.log(Number.isNaN(NaN)); // true
```

---

# 🧠 Core Difference

| Feature        | == (Loose Equality) | === (Strict Equality) |
| -------------- | ------------------- | --------------------- |
| Type check     | No                  | Yes                   |
| Type coercion  | Yes                 | No                    |
| Safety         | Less safe           | Recommended           |
| Performance    | Slightly slower     | Slightly faster       |
| Predictability | Low                 | High                  |

---

# 🔄 Internal Behavior (Important Interview Insight)

### When using `==`, JS performs:

1. Type conversion
2. Then comparison

Example:

```js id="int1"
"5" == 5;
```

Steps:

- "5" → 5 (string → number)
- 5 == 5 → true

---

# ⚠️ Common Interview Tricky Cases

## 1. null and undefined

```js id="t1"
console.log(null == undefined); // true
console.log(null === undefined); // false
```

---

## 2. Boolean coercion

```js id="t2"
console.log(true == 1); // true
console.log(true === 1); // false
```

---

## 3. Empty string

```js id="t3"
console.log("" == 0); // true
console.log("" === 0); // false
```

---

## 4. Arrays and objects

```js id="t4"
console.log([] == false); // true
console.log([] === false); // false
```

---

# 🧪 Real-world Example

## Bad practice (==)

```js id="r1"
if (userAge == "18") {
  console.log("Allowed");
}
```

👉 Can cause hidden bugs due to coercion

---

## Good practice (===)

```js id="r2"
if (userAge === 18) {
  console.log("Allowed");
}
```

👉 Predictable and safe

---

# 🚀 Best Practices (Interview Answer Key Point)

- Always prefer `===` in production code
- Avoid `==` unless you **intentionally want coercion behavior**
- Use strict equality to prevent hidden bugs

---

# 🎯 Final Summary

- `==` → loose equality, compares after type conversion
- `===` → strict equality, compares value + type
- `===` is safer, more predictable, and recommended
- Most JavaScript bugs related to comparisons come from `==`

## Question 8. What are JavaScript functions? How to declare them?

**Short answer:**
JavaScript functions are reusable blocks of code designed to perform a specific task. They can be declared in multiple ways such as function declarations, function expressions, arrow functions, and constructor functions.

---

# 🔍 Detailed Interview Explanation

A **function in JavaScript** is a first-class object that:

- Encapsulates logic
- Can take inputs (parameters)
- Can return outputs
- Can be passed around like variables

Functions are a core building block of JavaScript and heavily used in both functional and object-oriented programming styles.

---

# 1. 🧱 Function Declaration (Named Function)

## 📌 Syntax:

```js id="fd1"
function greet(name) {
  return `Hello, ${name}`;
}
```

## 📌 Usage:

```js id="fd2"
console.log(greet("John")); // Hello, John
```

---

## 🧠 Key features:

- Hoisted (can be used before declaration)
- Has its own `this`
- Most common and readable form

---

## Example of hoisting:

```js id="fd3"
console.log(add(2, 3)); // 5

function add(a, b) {
  return a + b;
}
```

👉 Works because function declarations are fully hoisted

---

# 2. 🧩 Function Expression

## 📌 Syntax:

```js id="fe1"
const greet = function (name) {
  return `Hello, ${name}`;
};
```

## 📌 Usage:

```js id="fe2"
console.log(greet("Alice"));
```

---

## 🧠 Key features:

- Not hoisted (only variable is hoisted, not function body)
- Can be anonymous or named
- More flexible (can be reassigned if using `let`)

---

## Example:

```js id="fe3"
console.log(test); // undefined

var test = function () {
  return "Hi";
};
```

---

# 3. 🚀 Arrow Functions (ES6)

## 📌 Syntax:

```js id="af1"
const greet = (name) => {
  return `Hello, ${name}`;
};
```

## Short form:

```js id="af2"
const greet = (name) => `Hello, ${name}`;
```

---

## 🧠 Key features:

- Shorter syntax
- Does NOT have its own `this`
- Cannot be used as constructors
- No `arguments` object

---

## Example:

```js id="af3"
const obj = {
  name: "John",
  greet: () => {
    console.log(this.name);
  },
};

obj.greet(); // undefined (because arrow function uses lexical this)
```

---

# 4. 🏗️ Function Constructor

## 📌 Syntax:

```js id="fc1"
const add = new Function("a", "b", "return a + b");
console.log(add(2, 3)); // 5
```

---

## 🧠 Key features:

- Rarely used
- Executes code from string (like eval)
- Not recommended due to security and performance issues

---

# 5. ⚙️ Immediately Invoked Function Expression (IIFE)

## 📌 Syntax:

```js id="iife1"
(function () {
  console.log("Executed immediately");
})();
```

---

## 🧠 Key features:

- Runs immediately after definition
- Used for encapsulation and avoiding global scope pollution

---

# 📊 Comparison Table

| Type                 | Syntax Style             | Hoisting | this binding | Usage              |
| -------------------- | ------------------------ | -------- | ------------ | ------------------ |
| Function Declaration | function fn() {}         | Yes      | Dynamic      | Most common        |
| Function Expression  | const fn = function() {} | No       | Dynamic      | Flexible           |
| Arrow Function       | const fn = () => {}      | No       | Lexical      | Modern preferred   |
| Constructor Function | new Function()           | No       | Dynamic      | Rare               |
| IIFE                 | (function(){})()         | No       | Dynamic      | One-time execution |

---

# ⚠️ Common Interview Pitfalls

---

## 1. Hoisting confusion

```js id="pit1"
sayHi();

function sayHi() {
  console.log("Hi");
}
```

✔ Works (function declaration)

---

```js id="pit2"
sayHi();

const sayHi = function () {
  console.log("Hi");
};
```

❌ Error (temporal dead zone / not hoisted fully)

---

## 2. Arrow function `this`

```js id="pit3"
const obj = {
  name: "JS",
  getName: () => {
    return this.name;
  },
};

console.log(obj.getName()); // undefined
```

---

## 3. Missing return in arrow function

```js id="pit4"
const add = (a, b) => {
  a + b;
};

console.log(add(2, 3)); // undefined
```

✔ Fix:

```js id="pit5"
const add = (a, b) => a + b;
```

---

# 🚀 Best Practices

- Use **function declarations** for reusable utilities
- Use **arrow functions** for callbacks and short logic
- Avoid `Function constructor`
- Prefer arrow functions in modern code unless `this` is needed
- Use IIFE only when isolation is required (legacy patterns)

---

# 🎯 Final Summary

- Functions are reusable blocks of code and first-class citizens in JavaScript
- They can be declared as:
  - Function declaration
  - Function expression
  - Arrow function (modern standard)
  - Constructor function (rare)
  - IIFE (immediate execution)

- Key differences involve **hoisting, syntax, and `this` behavior**

## Question 9. Difference between function declaration and function expression

**Short answer:**
A **function declaration** defines a named function that is hoisted completely, while a **function expression** defines a function assigned to a variable and is not hoisted in the same way (only the variable is hoisted, not the function definition).

---

# 🔍 Detailed Interview Explanation

In JavaScript, both function declaration and function expression are ways to define functions, but they differ in **hoisting, syntax, and usage behavior**.

---

# 1. 🧱 Function Declaration

## 📌 Syntax:

```js id="fd1"
function add(a, b) {
  return a + b;
}
```

---

## 📌 Key characteristics:

### ✔ Fully hoisted

You can call it before its definition:

```js id="fd2"
console.log(add(2, 3)); // 5

function add(a, b) {
  return a + b;
}
```

👉 Entire function is moved to the top during memory creation phase.

---

### ✔ Named function

Always has a name (`add` in this case).

---

### ✔ Best for reusable logic

Used for utility/helper functions.

---

# 2. 🧩 Function Expression

## 📌 Syntax:

```js id="fe1"
const add = function (a, b) {
  return a + b;
};
```

---

## 📌 Key characteristics:

### ❌ Not fully hoisted

Only the variable is hoisted, not the function body.

```js id="fe2"
console.log(add); // undefined

var add = function (a, b) {
  return a + b;
};
```

👉 Function is assigned only at runtime.

---

### ✔ Can be anonymous or named

```js id="fe3"
const greet = function () {
  return "Hello";
};
```

Or named:

```js id="fe4"
const greet = function sayHello() {
  return "Hello";
};
```

---

### ✔ Can be assigned dynamically

```js id="fe5"
let operation;

if (true) {
  operation = function (a, b) {
    return a - b;
  };
}

console.log(operation(5, 2)); // 3
```

---

# 📊 Key Differences Table

| Feature          | Function Declaration        | Function Expression              |
| ---------------- | --------------------------- | -------------------------------- |
| Syntax           | `function add() {}`         | `const add = function() {}`      |
| Hoisting         | Fully hoisted               | Only variable hoisted            |
| Execution timing | Available before definition | Available only after assignment  |
| Name required    | Yes                         | Optional                         |
| Flexibility      | Less flexible               | More flexible                    |
| Use case         | Utility functions           | Dynamic or conditional functions |

---

# 🧠 Hoisting Behavior (Very Important)

## Function Declaration:

```js id="h1"
sayHi(); // works

function sayHi() {
  console.log("Hi");
}
```

---

## Function Expression:

```js id="h2"
sayHi(); // ❌ TypeError or undefined

var sayHi = function () {
  console.log("Hi");
};
```

---

### 🔥 Why this happens:

During memory phase:

### Function declaration:

```js id="h3"
// fully loaded into memory
sayHi → function definition
```

### Function expression:

```js id="h4"
// only variable is hoisted
sayHi → undefined
```

---

# ⚠️ Common Interview Pitfalls

---

## 1. Confusing hoisting behavior

```js id="p1"
console.log(fn); // undefined

var fn = function () {
  return "Hello";
};
```

---

## 2. TDZ with let/const expressions

```js id="p2"
console.log(fn); // ReferenceError

const fn = function () {};
```

---

## 3. Assuming both behave the same

👉 They do NOT behave the same during execution context creation.

---

# 🚀 Best Practices

- Use **function declarations** for general reusable functions
- Use **function expressions** when:
  - You need conditional assignment
  - You want to pass functions as values
  - You want better control over scope

- Prefer `const` with function expressions to avoid reassignment bugs

```js id="bp1"
const multiply = function (a, b) {
  return a * b;
};
```

---

# 🎯 Final Summary

- **Function Declaration**
  - Fully hoisted
  - Can be called before definition
  - Best for reusable utilities

- **Function Expression**
  - Not fully hoisted
  - Assigned to variables at runtime
  - More flexible and commonly used in modern JS

## Question 10. What are arrow functions? Give examples

**Short answer:**
Arrow functions are a shorter syntax for writing functions in JavaScript (introduced in ES6). They also differ from normal functions in how they handle `this`, `arguments`, and constructors.

---

# 🔍 Detailed Interview Explanation

Arrow functions are **anonymous functions** with a concise syntax and **lexical `this` binding**. They are commonly used for callbacks, functional programming, and short logic blocks.

---

# 1. 🚀 Basic Syntax

## Traditional function:

```js id="f1"
function add(a, b) {
  return a + b;
}
```

---

## Arrow function:

```js id="f2"
const add = (a, b) => {
  return a + b;
};
```

---

## Shorter version (implicit return):

```js id="f3"
const add = (a, b) => a + b;

console.log(add(2, 3)); // 5
```

---

# 2. 🧠 Key Features of Arrow Functions

---

## ✔ 1. Shorter Syntax

```js id="k1"
const greet = (name) => `Hello ${name}`;
```

If there is only **one parameter**, parentheses can be omitted.

---

## ✔ 2. Implicit return

If function body has one expression:

```js id="k2"
const square = (x) => x * x;
```

No need for `return` keyword or `{}`.

---

## ✔ 3. Lexical `this` (MOST IMPORTANT INTERVIEW POINT)

Arrow functions do NOT have their own `this`. They inherit `this` from surrounding scope.

---

### Example (normal function):

```js id="t1"
const obj = {
  name: "JS",
  getName: function () {
    return this.name;
  },
};

console.log(obj.getName()); // JS
```

---

### Arrow function example:

```js id="t2"
const obj = {
  name: "JS",
  getName: () => {
    return this.name;
  },
};

console.log(obj.getName()); // undefined
```

👉 Because `this` is taken from outer scope (not `obj`)

---

# 3. 🚫 Arrow Functions Cannot Be Constructors

```js id="c1"
const Person = (name) => {
  this.name = name;
};

const p1 = new Person("John"); // ❌ Error
```

👉 Arrow functions cannot be used with `new`

---

# 4. 🚫 No `arguments` Object

## Normal function:

```js id="a1"
function test() {
  console.log(arguments);
}

test(1, 2, 3);
```

---

## Arrow function:

```js id="a2"
const test = () => {
  console.log(arguments); // ❌ ReferenceError
};
```

---

## Workaround:

Use rest parameters:

```js id="a3"
const test = (...args) => {
  console.log(args);
};

test(1, 2, 3);
```

---

# 5. 🧩 Arrow Functions in Array Methods

Very commonly used in modern JavaScript:

---

## map example:

```js id="m1"
const nums = [1, 2, 3];

const squared = nums.map((n) => n * n);

console.log(squared); // [1, 4, 9]
```

---

## filter example:

```js id="m2"
const nums = [1, 2, 3, 4];

const even = nums.filter((n) => n % 2 === 0);

console.log(even); // [2, 4]
```

---

## reduce example:

```js id="m3"
const nums = [1, 2, 3, 4];

const sum = nums.reduce((acc, n) => acc + n, 0);

console.log(sum); // 10
```

---

# 📊 Arrow Function vs Normal Function

| Feature             | Arrow Function             | Normal Function       |
| ------------------- | -------------------------- | --------------------- |
| Syntax              | Short                      | Verbose               |
| `this`              | Lexical                    | Dynamic               |
| `arguments`         | Not available              | Available             |
| Constructor (`new`) | ❌ Not allowed             | ✅ Allowed            |
| Use case            | Callbacks, functional code | Methods, constructors |

---

# ⚠️ Common Interview Pitfalls

---

## 1. Misunderstanding `this`

```js id="p1"
const obj = {
  name: "JS",
  getName: () => this.name,
};
```

👉 `this` does NOT refer to `obj`

---

## 2. Using arrow function as method

```js id="p2"
const obj = {
  value: 10,
  show: () => {
    console.log(this.value);
  },
};
```

❌ Wrong usage for object methods

---

## 3. Forgetting implicit return rules

```js id="p3"
const add = (a, b) => {
  a + b; // ❌ undefined
};
```

✔ Fix:

```js id="p4"
const add = (a, b) => a + b;
```

---

# 🚀 Best Practices

- Use arrow functions for:
  - Callbacks (`map`, `filter`, `reduce`)
  - Short utility functions

- Avoid arrow functions for:
  - Object methods
  - Constructors

- Prefer readability over excessive brevity

---

# 🎯 Final Summary

- Arrow functions are a **concise way to write functions**
- Introduced in **ES6**
- Key differences:
  - No own `this` (lexical binding)
  - No `arguments`
  - Cannot be used as constructors

- Best suited for **callbacks and functional programming**

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
