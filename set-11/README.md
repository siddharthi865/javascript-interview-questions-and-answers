# Set 11

| S.No. | Question                                                                                                                                                |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between document and window objects?](#question-1-what-is-the-difference-between-document-and-window-objects)                   |
| 2.    | [How do you check if a variable is null or undefined?](#question-2-how-do-you-check-if-a-variable-is-null-or-undefined)                                 |
| 3.    | [Difference between for...in and for...of loops](#question-3-difference-between-forin-and-forof-loops)                                                  |
| 4.    | [How to find the type of an object in JavaScript?](#question-4-how-to-find-the-type-of-an-object-in-javascript)                                         |
| 5.    | [What is the difference between primitive and non-primitive types?](#question-5-what-is-the-difference-between-primitive-and-non-primitive-types)       |
| 6.    | [Difference between mutable and immutable data types](#question-6-difference-between-mutable-and-immutable-data-types)                                  |
| 7.    | [How to concatenate arrays in JavaScript?](#question-7-how-to-concatenate-arrays-in-javascript)                                                         |
| 8.    | [How to check if a string contains another string?](#question-8-how-to-check-if-a-string-contains-another-string)                                       |
| 9.    | [How to convert a number to a string in binary, octal, or hexadecimal?](#question-9-how-to-convert-a-number-to-a-string-in-binary-octal-or-hexadecimal) |
| 10.   | [How to round a number to a fixed number of decimal places?](#question-10-how-to-round-a-number-to-a-fixed-number-of-decimal-places)                    |
| 11.   | [Difference between parseFloat and Number()](#question-11-difference-between-parsefloat-and-number)                                                     |
| 12.   | [What is the difference between undefined, null, and an empty string?](#question-12-what-is-the-difference-between-undefined-null-and-an-empty-string)  |
| 13.   | [How to find the length of an object or array?](#question-13-how-to-find-the-length-of-an-object-or-array)                                              |
| 14.   | [How to remove a property from an object?](#question-14-how-to-remove-a-property-from-an-object)                                                        |
| 15.   | [Difference between document.getElementById() and querySelector()](#question-15-difference-between-documentgetelementbyid-and-queryselector)            |
| 16.   | [How to prevent an HTML form from submitting?](#question-16-how-to-prevent-an-html-form-from-submitting)                                                |
| 17.   | [How to add an event listener to multiple elements?](#question-17-how-to-add-an-event-listener-to-multiple-elements)                                    |
| 18.   | [Difference between innerHTML, innerText, and textContent](#question-18-difference-between-innerhtml-innertext-and-textcontent)                         |
| 19.   | [How to check if a key exists in an object?](#question-19-how-to-check-if-a-key-exists-in-an-object)                                                    |
| 20.   | [How to find the largest/smallest number in an array?](#question-20-how-to-find-the-largestsmallest-number-in-an-array)                                 |

## Question 1. What is the difference between document and window objects?

In a browser environment:

- `window` is the **global object** representing the browser window/tab.
- `document` is a property of `window` that represents the **HTML page (DOM)** loaded inside that window.

So the relationship is:

```js
window.document;
```

`window` manages browser-level features, while `document` manages webpage content.

### Detailed Explanation

#### 1. What is the `window` Object?

The `window` object is the **top-level global object** in browsers.

It represents the browser tab/window and provides APIs for:

- Browser control
- Timers
- Navigation
- Storage
- Global variables/functions
- Screen/location/history access

##### Examples

```js
console.log(window);
```

##### Common `window` APIs

```js
window.alert("Hello");
window.setTimeout(() => {}, 1000);
window.location.href;
window.localStorage;
window.history.back();
```

##### Important Interview Point

In browsers, globally declared variables become properties of `window` (with `var`).

```js
var x = 10;

console.log(window.x); // 10
```

But `let` and `const` do NOT attach to `window`.

```js
let y = 20;

console.log(window.y); // undefined
```

#### 2. What is the `document` Object?

The `document` object represents the **DOM (Document Object Model)** of the webpage.

It allows JavaScript to:

- Access HTML elements
- Modify content
- Change styles
- Create/remove elements
- Handle DOM events

##### Examples

```js
console.log(document);
```

##### Common `document` APIs

```js
document.getElementById("title");
document.querySelector(".box");
document.createElement("div");
document.body.style.background = "lightblue";
```

### Relationship Between `window` and `document`

`document` is actually a property of `window`.

```js
console.log(window.document === document); // true
```

Hierarchy:

```text
window
 ├── document
 │    ├── body
 │    ├── forms
 │    └── links
 ├── location
 ├── history
 ├── localStorage
 └── setTimeout()
```

### Key Differences

| Feature      | `window`                   | `document`                           |
| ------------ | -------------------------- | ------------------------------------ |
| Represents   | Browser window/tab         | Loaded HTML page                     |
| Type         | Global browser object      | DOM object                           |
| Scope        | Entire browser environment | Webpage content                      |
| Access Level | Top-level object           | Child of `window`                    |
| Used For     | Browser APIs               | DOM manipulation                     |
| Example APIs | `alert()`, `setTimeout()`  | `querySelector()`, `createElement()` |

### Practical Examples

#### Example 1 — Using `window`

```js
window.alert("Welcome");
```

Controls browser behavior.

#### Example 2 — Using `document`

```js
document.body.style.backgroundColor = "yellow";
```

Manipulates webpage content.

### Browser Global Scope Behavior

Because `window` is global, you can often omit `window`.

These are equivalent:

```js
window.alert("Hi");
alert("Hi");
```

And:

```js
window.document;
document;
```

JavaScript automatically looks on `window`.

### Important Interview Concept

#### DOM vs BOM

##### `document` → DOM (Document Object Model)

Handles webpage structure.

##### `window` → BOM (Browser Object Model)

Handles browser features.

Examples of BOM objects:

- `navigator`
- `screen`
- `history`
- `location`

All accessible through `window`.

### Common Interview Questions

#### Q1: Is `document` part of `window`?

Yes.

```js
window.document;
```

#### Q2: Can `document` exist without `window`?

In browsers, no.

`document` is provided by the browser through `window`.

#### Q3: Which object is global in browsers?

`window`

#### Q4: Which object manipulates HTML elements?

`document`

### Example Combining Both

```html
<button id="btn">Click</button>

<script>
  document.getElementById("btn").addEventListener("click", () => {
    window.alert("Button clicked");
  });
</script>
```

- `document` selects the button
- `window` shows the alert

### Common Pitfalls

#### 1. Confusing Global Scope with DOM

```js
document.myVar; // ❌
```

Variables belong to `window`, not `document`.

#### 2. Assuming Everything is on `document`

Timers are on `window`, not `document`.

```js
setTimeout(() => {}, 1000);
```

Equivalent to:

```js
window.setTimeout(() => {}, 1000);
```

### Best Practices

- Use `document` for DOM manipulation.
- Use `window` only for browser/global APIs.
- Avoid polluting `window` with global variables.
- Prefer modules and block scope (`let`/`const`).

### Summary

> The `window` object represents the browser window and acts as the global object in browsers, providing APIs like `alert`, `setTimeout`, and `localStorage`.
> The `document` object represents the DOM of the loaded webpage and is used to access and manipulate HTML elements.
> `document` is actually a property of `window`, i.e., `window.document`.

## Question 2. How do you check if a variable is null or undefined?

## Short Answer

To check whether a variable is either `null` or `undefined`, the most common JavaScript pattern is:

```js
if (value == null) {
  console.log("Value is null or undefined");
}
```

Using loose equality (`==`) intentionally here works because:

```js
null == undefined; // true
```

but it does **not** match other falsy values like `0`, `false`, or `""`.

---

# Understanding `null` and `undefined`

## `undefined`

A variable is `undefined` when:

- It has been declared but not assigned a value.
- A function does not return anything.
- A non-existent object property is accessed.

```js
let x;

console.log(x); // undefined
```

---

## `null`

`null` is an intentional absence of a value.

```js
let user = null;

console.log(user); // null
```

---

# Different Ways to Check

## 1. Check for Both (`null` or `undefined`)

### Recommended

```js
if (value == null) {
  console.log("null or undefined");
}
```

Example:

```js
let a = null;
let b;

console.log(a == null); // true
console.log(b == null); // true
```

### Why it works

The only values for which this is true are:

```js
null == undefined; // true
```

No other values match.

```js
0 == null; // false
false == null; // false
"" == null; // false
```

---

## 2. Check Only for `undefined`

### Strict Equality

```js
if (value === undefined) {
  console.log("undefined");
}
```

Example:

```js
let x;

console.log(x === undefined); // true
```

---

## 3. Check Only for `null`

```js
if (value === null) {
  console.log("null");
}
```

Example:

```js
let user = null;

console.log(user === null); // true
```

---

## 4. Using `typeof` (Useful for Undeclared Variables)

Normally:

```js
console.log(nonExistingVar);
```

throws:

```text
ReferenceError
```

But:

```js
console.log(typeof nonExistingVar);
```

returns:

```text
"undefined"
```

Check safely:

```js
if (typeof nonExistingVar === "undefined") {
  console.log("Variable does not exist");
}
```

### Interview Point

`typeof` is useful when the variable may not even be declared.

---

# Comparing the Approaches

| Check                          | Detects `null` | Detects `undefined` |
| ------------------------------ | -------------- | ------------------- |
| `value == null`                | ✅             | ✅                  |
| `value === null`               | ✅             | ❌                  |
| `value === undefined`          | ❌             | ✅                  |
| `typeof value === "undefined"` | ❌             | ✅                  |

---

# Common Pitfall: Using Falsy Checks

Many developers write:

```js
if (!value) {
  console.log("No value");
}
```

This also matches:

```js
0;
false;
("");
NaN;
null;
undefined;
```

Example:

```js
console.log(!0); // true
console.log(!false); // true
console.log(!""); // true
console.log(!null); // true
console.log(!undefined); // true
```

So this is **not** a reliable way to specifically check for `null` or `undefined`.

---

# Modern JavaScript Alternative: Nullish Check

JavaScript introduced the **nullish coalescing operator (`??`)**.

```js
const name = value ?? "Default";
```

It only falls back when:

```js
value === null || value === undefined;
```

Example:

```js
console.log(null ?? "Guest"); // Guest
console.log(undefined ?? "Guest"); // Guest
console.log(0 ?? "Guest"); // 0
console.log("" ?? "Guest"); // ""
```

This avoids the problems of `||`.

---

# Interview-Friendly Summary

> `undefined` means a value has not been assigned, while `null` represents an intentional absence of a value. To check for both, the most common pattern is `value == null`, because it returns `true` only for `null` and `undefined`. To check individually, use strict equality (`===`). If a variable might not even be declared, use `typeof variable === "undefined"` to avoid a `ReferenceError`.

## Question 3. Difference between for...in and for...of loops

## Short Answer

- **`for...in`** iterates over **object keys (property names)**.
- **`for...of`** iterates over **values of iterable objects** (like arrays, strings, maps, sets).

In short:

> `for...in` → keys / indexes
> `for...of` → values

---

# Detailed Explanation

## 1. `for...in` Loop

The `for...in` loop is used to iterate over the **enumerable properties (keys)** of an object.

### Example with Object

```js id="a1b2c3"
const user = {
  name: "John",
  age: 30,
  role: "admin",
};

for (let key in user) {
  console.log(key, user[key]);
}
```

### Output

```
name John
age 30
role admin
```

### Key Characteristics

- Iterates over **keys (property names)**
- Works on objects (and arrays, but not recommended)
- Includes inherited enumerable properties (important pitfall)

---

## 2. `for...of` Loop

The `for...of` loop iterates over **values of iterable objects**.

### Example with Array

```js id="d4e5f6"
const numbers = [10, 20, 30];

for (let value of numbers) {
  console.log(value);
}
```

### Output

```
10
20
30
```

---

### Example with String

```js id="g7h8i9"
for (let char of "hello") {
  console.log(char);
}
```

### Output

```
h
e
l
l
o
```

---

# Key Differences

| Feature                       | `for...in`            | `for...of`                              |
| ----------------------------- | --------------------- | --------------------------------------- |
| Iterates over                 | Keys (property names) | Values                                  |
| Works on                      | Objects (and arrays)  | Iterables (arrays, strings, maps, sets) |
| Array usage                   | Not recommended       | Recommended                             |
| Includes inherited properties | Yes                   | No                                      |
| Use case                      | Object traversal      | Data iteration                          |

---

# Important Interview Insight

## Arrays: Why `for...in` is problematic

```js id="j1k2l3"
const arr = [10, 20, 30];

for (let i in arr) {
  console.log(i, arr[i]);
}
```

### Output

```
0 10
1 20
2 30
```

It looks fine—but there’s a problem.

### Problem: It iterates over keys, not values

Also:

```js id="m4n5o6"
Array.prototype.custom = "hello";

for (let i in arr) {
  console.log(i);
}
```

### Output

```
0
1
2
custom   ❌ unexpected
```

👉 This happens because `for...in` includes **enumerable prototype properties**

---

## Correct way for arrays

```js id="p7q8r9"
for (let value of arr) {
  console.log(value);
}
```

### Output

```
10
20
30
```

---

# How They Work Internally

## `for...in`

- Iterates over object keys using internal property enumeration
- Includes prototype chain (unless filtered manually)

---

## `for...of`

- Uses the **iterator protocol**
- Works with any object implementing `Symbol.iterator`

Example:

```js id="s1t2u3"
const iterable = [1, 2, 3];

const iterator = iterable[Symbol.iterator]();

console.log(iterator.next()); // { value: 1, done: false }
```

---

# When to Use What

## Use `for...in` when:

- You need to loop through object properties
- You are inspecting keys

```js id="v4w5x6"
const obj = { a: 1, b: 2 };

for (let key in obj) {
  console.log(key);
}
```

---

## Use `for...of` when:

- You are working with arrays, strings, maps, sets
- You need values directly

```js id="y7z8a9"
for (let val of new Set([1, 2, 3])) {
  console.log(val);
}
```

---

# Common Pitfalls

## 1. Using `for...in` with arrays

❌ Wrong (can include prototype keys):

```js id="b1c2d3"
for (let i in [1, 2, 3]) {
}
```

✔ Prefer:

```js id="e4f5g6"
for (let v of [1, 2, 3]) {
}
```

---

## 2. Assuming both behave the same

They are fundamentally different:

- `for...in` → property enumeration
- `for...of` → iterator consumption

---

# Interview-Friendly Summary

> `for...in` iterates over the keys of an object and is mainly used for object property enumeration. It can also iterate over arrays but is not recommended due to prototype chain issues.
> `for...of` iterates over values of iterable objects like arrays, strings, maps, and sets using the iterator protocol. It is the preferred way to loop over data collections in modern JavaScript.

## Question 4. How to find the type of an object in JavaScript?

## Short Answer

There are multiple ways to determine the type of a value in JavaScript:

```js
typeof value;
```

However, `typeof` has limitations for objects. For accurately identifying object types, common approaches are:

```js
Object.prototype.toString.call(value);
```

or

```js
value instanceof Constructor;
```

depending on the use case.

---

# 1. Using `typeof`

`typeof` is the most commonly used operator.

```js
console.log(typeof "hello"); // string
console.log(typeof 123); // number
console.log(typeof true); // boolean
console.log(typeof undefined); // undefined
console.log(typeof Symbol()); // symbol
console.log(typeof 10n); // bigint
console.log(typeof function () {}); // function
console.log(typeof {}); // object
```

### Interview Pitfall

```js
console.log(typeof null);
```

Output:

```js
"object";
```

This is a historical JavaScript bug that has been preserved for backward compatibility.

---

# 2. Using `Object.prototype.toString.call()`

This is one of the most reliable ways to identify built-in object types.

```js
console.log(Object.prototype.toString.call({}));
```

Output:

```js
"[object Object]";
```

Examples:

```js
console.log(Object.prototype.toString.call([]));
```

```js
"[object Array]";
```

```js
console.log(Object.prototype.toString.call(new Date()));
```

```js
"[object Date]";
```

```js
console.log(Object.prototype.toString.call(/abc/));
```

```js
"[object RegExp]";
```

```js
console.log(Object.prototype.toString.call(null));
```

```js
"[object Null]";
```

```js
console.log(Object.prototype.toString.call(undefined));
```

```js
"[object Undefined]";
```

### Utility Function

```js
function getType(value) {
  return Object.prototype.toString.call(value).slice(8, -1);
}

console.log(getType([])); // Array
console.log(getType({})); // Object
console.log(getType(null)); // Null
console.log(getType(new Date())); // Date
```

---

# 3. Using `instanceof`

Checks whether an object was created from a particular constructor.

```js
const arr = [];

console.log(arr instanceof Array);
```

Output:

```js
true;
```

More examples:

```js
console.log(new Date() instanceof Date); // true
console.log([] instanceof Object); // true
console.log([] instanceof Array); // true
```

### How it Works

`instanceof` checks the prototype chain.

```js
arr.__proto__ === Array.prototype;
```

(or somewhere higher in the chain)

---

# 4. Detecting Arrays

### Recommended

```js
Array.isArray(value);
```

Example:

```js
console.log(Array.isArray([])); // true
console.log(Array.isArray({})); // false
```

### Why Not `typeof`?

```js
typeof [];
```

returns:

```js
"object";
```

which is not very useful.

---

# 5. Detecting Functions

```js
typeof fn === "function";
```

Example:

```js
function greet() {}

console.log(typeof greet);
```

Output:

```js
"function";
```

---

# 6. Detecting Plain Objects

Sometimes interviewers ask:

> How do you check if something is a plain object?

```js
function isPlainObject(value) {
  return Object.prototype.toString.call(value) === "[object Object]";
}
```

Example:

```js
console.log(isPlainObject({})); // true
console.log(isPlainObject([])); // false
console.log(isPlainObject(new Date())); // false
```

---

# Comparison of Techniques

| Method                             | Best For                         | Limitations                                |
| ---------------------------------- | -------------------------------- | ------------------------------------------ |
| `typeof`                           | Primitive types                  | Arrays, Date, RegExp all return `"object"` |
| `instanceof`                       | Constructor checks               | Can fail across different realms/iframes   |
| `Array.isArray()`                  | Arrays                           | Arrays only                                |
| `Object.prototype.toString.call()` | Accurate built-in type detection | More verbose                               |

---

# Common Interview Questions

### Q1: Why does `typeof null` return `"object"`?

Because of a historical implementation bug in early JavaScript. The behavior was standardized and cannot be changed now due to backward compatibility.

```js
typeof null; // "object"
```

---

### Q2: How do you distinguish an Array from an Object?

```js
Array.isArray(value);
```

or

```js
Object.prototype.toString.call(value);
```

---

### Q3: Which is the most reliable way to detect object types?

For built-in types:

```js
Object.prototype.toString.call(value);
```

is generally the most reliable.

---

# Interview-Friendly Summary

> `typeof` is useful for detecting primitive types and functions, but it returns `"object"` for arrays, dates, and even `null`. For accurate object type detection, `Object.prototype.toString.call(value)` is the most reliable built-in approach. `instanceof` checks whether an object inherits from a specific constructor, while `Array.isArray()` is the preferred way to detect arrays. Understanding the strengths and limitations of each method is a common JavaScript interview topic.

## Question 5. What is the difference between primitive and non-primitive types?

## Short Answer

JavaScript values are divided into two categories:

### Primitive Types

Stored and copied **by value**.

```js
string;
number;
boolean;
undefined;
null;
symbol;
bigint;
```

### Non-Primitive Types (Reference Types)

Stored and copied **by reference**.

```js
Object;
Array;
Function;
Date;
Map;
Set;
RegExp;
```

The key difference is:

> **Primitives hold the actual value, whereas non-primitives hold a reference to an object in memory.**

---

# 1. Primitive Types

A primitive value is immutable and represents a single value.

```js
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20
```

### What happened?

```text
a = 10
b = copy of 10
```

Changing `b` does not affect `a`.

---

## Primitive Types in JavaScript

### String

```js
let name = "John";
```

### Number

```js
let age = 30;
```

### Boolean

```js
let isAdmin = true;
```

### Undefined

```js
let x;
```

### Null

```js
let user = null;
```

### Symbol

```js
const id = Symbol("id");
```

### BigInt

```js
const big = 123456789012345678901234567890n;
```

---

# 2. Non-Primitive Types

Non-primitives are objects.

Variables store a reference (memory address), not the actual object.

```js
const obj1 = {
  name: "John",
};

const obj2 = obj1;
```

Memory concept:

```text
obj1 ──► { name: "John" }
obj2 ──► same object
```

Changing one affects the other.

```js
obj2.name = "Alice";

console.log(obj1.name); // Alice
console.log(obj2.name); // Alice
```

---

# Common Non-Primitive Types

### Object

```js
const user = {
  name: "John",
};
```

### Array

```js
const nums = [1, 2, 3];
```

### Function

```js
function greet() {}
```

Functions are objects in JavaScript.

```js
console.log(typeof greet); // function
```

### Date

```js
const today = new Date();
```

### Map

```js
const map = new Map();
```

### Set

```js
const set = new Set();
```

---

# Copy Behavior

## Primitive Copy

```js
let a = 5;
let b = a;

b = 10;

console.log(a); // 5
```

Each variable has its own value.

---

## Object Copy

```js
const a = { count: 5 };
const b = a;

b.count = 10;

console.log(a.count); // 10
```

Both variables reference the same object.

---

# Equality Comparison

## Primitive Equality

```js
console.log(10 === 10); // true
console.log("JS" === "JS"); // true
```

Values are compared directly.

---

## Object Equality

```js
console.log({} === {}); // false
```

Output:

```text
false
```

Because these are different objects in memory.

```js
const obj = {};

console.log(obj === obj); // true
```

Same reference.

---

# Mutability

## Primitives are Immutable

```js
let str = "hello";

str[0] = "H";

console.log(str);
```

Output:

```text
hello
```

Strings cannot be modified in place.

Instead:

```js
str = "Hello";
```

creates a new string.

---

## Objects are Mutable

```js
const user = {
  name: "John",
};

user.name = "Alice";

console.log(user.name);
```

Output:

```text
Alice
```

Object contents can change.

---

# `const` Behavior

A common interview trick:

```js
const obj = {
  name: "John",
};

obj.name = "Alice";
```

Works perfectly.

Why?

Because `const` prevents reassignment of the variable, not mutation of the object.

```js
obj = {}; // ❌ TypeError
```

---

# Memory Representation

### Primitive

```text
a = 100
```

```text
Variable
   │
   ▼
 100
```

---

### Object

```js
const user = { name: "John" };
```

```text
Variable
   │
   ▼
Reference ───► Object in Heap
```

---

# `typeof` Results

| Value          | Result                      |
| -------------- | --------------------------- |
| `"hello"`      | `"string"`                  |
| `10`           | `"number"`                  |
| `true`         | `"boolean"`                 |
| `undefined`    | `"undefined"`               |
| `Symbol()`     | `"symbol"`                  |
| `10n`          | `"bigint"`                  |
| `{}`           | `"object"`                  |
| `[]`           | `"object"`                  |
| `function(){}` | `"function"`                |
| `null`         | `"object"` (historical bug) |

---

# Common Interview Pitfalls

## Pitfall 1: Arrays are Objects

```js
typeof [];
```

Output:

```js
"object";
```

Use:

```js
Array.isArray([]);
```

---

## Pitfall 2: Object Assignment Creates a Reference

```js
const a = { x: 1 };
const b = a;
```

This is not a copy.

Both variables point to the same object.

---

## Pitfall 3: `null` is Primitive

Although:

```js
typeof null === "object";
```

`null` is officially considered a **primitive type**.

---

# Interview-Friendly Summary

> Primitive types (`string`, `number`, `boolean`, `undefined`, `null`, `symbol`, `bigint`) store actual values and are copied by value. They are immutable. Non-primitive types (objects, arrays, functions, maps, sets, dates, etc.) store references to memory locations and are copied by reference. Changes made through one reference are visible through all references pointing to the same object. This distinction affects assignment, equality comparisons, memory usage, and mutation behavior, making it one of the most important JavaScript fundamentals.

## Question 6. Difference between mutable and immutable data types

## Short Answer

- **Mutable** data can be changed after it is created.
- **Immutable** data cannot be changed after it is created; any "modification" creates a new value instead.

In JavaScript:

- **Primitive types** are **immutable**.
- **Objects, Arrays, Functions, Maps, Sets** are **mutable** by default.

---

# What is Mutability?

Mutability refers to whether a value can be modified after creation.

## Immutable Example

```js
let str = "hello";

str[0] = "H";

console.log(str);
```

Output:

```js
"hello";
```

The string remains unchanged because strings are immutable.

To change it:

```js
str = "Hello";

console.log(str);
```

Output:

```js
"Hello";
```

A new string is created and assigned to `str`.

---

# Immutable Data Types

All JavaScript primitive values are immutable:

```js
string;
number;
boolean;
undefined;
null;
symbol;
bigint;
```

## Example: Number

```js
let a = 10;

a += 5;

console.log(a);
```

Output:

```js
15;
```

The original number wasn't modified; a new value was produced and assigned back to `a`.

---

## Example: String Methods

```js
const str = "javascript";

const upper = str.toUpperCase();

console.log(str); // javascript
console.log(upper); // JAVASCRIPT
```

`toUpperCase()` returns a new string instead of changing the original.

---

# Mutable Data Types

Objects are mutable.

```js
const user = {
  name: "John",
};

user.name = "Alice";

console.log(user.name);
```

Output:

```js
Alice;
```

The existing object was modified.

---

## Arrays are Mutable

```js
const arr = [1, 2, 3];

arr.push(4);

console.log(arr);
```

Output:

```js
[1, 2, 3, 4];
```

The original array changed.

---

# Common Mutable Types

```js
Object;
Array;
Function;
Date;
Map;
Set;
RegExp;
```

Example:

```js
const map = new Map();

map.set("name", "John");
```

The Map itself is mutated.

---

# Array Methods: Mutable vs Immutable

This is a very common interview topic.

## Mutable Array Methods

These modify the original array:

```js
push();
pop();
shift();
unshift();
splice();
sort();
reverse();
fill();
copyWithin();
```

Example:

```js
const arr = [1, 2, 3];

arr.push(4);

console.log(arr);
```

Output:

```js
[1, 2, 3, 4];
```

---

## Immutable Array Methods

These return a new array:

```js
map();
filter();
slice();
concat();
reduce();
flat();
flatMap();
```

Example:

```js
const arr = [1, 2, 3];

const result = arr.map((x) => x * 2);

console.log(arr);
console.log(result);
```

Output:

```js
[1, 2, 3][(2, 4, 6)];
```

---

# Objects and References

Mutability becomes important because objects are passed by reference.

```js
const obj1 = {
  name: "John",
};

const obj2 = obj1;

obj2.name = "Alice";

console.log(obj1.name);
```

Output:

```js
Alice;
```

Both variables point to the same object.

---

# `const` Does NOT Make Objects Immutable

Common interview trick:

```js
const user = {
  name: "John",
};

user.name = "Alice";
```

This works.

Output:

```js
Alice;
```

Because:

```js
const
```

prevents reassignment, not mutation.

The following fails:

```js
user = {};
```

Output:

```js
TypeError;
```

---

# Creating Immutable Objects

## Using `Object.freeze()`

```js
const user = Object.freeze({
  name: "John",
});

user.name = "Alice";

console.log(user.name);
```

Output:

```js
John;
```

The object cannot be modified.

---

## Limitation of `Object.freeze()`

It is shallow.

```js
const user = Object.freeze({
  address: {
    city: "Delhi",
  },
});

user.address.city = "Mumbai";

console.log(user.address.city);
```

Output:

```js
Mumbai;
```

Nested objects remain mutable.

---

# Why Immutability is Important

## Predictable State

Easier debugging.

```js
const newState = {
  ...oldState,
  count: oldState.count + 1,
};
```

No accidental mutations.

---

## React and State Management

React relies heavily on immutable updates.

```js
setUsers([...users, newUser]);
```

instead of:

```js
users.push(newUser); // bad practice
```

---

## Easier Change Detection

```js
oldState !== newState;
```

Simple reference comparison becomes possible.

---

# Interview Comparison Table

| Feature                     | Mutable       | Immutable |
| --------------------------- | ------------- | --------- |
| Can change after creation   | ✅ Yes        | ❌ No     |
| Original value modified     | ✅ Yes        | ❌ No     |
| New value created on update | ❌ Usually No | ✅ Yes    |
| Primitive types             | ❌            | ✅        |
| Objects/Arrays              | ✅            | ❌        |
| Safer for state management  | ❌            | ✅        |
| Easier debugging            | ❌            | ✅        |

---

# Common Interview Questions

## Are strings mutable?

No.

```js
let str = "abc";

str[0] = "x";

console.log(str);
```

Output:

```js
abc;
```

---

## Are arrays mutable?

Yes.

```js
const arr = [1, 2];

arr.push(3);
```

The original array changes.

---

## Does `const` make objects immutable?

No.

```js
const obj = { a: 1 };

obj.a = 2; // allowed
```

Only reassignment is blocked.

---

# Interview-Friendly Summary

> Mutable data can be modified after creation, while immutable data cannot. In JavaScript, all primitive types (`string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`) are immutable. Objects, arrays, functions, maps, and sets are mutable by default. Understanding mutability is important because object mutations affect all references to the same object, while immutable updates create new values, leading to safer and more predictable code, especially in modern frameworks like React.

## Question 7. How to concatenate arrays in JavaScript?

## Short Answer

There are several ways to concatenate arrays in JavaScript:

### Using `concat()` (classic approach)

```js
const arr1 = [1, 2];
const arr2 = [3, 4];

const result = arr1.concat(arr2);

console.log(result); // [1, 2, 3, 4]
```

### Using the Spread Operator (modern approach)

```js
const result = [...arr1, ...arr2];
```

The spread operator is generally the preferred modern solution because it is concise and flexible.

---

# 1. Using `Array.prototype.concat()`

`concat()` returns a **new array** containing the elements of the original arrays.

```js
const arr1 = [1, 2];
const arr2 = [3, 4];

const result = arr1.concat(arr2);

console.log(result);
```

Output:

```js
[1, 2, 3, 4];
```

### Important

`concat()` does **not** modify the original arrays.

```js
const arr1 = [1, 2];
const arr2 = [3, 4];

const result = arr1.concat(arr2);

console.log(arr1); // [1, 2]
console.log(arr2); // [3, 4]
```

---

# 2. Using the Spread Operator (`...`)

Modern ES6+ approach:

```js
const arr1 = [1, 2];
const arr2 = [3, 4];

const result = [...arr1, ...arr2];

console.log(result);
```

Output:

```js
[1, 2, 3, 4];
```

### Why it's popular

It allows combining arrays with additional values:

```js
const result = [0, ...arr1, ...arr2, 5];
```

Output:

```js
[0, 1, 2, 3, 4, 5];
```

---

# 3. Concatenating Multiple Arrays

### Using `concat()`

```js
const result = arr1.concat(arr2, arr3, arr4);
```

### Using Spread

```js
const result = [...arr1, ...arr2, ...arr3, ...arr4];
```

Example:

```js
const a = [1];
const b = [2];
const c = [3];

console.log([...a, ...b, ...c]);
```

Output:

```js
[1, 2, 3];
```

---

# 4. Using `push()` with Spread (Mutates Original Array)

If you want to append one array into another:

```js
const arr1 = [1, 2];
const arr2 = [3, 4];

arr1.push(...arr2);

console.log(arr1);
```

Output:

```js
[1, 2, 3, 4];
```

### Important

This modifies `arr1`.

```js
arr1 === originalArr1; // true
```

---

# 5. Using `Array.prototype.push.apply()`

Older ES5 technique:

```js
const arr1 = [1, 2];
const arr2 = [3, 4];

Array.prototype.push.apply(arr1, arr2);

console.log(arr1);
```

Output:

```js
[1, 2, 3, 4];
```

Today, spread syntax is preferred.

---

# 6. Concatenating Nested Arrays

```js
const arr1 = [1, 2];
const arr2 = [[3, 4]];

const result = arr1.concat(arr2);

console.log(result);
```

Output:

```js
[1, 2, [3, 4]];
```

Notice that `concat()` performs a **shallow merge**, not a deep merge.

---

# Flattening While Concatenating

If you want a flat result:

```js
const result = [...arr1, ...arr2].flat();
```

Example:

```js
const arr1 = [1, 2];
const arr2 = [[3, 4]];

console.log([...arr1, ...arr2].flat());
```

Output:

```js
[1, 2, 3, 4];
```

---

# Performance Considerations

## Immutable Approach

```js
const result = [...arr1, ...arr2];
```

or

```js
const result = arr1.concat(arr2);
```

Creates a new array.

### Pros

- Safer
- Functional programming style
- Works well with React/Redux

### Cons

- Allocates new memory

---

## Mutable Approach

```js
arr1.push(...arr2);
```

### Pros

- Less memory allocation
- Faster for some large-array scenarios

### Cons

- Changes the original array

---

# Common Interview Questions

## Does `concat()` mutate the original array?

No.

```js
const a = [1];
const b = [2];

const c = a.concat(b);

console.log(a); // [1]
```

---

## Which is preferred: `concat()` or spread?

Modern JavaScript typically favors spread syntax:

```js
const merged = [...a, ...b];
```

because it is more readable and flexible.

---

## Is concatenation a deep copy?

No.

```js
const obj = { x: 1 };

const arr1 = [obj];
const arr2 = arr1.concat();

obj.x = 100;

console.log(arr2[0].x);
```

Output:

```js
100;
```

The object reference is shared.

---

# Interview-Friendly Summary

> Arrays can be concatenated using `concat()`, the spread operator (`...`), or by mutating an existing array with `push(...arr)`. `concat()` and spread syntax create a new array and do not modify the originals, making them the preferred approaches in modern JavaScript. The spread operator is especially popular because it is concise, readable, and works naturally when combining multiple arrays or inserting additional elements. Both methods perform shallow copies, meaning nested objects and arrays remain shared by reference.

## Question 8. How to check if a string contains another string?

## Short Answer

To check if a string contains another string in JavaScript, the most modern and readable way is:

```js id="k3n8x1"
str.includes(substring);
```

It returns `true` or `false`.

---

# 1. Using `includes()` (Recommended)

Introduced in ES6, this is the standard approach today.

```js id="a9v2pl"
const str = "Hello JavaScript";

console.log(str.includes("JavaScript")); // true
console.log(str.includes("java")); // false (case-sensitive)
```

### Key Features

- Returns a boolean
- Case-sensitive
- Simple and readable

---

# 2. Using `indexOf()` (Older Approach)

Before `includes()`, developers used `indexOf()`:

```js id="m1c8xq"
const str = "Hello JavaScript";

console.log(str.indexOf("JavaScript") !== -1); // true
```

### How it works

- Returns index of match
- Returns `-1` if not found

```js id="b7t2ka"
str.indexOf("JS"); // -1 if not found
```

---

### Comparison

| Method       | Returns | Readability |
| ------------ | ------- | ----------- |
| `includes()` | boolean | ⭐ Best     |
| `indexOf()`  | number  | Older style |

---

# 3. Using Regular Expressions (`RegExp`)

For advanced pattern matching:

```js id="q9r4mn"
const str = "Hello JavaScript";

console.log(/JavaScript/.test(str)); // true
```

### Case-insensitive search

```js id="z8p2lw"
console.log(/javascript/i.test(str)); // true
```

---

# 4. Using `search()` Method

```js id="u2k7dr"
const str = "Hello JavaScript";

console.log(str.search("JavaScript") !== -1); // true
```

Or with regex:

```js id="c5v9xz"
console.log(str.search(/javascript/i) !== -1);
```

---

# 5. Case Sensitivity (Important Interview Point)

All string matching methods are **case-sensitive by default**:

```js id="h8t3qp"
const str = "JavaScript";

console.log(str.includes("javascript")); // false
```

### Solution: Normalize case

```js id="p3k8ld"
str.toLowerCase().includes("javascript");
```

---

# 6. Edge Cases

## Empty string always returns true

```js id="v7n2sa"
console.log("hello".includes("")); // true
```

Because an empty string is considered to exist everywhere.

---

## Searching in undefined/null causes error

```js id="d1k9xq"
let str = null;

// str.includes("a"); ❌ TypeError
```

Always validate first:

```js id="s8m2qp"
if (str && str.includes("a")) {
  console.log("Found");
}
```

---

# 7. Real-World Example

```js id="x4t9nb"
const email = "user@example.com";

if (email.includes("@")) {
  console.log("Valid email format");
}
```

---

# 8. Performance Considerations

| Method       | Performance | Use case             |
| ------------ | ----------- | -------------------- |
| `includes()` | Fast        | General usage        |
| `indexOf()`  | Similar     | Legacy support       |
| RegExp       | Slower      | Pattern-based search |

---

# 9. When to Use What

## Use `includes()` when:

- Simple substring check
- Clean and readable code needed

```js id="q1w8zd"
text.includes("hello");
```

---

## Use RegExp when:

- Case-insensitive search
- Complex patterns

```js id="t6m3qk"
/hello/i.test(text);
```

---

## Use `indexOf()` when:

- Supporting very old environments

```js id="r8c2nw"
text.indexOf("hello") !== -1;
```

---

# Interview-Friendly Summary

> To check if a string contains another string in JavaScript, the modern and preferred approach is `includes()`, which returns a boolean and is easy to read. Older code uses `indexOf() !== -1`. For advanced or pattern-based matching, regular expressions with `.test()` are used. All string matching methods are case-sensitive by default unless explicitly handled using transformations or regex flags.

## Question 9. How to convert a number to a string in binary, octal, or hexadecimal?

## Question 10. How to round a number to a fixed number of decimal places?

## Question 11. Difference between parseFloat and Number()

## Question 12. What is the difference between undefined, null, and an empty string?

## Question 13. How to find the length of an object or array?

## Question 14. How to remove a property from an object?

## Question 15. Difference between document.getElementById() and querySelector()

## Question 16. How to prevent an HTML form from submitting?

## Question 17. How to add an event listener to multiple elements?

## Question 18. Difference between innerHTML, innerText, and textContent

## Question 19. How to check if a key exists in an object?

## Question 20. How to find the largest/smallest number in an array?
