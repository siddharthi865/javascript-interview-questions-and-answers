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

**Direct answer:**
`Infinity` in JavaScript is a special numeric value representing **mathematical infinity**, used when a number exceeds the largest representable finite number or results from division by zero or overflow-like operations.

---

# Detailed Explanation (Interview Perspective)

In JavaScript, `Infinity` is part of the **Number type** and represents an **unbounded value larger than any finite number**.

It is defined in the global scope:

```javascript
Infinity === Number.POSITIVE_INFINITY; // true
```

And there is also:

```javascript
-Infinity === Number.NEGATIVE_INFINITY;
```

---

# 1. When does `Infinity` occur?

## 1. Division by zero

```javascript id="i1"
console.log(10 / 0); // Infinity
console.log(-10 / 0); // -Infinity
```

👉 Unlike some languages, JavaScript does NOT throw an error.

---

## 2. Overflow beyond Number.MAX_VALUE

```javascript id="i2"
console.log(Number.MAX_VALUE); // largest finite number

console.log(Number.MAX_VALUE * 2); // Infinity
```

👉 Once a number exceeds the representable range, it becomes `Infinity`.

---

## 3. Mathematical operations

```javascript id="i3"
console.log(Math.pow(10, 1000)); // Infinity
```

---

## 4. Explicit assignment

```javascript id="i4"
let x = Infinity;
console.log(x); // Infinity
```

---

# 2. Types of Infinity

JavaScript has two special values:

| Value       | Meaning           |
| ----------- | ----------------- |
| `Infinity`  | Positive infinity |
| `-Infinity` | Negative infinity |

```javascript id="i5"
console.log(1 / 0); // Infinity
console.log(-1 / 0); // -Infinity
```

---

# 3. Type of Infinity

```javascript id="i6"
console.log(typeof Infinity); // "number"
```

👉 Important interview point:
Even though it represents “infinity”, it is still a **number type**.

---

# 4. Comparisons with Infinity

```javascript id="i7"
console.log(Infinity > 1000000); // true
console.log(Infinity > Number.MAX_VALUE); // true
console.log(-Infinity < -999999); // true
```

---

# 5. Special edge cases

## NaN vs Infinity

```javascript id="i8"
console.log(Infinity - Infinity); // NaN
```

👉 Any undefined mathematical operation results in `NaN`.

---

## Arithmetic with Infinity

```javascript id="i9"
console.log(Infinity + 1); // Infinity
console.log(Infinity * 2); // Infinity
console.log(Infinity / 2); // Infinity
```

👉 Infinity behaves like an absorbing value in most operations.

---

# 6. Checking for Infinity

## Correct way:

```javascript id="i10"
Number.isFinite(10); // true
Number.isFinite(Infinity); // false
```

## Simple check:

```javascript id="i11"
Infinity === value;
```

---

# 7. Pitfall: `isFinite()` vs `Number.isFinite()`

## Global `isFinite()` (coerces value):

```javascript id="i12"
isFinite("10"); // true (string coerced)
```

## Strict version:

```javascript id="i13"
Number.isFinite("10"); // false
```

---

# 8. Real-world usage

### 1. Initialization in algorithms

```javascript id="i14"
let min = Infinity;

for (let num of [5, 2, 9]) {
  if (num < min) min = num;
}

console.log(min); // 2
```

---

### 2. Graph / distance problems

Used as a placeholder for "very large value".

---

# 9. Internal behavior (engine-level insight)

In engines like V8:

- `Infinity` is a **special IEEE 754 floating-point value**
- JavaScript numbers are 64-bit floating point (double precision)
- This allows representation of:
  - finite numbers
  - `NaN`
  - `Infinity` / `-Infinity`

---

# 10. Key interview summary

> `Infinity` in JavaScript is a special numeric value representing values larger than the maximum finite number or results of operations like division by zero or overflow. It is of type "number", follows IEEE 754 standards, and has both positive and negative forms. Most arithmetic operations with Infinity return Infinity, except undefined operations like `Infinity - Infinity`, which result in `NaN`.

