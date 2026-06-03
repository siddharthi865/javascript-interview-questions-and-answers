# Set 12

| S.No. | Question                                                                                                                                                     |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How to reverse a string in JavaScript?](#question-1-how-to-reverse-a-string-in-javascript)                                                                  |
| 2.    | [Difference between `Math.max.apply()` and `Math.max(...array)`](#question-2-difference-between-mathmaxapply-and-mathmaxarray)                               |
| 3.    | [How to merge multiple arrays into one?](#question-3-how-to-merge-multiple-arrays-into-one)                                                                  |
| 4.    | [Difference between shallow copy and reference copy of arrays](#question-4-difference-between-shallow-copy-and-reference-copy-of-arrays)                     |
| 5.    | [How to empty an array in JavaScript?](#question-5-how-to-empty-an-array-in-javascript)                                                                      |
| 6.    | [Difference between `Array.isArray()` and `instanceof Array`](#question-6-difference-between-arrayisarray-and-instanceof-array)                              |
| 7.    | [What is the difference between `document.createElement()` and `innerHTML`?](#question-7-what-is-the-difference-between-documentcreateelement-and-innerhtml) |
| 8.    | [How to get attributes of an HTML element using JS?](#question-8-how-to-get-attributes-of-an-html-element-using-js)                                          |
| 9.    | [Difference between event bubbling and event capturing](#question-9-difference-between-event-bubbling-and-event-capturing)                                   |
| 10.   | [How to get the current timestamp in JavaScript?](#question-10-how-to-get-the-current-timestamp-in-javascript)                                               |
| 11.   | [Difference between `Date.now()` and `new Date()`](#question-11-difference-between-datenow-and-new-date)                                                     |
| 12.   | [How to calculate the difference between two dates](#question-12-how-to-calculate-the-difference-between-two-dates)                                          |
| 13.   | [How to convert a string to a Date object](#question-13-how-to-convert-a-string-to-a-date-object)                                                            |
| 14.   | [What are regular expressions in JavaScript?](#question-14-what-are-regular-expressions-in-javascript)                                                       |
| 15.   | [How to test a string using regex](#question-15-how-to-test-a-string-using-regex)                                                                            |
| 16.   | [Difference between `setTimeout` and `setInterval`](#question-16-difference-between-settimeout-and-setinterval)                                              |
| 17.   | [How to cancel a `setTimeout` or `setInterval`](#question-17-how-to-cancel-a-settimeout-or-setinterval)                                                      |
| 18.   | [How to debounce a function call](#question-18-how-to-debounce-a-function-call)                                                                              |
| 19.   | [How to throttle a function call](#question-19-how-to-throttle-a-function-call)                                                                              |
| 20.   | [Difference between deep copy and shallow copy of objects](#question-20-difference-between-deep-copy-and-shallow-copy-of-objects)                            |

## Question 1. How to reverse a string in JavaScript?

A string can be reversed in JavaScript in several ways, but the most common interview answer is:

```js
const str = "hello";

const reversed = str.split("").reverse().join("");

console.log(reversed); // "olleh"
```

### Detailed Explanation

JavaScript strings are **immutable**, meaning you cannot directly modify characters inside a string.
So reversing a string usually involves:

1. Converting the string into an array
2. Reversing the array
3. Converting it back into a string

#### Method 1 — Using `split()`, `reverse()`, and `join()` (Most Common)

```js
function reverseString(str) {
  return str.split("").reverse().join("");
}

console.log(reverseString("JavaScript"));
// "tpircSavaJ"
```

##### How it works

###### 1. `split("")`

Converts the string into an array of characters.

```js
"hello".split("");
// ["h", "e", "l", "l", "o"]
```

###### 2. `reverse()`

Reverses the array in place.

```js
["h", "e", "l", "l", "o"].reverse();
// ["o", "l", "l", "e", "h"]
```

###### 3. `join("")`

Converts the array back into a string.

```js
["o", "l", "l", "e", "h"].join("");
// "olleh"
```

#### Time and Space Complexity

| Operation | Complexity |
| --------- | ---------- |
| split()   | O(n)       |
| reverse() | O(n)       |
| join()    | O(n)       |

Overall:

- **Time Complexity:** O(n)
- **Space Complexity:** O(n)

#### Method 2 — Using a Loop

Interviewers sometimes ask you to reverse a string **without using `reverse()`**.

```js
function reverseString(str) {
  let reversed = "";

  for (let i = str.length - 1; i >= 0; i--) {
    reversed += str[i];
  }

  return reversed;
}

console.log(reverseString("hello"));
// "olleh"
```

#### Method 3 — Using Recursion

```js
function reverseString(str) {
  if (str === "") {
    return "";
  }

  return reverseString(str.substr(1)) + str[0];
}

console.log(reverseString("hello"));
// "olleh"
```

##### Important Note

This is elegant but not ideal for large strings because recursion can cause:

- Higher memory usage
- Stack overflow for very long strings

#### Method 4 — Using `reduce()`

```js
function reverseString(str) {
  return str.split("").reduce((rev, char) => char + rev, "");
}

console.log(reverseString("hello"));
// "olleh"
```

#### Unicode / Emoji Edge Case

A common issue with `split("")` is that it may break Unicode surrogate pairs like emojis.

Example:

```js
const str = "😊👍";

console.log(str.split("").reverse().join(""));
// Incorrect result
```

Better approach:

```js
function reverseString(str) {
  return [...str].reverse().join("");
}

console.log(reverseString("😊👍"));
// 👍😊
```

Using the spread operator (`...str`) correctly handles many Unicode characters.

### Modern ES6+ Version

```js
const reverseString = (str) => [...str].reverse().join("");
```

### Common Interview Follow-Ups

#### Q1: Why can't we directly reverse a string?

Because JavaScript strings are immutable.

```js
let str = "hello";
str[0] = "H";

console.log(str); // still "hello"
```

#### Q2: Does `reverse()` work on strings?

No. `reverse()` is an Array method.

```js
"hello".reverse(); // Error
```

#### Q3: Which method is best?

Usually:

```js
str.split("").reverse().join("");
```

is considered the standard interview solution.

For Unicode-safe implementations:

```js
[...str].reverse().join("");
```

is better.

### Best Practice Recommendation

For production-quality modern JavaScript:

```js
const reverseString = (str) => [...str].reverse().join("");
```

because it is:

- Clean
- Readable
- ES6+
- More Unicode-friendly than `split("")`

## Question 2. Difference between `Math.max.apply()` and `Math.max(...array)`

## Short Answer

Both `Math.max.apply()` and `Math.max(...array)` are used to pass array elements as individual arguments to `Math.max()`, but the spread operator (`...`) is the modern ES6+ approach and is generally preferred.

```js
const numbers = [10, 20, 30, 40];

Math.max.apply(null, numbers); // 40
Math.max(...numbers); // 40
```

---

# Why Are They Needed?

`Math.max()` expects individual arguments, not an array.

```js
Math.max(10, 20, 30); // 30

Math.max([10, 20, 30]); // NaN
```

To use an array, you must expand its elements into separate arguments.

---

# 1. Using `Math.max.apply()`

Before ES6, developers commonly used `apply()`.

```js
const arr = [5, 10, 15];

const max = Math.max.apply(null, arr);

console.log(max); // 15
```

### How it works

`apply()` calls a function with:

```js
func.apply(thisArg, argsArray);
```

Example:

```js
Math.max.apply(null, [5, 10, 15]);
```

Internally behaves like:

```js
Math.max(5, 10, 15);
```

### Why `null`?

`Math.max()` doesn't use `this`, so `null` is commonly passed.

```js
Math.max.apply(null, arr);
```

---

# 2. Using Spread Operator (`...`)

ES6 introduced the spread syntax.

```js
const arr = [5, 10, 15];

const max = Math.max(...arr);

console.log(max); // 15
```

Internally:

```js
Math.max(...arr);
```

becomes:

```js
Math.max(5, 10, 15);
```

---

# Comparison

| Feature              | `Math.max.apply()`    | `Math.max(...array)`  |
| -------------------- | --------------------- | --------------------- |
| Syntax               | Older ES5 style       | Modern ES6+ style     |
| Readability          | Less readable         | More readable         |
| Requires `thisArg`   | Yes                   | No                    |
| Common today         | Rare                  | Preferred             |
| Performance          | Similar in most cases | Similar in most cases |
| Interview preference | Legacy knowledge      | Recommended approach  |

---

# Example Comparison

### ES5

```js
const arr = [1, 2, 3];

const max = Math.max.apply(null, arr);
```

### ES6+

```js
const arr = [1, 2, 3];

const max = Math.max(...arr);
```

The second version is shorter and easier to understand.

---

# Large Array Consideration

Both approaches pass every element as a function argument.

For very large arrays, you may hit the JavaScript engine's maximum argument limit.

```js
const hugeArray = new Array(1000000).fill(1);

Math.max(...hugeArray); // May throw RangeError
```

A safer approach:

```js
const max = hugeArray.reduce(
  (currentMax, value) => Math.max(currentMax, value),
  -Infinity,
);
```

### Complexity

- Time: **O(n)**
- Space: **O(1)** (excluding array storage)

---

# Related: `call()` vs `apply()`

```js
Math.max.call(null, 1, 2, 3);

Math.max.apply(null, [1, 2, 3]);
```

- `call()` accepts arguments individually.
- `apply()` accepts an array of arguments.

The spread operator often replaces `apply()`:

```js
Math.max(...[1, 2, 3]);
```

---

# Interview Answer

> `Math.max.apply(null, array)` and `Math.max(...array)` both expand an array into individual arguments for `Math.max()`. `apply()` was the traditional ES5 approach and requires a `this` value plus an array of arguments. The spread operator (`...`) introduced in ES6 provides a cleaner and more readable syntax, so it is generally preferred in modern JavaScript. Both can have issues with extremely large arrays because they pass all elements as function arguments. For very large datasets, a `reduce()`-based solution is often safer.

## Question 3. How to merge multiple arrays into one?

## Short Answer

You can merge multiple arrays in JavaScript using:

- **Spread operator (`...`)** → modern and preferred
- **`concat()`** → classic approach
- **`Array.prototype.push(...arrays)`** → mutation-based
- **Loops / `reduce()`** → for dynamic or custom merging

---

# 1. Using Spread Operator (Best Practice)

## Example

```js id="k2p8xq"
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const merged = [...arr1, ...arr2, ...arr3];

console.log(merged);
// [1, 2, 3, 4, 5, 6]
```

### Why this is preferred

- Clean and readable
- ES6 standard
- Does not mutate original arrays
- Works with any number of arrays

---

# 2. Using `concat()`

## Example

```js id="v9m1lz"
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const merged = arr1.concat(arr2, arr3);

console.log(merged);
// [1, 2, 3, 4, 5, 6]
```

### Key points

- Returns a new array (non-mutating)
- Older but still widely used
- Slightly less flexible than spread syntax

---

# 3. Using `push()` with Spread (Mutating Approach)

## Example

```js id="q8r2bc"
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const result = [];
result.push(...arr1, ...arr2, ...arr3);

console.log(result);
// [1, 2, 3, 4, 5, 6]
```

### Important

- This modifies `result`
- Useful when building arrays incrementally

---

# 4. Using `reduce()` (Dynamic Merging)

Useful when arrays are in a collection.

## Example

```js id="x1t7mn"
const arrays = [
  [1, 2],
  [3, 4],
  [5, 6],
];

const merged = arrays.reduce((acc, curr) => {
  return acc.concat(curr);
}, []);

console.log(merged);
// [1, 2, 3, 4, 5, 6]
```

### Or using spread inside reduce

```js id="p6w8zk"
const merged = arrays.reduce((acc, curr) => [...acc, ...curr], []);
```

---

# 5. Using `flat()` (Modern & Elegant)

If you have nested arrays:

```js id="d3k9qp"
const arrays = [
  [1, 2],
  [3, 4],
  [5, 6],
];

const merged = arrays.flat();

console.log(merged);
// [1, 2, 3, 4, 5, 6]
```

### Deeper nesting

```js id="z7n2vc"
const arr = [[1, [2]], [[3, 4]]];

arr.flat(2);
// [1, 2, 3, 4]
```

---

# Comparison

| Method            | Mutates? | Readability | Use Case                      |
| ----------------- | -------- | ----------- | ----------------------------- |
| `[...a, ...b]`    | ❌ No    | ⭐⭐⭐⭐⭐  | Best general approach         |
| `concat()`        | ❌ No    | ⭐⭐⭐⭐    | Legacy + safe                 |
| `push(...arrays)` | ✅ Yes   | ⭐⭐⭐      | When modifying existing array |
| `reduce()`        | ❌ No    | ⭐⭐⭐      | Dynamic array list            |
| `flat()`          | ❌ No    | ⭐⭐⭐⭐⭐  | Nested arrays                 |

---

# Edge Cases & Interview Notes

## 1. Sparse arrays

```js id="f4nq9k"
const arr = [1, , 3];

[...arr]; // preserves empty slots as undefined
```

---

## 2. Large arrays

Spread and `apply`-like behavior can hit argument limits:

```js id="t5qk2v"
Math.max(...new Array(1e6)); // may crash
```

Same caution applies to:

```js id="j8m1wp"
[...bigArray1, ...bigArray2];
```

For extremely large datasets, prefer iterative merging.

---

## 3. Performance note

- Spread and `concat()` are both O(n)
- Spread may have slightly better engine optimization in modern JS engines
- `reduce([...acc, ...curr])` can be less efficient due to repeated allocations

---

# Interview-Ready Answer

> The most modern and preferred way to merge multiple arrays in JavaScript is using the spread operator: `[...arr1, ...arr2]`, which is clean, readable, and non-mutating. Alternatively, `concat()` can be used in older codebases. For nested arrays, `flat()` is a very elegant solution. If mutation is acceptable, `push(...arrays)` can be used. For dynamic or unknown number of arrays, `reduce()` works well.

## Question 4. Difference between shallow copy and reference copy of arrays

## Short Answer

A **reference copy** means two variables point to the **same array in memory**. Modifying one affects the other.

A **shallow copy** creates a **new array object**, but nested objects inside the array are still shared by reference.

---

# 1. Reference Copy

When you assign one array to another variable:

```js
const arr1 = [1, 2, 3];
const arr2 = arr1;

arr2.push(4);

console.log(arr1); // [1, 2, 3, 4]
console.log(arr2); // [1, 2, 3, 4]
```

## Memory Representation

```js
arr1 ──┐
       ▼
     [1,2,3]

arr2 ──┘
```

Both variables point to the **same array**.

### Equality

```js
const arr1 = [1, 2];
const arr2 = arr1;

console.log(arr1 === arr2);
// true
```

Because both references point to the same object.

---

# 2. Shallow Copy

A shallow copy creates a new array.

```js
const arr1 = [1, 2, 3];
const arr2 = [...arr1];

arr2.push(4);

console.log(arr1); // [1, 2, 3]
console.log(arr2); // [1, 2, 3, 4]
```

## Memory Representation

```js
arr1 ──► [1,2,3]

arr2 ──► [1,2,3]
```

Different arrays in memory.

### Equality

```js
const arr1 = [1, 2];
const arr2 = [...arr1];

console.log(arr1 === arr2);
// false
```

---

# The Important Interview Gotcha

A shallow copy only copies the first level.

```js
const arr1 = [{ name: "John" }];

const arr2 = [...arr1];

arr2[0].name = "Mike";

console.log(arr1[0].name);
// Mike
```

Why?

Because the object inside the array is still shared.

## Memory

```js
arr1 ──► [ obj ]
            ▲
            │
arr2 ──► [ obj ]
```

The arrays are different, but the nested object is the same.

---

# Common Ways to Create a Shallow Copy

### Spread Operator

```js
const copy = [...arr];
```

### slice()

```js
const copy = arr.slice();
```

### Array.from()

```js
const copy = Array.from(arr);
```

### concat()

```js
const copy = [].concat(arr);
```

All create a **shallow copy**.

---

# Deep vs Shallow Copy

```js
const arr1 = [{ x: 1 }];

const shallow = [...arr1];
const deep = structuredClone(arr1);

shallow[0].x = 100;

console.log(arr1[0].x);
// 100

deep[0].x = 200;

console.log(arr1[0].x);
// 100
```

`structuredClone()` creates a true deep copy of nested objects and arrays.

---

# Comparison Table

| Feature                                     | Reference Copy | Shallow Copy |
| ------------------------------------------- | -------------- | ------------ |
| New array created?                          | ❌ No          | ✅ Yes       |
| Top-level array shared?                     | ✅ Yes         | ❌ No        |
| Nested objects shared?                      | ✅ Yes         | ✅ Yes       |
| `===` comparison                            | true           | false        |
| Changes to array structure affect original? | ✅ Yes         | ❌ No        |
| Changes to nested objects affect original?  | ✅ Yes         | ✅ Yes       |

---

# Interview Example

```js
const a = [{ value: 1 }];
const b = a; // reference copy
const c = [...a]; // shallow copy

b.push({ value: 2 });

console.log(a.length); // 2
console.log(c.length); // 1

c[0].value = 99;

console.log(a[0].value); // 99
```

### Explanation

- `b = a` → same array reference.
- `c = [...a]` → new array.
- Pushing into `b` affects `a`.
- Modifying `c[0]` affects `a[0]` because the object inside is shared.

---

# Interview-Ready Answer

> A reference copy occurs when you assign one array variable to another (`const b = a`). Both variables point to the same array in memory, so any modification through one reference is visible through the other. A shallow copy (`const b = [...a]`, `slice()`, `Array.from()`) creates a new array object, so changes to the array structure do not affect the original. However, nested objects and arrays are still copied by reference, meaning changes to nested data are reflected in both arrays. This is why shallow copies solve top-level mutation issues but not nested mutation issues.

## Question 5. How to empty an array in JavaScript?

## Short Answer

There are several ways to empty an array in JavaScript, but the most common and recommended method is:

```js
arr.length = 0;
```

This clears the array **in place** and preserves existing references.

---

# Method 1: Set `length = 0` (Recommended)

```js
const arr = [1, 2, 3, 4];

arr.length = 0;

console.log(arr); // []
```

### Why it's good

- Simple and fast
- Modifies the existing array
- Keeps all references pointing to the same (now empty) array

```js
const arr1 = [1, 2, 3];
const arr2 = arr1;

arr1.length = 0;

console.log(arr2); // []
```

Both variables reference the same array, so both see the change.

---

# Method 2: Assign a New Empty Array

```js
let arr = [1, 2, 3];

arr = [];

console.log(arr); // []
```

### Important Difference

This creates a **new array** rather than clearing the existing one.

```js
let arr1 = [1, 2, 3];
const arr2 = arr1;

arr1 = [];

console.log(arr1); // []
console.log(arr2); // [1, 2, 3]
```

`arr2` still points to the original array.

---

# Method 3: Using `splice()`

```js
const arr = [1, 2, 3, 4];

arr.splice(0, arr.length);

console.log(arr); // []
```

Or:

```js
arr.splice(0);
```

### Characteristics

- Mutates the original array
- Preserves references
- Slightly more verbose than `length = 0`

---

# Method 4: Remove Elements in a Loop

```js
const arr = [1, 2, 3];

while (arr.length) {
  arr.pop();
}

console.log(arr); // []
```

### Why it's rarely used

- Less efficient
- More code
- Usually only useful when you need per-element cleanup logic

---

# Comparison

| Method                        | Preserves References? | Mutates Original? | Recommended?                     |
| ----------------------------- | --------------------- | ----------------- | -------------------------------- |
| `arr.length = 0`              | ✅ Yes                | ✅ Yes            | ⭐ Best                          |
| `arr = []`                    | ❌ No                 | ❌ No             | Use when references don't matter |
| `arr.splice(0)`               | ✅ Yes                | ✅ Yes            | Good                             |
| `while(arr.length) arr.pop()` | ✅ Yes                | ✅ Yes            | Rarely                           |

---

# Common Interview Question

## What happens here?

```js
const a = [1, 2, 3];
const b = a;

a = [];
```

This actually throws an error because `a` is declared with `const`.

Let's use `let`:

```js
let a = [1, 2, 3];
const b = a;

a = [];

console.log(b);
```

Output:

```js
[1, 2, 3];
```

Because `a = []` creates a new array and only changes what `a` points to.

---

## What about this?

```js
const a = [1, 2, 3];
const b = a;

a.length = 0;

console.log(b);
```

Output:

```js
[];
```

Because the original array object was emptied.

---

# Time Complexity

| Method       | Complexity |
| ------------ | ---------- |
| `length = 0` | O(1)       |
| `arr = []`   | O(1)       |
| `splice(0)`  | O(n)       |
| `pop()` loop | O(n)       |

---

# Interview-Ready Answer

> The most common way to empty an array is `arr.length = 0`. It clears the existing array in place, preserves references, and is very efficient. Another option is `arr = []`, but that creates a new array and does not affect other variables referencing the original array. `splice(0)` can also be used when you want to mutate the original array while keeping references intact. For most cases, `arr.length = 0` is the preferred solution.

## Question 6. Difference between `Array.isArray()` and `instanceof Array`

## Short Answer

Both are used to check whether a value is an array, but **`Array.isArray()` is the recommended and more reliable approach**.

```js id="x0w4d2"
Array.isArray(value);
```

is preferred over:

```js id="z9k7mp"
value instanceof Array;
```

because `Array.isArray()` works correctly across different JavaScript execution contexts (e.g., iframes, workers).

---

# 1. Using `Array.isArray()`

```js id="b4r8nc"
const arr = [1, 2, 3];

console.log(Array.isArray(arr));
// true
```

### Examples

```js id="m7p2kt"
Array.isArray([]);
Array.isArray([1, 2]);
Array.isArray(new Array());
```

Output:

```js id="f5q1uv"
true;
true;
true;
```

### Non-arrays

```js id="a8v3lr"
Array.isArray({});
Array.isArray("hello");
Array.isArray(null);
```

Output:

```js id="k2d6zs"
false;
false;
false;
```

---

# 2. Using `instanceof Array`

```js id="w9n4bc"
const arr = [1, 2, 3];

console.log(arr instanceof Array);
// true
```

### How it works

`instanceof` checks the prototype chain.

```js id="m3t7qp"
arr instanceof Array;
```

is roughly asking:

> "Does `Array.prototype` exist in the prototype chain of `arr`?"

---

# Why `instanceof` Can Fail

The biggest interview point is **cross-realm objects**.

A realm is a separate JavaScript environment:

- iframe
- worker
- another browser window

Each realm has its own global `Array` constructor.

Example conceptually:

```js id="n1k8yd"
const iframeArray = iframe.contentWindow.Array;
```

Now:

```js id="r6c2mx"
const arr = new iframeArray();

arr instanceof Array;
```

Result:

```js id="u8j5we"
false;
```

Why?

Because:

```js id="q4z7nf"
arr.__proto__ === iframeArray.prototype;
```

not

```js id="h2m9vk"
Array.prototype;
```

So the prototype chain check fails.

---

# `Array.isArray()` Handles This Correctly

```js id="t7v1pb"
Array.isArray(arr);
```

Result:

```js id="bg6w4n"
true;
```

because it performs an internal array-brand check rather than checking constructors.

---

# Comparison

| Feature                      | `Array.isArray()` | `instanceof Array` |
| ---------------------------- | ----------------- | ------------------ |
| Detects arrays               | ✅                | ✅                 |
| Cross-frame safe             | ✅                | ❌                 |
| Based on internal array type | ✅                | ❌                 |
| Prototype-chain dependent    | ❌                | ✅                 |
| Recommended                  | ✅                | ❌                 |

---

# Tricky Interview Examples

## Example 1

```js id="t4j8qk"
const arr = [];

console.log(Array.isArray(arr));
console.log(arr instanceof Array);
```

Output:

```js id="c9p2lv"
true;
true;
```

---

## Example 2

```js id="x5n1ub"
const obj = {};

console.log(Array.isArray(obj));
console.log(obj instanceof Array);
```

Output:

```js id="g8w6mr"
false;
false;
```

---

## Example 3

```js id="v2q7pd"
const arr = [];

console.log(typeof arr);
```

Output:

```js id="r3k9mf"
"object";
```

This is why neither `typeof` nor plain object checks are sufficient.

---

# Historical Alternative

Before ES5:

```js id="p6t4xd"
Object.prototype.toString.call([]);
```

Output:

```js id="s7w2qc"
"[object Array]";
```

Used as:

```js id="e1m8vb"
Object.prototype.toString.call(value) === "[object Array]";
```

Today, `Array.isArray()` is preferred.

---

# Performance

For practical applications:

```js id="d4q7nz"
Array.isArray(value);
```

and

```js id="m9v1rk"
value instanceof Array;
```

have negligible performance differences.

Choose based on correctness, not speed.

---

# Interview-Ready Answer

> `Array.isArray()` and `instanceof Array` can both detect arrays, but `Array.isArray()` is the recommended approach. `instanceof` works by checking whether `Array.prototype` exists in an object's prototype chain, which can fail for arrays created in different JavaScript realms such as iframes or workers. `Array.isArray()` performs an internal array-type check and correctly identifies arrays across realms, making it more reliable and the standard choice in modern JavaScript.

## Question 7. What is the difference between `document.createElement()` and `innerHTML`?

## Short Answer

Both `document.createElement()` and `innerHTML` are used to create and insert DOM elements, but they work differently:

- **`document.createElement()`** creates DOM nodes programmatically.
- **`innerHTML`** inserts HTML by parsing a string.

For dynamic content, security-sensitive code, and large applications, **`createElement()` is generally preferred**. For quickly inserting static HTML templates, **`innerHTML` is often more concise**.

---

# 1. Using `document.createElement()`

Creates actual DOM elements using JavaScript.

```js
const div = document.createElement("div");
div.textContent = "Hello World";

document.body.appendChild(div);
```

### What happens?

1. Create a `<div>` element.
2. Set its content.
3. Append it to the DOM.

Result:

```html
<div>Hello World</div>
```

---

# 2. Using `innerHTML`

Creates elements by parsing an HTML string.

```js
document.body.innerHTML += `
  <div>Hello World</div>
`;
```

Browser parses the string and generates DOM nodes.

---

# Example Comparison

### createElement()

```js
const ul = document.createElement("ul");

const li = document.createElement("li");
li.textContent = "JavaScript";

ul.appendChild(li);

document.body.appendChild(ul);
```

### innerHTML

```js
document.body.innerHTML += `
  <ul>
    <li>JavaScript</li>
  </ul>
`;
```

Both produce:

```html
<ul>
  <li>JavaScript</li>
</ul>
```

---

# Security Difference (Very Important Interview Point)

## `innerHTML` Can Introduce XSS

```js
const userInput = '<img src=x onerror="alert(`Hacked`)">';

element.innerHTML = userInput;
```

If user input contains malicious HTML or JavaScript, it may execute.

This is known as:

```text
Cross-Site Scripting (XSS)
```

---

## createElement() Is Safer

```js
const div = document.createElement("div");

div.textContent = userInput;

element.appendChild(div);
```

Output:

```html
<div>&lt;img src=x onerror="alert('Hacked')"&gt;</div>
```

The content is treated as text, not executable HTML.

---

# Performance Considerations

## createElement()

```js
for (let i = 0; i < 1000; i++) {
  const li = document.createElement("li");
  li.textContent = i;
  ul.appendChild(li);
}
```

Pros:

- Fine-grained DOM control
- Efficient updates to specific nodes
- No HTML parsing overhead

---

## innerHTML

```js
let html = "";

for (let i = 0; i < 1000; i++) {
  html += `<li>${i}</li>`;
}

ul.innerHTML = html;
```

Pros:

- Often faster for inserting large chunks at once
- Simpler template creation

Cons:

- Requires HTML parsing
- Replaces existing DOM structure

---

# Event Listener Gotcha

Consider:

```js
button.addEventListener("click", () => {
  console.log("clicked");
});
```

Now:

```js
container.innerHTML += "<p>New Item</p>";
```

The browser recreates part of the DOM.

Existing references and event listeners may be lost.

---

Using `createElement()`:

```js
const p = document.createElement("p");
p.textContent = "New Item";

container.appendChild(p);
```

Existing nodes remain intact.

---

# DOM Manipulation Granularity

### createElement()

Can manipulate individual nodes:

```js
const img = document.createElement("img");
img.src = "photo.jpg";
img.alt = "Profile";
```

### innerHTML

Must construct strings:

```js
container.innerHTML = '<img src="photo.jpg" alt="Profile">';
```

Less type-safe and harder to maintain in large codebases.

---

# Modern Best Practice

For user-generated or dynamic content:

```js
const div = document.createElement("div");
div.textContent = data;
container.appendChild(div);
```

For static templates:

```js
container.innerHTML = `
  <section>
    <h2>Dashboard</h2>
    <p>Welcome!</p>
  </section>
`;
```

Many modern frameworks (React, Vue, Angular) internally create/manipulate DOM nodes rather than relying heavily on raw `innerHTML`.

---

# Comparison Table

| Feature                            | `createElement()` | `innerHTML`             |
| ---------------------------------- | ----------------- | ----------------------- |
| Creates real DOM nodes             | ✅                | ❌ (parses HTML string) |
| Safe against XSS by default        | ✅                | ❌                      |
| Supports event listeners naturally | ✅                | ❌ Can replace nodes    |
| Fine-grained updates               | ✅                | ❌                      |
| Good for large HTML templates      | ❌ More verbose   | ✅                      |
| Requires HTML parsing              | ❌                | ✅                      |
| Preferred for dynamic user content | ✅                | ❌                      |

---

# Interview-Ready Answer

> `document.createElement()` creates DOM nodes programmatically and is generally safer because it avoids HTML parsing and helps prevent XSS attacks when combined with `textContent`. `innerHTML` takes an HTML string, parses it, and inserts the resulting DOM structure. While `innerHTML` is concise and useful for rendering large static templates, it can introduce security risks and may replace existing DOM nodes, causing event listeners to be lost. For dynamic or user-generated content, `createElement()` is usually the preferred approach.

## Question 8. How to get attributes of an HTML element using JS?

## Short Answer

You can get HTML element attributes in JavaScript using:

- `getAttribute()` → most explicit and reliable
- Direct property access (e.g. `element.id`, `element.src`) → for standard attributes
- `attributes` collection → for all attributes

---

# 1. Using `getAttribute()` (Recommended)

This is the most commonly used method.

```js
const el = document.querySelector("img");

console.log(el.getAttribute("src"));
console.log(el.getAttribute("alt"));
```

### Example HTML

```html
<img id="profile" src="photo.jpg" alt="User photo" />
```

### Output

```js
"photo.jpg";
"User photo";
```

---

# 2. Using Direct Property Access

For standard attributes, you can access them directly as properties.

```js
const el = document.querySelector("img");

console.log(el.src);
console.log(el.alt);
console.log(el.id);
```

### Important difference

```js
el.getAttribute("src"); // original value in HTML
el.src; // fully resolved absolute URL
```

Example:

```js
<img src="photo.jpg" />
```

```js
el.getAttribute("src");
// "photo.jpg"

el.src;
// "https://example.com/photo.jpg"
```

👉 This is a very common interview trick.

---

# 3. Using `attributes` Property

This gives access to all attributes as a NamedNodeMap.

```js
const el = document.querySelector("img");

console.log(el.attributes);
```

### Access specific attribute

```js
console.log(el.attributes.src.value);
console.log(el.attributes.alt.value);
```

### Loop through all attributes

```js
for (let attr of el.attributes) {
  console.log(attr.name, attr.value);
}
```

Output:

```
id profile
src photo.jpg
alt User photo
```

---

# 4. Checking if an Attribute Exists

```js
const el = document.querySelector("img");

console.log(el.hasAttribute("src")); // true
console.log(el.hasAttribute("href")); // false
```

---

# 5. Getting All Data Attributes (`dataset`)

For `data-*` attributes:

```html
<div data-user-id="123" data-role="admin"></div>
```

```js
const el = document.querySelector("div");

console.log(el.dataset.userId); // "123"
console.log(el.dataset.role); // "admin"
```

👉 Converts `data-user-id` → `userId`

---

# Key Differences (Very Important for Interviews)

| Method             | Use Case               | Returns                 |
| ------------------ | ---------------------- | ----------------------- |
| `getAttribute()`   | Any attribute          | Exact HTML value        |
| `element.property` | Standard attributes    | Parsed / computed value |
| `attributes`       | Inspect all attributes | NamedNodeMap            |
| `dataset`          | data-\* attributes     | Object                  |

---

# Common Interview Traps

## 1. `getAttribute` vs property mismatch

```html
<a href="/home"></a>
```

```js
el.getAttribute("href"); // "/home"
el.href; // "https://example.com/home"
```

---

## 2. Boolean attributes

```html
<input disabled />
```

```js
el.getAttribute("disabled"); // "" (empty string)
el.disabled; // true
```

---

## 3. Non-existent attribute

```js
el.getAttribute("xyz"); // null
```

---

# Performance Note

- `element.property` is generally faster (direct access)
- `getAttribute()` is more flexible and consistent

---

# Interview-Ready Answer

> In JavaScript, HTML attributes can be accessed using `getAttribute()`, direct property access, or the `attributes` collection. `getAttribute()` returns the exact value as written in HTML and works for any attribute. Direct property access (like `element.id` or `element.src`) is faster and returns the processed or resolved value, such as absolute URLs for `src`. The `attributes` property provides a collection of all attributes on an element. For `data-*` attributes, the `dataset` API is used. In practice, `getAttribute()` is used for general-purpose access, while property access is preferred for standard attributes.

## Question 9. Difference between event bubbling and event capturing

## Short Answer

When an event occurs on an element, it travels through the DOM in three phases:

1. **Capturing Phase** (top → target)
2. **Target Phase** (on the clicked element)
3. **Bubbling Phase** (target → top)

**Event Capturing** means handling the event while it travels **down** the DOM tree.

**Event Bubbling** means handling the event while it travels **up** the DOM tree.

By default, most event listeners work in the **bubbling phase**.

---

# Example DOM Structure

```html
<div id="grandparent">
  <div id="parent">
    <button id="child">Click Me</button>
  </div>
</div>
```

DOM Tree:

```text
grandparent
    │
  parent
    │
  child
```

---

# Event Bubbling

When the button is clicked:

```js
document.getElementById("grandparent").addEventListener("click", () => {
  console.log("grandparent");
});

document.getElementById("parent").addEventListener("click", () => {
  console.log("parent");
});

document.getElementById("child").addEventListener("click", () => {
  console.log("child");
});
```

Clicking the button outputs:

```text
child
parent
grandparent
```

### Why?

The event starts at the target and bubbles upward:

```text
child
  ↑
parent
  ↑
grandparent
```

This is the default behavior.

---

# Event Capturing

To listen during the capturing phase:

```js
document
  .getElementById("grandparent")
  .addEventListener("click", () => console.log("grandparent"), true);

document
  .getElementById("parent")
  .addEventListener("click", () => console.log("parent"), true);

document
  .getElementById("child")
  .addEventListener("click", () => console.log("child"), true);
```

Output:

```text
grandparent
parent
child
```

The event travels downward:

```text
grandparent
  ↓
parent
  ↓
child
```

---

# The Three Event Phases

When clicking the button:

```text
1. Capturing Phase
   document
      ↓
   html
      ↓
   body
      ↓
   grandparent
      ↓
   parent
      ↓
   child

2. Target Phase
   child

3. Bubbling Phase
   child
      ↑
   parent
      ↑
   grandparent
      ↑
   body
      ↑
   html
      ↑
   document
```

---

# Mixing Capturing and Bubbling

```js
grandparent.addEventListener(
  "click",
  () => console.log("grandparent capture"),
  true,
);

parent.addEventListener("click", () => console.log("parent bubble"));

child.addEventListener("click", () => console.log("child"));
```

Click button:

```text
grandparent capture
child
parent bubble
```

Capturing listeners run first, then target, then bubbling listeners.

---

# Using `event.eventPhase`

```js
element.addEventListener("click", (event) => {
  console.log(event.eventPhase);
});
```

Values:

| Value | Phase     |
| ----- | --------- |
| 1     | Capturing |
| 2     | Target    |
| 3     | Bubbling  |

---

# Stopping Propagation

## `stopPropagation()`

```js
child.addEventListener("click", (e) => {
  e.stopPropagation();
  console.log("child");
});
```

Output:

```text
child
```

Parent and grandparent listeners won't execute.

---

## `stopImmediatePropagation()`

Stops:

1. Event propagation
2. Remaining listeners on the same element

```js
button.addEventListener("click", (e) => {
  e.stopImmediatePropagation();
  console.log("First");
});

button.addEventListener("click", () => {
  console.log("Second");
});
```

Output:

```text
First
```

---

# Why Bubbling Is Important

Event delegation relies on bubbling.

```html
<ul id="list">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</ul>
```

Instead of attaching listeners to every `<li>`:

```js
document.getElementById("list").addEventListener("click", (event) => {
  console.log(event.target.textContent);
});
```

The click bubbles from the `<li>` to the `<ul>`.

This is:

- More memory efficient
- Better for dynamic elements
- Commonly used in large applications

---

# Common Interview Questions

### Does every event bubble?

No.

Examples that do **not** bubble:

- `focus`
- `blur`
- `mouseenter`
- `mouseleave`

Examples that bubble:

- `click`
- `keydown`
- `keyup`
- `input`

---

### Which phase is used by default?

```js
element.addEventListener("click", handler);
```

Equivalent to:

```js
element.addEventListener("click", handler, false);
```

Default = **bubbling phase**.

---

# Comparison Table

| Feature                       | Event Capturing | Event Bubbling |
| ----------------------------- | --------------- | -------------- |
| Direction                     | Top → Target    | Target → Top   |
| Phase Number                  | 1               | 3              |
| Default Listener Phase        | ❌ No           | ✅ Yes         |
| `addEventListener(..., true)` | ✅              | ❌             |
| Event Delegation              | Rarely Used     | Commonly Used  |
| Execution Order               | First           | Last           |

---

# Interview-Ready Answer

> Event capturing and event bubbling describe how events propagate through the DOM. In the capturing phase, the event travels from the root element down to the target element. In the bubbling phase, it travels from the target element back up through its ancestors. By default, event listeners are attached in the bubbling phase. For example, when a button inside a div is clicked, the click event first reaches the button, then bubbles up to the div and other ancestors. Capturing can be enabled by passing `true` or `{ capture: true }` to `addEventListener()`. Event bubbling is particularly important because it enables event delegation, a common performance optimization technique in JavaScript applications.

## Question 10. How to get the current timestamp in JavaScript?

## Short Answer

The most common ways to get the current timestamp in JavaScript are:

```js
Date.now();
```

or

```js
new Date().getTime();
```

Both return the number of **milliseconds since January 1, 1970 (Unix Epoch)**.

---

# 1. Using `Date.now()` (Recommended)

```js
const timestamp = Date.now();

console.log(timestamp);
```

Example output:

```js
1718179200000;
```

### What it returns

```text
Milliseconds since:
January 1, 1970 00:00:00 UTC
```

This is the modern and preferred approach.

---

# 2. Using `new Date().getTime()`

```js
const timestamp = new Date().getTime();

console.log(timestamp);
```

Output:

```js
1718179200000;
```

Equivalent to:

```js
Date.now() === new Date().getTime();
```

Usually evaluates to:

```js
true;
```

---

# 3. Using `new Date().valueOf()`

```js
const timestamp = new Date().valueOf();

console.log(timestamp);
```

Also returns:

```js
1718179200000;
```

Because `valueOf()` returns the primitive timestamp value of a Date object.

---

# Milliseconds vs Seconds

JavaScript timestamps are in **milliseconds**.

### Milliseconds

```js
Date.now();
// 1718179200000
```

### Seconds

```js
Math.floor(Date.now() / 1000);
// 1718179200
```

A common interview question is:

> "Why divide by 1000?"

Because Unix timestamps are often stored in seconds in databases and APIs.

---

# Converting Timestamp to Date

```js
const timestamp = Date.now();

const date = new Date(timestamp);

console.log(date);
```

Output:

```js
Wed Jun 12 2026 ...
```

---

# Getting High-Resolution Timing

For performance measurements:

```js
performance.now();
```

Example:

```js
console.log(performance.now());
```

Output:

```js
1234.5678;
```

### Difference

| Method              | Purpose                          |
| ------------------- | -------------------------------- |
| `Date.now()`        | Current real-world timestamp     |
| `performance.now()` | Precise elapsed time measurement |

---

# Example: Measure Execution Time

```js
const start = performance.now();

for (let i = 0; i < 1000000; i++) {}

const end = performance.now();

console.log(`Took ${end - start} ms`);
```

This is more accurate than `Date.now()` for benchmarking.

---

# Common Interview Examples

### Generate Unique ID

```js
const id = Date.now();

console.log(id);
```

---

### Store Creation Time

```js
const user = {
  name: "John",
  createdAt: Date.now(),
};
```

---

### Check Elapsed Time

```js
const start = Date.now();

// some operation

const elapsed = Date.now() - start;

console.log(elapsed + " ms");
```

---

# Comparison

| Method                 | Returns                      | Recommended         |
| ---------------------- | ---------------------------- | ------------------- |
| `Date.now()`           | Timestamp in ms              | ✅ Yes              |
| `new Date().getTime()` | Timestamp in ms              | ✅ Yes              |
| `new Date().valueOf()` | Timestamp in ms              | ⚠️ Less common      |
| `performance.now()`    | High-resolution elapsed time | ✅ For benchmarking |

---

# Interview-Ready Answer

> The most common way to get the current timestamp in JavaScript is `Date.now()`, which returns the number of milliseconds elapsed since January 1, 1970 UTC (the Unix Epoch). An equivalent older approach is `new Date().getTime()`. If a timestamp in seconds is needed, divide the result by 1000 and use `Math.floor()`. For performance measurements and benchmarking, `performance.now()` is preferred because it provides higher precision timing.

## Question 11. Difference between `Date.now()` and `new Date()`

## Question 12. How to calculate the difference between two dates

## Question 13. How to convert a string to a Date object

## Question 14. What are regular expressions in JavaScript?

## Question 15. How to test a string using regex

## Question 16. Difference between `setTimeout` and `setInterval`

## Question 17. How to cancel a `setTimeout` or `setInterval`

## Question 18. How to debounce a function call

## Question 19. How to throttle a function call

## Question 20. Difference between deep copy and shallow copy of objects
