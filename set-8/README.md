# Set 8

| S.No. | Question                                                                                                                                                           |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [What are computed property names in ES6?](#question-1-what-are-computed-property-names-in-es6)                                                                    |
| 2.    | [Difference between object literal and constructor function](#question-2-difference-between-object-literal-and-constructor-function)                               |
| 3.    | [What are default parameters in functions?](#question-3-what-are-default-parameters-in-functions)                                                                  |
| 4.    | [How to swap two variables without using a third variable?](#question-4-how-to-swap-two-variables-without-using-a-third-variable)                                  |
| 5.    | [What is rest parameter vs arguments object?](#question-5-what-is-rest-parameter-vs-arguments-object)                                                              |
| 6.    | [Explain dynamic import in ES6](#question-6-explain-dynamic-import-in-es6)                                                                                         |
| 7.    | [What are JavaScript generators? How do they differ from regular functions?](#question-7-what-are-javascript-generators-how-do-they-differ-from-regular-functions) |
| 8.    | [Difference between for…of and forEach() for arrays](#question-8-difference-between-forof-and-foreach-for-arrays)                                                  |
| 9.    | [Explain async/await syntax](#question-9-explain-asyncawait-syntax)                                                                                                |
| 10.   | [Difference between Promise.all and Promise.race](#question-10-difference-between-promiseall-and-promiserace)                                                      |
| 11.   | [How to handle multiple async operations sequentially](#question-11-how-to-handle-multiple-async-operations-sequentially)                                          |
| 12.   | [Explain try…catch in async functions](#question-12-explain-trycatch-in-async-functions)                                                                           |
| 13.   | [Difference between setTimeout(fn, 0) and Promise.resolve().then(fn)](#question-13-difference-between-settimeoutfn-0-and-promiseresolvethenfn)                     |
| 14.   | [What are memory leaks in JavaScript? Examples?](#question-14-what-are-memory-leaks-in-javascript-examples)                                                        |
| 15.   | [Explain closures with practical examples](#question-15-explain-closures-with-practical-examples)                                                                  |
| 16.   | [How to use bind to create partially applied functions](#question-16-how-to-use-bind-to-create-partially-applied-functions)                                        |
| 17.   | [What is function currying? Example in JS](#question-17-what-is-function-currying-example-in-js)                                                                   |
| 18.   | [Explain optional parameters in functions](#question-18-explain-optional-parameters-in-functions)                                                                  |
| 19.   | [Difference between map() and reduce()](#question-19-difference-between-map-and-reduce)                                                                            |

## Question 1. What are computed property names in ES6?

Computed property names in ES6 allow you to define object property keys dynamically using expressions inside square brackets `[]`.

They are useful when the property name is not known until runtime or needs to be generated programmatically.

### Basic Syntax

```js
const key = "username";

const user = {
  [key]: "john_doe",
};

console.log(user.username); // john_doe
```

Here:

```js
[key];
```

is evaluated first, and its result becomes the property name.

### Why Computed Property Names Were Added

Before ES6, dynamic property assignment usually looked like this:

```js
const obj = {};
obj[key] = value;
```

ES6 made it possible to define dynamic keys directly inside object literals.

#### Example 1 — Dynamic Keys

```js
const field = "email";

const formData = {
  [field]: "test@example.com",
};

console.log(formData);
// { email: "test@example.com" }
```

#### Example 2 — Using Expressions

The expression inside `[]` can be any valid JavaScript expression.

```js
const prefix = "user";

const obj = {
  [prefix + "Name"]: "Alice",
  [prefix + "Age"]: 25,
};

console.log(obj);
// { userName: "Alice", userAge: 25 }
```

#### Example 3 — Using Functions

```js
function getKey(id) {
  return `item_${id}`;
}

const data = {
  [getKey(101)]: "Laptop",
};

console.log(data);
// { item_101: "Laptop" }
```

#### Example 4 — Computed Method Names

Computed property names also work with methods.

```js
const methodName = "sayHello";

const person = {
  [methodName]() {
    console.log("Hello!");
  },
};

person.sayHello();
```

### Real-World Use Cases

#### 1. Dynamic API Responses

```js
const apiField = "status";

const response = {
  [apiField]: "success",
};
```

#### 2. Redux / State Updates

```js
const field = "email";

const state = {
  name: "John",
  [field]: "john@example.com",
};
```

Very common in React state management.

#### 3. Configuration Objects

```js
const env = "development";

const config = {
  [`${env}_url`]: "localhost:3000",
};
```

### Difference Between Normal and Computed Properties

#### Normal Property

```js
const obj = {
  name: "John",
};
```

Property key is fixed.

#### Computed Property

```js
const key = "name";

const obj = {
  [key]: "John",
};
```

Property key is evaluated dynamically.

### Important Points

#### 1. Expressions Are Evaluated at Runtime

```js
const a = "x";

const obj = {
  [a + 1]: 10,
};

console.log(obj); // { x1: 10 }
```

#### 2. Without `[]`, It Becomes Literal Text

```js
const key = "age";

const obj1 = {
  key: 25,
};

console.log(obj1.key); // 25
```

This creates a property literally named `"key"`.

Correct dynamic version:

```js
const obj2 = {
  [key]: 25,
};

console.log(obj2.age); // 25
```

#### 3. Duplicate Computed Keys Override Earlier Ones

```js
const obj = {
  ["a"]: 1,
  ["a"]: 2,
};

console.log(obj.a); // 2
```

Last assignment wins.

### Relation to Other ES6 Features

Computed property names are commonly used with:

- Object destructuring
- Spread operator
- Template literals
- Dynamic object creation
- Maps and reducers
- React state updates

Example:

```js
const key = "theme";

const settings = {
  darkMode: true,
  [key]: "dark",
};
```

### Common Pitfalls

#### Pitfall 1 — Forgetting Brackets

```js
const key = "color";

const obj = {
  key: "red",
};

console.log(obj.color); // undefined
```

#### Pitfall 2 — Non-String Keys

Object keys are automatically converted to strings.

```js
const obj = {
  [1 + 2]: "three",
};

console.log(obj);
// { "3": "three" }
```

### Interview-Friendly Definition

> Computed property names in ES6 allow object property keys to be created dynamically using expressions inside square brackets `[]`. The expression is evaluated at runtime, and its result becomes the property name. They are commonly used for dynamic object creation and state management.

## Question 2. Difference between object literal and constructor function

## Direct Answer

Both **object literals** and **constructor functions** are used to create objects in JavaScript, but they serve different purposes:

- **Object Literal (`{}`)**: Best for creating a single object quickly.
- **Constructor Function (`new`)**: Best for creating multiple objects with the same structure and behavior.

---

# 1. Object Literal

An object literal creates a single object directly.

```js
const person = {
  name: "John",
  age: 30,
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  },
};

person.greet();
```

### Characteristics

- Simple and concise.
- Creates one object instance.
- No need for `new`.
- Suitable for configuration objects, JSON-like data, and one-off objects.

### Memory Consideration

If you create many similar objects using literals, methods get duplicated:

```js
const user1 = {
  name: "John",
  greet() {
    console.log("Hello");
  },
};

const user2 = {
  name: "Alice",
  greet() {
    console.log("Hello");
  },
};
```

Each object has its own copy of `greet`.

---

# 2. Constructor Function

A constructor function acts as a blueprint for creating objects.

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const p1 = new Person("John", 30);
const p2 = new Person("Alice", 25);

console.log(p1);
console.log(p2);
```

### What `new` Does

```js
const p1 = new Person("John", 30);
```

Internally:

1. Creates a new empty object.
2. Sets its prototype to `Person.prototype`.
3. Binds `this` to the new object.
4. Executes the constructor.
5. Returns the object.

---

# Sharing Methods via Prototype

Instead of creating methods per object:

```js
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  console.log(`Hello, I'm ${this.name}`);
};
```

Now all instances share the same method:

```js
const p1 = new Person("John");
const p2 = new Person("Alice");

p1.greet();
p2.greet();
```

This is more memory-efficient.

---

# Comparison Table

| Feature           | Object Literal        | Constructor Function     |
| ----------------- | --------------------- | ------------------------ |
| Syntax            | `{}`                  | `function + new`         |
| Purpose           | Single object         | Multiple similar objects |
| Reusability       | Low                   | High                     |
| Prototype support | Uses Object.prototype | Custom prototype chain   |
| Memory efficiency | Methods duplicated    | Methods can be shared    |
| Requires `new`    | No                    | Yes                      |
| OOP suitability   | Limited               | Better                   |

---

# Example: Same Problem Solved Both Ways

### Object Literal

```js
const car1 = {
  brand: "Toyota",
  drive() {
    console.log("Driving...");
  },
};

const car2 = {
  brand: "Honda",
  drive() {
    console.log("Driving...");
  },
};
```

Method duplicated in each object.

---

### Constructor Function

```js
function Car(brand) {
  this.brand = brand;
}

Car.prototype.drive = function () {
  console.log("Driving...");
};

const car1 = new Car("Toyota");
const car2 = new Car("Honda");
```

Single shared `drive` method.

---

# Modern Alternative: ES6 Classes

Constructor functions are the foundation of ES6 classes.

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
}
```

Equivalent to:

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.greet = function () {
  console.log(`Hello, I'm ${this.name}`);
};
```

Classes provide cleaner syntax but still use JavaScript's prototype system internally.

---

# Common Interview Questions

### Q: Can an object literal be used as a constructor?

No.

```js
const person = {
  name: "John",
};

new person(); // TypeError
```

Only functions (or classes) can be used with `new`.

---

### Q: What happens if you forget `new`?

```js
function Person(name) {
  this.name = name;
}

const p = Person("John");
```

In non-strict mode, `this` may refer to the global object, causing bugs.

A safer pattern:

```js
function Person(name) {
  if (!(this instanceof Person)) {
    return new Person(name);
  }

  this.name = name;
}
```

---

# Interview-Friendly Summary

> An object literal creates a single object directly using `{}` and is ideal for one-off objects. A constructor function acts as a blueprint for creating multiple similar objects using `new`. Constructor functions support prototype-based method sharing, making them more reusable and memory-efficient for object-oriented programming. ES6 classes are syntactic sugar over constructor functions and prototypes.

## Question 3. What are default parameters in functions?

## Direct Answer

**Default parameters** in JavaScript allow you to assign a default value to a function parameter if no argument (or `undefined`) is passed.

```js
function greet(name = "Guest") {
  return `Hello, ${name}`;
}

greet(); // "Hello, Guest"
greet("John"); // "Hello, John"
```

---

# Detailed Interview Explanation

Before ES6, JavaScript did not have built-in default parameters. Developers had to manually handle missing arguments.

Default parameters solve this cleanly by allowing parameter initialization directly in the function signature.

---

## 1. Basic Syntax

```js
function multiply(a = 1, b = 1) {
  return a * b;
}

console.log(multiply()); // 1
console.log(multiply(5)); // 5
console.log(multiply(5, 2)); // 10
```

If a value is not provided, the default is used.

---

## 2. Important Behavior Rules

### Rule 1: Only `undefined` triggers default value

```js
function test(a = 10) {
  return a;
}

test(undefined); // 10
test(null); // null (NOT defaulted)
test(0); // 0
test(""); // ""
```

👉 Only `undefined` activates the default.

---

### Rule 2: Default parameters can be expressions

```js
function getId() {
  return 100;
}

function user(id = getId()) {
  return id;
}

console.log(user()); // 100
```

The expression is evaluated only when needed.

---

### Rule 3: Parameters are evaluated left to right

```js
function example(a = 1, b = a + 1) {
  return [a, b];
}

console.log(example()); // [1, 2]
```

But reverse reference is NOT allowed:

```js
function example(a = b, b = 2) {
  return [a, b];
}

// ReferenceError: Cannot access 'b' before initialization
```

---

## 3. Default Parameters vs Old Pattern

### Before ES6

```js
function greet(name) {
  name = name || "Guest";
  return `Hello, ${name}`;
}
```

### Problem with old approach:

```js
greet(""); // "Hello, Guest" ❌ wrong behavior
```

Because `""` is falsy.

---

### With default parameters (correct)

```js
function greet(name = "Guest") {
  return `Hello, ${name}`;
}

greet(""); // "Hello, " ✅ correct
```

---

## 4. Using Objects and Destructuring Defaults

Very common in real-world APIs.

```js
function createUser({ name = "Anonymous", age = 18 } = {}) {
  return { name, age };
}

console.log(createUser({ name: "John" }));
// { name: "John", age: 18 }

console.log(createUser());
// { name: "Anonymous", age: 18 }
```

👉 `= {}` ensures function doesn’t break when no argument is passed.

---

## 5. Default Parameters + Function Scope

Default parameters create their own scope.

```js
let x = 10;

function test(y = x + 1) {
  let x = 50;
  return y;
}

console.log(test()); // 11
```

👉 Default values do NOT use inner function scope.

---

## 6. Common Pitfalls

### Pitfall 1: Using `||` incorrectly

```js
function count(n) {
  n = n || 10;
  return n;
}

count(0); // 10 ❌ unexpected
```

Fixed:

```js
function count(n = 10) {
  return n;
}
```

---

### Pitfall 2: Reference errors in ordering

```js
function test(a = b, b = 2) {
  return a + b;
}
```

❌ `b` is not initialized yet.

---

### Pitfall 3: Forgetting default for object parameter

```js
function print({ name }) {
  console.log(name);
}

print(); // ❌ Error
```

Fix:

```js
function print({ name } = {}) {
  console.log(name);
}
```

---

## 7. Real-World Use Cases

### API handlers

```js
function fetchData(url, method = "GET") {
  console.log(url, method);
}
```

---

### Utility functions

```js
function delay(ms = 1000) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}
```

---

### React-style props

```js
function Button({ label = "Click", disabled = false } = {}) {
  return { label, disabled };
}
```

---

## 8. Interview Summary

> Default parameters in JavaScript allow function parameters to have fallback values when arguments are `undefined`. They improve readability, eliminate the need for manual checks like `||`, support expressions, and are evaluated only when needed. Unlike falsy-based defaults, they correctly distinguish between `undefined`, `null`, `0`, and empty strings, making them safer and more predictable in modern JavaScript development.

## Question 4. How to swap two variables without using a third variable?

## Direct Answer

You can swap two variables without a third variable using techniques like **destructuring assignment**, **arithmetic operations**, or **bitwise XOR**.

In modern JavaScript, the **best and recommended way is array destructuring**:

```js
let a = 10;
let b = 20;

[a, b] = [b, a];

console.log(a, b); // 20 10
```

---

# Detailed Interview Explanation

Swapping variables is a classic interview question that tests your understanding of assignment, memory manipulation, and JavaScript evaluation behavior.

There are multiple ways to do it, each with different trade-offs.

---

# 1. ES6 Destructuring (Best Approach)

```js id="swap1"
let a = 5;
let b = 10;

[a, b] = [b, a];

console.log(a, b); // 10 5
```

## How it works

- A temporary array `[b, a]` is created.
- Values are unpacked into `a` and `b` simultaneously.
- No explicit temporary variable needed.

## Why it's preferred

- Clean and readable
- Safe for all data types
- No overflow or precision issues
- Widely used in production code

---

# 2. Using Arithmetic Operators

### Addition/Subtraction method

```js id="swap2"
let a = 5;
let b = 10;

a = a + b;
b = a - b;
a = a - b;

console.log(a, b); // 10 5
```

## How it works

Step-by-step:

- `a = 15`
- `b = 15 - 10 = 5`
- `a = 15 - 5 = 10`

## Pros

- No extra variable

## Cons / Pitfalls

- Risk of **integer overflow**
- Not safe for very large numbers
- Not readable compared to destructuring

---

# 3. Using Multiplication/Division

```js id="swap3"
let a = 5;
let b = 10;

a = a * b;
b = a / b;
a = a / b;

console.log(a, b); // 10 5
```

## Issues

- Fails when one value is `0`
- Not safe for decimals due to floating-point precision

```js
let a = 0;
let b = 10;

// breaks logic
```

---

# 4. Using Bitwise XOR (Advanced)

Works only with integers.

```js id="swap4"
let a = 5;
let b = 10;

a = a ^ b;
b = a ^ b;
a = a ^ b;

console.log(a, b); // 10 5
```

## How it works

Uses XOR property:

- `x ^ x = 0`
- `x ^ 0 = x`

So values cancel and reconstruct each other.

## Limitations

- Only works with **32-bit integers**
- Not readable for most teams
- Rarely used in modern JavaScript interviews except for edge-case discussions

---

# 5. Using Array Reassignment (Alternative to destructuring syntax)

```js
let a = 1,
  b = 2;

const temp = [a, b];
a = temp[1];
b = temp[0];
```

This is essentially manual destructuring.

---

# Comparison Table

| Method         | Readability | Safety     | Performance | Recommendation          |
| -------------- | ----------- | ---------- | ----------- | ----------------------- |
| Destructuring  | ⭐⭐⭐⭐⭐  | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐  | ✅ Best                 |
| Arithmetic     | ⭐⭐        | ⭐⭐       | ⭐⭐⭐      | ⚠️ Avoid                |
| Multiplication | ⭐          | ⭐         | ⭐⭐⭐      | ❌ Not safe             |
| XOR            | ⭐          | ⭐⭐       | ⭐⭐⭐⭐    | ⚠️ Interview trick only |

---

# Common Interview Follow-Ups

## Q1: Why is destructuring preferred?

Because it is:

- Declarative
- Safe for all data types
- Less error-prone
- Internally optimized by JS engines

---

## Q2: Does destructuring use a temporary variable?

Yes internally, but it's abstracted away by the engine.

---

## Q3: Can we swap objects using same technique?

Yes:

```js
let obj1 = { a: 1 };
let obj2 = { b: 2 };

[obj1, obj2] = [obj2, obj1];
```

---

# Interview Summary

> The most modern and preferred way to swap two variables in JavaScript is using array destructuring: `[a, b] = [b, a]`. While arithmetic and bitwise XOR methods exist and are often asked in interviews, they are less safe and less readable. Destructuring is the cleanest, most reliable, and production-ready approach.

## Question 5. What is rest parameter vs arguments object?

## Direct Answer

- The **rest parameter (`...args`)** is a modern ES6 feature that collects arguments into a real array.
- The **`arguments` object** is an old, array-like object available inside regular functions that contains all passed arguments.

👉 Key difference:
**Rest parameters are real arrays and work in all modern JS features; `arguments` is array-like and has several limitations.**

---

# 1. Rest Parameter (`...args`)

Introduced in ES6, it collects remaining arguments into an actual array.

```js id="rest1"
function sum(...numbers) {
  return numbers.reduce((acc, num) => acc + num, 0);
}

console.log(sum(1, 2, 3, 4)); // 10
```

## Key Features

- Always an **array**
- Can use array methods (`map`, `filter`, `reduce`)
- Must be the **last parameter**
- Works with arrow functions

---

### Example: Multiple arguments

```js id="rest2"
function show(a, b, ...rest) {
  console.log(a); // 1
  console.log(b); // 2
  console.log(rest); // [3, 4, 5]
}

show(1, 2, 3, 4, 5);
```

---

# 2. `arguments` Object

A special **array-like object** available in non-arrow functions.

```js id="args1"
function sum() {
  let total = 0;

  for (let i = 0; i < arguments.length; i++) {
    total += arguments[i];
  }

  return total;
}

console.log(sum(1, 2, 3)); // 6
```

---

## Key Features

- Not a real array (array-like)
- Has `.length`, but not array methods
- Only available in **regular functions**
- Not available in arrow functions

---

# 3. Key Differences (Important for Interviews)

| Feature                  | Rest Parameter | Arguments Object  |
| ------------------------ | -------------- | ----------------- |
| Type                     | Real Array     | Array-like object |
| ES Version               | ES6+           | Pre-ES6           |
| Arrow functions          | Works          | Not available     |
| Array methods            | Yes            | No                |
| Readability              | High           | Low               |
| Flexibility              | High           | Limited           |
| Named parameters support | Yes            | No                |

---

# 4. Arrow Function Difference (Very Important)

### arguments does NOT work:

```js id="arrow1"
const fn = () => {
  console.log(arguments);
};

fn(1, 2, 3); // ReferenceError
```

---

### Rest parameter works:

```js id="arrow2"
const fn = (...args) => {
  console.log(args);
};

fn(1, 2, 3); // [1, 2, 3]
```

---

# 5. Array-Like Nature of `arguments`

You cannot directly use array methods:

```js id="args2"
function test() {
  console.log(arguments.map); // undefined
}
```

But you can convert it:

```js id="args3"
function test() {
  const argsArray = Array.from(arguments);
  console.log(argsArray.map((x) => x * 2));
}

test(1, 2, 3);
```

---

# 6. Use Cases

## Rest Parameter (Modern usage)

```js id="rest3"
function multiply(multiplier, ...nums) {
  return nums.map((n) => n * multiplier);
}

console.log(multiply(2, 1, 2, 3));
```

---

## Arguments Object (Legacy code)

Still found in:

- Older codebases
- Polyfills
- Internal utilities

---

# 7. Common Pitfalls

### Pitfall 1: Mixing both

```js id="mix1"
function test(...args) {
  console.log(arguments); // still exists but not recommended
}
```

👉 Avoid mixing `arguments` with rest parameters.

---

### Pitfall 2: Using arguments in arrow functions

```js id="mix2"
const fn = () => arguments; // ❌ error
```

---

### Pitfall 3: Thinking arguments is an array

```js id="mix3"
arguments.push(10); // ❌ TypeError
```

---

# 8. Why Rest Parameter is Preferred (Modern JS)

- Cleaner syntax
- Works with modern APIs
- Supports functional programming
- Better performance optimizations in engines
- Compatible with arrow functions

---

# 9. Interview-Friendly Summary

> The rest parameter (`...args`) is a modern ES6 feature that collects function arguments into a real array, making it easier to work with array methods and arrow functions. The `arguments` object is an older array-like object available only in regular functions and lacks array methods and modern flexibility. In modern JavaScript, rest parameters are preferred due to better readability, usability, and compatibility with ES6+ features.

## Question 6. Explain dynamic import in ES6

## Direct Answer

**Dynamic import in ES6** allows you to load JavaScript modules **on demand (at runtime)** using the `import()` function instead of static `import` statements at the top of the file.

It returns a **Promise**, enabling lazy loading and code splitting.

```js id="dyn1"
import("./math.js").then((module) => {
  console.log(module.add(2, 3));
});
```

---

# Detailed Interview Explanation

Before ES6 modules were widely adopted, all dependencies were loaded upfront. Even with ES6 static imports:

```js id="dyn2"
import { add } from "./math.js";
```

👉 The module is loaded immediately when the file is executed.

This is fine for small apps but problematic for large applications.

---

# 1. What is Dynamic Import?

Dynamic import allows you to load modules **only when needed**, at runtime.

```js id="dyn3"
const modulePromise = import("./math.js");
```

- Returns a **Promise**
- Resolves to a module object
- Works in **modern browsers + Node.js**

---

# 2. Basic Example

```js id="dyn4"
async function loadMath() {
  const math = await import("./math.js");

  console.log(math.add(5, 10));
}

loadMath();
```

---

# 3. Module Structure Returned

Dynamic import returns a module namespace object:

```js id="dyn5"
import("./math.js").then((module) => {
  console.log(module);
});
```

Example output:

```js
{
  add: [Function],
  subtract: [Function],
  default: [Function]
}
```

---

# 4. Named vs Default Exports

### math.js

```js id="dyn6"
export function add(a, b) {
  return a + b;
}

export default function multiply(a, b) {
  return a * b;
}
```

---

### Dynamic import usage

```js id="dyn7"
const math = await import("./math.js");

math.add(2, 3); // named export
math.default(2, 3); // default export
```

---

# 5. Why Dynamic Import Exists (Real Use Case)

## 1. Code Splitting (Performance Optimization)

Instead of loading everything upfront:

```js
// heavy module loaded immediately ❌
import "./chartLibrary.js";
```

Load only when needed:

```js id="dyn8"
button.addEventListener("click", async () => {
  const chart = await import("./chartLibrary.js");
  chart.render();
});
```

👉 Improves initial load time.

---

## 2. Lazy Loading Features

```js id="dyn9"
async function openEditor() {
  const editor = await import("./editor.js");
  editor.open();
}
```

Used in:

- React lazy loading
- Webpack chunk splitting
- Micro-frontends

---

## 3. Conditional Loading

```js id="dyn10"
if (user.isAdmin) {
  const adminModule = await import("./admin.js");
  adminModule.init();
}
```

👉 Load only when required.

---

# 6. Dynamic Import vs Static Import

| Feature             | Static Import     | Dynamic Import         |
| ------------------- | ----------------- | ---------------------- |
| Syntax              | `import ... from` | `import()`             |
| Timing              | Compile time      | Runtime                |
| Returns             | Direct binding    | Promise                |
| Conditional loading | ❌ Not allowed    | ✅ Allowed             |
| Tree-shaking        | Better            | Depends                |
| Use case            | Core dependencies | Optional/large modules |

---

# 7. Important Characteristics

## 1. Always returns a Promise

```js id="dyn11"
const mod = import("./file.js"); // Promise
```

---

## 2. Works anywhere in code

Unlike static imports:

```js
// ❌ not allowed conditionally
if (true) {
  import x from "./file.js";
}
```

Dynamic import works:

```js id="dyn12"
if (true) {
  const x = await import("./file.js");
}
```

---

## 3. Can be used in CommonJS-style environments (Node.js)

```js id="dyn13"
const module = await import("./utils.mjs");
```

---

# 8. Error Handling

Since it returns a Promise:

```js id="dyn14"
import("./math.js")
  .then((m) => console.log(m.add(1, 2)))
  .catch((err) => console.error("Failed to load module", err));
```

---

# 9. Common Pitfalls

## Pitfall 1: Forgetting async/await or then

```js id="dyn15"
const mod = import("./math.js");
console.log(mod.add); // ❌ undefined (Promise not resolved)
```

---

## Pitfall 2: Misunderstanding default export

```js id="dyn16"
const m = await import("./file.js");

m(); // ❌ only works if default is function and accessed via m.default
```

Correct:

```js
m.default();
```

---

## Pitfall 3: Overusing dynamic import

Too many dynamic imports can:

- Increase network requests
- Add runtime complexity

---

# 10. Real-World Use Cases

### React lazy loading

```js id="dyn17"
const Component = React.lazy(() => import("./Component"));
```

---

### Feature toggles

```js id="dyn18"
if (featureEnabled) {
  const feature = await import("./feature.js");
  feature.run();
}
```

---

### Large libraries (charts, maps)

```js id="dyn19"
button.onclick = async () => {
  const { Chart } = await import("chart.js");
  new Chart(...);
};
```

---

# 11. Interview Summary

> Dynamic import in ES6 allows modules to be loaded at runtime using `import()`, which returns a Promise. It enables lazy loading, code splitting, and conditional module loading, improving performance in large applications. Unlike static imports, dynamic imports can be used conditionally and inside functions, making them ideal for optimizing resource-heavy features.

## Question 7. What are JavaScript generators? How do they differ from regular functions?

## Direct Answer

**JavaScript generators** are special functions that can be **paused and resumed** during execution using the `function*` syntax and the `yield` keyword.

Unlike regular functions, which run **to completion once**, generators can **produce multiple values over time**.

---

# 1. What is a Generator Function?

A generator is defined using `function*`:

```js id="gen1"
function* generator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = generator();

console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }
```

---

# 2. Key Concept: `yield`

The `yield` keyword:

- Pauses function execution
- Returns a value
- Saves the function state
- Resumes from the same point when called again

---

# 3. Regular Function vs Generator Function

## Regular Function

```js id="gen2"
function normal() {
  return 1;
  return 2; // ❌ unreachable
}
```

### Behavior:

- Executes fully in one go
- Only one return value
- Cannot pause/resume

---

## Generator Function

```js id="gen3"
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}
```

### Behavior:

- Can pause execution
- Can return multiple values
- Maintains internal state

---

# 4. How Iteration Works

Generators are **iterables**:

```js id="gen4"
function* numbers() {
  yield 10;
  yield 20;
  yield 30;
}

for (const num of numbers()) {
  console.log(num);
}
```

Output:

```
10
20
30
```

---

# 5. Difference Between `.next()` Calls

Each call to `.next()` resumes execution from last `yield`.

```js id="gen5"
function* demo() {
  console.log("Start");
  yield 1;

  console.log("Middle");
  yield 2;

  console.log("End");
}

const g = demo();

g.next(); // logs "Start"
g.next(); // logs "Middle"
g.next(); // logs "End"
```

---

# 6. Returning Values from Generators

You can also use `return`:

```js id="gen6"
function* gen() {
  yield 1;
  return 99;
  yield 2;
}

const g = gen();

console.log(g.next()); // { value: 1, done: false }
console.log(g.next()); // { value: 99, done: true }
console.log(g.next()); // { value: undefined, done: true }
```

👉 After `return`, generator is finished.

---

# 7. Two-Way Communication (Advanced Feature)

Generators can receive input using `next(value)`:

```js id="gen7"
function* gen() {
  const x = yield 1;
  const y = yield x + 2;

  return y + 3;
}

const g = gen();

console.log(g.next()); // 1
console.log(g.next(10)); // 12 (10 + 2)
console.log(g.next(20)); // 23 (20 + 3)
```

---

# 8. Infinite Sequences

Generators are great for infinite data streams:

```js id="gen8"
function* infiniteCounter() {
  let i = 0;

  while (true) {
    yield i++;
  }
}

const counter = infiniteCounter();

console.log(counter.next().value); // 0
console.log(counter.next().value); // 1
console.log(counter.next().value); // 2
```

👉 No memory overflow because values are generated lazily.

---

# 9. Real-World Use Cases

## 1. Custom Iterators

```js id="gen9"
const obj = {
  start: 1,
  end: 3,
  *[Symbol.iterator]() {
    for (let i = this.start; i <= this.end; i++) {
      yield i;
    }
  },
};

console.log([...obj]); // [1, 2, 3]
```

---

## 2. Lazy Data Processing

```js id="gen10"
function* processData(arr) {
  for (const item of arr) {
    yield item * 2;
  }
}
```

---

## 3. Async Control Flow (before async/await)

```js id="gen11"
function* task() {
  const data = yield fetch("/api");
  console.log(data);
}
```

Used in libraries like Redux-Saga.

---

# 10. Generator vs Iterator

| Feature        | Generator             | Iterator               |
| -------------- | --------------------- | ---------------------- |
| Syntax         | `function*`           | Manual object          |
| State handling | Automatic             | Manual                 |
| Simplicity     | High                  | Low                    |
| Use case       | Iteration, async flow | Custom iteration logic |

---

# 11. Common Pitfalls

## Pitfall 1: Forgetting to call `.next()`

```js id="gen12"
function* g() {
  yield 1;
}

g(); // nothing happens ❌
```

Must use:

```js id="gen13"
const it = g();
it.next();
```

---

## Pitfall 2: Confusing with normal functions

```js id="gen14"
function* g() {
  return 1;
}

console.log(g()); // iterator object, not value
```

---

## Pitfall 3: Not understanding pause behavior

Each `yield` is a pause point, not a return.

---

# 12. Key Differences (Interview Table)

| Feature     | Regular Function | Generator Function   |
| ----------- | ---------------- | -------------------- |
| Syntax      | `function`       | `function*`          |
| Execution   | Runs fully       | Pauses/resumes       |
| Output      | Single return    | Multiple yields      |
| State       | Not preserved    | Preserved            |
| Return type | Value            | Iterator object      |
| Usage       | Standard logic   | Iteration, lazy eval |

---

# 13. Interview Summary

> JavaScript generators are special functions defined using `function*` that can pause execution using `yield` and resume later. Unlike regular functions, which execute once and return a single value, generators produce a sequence of values over time and maintain their internal state. They are commonly used for iterators, lazy evaluation, and complex asynchronous control flow.

## Question 8. Difference between for…of and forEach() for arrays

## Direct Answer

- **`for...of`** is a loop that iterates over iterable values (like arrays) and supports **`break`, `continue`, and `await`**.
- **`forEach()`** is an array method that executes a callback for each element but **does not support `break` or `continue` and cannot be easily stopped early**.

👉 Key takeaway:
**Use `for...of` for control flow flexibility, and `forEach()` for simple iteration without early exit needs.**

---

# 1. `for...of` Loop

Introduced in ES6, it iterates over **values of an iterable**.

```js id="fo1"
const arr = [10, 20, 30];

for (const value of arr) {
  console.log(value);
}
```

### Output:

```
10
20
30
```

---

## Key Features

- Works with **arrays, strings, maps, sets**
- Gives **direct value**
- Supports:
  - `break`
  - `continue`
  - `return` (inside functions)
  - `await` (in async loops)

---

## Example with break

```js id="fo2"
for (const num of [1, 2, 3, 4]) {
  if (num === 3) break;
  console.log(num);
}
```

Output:

```
1
2
```

---

## Example with async/await

```js id="fo3"
async function process() {
  for (const id of [1, 2, 3]) {
    await fetch(`/api/${id}`);
  }
}
```

---

# 2. `forEach()` Method

`forEach()` is an **array method** that executes a callback for each element.

```js id="fe1"
const arr = [10, 20, 30];

arr.forEach((value) => {
  console.log(value);
});
```

---

## Key Features

- Only works with **arrays**
- Provides:
  - value
  - index
  - array reference

- Cleaner functional style

---

## Example

```js id="fe2"
[1, 2, 3].forEach((value, index) => {
  console.log(index, value);
});
```

---

# 3. Key Differences (Important Table)

| Feature             | `for...of`       | `forEach()`             |
| ------------------- | ---------------- | ----------------------- |
| Type                | Loop statement   | Array method            |
| Works on            | Any iterable     | Arrays only             |
| Access              | Values directly  | Callback parameters     |
| `break` support     | ✅ Yes           | ❌ No                   |
| `continue` support  | ✅ Yes           | ❌ No                   |
| Async/await support | ✅ Yes           | ❌ (not reliable)       |
| Return behavior     | Can return early | Always runs full loop   |
| Readability         | Moderate         | High (functional style) |

---

# 4. Major Behavioral Differences

---

## 1. Cannot break in `forEach`

```js id="diff1"
[1, 2, 3, 4].forEach((num) => {
  if (num === 3) return; // ❌ only exits callback, not loop
  console.log(num);
});
```

Output:

```
1
2
4
```

👉 It does NOT stop iteration.

---

## 2. `for...of` can break cleanly

```js id="diff2"
for (const num of [1, 2, 3, 4]) {
  if (num === 3) break;
  console.log(num);
}
```

---

## 3. Async behavior difference (very important interview point)

### ❌ Wrong with forEach

```js id="diff3"
[1, 2, 3].forEach(async (num) => {
  await new Promise((res) => setTimeout(res, 1000));
  console.log(num);
});
```

👉 Does NOT wait properly; runs in parallel, not sequential control.

---

### ✅ Correct with for...of

```js id="diff4"
async function run() {
  for (const num of [1, 2, 3]) {
    await new Promise((res) => setTimeout(res, 1000));
    console.log(num);
  }
}
```

👉 Executes sequentially.

---

# 5. Return Behavior

## forEach ignores return

```js id="diff5"
const result = [1, 2, 3].forEach((n) => n * 2);

console.log(result); // undefined
```

---

## for...of allows building results

```js id="diff6"
const result = [];

for (const n of [1, 2, 3]) {
  result.push(n * 2);
}

console.log(result); // [2, 4, 6]
```

---

# 6. Performance Note

- Both are similar in modern engines
- `for...of` can be slightly faster in tight loops
- `forEach` adds callback overhead

But in real-world apps → difference is negligible.

---

# 7. When to Use What (Interview Answer)

## Use `for...of` when:

- You need `break` / `continue`
- You need `await`
- You want better control flow
- You are iterating async tasks sequentially

---

## Use `forEach()` when:

- You only need simple iteration
- No early exit required
- Functional style is preferred
- You are doing side-effects only

---

# 8. Common Pitfalls

### Pitfall 1: Expecting break in forEach

```js id="pit1"
arr.forEach(n => {
  if (n === 2) break; // ❌ SyntaxError
});
```

---

### Pitfall 2: Expecting async sequencing

```js id="pit2"
arr.forEach(async (n) => {
  await doSomething(n);
});
```

👉 Runs concurrently, not sequentially.

---

### Pitfall 3: Thinking return stops loop

```js id="pit3"
arr.forEach((n) => {
  if (n === 2) return; // only skips current iteration
});
```

---

# 9. Interview Summary

> `for...of` is a modern loop that iterates over iterable values and supports full control flow features like `break`, `continue`, and `await`, making it suitable for complex and asynchronous operations. `forEach()` is a higher-order array method designed for simple iteration with callbacks, but it does not support early exit or proper async control flow. In interviews, `for...of` is generally preferred when control and flexibility are required, while `forEach()` is used for clean, functional-style iteration.

## Question 9. Explain async/await syntax

## Direct Answer

**`async/await`** is syntactic sugar over Promises in JavaScript that allows you to write asynchronous code in a **synchronous-looking style**.

- `async` makes a function return a Promise
- `await` pauses execution until a Promise resolves or rejects

---

# 1. Basic Syntax

```js id="aa1"
async function fetchData() {
  const response = await fetch("https://api.example.com/data");
  const data = await response.json();

  return data;
}
```

---

# 2. What `async` does

When you mark a function as `async`, it always returns a Promise:

```js id="aa2"
async function test() {
  return 10;
}

test().then(console.log); // 10
```

👉 Equivalent to:

```js id="aa3"
function test() {
  return Promise.resolve(10);
}
```

---

# 3. What `await` does

`await` pauses execution inside an `async` function until the Promise resolves.

```js id="aa4"
function delay() {
  return new Promise((resolve) => setTimeout(resolve, 1000));
}

async function run() {
  console.log("Start");

  await delay(); // pauses here

  console.log("End");
}

run();
```

Output (after 1 second):

```
Start
End
```

---

# 4. How async/await works internally

This:

```js id="aa5"
async function fn() {
  const data = await fetchData();
  return data;
}
```

is equivalent to:

```js id="aa6"
function fn() {
  return fetchData().then((data) => {
    return data;
  });
}
```

👉 So `async/await` is just cleaner Promise chaining.

---

# 5. Sequential vs Parallel Execution

## ❌ Sequential (slow if independent)

```js id="aa7"
async function run() {
  const a = await fetch("/api/a");
  const b = await fetch("/api/b");
}
```

Each waits for the previous one.

---

## ✅ Parallel (better performance)

```js id="aa8"
async function run() {
  const aPromise = fetch("/api/a");
  const bPromise = fetch("/api/b");

  const a = await aPromise;
  const b = await bPromise;
}
```

Or better:

```js id="aa9"
const [a, b] = await Promise.all([fetch("/api/a"), fetch("/api/b")]);
```

---

# 6. Error Handling with try/catch

```js id="aa10"
async function run() {
  try {
    const res = await fetch("/api/data");

    if (!res.ok) {
      throw new Error("Network error");
    }

    const data = await res.json();
    return data;
  } catch (error) {
    console.error("Error:", error);
  }
}
```

---

# 7. Important Rules

## Rule 1: `await` only works inside async functions

```js id="aa11"
// ❌ SyntaxError
const data = await fetch("/api");
```

Correct:

```js id="aa12"
async function run() {
  const data = await fetch("/api");
}
```

---

## Rule 2: Top-level await (ES2022+)

In modern modules:

```js id="aa13"
const data = await fetch("/api");
```

Only works in ES modules.

---

## Rule 3: async always returns Promise

```js id="aa14"
async function test() {
  return 5;
}

console.log(test()); // Promise { 5 }
```

---

# 8. Async/Await vs Promises

| Feature        | Promises           | async/await      |
| -------------- | ------------------ | ---------------- |
| Readability    | Medium             | High             |
| Syntax style   | Chaining `.then()` | Synchronous-like |
| Error handling | `.catch()`         | try/catch        |
| Debugging      | Harder             | Easier           |
| Control flow   | Nested chains      | Linear code      |

---

# 9. Common Pitfalls

---

## Pitfall 1: Forgetting await

```js id="aa15"
async function run() {
  const data = fetch("/api"); // ❌ Promise, not result
}
```

---

## Pitfall 2: Sequential execution mistake

```js id="aa16"
await fetch(a);
await fetch(b);
```

👉 Slower if requests are independent.

---

## Pitfall 3: Using await in forEach

```js id="aa17"
arr.forEach(async (item) => {
  await process(item);
});
```

❌ Does not behave sequentially.

---

## Correct way:

```js id="aa18"
for (const item of arr) {
  await process(item);
}
```

---

# 10. Real-world Use Cases

## API calls

```js id="aa19"
async function getUser() {
  const res = await fetch("/user");
  return await res.json();
}
```

---

## Multiple dependent steps

```js id="aa20"
async function processOrder() {
  const user = await getUser();
  const order = await createOrder(user.id);
  return order;
}
```

---

## Parallel execution optimization

```js id="aa21"
async function loadData() {
  const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);
}
```

---

# 11. Event Loop Insight (Interview-Level Concept)

- `await` pauses function execution
- Function is suspended, not blocking the thread
- Control returns to event loop
- Resumes in **microtask queue** when Promise resolves

👉 This is why async/await is non-blocking.

---

# 12. Interview Summary

> `async/await` is a modern JavaScript syntax built on top of Promises that allows asynchronous code to be written in a synchronous style. An `async` function always returns a Promise, while `await` pauses execution until a Promise resolves. It improves readability, simplifies error handling with try/catch, and makes asynchronous flow easier to manage compared to traditional Promise chaining.

## Question 10. Difference between Promise.all and Promise.race

## Direct Answer

- **`Promise.all()`** waits for **all Promises to resolve** (or fails fast if any reject) and returns an array of results.
- **`Promise.race()`** returns the result of the **first settled Promise** (either resolved or rejected).

👉 Key idea:
**`all` = wait for everyone, `race` = first one wins**

---

# 1. `Promise.all()`

### Definition

Runs multiple Promises in parallel and waits until **all succeed**.

```js id="pa1"
const p1 = Promise.resolve(10);
const p2 = Promise.resolve(20);
const p3 = Promise.resolve(30);

Promise.all([p1, p2, p3]).then((results) => {
  console.log(results); // [10, 20, 30]
});
```

---

## Key Behavior

- Executes Promises in parallel
- Returns results in **same order as input**
- Fails immediately if **any Promise rejects**

---

## Example with rejection

```js id="pa2"
const p1 = Promise.resolve(1);
const p2 = Promise.reject("Error");
const p3 = Promise.resolve(3);

Promise.all([p1, p2, p3])
  .then(console.log)
  .catch((err) => console.log(err));
```

### Output:

```
Error
```

👉 Even though p3 succeeds, it is ignored.

---

## Use Cases

- Loading multiple API calls together
- Parallel data fetching
- Batch processing

---

# 2. `Promise.race()`

### Definition

Returns the result of the **first Promise that settles (resolves or rejects)**.

```js id="pr1"
const p1 = new Promise((res) => setTimeout(() => res("First"), 1000));
const p2 = new Promise((res) => setTimeout(() => res("Second"), 200));

Promise.race([p1, p2]).then(console.log); // "Second"
```

---

## Key Behavior

- Returns **first settled value**
- Doesn’t wait for others
- Can resolve OR reject first

---

## Example with rejection winning race

```js id="pr2"
const fastFail = new Promise((_, reject) =>
  setTimeout(() => reject("Error first"), 100),
);

const slowSuccess = new Promise((resolve) =>
  setTimeout(() => resolve("Success"), 500),
);

Promise.race([fastFail, slowSuccess]).then(console.log).catch(console.log);
```

### Output:

```
Error first
```

---

## Use Cases

- Timeout handling
- First-response wins scenarios
- Competing requests (e.g., multiple servers)

---

# 3. Key Differences (Interview Table)

| Feature            | Promise.all      | Promise.race               |
| ------------------ | ---------------- | -------------------------- |
| Waits for          | All Promises     | First settled Promise      |
| Output             | Array of results | Single value               |
| Order preserved    | Yes              | Not applicable             |
| Failure behavior   | Fails fast       | First rejection wins       |
| Parallel execution | Yes              | Yes                        |
| Use case           | Batch processing | Timeout / fastest response |

---

# 4. Real-world Example

## Promise.all → Fetch multiple APIs

```js id="pa3"
async function getDashboard() {
  const [user, posts, notifications] = await Promise.all([
    fetch("/user"),
    fetch("/posts"),
    fetch("/notifications"),
  ]);

  return { user, posts, notifications };
}
```

---

## Promise.race → Timeout control

```js id="pr3"
function timeout(ms) {
  return new Promise((_, reject) =>
    setTimeout(() => reject("Request timed out"), ms),
  );
}

Promise.race([fetch("/data"), timeout(2000)])
  .then((res) => console.log(res))
  .catch((err) => console.log(err));
```

---

# 5. Important Edge Cases

## 1. Empty array

```js id="edge1"
Promise.all([]).then(console.log); // []
Promise.race([]); // never settles
```

---

## 2. Order in Promise.all

Even if resolved out of order:

```js id="edge2"
const p1 = new Promise((res) => setTimeout(() => res(1), 300));
const p2 = new Promise((res) => setTimeout(() => res(2), 100));

Promise.all([p1, p2]).then(console.log);
// [1, 2] (order preserved)
```

---

## 3. Promise.race does NOT cancel others

```js id="edge3"
const p1 = new Promise((res) => setTimeout(() => res("slow"), 1000));
const p2 = new Promise((res) => setTimeout(() => res("fast"), 200));

Promise.race([p1, p2]).then(console.log);
```

👉 `p1` still runs in background.

---

# 6. Interview Insight

## Promise.all

- Think: **"Wait for all tasks to complete successfully"**
- Fail-fast behavior

## Promise.race

- Think: **"Take the fastest response (success or failure)"**
- Useful for performance optimization and timeouts

---

# 7. Interview Summary

> `Promise.all` waits for all promises to resolve and returns their results as an array, failing if any promise rejects. `Promise.race` returns the result of the first promise that settles (either resolve or reject). `Promise.all` is used for batch processing, while `Promise.race` is used for scenarios like timeouts or fastest-response selection.

## Question 11. How to handle multiple async operations sequentially

## Direct Answer

To handle **multiple async operations sequentially in JavaScript**, you use:

👉 **`async/await` inside a loop (preferred)**
or
👉 **Promise chaining**

The most modern and clean approach is:

```js id="seq1"
async function runTasks() {
  await task1();
  await task2();
  await task3();
}
```

or dynamically:

```js id="seq2"
for (const task of tasks) {
  await task();
}
```

---

# 1. What “Sequential Async” Means

Sequential execution means:

> Each async operation waits for the previous one to finish before starting.

Example order:

```
Task 1 → Task 2 → Task 3 → Task 4
```

---

# 2. Best Approach: async/await (Modern Standard)

## Example

```js id="seq3"
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function run() {
  console.log("Start");

  await delay(1000);
  console.log("Task 1 done");

  await delay(1000);
  console.log("Task 2 done");

  await delay(1000);
  console.log("Task 3 done");

  console.log("End");
}

run();
```

---

# 3. Sequential Execution with Array of Tasks

## Case: dynamic tasks

```js id="seq4"
const tasks = [
  () => fetch("/api/1"),
  () => fetch("/api/2"),
  () => fetch("/api/3"),
];

async function runSequentially() {
  const results = [];

  for (const task of tasks) {
    const res = await task();
    results.push(res);
  }

  return results;
}
```

---

# 4. Why NOT use forEach with async

❌ Wrong approach:

```js id="seq5"
tasks.forEach(async (task) => {
  await task();
});
```

### Problem:

- Does NOT wait for previous task
- Runs all tasks concurrently

---

# 5. Promise Chaining (Older Approach)

Before async/await:

```js id="seq6"
task1()
  .then(() => task2())
  .then(() => task3())
  .then(() => console.log("Done"));
```

---

## Dynamic chaining (less readable)

```js id="seq7"
tasks.reduce((promise, task) => {
  return promise.then(() => task());
}, Promise.resolve());
```

---

# 6. Sequential vs Parallel (Important Interview Concept)

## Sequential (slow but ordered)

```js id="seq8"
for (const task of tasks) {
  await task();
}
```

---

## Parallel (fast but unordered)

```js id="seq9"
await Promise.all(tasks.map((task) => task()));
```

---

# 7. Real-world Example

## Sequential API calls (dependent)

```js id="seq10"
async function processOrder() {
  const user = await fetchUser();
  const cart = await fetchCart(user.id);
  const order = await createOrder(cart);

  return order;
}
```

👉 Each step depends on previous result.

---

## Sequential logging / processing

```js id="seq11"
async function processFiles(files) {
  for (const file of files) {
    const data = await readFile(file);
    console.log(data);
  }
}
```

---

# 8. Error Handling in Sequential Flow

```js id="seq12"
async function run() {
  try {
    for (const task of tasks) {
      await task();
    }
  } catch (err) {
    console.error("Error occurred:", err);
  }
}
```

---

## Continue even if one fails

```js id="seq13"
async function run() {
  for (const task of tasks) {
    try {
      await task();
    } catch (err) {
      console.log("Task failed:", err);
    }
  }
}
```

---

# 9. Advanced Pattern: Sequential with delay

```js id="seq14"
async function runWithDelay(tasks) {
  for (const task of tasks) {
    await task();
    await new Promise((res) => setTimeout(res, 500));
  }
}
```

---

# 10. Common Pitfalls

## Pitfall 1: using forEach

```js id="seq15"
tasks.forEach(async (task) => {
  await task(); // ❌ not sequential
});
```

---

## Pitfall 2: using Promise.all when order matters

```js id="seq16"
await Promise.all(tasks.map((task) => task())); // ❌ parallel execution
```

---

## Pitfall 3: forgetting await in loop

```js id="seq17"
for (const task of tasks) {
  task(); // ❌ not waiting
}
```

---

# 11. Interview Insight

### When interviewer asks:

> “How do you run async tasks sequentially?”

## Best answer:

> “I use a `for...of` loop with `await` because it ensures each Promise resolves before the next begins, maintaining order and control flow. Alternatively, Promise chaining or reduce-based patterns can be used, but `async/await + loop` is the most readable and modern approach.”

---

# 12. Interview Summary

> To handle multiple async operations sequentially in JavaScript, the best approach is using `async/await` inside a `for...of` loop, which ensures each operation completes before the next starts. Older approaches include Promise chaining or reduce-based patterns. Sequential execution is useful when tasks are dependent, while parallel execution (`Promise.all`) is preferred for independent tasks to improve performance.

## Question 12. Explain try…catch in async functions

## Question 13. Difference between setTimeout(fn, 0) and Promise.resolve().then(fn)

## Question 14. What are memory leaks in JavaScript? Examples?

## Question 15. Explain closures with practical examples

## Question 16. How to use bind to create partially applied functions

## Question 17. What is function currying? Example in JS

## Question 18. Explain optional parameters in functions

## Question 19. Difference between map() and reduce()