## Question 7. Difference between mutable and immutable objects

**Direct answer:**
Mutable objects are those whose **state or values can be changed after creation**, while immutable objects are those whose **state cannot be changed once created**—any modification creates a new value instead of altering the original.

---

# Detailed Explanation (Interview Perspective)

This concept is important in JavaScript for understanding **memory behavior, side effects, and functional programming patterns**.

---

# 1. Immutable Objects

An object is **immutable** if its value cannot be changed once created.

In JavaScript, most **primitive types are immutable**.

### Examples of immutable types:

- `string`
- `number`
- `boolean`
- `null`
- `undefined`
- `bigint`
- `symbol`

---

## Example (String immutability)

```javascript id="imm1"
let str = "hello";
str[0] = "H";

console.log(str); // "hello"
```

👉 The string does NOT change.

If you modify it, a **new string is created**:

```javascript id="imm2"
let str = "hello";
str = "H" + str.slice(1);

console.log(str); // "Hello"
```

---

## Key properties of immutable values:

- Cannot be changed in place
- Any “modification” creates a new value
- Safe to share between variables

---

# 2. Mutable Objects

Mutable objects are those whose **internal state can be changed without creating a new object**.

In JavaScript, most **reference types are mutable**:

- Objects `{ }`
- Arrays `[ ]`
- Functions
- Maps, Sets, etc.

---

## Example (Object mutation)

```javascript id="mut1"
let obj = { name: "John" };

obj.name = "Alice";

console.log(obj.name); // "Alice"
```

👉 The same object is modified in memory.

---

## Example (Array mutation)

```javascript id="mut2"
let arr = [1, 2, 3];

arr.push(4);

console.log(arr); // [1, 2, 3, 4]
```

---

## Key properties of mutable objects:

- Can be changed in place
- Multiple references point to same object
- Changes are visible everywhere

---

# 3. Memory Behavior (Important Interview Concept)

## Immutable (Primitive)

```text
Stack:
a → "hello"
b → "hello" (new copy)
```

Each variable holds its own value.

---

## Mutable (Reference type)

```text
Stack:
obj1 → 0x001
obj2 → 0x001

Heap:
0x001 → { name: "Alice" }
```

Both variables point to same memory.

---

# 4. Example of shared mutation problem

```javascript id="mut3"
let user1 = { name: "John" };
let user2 = user1;

user2.name = "Alice";

console.log(user1.name); // "Alice"
```

👉 Because objects are mutable and shared by reference.

---

# 5. How to achieve immutability in objects

## 1. Spread operator (shallow copy)

```javascript id="imm3"
const obj1 = { a: 1 };
const obj2 = { ...obj1 };

obj2.a = 10;

console.log(obj1.a); // 1
```

---

## 2. Object.freeze()

```javascript id="imm4"
const obj = Object.freeze({ a: 1 });

obj.a = 10;

console.log(obj.a); // 1 (no change)
```

👉 Prevents modification (shallow freeze only)

---

## 3. Deep immutability (modern approach)

```javascript id="imm5"
const obj1 = { a: { b: 1 } };

const obj2 = structuredClone(obj1);

obj2.a.b = 99;

console.log(obj1.a.b); // 1
```

---

# 6. Important Differences Table

| Feature         | Immutable         | Mutable                    |
| --------------- | ----------------- | -------------------------- |
| Can be changed  | ❌ No             | ✅ Yes                     |
| Memory behavior | New copy created  | Same object modified       |
| Data types      | Primitives        | Objects                    |
| Safety          | High              | Risk of side effects       |
| Performance     | More memory usage | More efficient for updates |

---

# 7. Real-world usage

## Immutable usage:

- Functional programming
- Redux state management
- Avoiding side effects

## Mutable usage:

- DOM manipulation
- Performance-heavy operations
- Data structures like arrays, maps

---

