# Set 6

| S.No. | Question                                                                                                                                                    |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is a JavaScript engine? Give examples](#question-1-what-is-a-javascript-engine-give-examples)                                                         |
| 2.    | [What is the difference between client-side and server-side JavaScript?](#question-2-what-is-the-difference-between-client-side-and-server-side-javascript) |
| 3.    | [How does typeof operator work? Give examples](#question-3-how-does-typeof-operator-work-give-examples)                                                     |
| 4.    | [Explain the difference between undefined and undeclared variables](#question-4-explain-the-difference-between-undefined-and-undeclared-variables)          |
| 5.    | [What is the difference between primitive and reference data types?](#question-5-what-is-the-difference-between-primitive-and-reference-data-types)         |
| 6.    | [How does Infinity work in JavaScript?](#question-6-how-does-infinity-work-in-javascript)                                                                   |
| 7.    | [Difference between mutable and immutable objects](#question-7-difference-between-mutable-and-immutable-objects)                                            |
| 8.    | [What is the difference between a function and a method?](#question-8-what-is-the-difference-between-a-function-and-a-method)                               |
| 9.    | [How to check if a variable is an array?](#question-9-how-to-check-if-a-variable-is-an-array)                                                               |
| 10.   | [Difference between isArray() and instanceof Array](#question-10-difference-between-isarray-and-instanceof-array)                                           |
| 11.   | [Explain string methods like split(), join(), includes()](#question-11-explain-string-methods-like-split-join-includes)                                     |
| 12.   | [What is the difference between substr(), substring(), and slice()?](#question-12-what-is-the-difference-between-substr-substring-and-slice)                |
| 13.   | [Difference between push() and concat() in arrays](#question-13-difference-between-push-and-concat-in-arrays)                                               |
| 14.   | [How to reverse an array in JavaScript?](#question-14-how-to-reverse-an-array-in-javascript)                                                                |
| 15.   | [What is the difference between pop() and shift()?](#question-15-what-is-the-difference-between-pop-and-shift)                                              |
| 16.   | [How to find the largest number in an array?](#question-16-how-to-find-the-largest-number-in-an-array)                                                      |
| 17.   | [Difference between indexOf() and findIndex()](#question-17-difference-between-indexof-and-findindex)                                                       |
| 18.   | [Explain truthy and falsy values with examples](#question-18-explain-truthy-and-falsy-values-with-examples)                                                 |
| 19.   | [How to convert a string to number in JavaScript?](#question-19-how-to-convert-a-string-to-number-in-javascript)                                            |
| 20.   | [How to convert a number to a string in JavaScript?](#question-20-how-to-convert-a-number-to-a-string-in-javascript)                                        |

## Question 1. What is a JavaScript engine? Give examples

> A JavaScript engine is a program or interpreter that executes JavaScript code by converting it into machine code that the computer can understand and run.

### Detailed Explanation

A JavaScript engine is the **core component inside browsers and runtime environments (like Node.js)** that takes your JavaScript source code and executes it efficiently.

Since JavaScript is a high-level language, it cannot run directly on hardware. The engine is responsible for:

#### 1. Parsing

- Converts JavaScript code into an **Abstract Syntax Tree (AST)**
- Checks for syntax errors

#### 2. Compilation (Modern engines use JIT compilation)

- JavaScript is usually **Just-In-Time (JIT) compiled**
- Converts JS into optimized machine code at runtime
- Improves performance significantly

#### 3. Execution

- Executes the compiled machine code
- Manages memory (via garbage collection)

#### 4. Optimization

- Detects frequently used code paths
- Re-optimizes for better performance

### Popular JavaScript Engines

#### In Browsers

- **V8** → Used in Google Chrome, Microsoft Edge
- **SpiderMonkey** → Used in Mozilla Firefox
- **JavaScriptCore (Nitro)** → Used in Safari

#### Outside Browsers

- **Node.js uses V8 engine** (same as Chrome)

### Example (How it works conceptually)

```javascript
function add(a, b) {
  return a + b;
}

console.log(add(2, 3));
```

#### What the engine does

1. Parses the code into AST
2. Compiles it into optimized machine code
3. Executes `add(2, 3)`
4. Outputs `5`

### Key Concepts

#### 1. JIT Compilation

Modern engines don’t just interpret code—they compile it at runtime for speed.

#### 2. Memory Management

- JavaScript engines use **Garbage Collection**
- Automatically frees unused memory

#### 3. Event Loop Integration

The engine works with:

- Call Stack
- Heap
- Callback Queue / Microtask Queue

### Common Pitfalls / Misconceptions

#### ❌ JavaScript engine ≠ JavaScript runtime

- Engine: executes JS (e.g., V8)
- Runtime: includes engine + APIs (e.g., browser APIs or Node.js environment)

#### ❌ JavaScript is not purely interpreted anymore

- Modern engines use **hybrid interpretation + compilation (JIT)**

### Best Practice Insight

Understanding engines helps in:

- Writing **performance-optimized code**
- Avoiding memory leaks
- Understanding async behavior and event loop

## Question 2. What is the difference between client-side and server-side JavaScript?

**Direct answer:**

The main difference is **where the JavaScript code runs**:

- **Client-side JavaScript** runs in the user's browser and is mainly used for UI interactions, DOM manipulation, form validation, and making API requests.
- **Server-side JavaScript** runs on a server (typically using Node.js) and is used for handling requests, business logic, database operations, authentication, and generating responses.

---

# Detailed Explanation

## Client-Side JavaScript

Client-side JavaScript executes inside the browser's JavaScript engine (such as V8 in Chrome).

### Responsibilities

- Manipulating the DOM
- Handling user interactions
- Form validation
- Animations
- AJAX/Fetch API calls
- Updating UI without page reloads

### Example

```javascript
document.getElementById("btn").addEventListener("click", () => {
  document.body.style.backgroundColor = "lightblue";
});
```

When the user clicks the button, the browser immediately updates the page.

### Browser APIs Available

Client-side JavaScript can access:

```javascript
window;
document;
localStorage;
sessionStorage;
navigator;
fetch;
```

Example:

```javascript
console.log(window.location.href);
```

---

## Server-Side JavaScript

Server-side JavaScript runs on a server, commonly using Node.js.

### Responsibilities

- Processing HTTP requests
- Authentication and authorization
- Database operations
- File system access
- Business logic
- API creation

### Example

```javascript
const http = require("http");

http
  .createServer((req, res) => {
    res.end("Hello from server");
  })
  .listen(3000);
```

The code runs on the server and sends a response to clients.

### Server APIs Available

Node.js provides APIs such as:

```javascript
fs;
http;
https;
path;
crypto;
stream;
```

Example:

```javascript
const fs = require("fs");

const content = fs.readFileSync("data.txt", "utf8");
console.log(content);
```

---

# Comparison Table

| Feature                  | Client-Side JavaScript | Server-Side JavaScript    |
| ------------------------ | ---------------------- | ------------------------- |
| Runs On                  | Browser                | Server                    |
| Engine                   | Browser JS Engine      | Usually V8 via Node.js    |
| Access DOM               | ✅ Yes                 | ❌ No                     |
| Access Filesystem        | ❌ No                  | ✅ Yes                    |
| Access Database          | ❌ Directly No         | ✅ Yes                    |
| Visible to User          | ✅ Yes                 | ❌ No                     |
| Security Sensitive Logic | ❌ Not recommended     | ✅ Yes                    |
| Typical Use              | UI and interactions    | Backend services and APIs |

---

# Real-World Flow

Consider a login form:

### Client Side

```javascript
loginButton.addEventListener("click", async () => {
  const response = await fetch("/login", {
    method: "POST",
    body: JSON.stringify(credentials),
  });
});
```

### Server Side

```javascript
app.post("/login", async (req, res) => {
  const user = await db.findUser(req.body.email);

  if (user) {
    res.send("Login successful");
  }
});
```

The browser collects user input and sends it to the server. The server validates credentials and returns a response.

---

# Security Considerations

### Never trust client-side validation

Bad:

```javascript
if (age >= 18) {
  allowAccess();
}
```

A user can modify this code in browser developer tools.

Always validate again on the server:

```javascript
if (user.age >= 18) {
  grantAccess();
}
```

Server-side validation is the authoritative check.

---

# Interview Tip

A common interview answer is:

> Client-side JavaScript runs in the browser and focuses on user interface and interaction. Server-side JavaScript runs on the server, typically with Node.js, and handles business logic, databases, authentication, and API responses. Client-side code can access the DOM but not server resources directly, while server-side code can access files, databases, and system resources but cannot manipulate the browser DOM.

## Question 3. How does typeof operator work? Give examples

**Direct answer:**
The `typeof` operator in JavaScript returns a **string indicating the type of a value or variable at runtime**. It is mainly used for type checking.

---

# Detailed Explanation (Interview Perspective)

The `typeof` operator is a **unary operator** that evaluates its operand and returns a string representing its type.

### Syntax:

```javascript
typeof value;
typeof variable;
```

---

# How `typeof` Works Internally

JavaScript is a **dynamically typed language**, meaning types are determined at runtime. `typeof` helps inspect the runtime type.

It checks:

- Primitive types (string, number, boolean, etc.)
- Functions
- Objects (arrays, null, etc.)

---

# Return Values of `typeof`

| Value           | Result                         |
| --------------- | ------------------------------ |
| `"hello"`       | `"string"`                     |
| `123`           | `"number"`                     |
| `true`          | `"boolean"`                    |
| `undefined`     | `"undefined"`                  |
| `function() {}` | `"function"`                   |
| `{}`            | `"object"`                     |
| `null`          | `"object"` ⚠️ (historical bug) |

---

# Examples

## 1. Primitive types

```javascript
typeof "hello"; // "string"
typeof 42; // "number"
typeof true; // "boolean"
typeof undefined; // "undefined"
```

---

## 2. Objects

```javascript
typeof { name: "John" }; // "object"
typeof [1, 2, 3]; // "object"
typeof null; // "object" ❗
```

### Important pitfall:

```javascript
typeof null === "object"; // true
```

👉 This is a **well-known bug in JavaScript since its early days** and cannot be fixed due to backward compatibility.

To properly check null:

```javascript
value === null;
```

---

## 3. Functions

```javascript
function greet() {}
typeof greet; // "function"
```

Even though functions are objects in JavaScript, `typeof` treats them specially.

---

## 4. Arrays (important interview point)

```javascript
const arr = [1, 2, 3];

typeof arr; // "object"
```

To properly check arrays:

```javascript
Array.isArray(arr); // true
```

---

## 5. Undefined variable

```javascript
let x;
typeof x; // "undefined"
```

Even if a variable exists but has no value, it returns `"undefined"`.

---

## 6. Undeclared variables (special behavior)

```javascript
typeof notDeclared; // "undefined"
```

👉 Unlike normal access, this does NOT throw a ReferenceError.

This makes `typeof` useful for safe checks.

---

# Edge Cases & Pitfalls

## 1. `null` bug

```javascript
typeof null; // "object"
```

Reason: legacy JavaScript implementation error.

---

## 2. Arrays are objects

```javascript
typeof []; // "object"
```

Use:

```javascript
Array.isArray([]);
```

---

## 3. NaN is a number

```javascript
typeof NaN; // "number"
```

Even though NaN means "Not a Number", its type is number.

Check:

```javascript
Number.isNaN(value);
```

---

# Best Practices

### ✔ Use `typeof` for:

- Primitive type checks
- Function checks
- Safe access to possibly undefined variables

### ❌ Avoid using it for:

- Arrays → use `Array.isArray()`
- null → use strict equality
- Complex object type detection → use `Object.prototype.toString.call()`

---

# Advanced Interview Insight

Internally:

- `typeof` is not a function but an **operator**
- It operates at the engine level (e.g., V8 engine in V8)
- It performs **type tag inspection of values in memory**

---

# Summary (Interview-ready answer)

> The `typeof` operator returns a string representing the type of a value at runtime. It works for primitive types, functions, and objects. However, it has known quirks such as returning `"object"` for `null` and arrays. Therefore, while `typeof` is useful for basic type checking, additional methods like `Array.isArray()` and strict equality checks are often required for accurate type detection.

## Question 4. Explain the difference between undefined and undeclared variables

**Direct answer:**

- **`undefined`** means a variable has been declared but **not assigned a value**.
- **Undeclared** means a variable has **never been declared in the current scope at all**.

---

# Detailed Explanation (Interview Perspective)

This is a very common JavaScript interview concept because it tests understanding of **scope, hoisting, and runtime behavior**.

---

# 1. Undefined Variable

A variable is **declared but not assigned a value**.

### Example:

```javascript id="u1"
let a;
console.log(a); // undefined
```

### What is happening:

- Memory is allocated for `a`
- JavaScript initializes it with `undefined`

---

## Key characteristics of `undefined`

- It is a **primitive value**
- It is a valid value in JavaScript
- Type of undefined is:

```javascript id="u2"
typeof undefined; // "undefined"
```

---

## Common causes of `undefined`

### 1. Variable declared but not initialized

```javascript id="u3"
let x;
console.log(x); // undefined
```

### 2. Missing return value

```javascript id="u4"
function test() {}
console.log(test()); // undefined
```

### 3. Missing object property

```javascript id="u5"
const obj = {};
console.log(obj.name); // undefined
```

---

# 2. Undeclared Variable

A variable is **never declared using `var`, `let`, or `const`**.

### Example:

```javascript id="u6"
console.log(b); // ReferenceError: b is not defined
```

---

## Key characteristics of undeclared variables

- They do **not exist in memory**
- Accessing them throws a **ReferenceError**
- They are not part of any scope

---

# 3. Key Difference Table

| Feature           | undefined   | undeclared                 |
| ----------------- | ----------- | -------------------------- |
| Declared?         | Yes         | No                         |
| Memory allocation | Yes         | No                         |
| Value             | undefined   | Does not exist             |
| Error on access   | No          | Yes (ReferenceError)       |
| Typeof result     | "undefined" | "undefined" (special case) |

---

# 4. Important Interview Trick: `typeof`

```javascript id="u7"
typeof notDeclared; // "undefined"
```

👉 Even though `notDeclared` does not exist, `typeof` does NOT throw an error.

### Why?

Because `typeof` is designed to be **safe for undeclared variables**.

---

# 5. Hoisting Connection (Very Important)

### `var` example:

```javascript id="u8"
console.log(a); // undefined
var a = 10;
```

Internally:

```javascript
var a; // hoisted
console.log(a); // undefined
a = 10;
```

👉 This is still **undefined, not undeclared**

---

### `let/const` example:

```javascript id="u9"
console.log(a); // ReferenceError
let a = 10;
```

👉 This is **temporal dead zone (TDZ)**, not undefined

---

# 6. Temporal Dead Zone (Advanced Insight)

For `let` and `const`:

- Variable exists in scope
- But cannot be accessed before initialization

```javascript id="u10"
console.log(a); // ReferenceError
let a = 5;
```

This is different from both:

- undefined ❌
- undeclared ❌

---

# 7. Practical Example Comparison

```javascript id="u11"
let x;
console.log(x); // undefined (declared but not assigned)

console.log(y); // ReferenceError (undeclared variable)
```

---

# 8. Best Practices

### ✔ Avoid confusion:

- Always initialize variables
- Use `strict mode` to catch undeclared variables early

```javascript id="u12"
"use strict";

z = 10; // ReferenceError (good catch)
```

---

# Interview Summary Answer

> An `undefined` variable is one that has been declared but not assigned a value, while an undeclared variable has never been defined in the current scope. Accessing an undefined variable returns `undefined`, but accessing an undeclared variable throws a `ReferenceError`. The `typeof` operator is an exception, as it returns `"undefined"` even for undeclared variables without throwing an error.

## Question 5. What is the difference between primitive and reference data types?

**Direct answer:**
Primitive data types store **single values and are immutable**, while reference data types store **memory addresses pointing to objects**, and are mutable.

---

# Detailed Explanation (Interview Perspective)

JavaScript divides data types into two categories:

1. **Primitive types**
2. **Reference types (Objects)**

This difference is crucial for understanding **memory behavior, copying, and mutation**.

---

# 1. Primitive Data Types

Primitive types store **actual values directly in memory (stack)**.

### Primitive types in JavaScript:

- `string`
- `number`
- `boolean`
- `undefined`
- `null`
- `bigint`
- `symbol`

---

## Example:

```javascript id="p1"
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20
```

### Why this happens:

- `a` and `b` are stored independently
- Changing `b` does NOT affect `a`

---

## Key properties of primitives:

- Stored in **stack memory**
- **Immutable** (cannot be changed, only reassigned)
- Compared by **value**

```javascript id="p2"
console.log(5 === 5); // true
```

---

# 2. Reference Data Types

Reference types store a **reference (memory address) to the actual object**, not the value itself.

### Reference types:

- Objects `{ }`
- Arrays `[ ]`
- Functions `function() {}`
- Dates, Maps, Sets, etc.

---

## Example:

```javascript id="r1"
let obj1 = { name: "John" };
let obj2 = obj1;

obj2.name = "Alice";

console.log(obj1.name); // Alice
console.log(obj2.name); // Alice
```

### Why this happens:

- Both `obj1` and `obj2` point to the same memory reference
- Changing one affects the other

---

## Key properties of reference types:

- Stored in **heap memory**
- Variable stores a **reference (address)**
- **Mutable** (can be changed)
- Compared by **reference**

```javascript id="r2"
console.log({} === {}); // false (different references)
```

---

# 3. Memory Model (Important Interview Concept)

### Primitive:

```
Stack:
a → 10
b → 20
```

### Reference:

```
Stack:
obj1 → 0x001

Heap:
0x001 → { name: "Alice" }
```

---

# 4. Copying Behavior

## Primitive copy (independent):

```javascript id="c1"
let x = 5;
let y = x;

y = 10;

console.log(x); // 5
```

---

## Reference copy (shared reference):

```javascript id="c2"
let arr1 = [1, 2, 3];
let arr2 = arr1;

arr2.push(4);

console.log(arr1); // [1, 2, 3, 4]
```

---

# 5. How to create independent copies (important in interviews)

## For objects:

```javascript id="c3"
let obj1 = { a: 1 };

let obj2 = { ...obj1 }; // shallow copy
```

## For arrays:

```javascript id="c4"
let arr1 = [1, 2, 3];

let arr2 = [...arr1];
```

---

## Deep copy (important edge case):

```javascript id="c5"
let obj1 = { a: 1, b: { c: 2 } };

let obj2 = structuredClone(obj1);

obj2.b.c = 99;

console.log(obj1.b.c); // 2
```

---

# 6. Common Pitfalls

### ❌ Misunderstanding object copying:

```javascript id="p3"
let a = { x: 1 };
let b = a;

b.x = 10;

console.log(a.x); // 10 (unexpected for beginners)
```

---

### ❌ Comparing objects:

```javascript id="p4"
console.log({} === {}); // false
```

Because references differ.

---

# 7. Interview-Level Insight

### Key concept interviewers expect:

| Concept        | Primitive   | Reference       |
| -------------- | ----------- | --------------- |
| Stored in      | Stack       | Heap            |
| Copy behavior  | Value copy  | Reference copy  |
| Mutability     | Immutable   | Mutable         |
| Equality check | Value-based | Reference-based |

---

# 8. Real-world implication

### Bug scenario:

```javascript id="p5"
function update(user) {
  user.name = "Changed";
}
```

If `user` is passed as an object, original data changes due to reference sharing.

---

# Interview Summary Answer

> Primitive data types in JavaScript store values directly and are immutable, while reference data types store memory references to objects and are mutable. When assigning primitives, a copy of the value is created, but with reference types, multiple variables can point to the same object in memory, so changes affect all references. Understanding this distinction is essential for avoiding unintended side effects and managing data correctly.

## Question 6. How does Infinity work in JavaScript?

## Question 7. Difference between mutable and immutable objects

## Question 8. What is the difference between a function and a method?

## Question 9. How to check if a variable is an array?

## Question 10. Difference between isArray() and instanceof Array

## Question 11. Explain string methods like split(), join(), includes()

## Question 12. What is the difference between substr(), substring(), and slice()?

## Question 13. Difference between push() and concat() in arrays

## Question 14. How to reverse an array in JavaScript?

## Question 15. What is the difference between pop() and shift()?

## Question 16. How to find the largest number in an array?

## Question 17. Difference between indexOf() and findIndex()

## Question 18. Explain truthy and falsy values with examples

## Question 19. How to convert a string to number in JavaScript?

## Question 20. How to convert a number to a string in JavaScript?