# 8. Common Interview Pitfall

### ❌ Misconception:

“Objects are copied when assigned”

```javascript id="pit1"
let a = { x: 1 };
let b = a;
```

👉 This does NOT create a copy — it copies reference.

---

# 9. Key Interview Summary

> Immutable objects cannot be changed after creation; any modification creates a new value. In JavaScript, primitives are immutable. Mutable objects, such as arrays and objects, can be modified directly in memory, and multiple variables may reference the same object. Understanding this distinction is crucial for avoiding unintended side effects and managing state correctly in applications.

## Question 8. What is the difference between a function and a method?

**Direct answer:**
A **function** is a standalone block of reusable code, while a **method** is a function that is defined as a property of an object and is called on that object.

---

# Detailed Explanation (Interview Perspective)

In JavaScript, the distinction between _function_ and _method_ is based on **context and ownership**, not on syntax.

---

# 1. Function

A function is an **independent reusable block of code**.

### Example:

```javascript id="f1"
function greet(name) {
  return `Hello, ${name}`;
}

console.log(greet("John"));
```

### Key characteristics:

- Not tied to any object
- Can be called directly
- Has its own execution context
- `this` depends on how it is called

---

## Function as expression:

```javascript id="f2"
const add = function (a, b) {
  return a + b;
};
```

---

## Arrow function example:

```javascript id="f3"
const multiply = (a, b) => a * b;
```

---

# 2. Method

A method is a **function that is a property of an object**.

### Example:

```javascript id="m1"
const user = {
  name: "John",
  greet: function () {
    return `Hello, ${this.name}`;
  },
};

console.log(user.greet());
```

👉 Here, `greet` is a method because it belongs to `user`.

---

## Modern shorthand method syntax:

```javascript id="m2"
const user = {
  name: "John",
  greet() {
    return `Hello, ${this.name}`;
  },
};
```

---

# 3. Key Difference

| Feature         | Function                | Method                     |
| --------------- | ----------------------- | -------------------------- |
| Definition      | Standalone code block   | Function inside an object  |
| Ownership       | Independent             | Belongs to an object       |
| Call style      | `fn()`                  | `obj.fn()`                 |
| `this` behavior | Depends on call context | Refers to object (usually) |
| Usage           | Utility logic           | Object behavior            |

---

# 4. The `this` keyword difference (very important)

## Function:

```javascript id="t1"
function show() {
  console.log(this);
}

show(); // window (browser) or undefined in strict mode
```

---

## Method:

```javascript id="t2"
const obj = {
  name: "Alice",
  show() {
    console.log(this.name);
  },
};

obj.show(); // "Alice"
```

👉 In methods, `this` usually refers to the object.

---

# 5. Important Edge Case (Interview favorite)

### Losing `this` in methods:

```javascript id="e1"
const obj = {
  name: "John",
  greet() {
    console.log(this.name);
  },
};

const fn = obj.greet;
fn(); // undefined or error
```

👉 Because it is now a **standalone function**, not a method call.

---

# 6. Fixing `this` loss

### Using bind:

```javascript id="e2"
const boundFn = obj.greet.bind(obj);
boundFn(); // John
```

---

### Using arrow function inside method:

```javascript id="e3"
const obj = {
  name: "John",
  greet() {
    const inner = () => console.log(this.name);
    inner();
  },
};
```

Arrow functions inherit `this` from surrounding scope.

---

# 7. Real-world analogy

- **Function** → a tool in a toolbox (independent utility)
- **Method** → a tool attached to a machine (object behavior)

---

# 8. Advanced Insight

In JavaScript, **everything is technically a function**, but:

- When a function is assigned to an object property → it becomes a method in usage context.

---

# 9. Interview Summary Answer

> A function is a standalone reusable block of code that can be called independently, whereas a method is a function that is associated with an object and is invoked through that object. The key difference lies in ownership and context—methods have access to the object via `this`, while functions do not unless explicitly bound.

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
