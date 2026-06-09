# Set 13

| S.No. | Question                                                                                                                                                                    |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to merge multiple objects into one](#question-1-how-to-merge-multiple-objects-into-one)                                                                                |
| 2.    | [Difference between `Object.keys()`, `Object.values()`, and `Object.entries()`](#question-2-difference-between-objectkeys-objectvalues-and-objectentries)                   |
| 3.    | [Difference between `for...of` and `forEach()` for arrays](#question-3-difference-between-forof-and-foreach-for-arrays)                                                     |
| 4.    | [How to remove duplicates from an array without using Set](#question-4-how-to-remove-duplicates-from-an-array-without-using-set)                                            |
| 5.    | [Difference between `find()` and `filter()` in arrays](#question-5-difference-between-find-and-filter-in-arrays)                                                            |
| 6.    | [Difference between `some()` and `every()` in arrays](#question-6-difference-between-some-and-every-in-arrays)                                                              |
| 7.    | [How to sort an array of objects by a key](#question-7-how-to-sort-an-array-of-objects-by-a-key)                                                                            |
| 8.    | [How to flatten nested arrays using recursion](#question-8-how-to-flatten-nested-arrays-using-recursion)                                                                    |
| 9.    | [How to implement a simple stack using arrays](#question-9-how-to-implement-a-simple-stack-using-arrays)                                                                    |
| 10.   | [How to implement a queue using arrays](#question-10-how-to-implement-a-queue-using-arrays)                                                                                 |
| 11.   | [Difference between `bind()`, `call()`, and `apply()`](#question-11-difference-between-bind-call-and-apply)                                                                 |
| 12.   | [How to create a closure and use it in a function](#question-12-how-to-create-a-closure-and-use-it-in-a-function)                                                           |
| 13.   | [How to implement a private variable using closures](#question-13-how-to-implement-a-private-variable-using-closures)                                                       |
| 14.   | [What is the difference between `this` in arrow functions and regular functions](#question-14-what-is-the-difference-between-this-in-arrow-functions-and-regular-functions) |
| 15.   | [How to implement a Singleton pattern in JavaScript](#question-15-how-to-implement-a-singleton-pattern-in-javascript)                                                       |
| 16.   | [Difference between nullish coalescing (`??`) and OR operator](#question-16-difference-between-nullish-coalescing--and-or--operator)                                        |
| 17.   | [How to use optional chaining (`?.`) in JS](#question-17-how-to-use-optional-chaining--in-js)                                                                               |
| 18.   | [Difference between `let` in block scope vs `var` in function scope](#question-18-difference-between-let-in-block-scope-vs-var-in-function-scope)                           |
| 19.   | [How to use default parameters in functions](#question-19-how-to-use-default-parameters-in-functions)                                                                       |
| 20.   | [Difference between synchronous and asynchronous code](#question-20-difference-between-synchronous-and-asynchronous-code)                                                   |

## Question 1. How to merge multiple objects into one

You can merge multiple objects into one in JavaScript using:

1. **Spread operator (`...`)** — modern and most common
2. **`Object.assign()`**
3. Custom deep merge logic (for nested objects)

Example using spread syntax:

```js
const obj1 = { name: "Alice" };
const obj2 = { age: 25 };
const obj3 = { city: "Delhi" };

const merged = { ...obj1, ...obj2, ...obj3 };

console.log(merged);
// { name: 'Alice', age: 25, city: 'Delhi' }
```

### Detailed Explanation

#### 1. Using the Spread Operator (`...`) — Recommended

The spread operator was introduced in ES6 and is the cleanest way to merge objects.

```js
const user = { name: "John" };
const details = { age: 30 };
const address = { city: "Mumbai" };

const result = {
  ...user,
  ...details,
  ...address,
};

console.log(result);
```

##### Output

```js
{
  name: "John",
  age: 30,
  city: "Mumbai"
}
```

#### 2. Property Conflict Behavior

If multiple objects contain the same key, the **last object's value overwrites earlier ones**.

```js
const obj1 = { name: "Alice", age: 20 };
const obj2 = { age: 30 };

const merged = { ...obj1, ...obj2 };

console.log(merged);
```

##### Output

```js
{
  name: "Alice",
  age: 30
}
```

Because `obj2.age` overwrites `obj1.age`.

> Object merging in JavaScript follows a "last write wins" rule.

#### 3. Using `Object.assign()`

Before spread syntax, `Object.assign()` was commonly used.

```js
const obj1 = { a: 1 };
const obj2 = { b: 2 };

const merged = Object.assign({}, obj1, obj2);

console.log(merged);
```

##### Output

```js
{ a: 1, b: 2 }
```

#### Difference Between Spread and Object.assign

| Feature        | Spread Operator | Object.assign  |
| -------------- | --------------- | -------------- |
| Syntax         | Cleaner         | Verbose        |
| Mutates target | No              | Can mutate     |
| Modern usage   | Preferred       | Older approach |

Example of mutation:

```js
const target = { a: 1 };

Object.assign(target, { b: 2 });

console.log(target);
// target is modified
```

Spread creates a new object more naturally.

#### 4. Merging Multiple Objects Dynamically

If you have an array of objects:

```js
const objects = [{ a: 1 }, { b: 2 }, { c: 3 }];

const merged = Object.assign({}, ...objects);

console.log(merged);
```

Or using `reduce()`:

```js
const merged = objects.reduce((acc, obj) => {
  return { ...acc, ...obj };
}, {});
```

### 5. Important: Shallow Merge vs Deep Merge

Both spread and `Object.assign()` perform a **shallow merge**.

#### Shallow Merge Example

```js
const obj1 = {
  user: {
    name: "Alice",
  },
};

const obj2 = {
  user: {
    age: 25,
  },
};

const merged = { ...obj1, ...obj2 };

console.log(merged);
```

##### Output

```js
{
  user: {
    age: 25;
  }
}
```

The entire `user` object gets replaced.

#### 6. Deep Merge (Nested Objects)

To preserve nested properties, you need custom logic or a utility library like Lodash.

Example:

```js
const obj1 = {
  user: {
    name: "Alice",
  },
};

const obj2 = {
  user: {
    age: 25,
  },
};

const merged = {
  user: {
    ...obj1.user,
    ...obj2.user,
  },
};

console.log(merged);
```

##### Output

```js
{
  user: {
    name: "Alice",
    age: 25
  }
}
```

#### 7. Using Lodash `merge()` for Deep Merge

```js
import _ from "lodash";

const result = _.merge(obj1, obj2);
```

##### Why Use It?

- Handles deeply nested objects
- Easier for complex structures
- Safer for production applications

### 8. Common Interview Pitfalls

#### Pitfall 1: Thinking spread creates deep copies

```js
const obj1 = {
  address: { city: "Delhi" },
};

const copy = { ...obj1 };

copy.address.city = "Mumbai";

console.log(obj1.address.city);
```

##### Output

```js
"Mumbai";
```

Nested objects are still shared references.

#### Pitfall 2: Forgetting overwrite order

```js
{
  ...obj1,
  ...obj2
}
```

`obj2` overrides `obj1`.

Order matters.

### 9. Performance Considerations

For very large objects:

- Spread syntax creates new objects repeatedly
- `Object.assign()` may be slightly faster in some engines
- Deep merging can become expensive

In most frontend/backend applications, spread syntax is perfectly fine.

#### Best Practice

For modern JavaScript:

```js
const merged = { ...obj1, ...obj2, ...obj3 };
```

Use:

- **Spread syntax** for shallow merges
- **Custom logic / Lodash merge** for deep merges

-

### Summary

- Multiple objects can be merged using:
  - Spread operator
  - `Object.assign()`

- Later properties overwrite earlier ones
- Both methods perform shallow merges
- Nested objects require deep merge handling
- Spread syntax is the modern preferred approach

## Question 2. Difference between `Object.keys()`, `Object.values()`, and `Object.entries()`

### Direct Answer

`Object.keys()`, `Object.values()`, and `Object.entries()` are methods used to extract information from an object's **own enumerable properties**:

| Method                | Returns                        |
| --------------------- | ------------------------------ |
| `Object.keys(obj)`    | Array of property names (keys) |
| `Object.values(obj)`  | Array of property values       |
| `Object.entries(obj)` | Array of `[key, value]` pairs  |

Example:

```js
const user = {
  name: "Alice",
  age: 25,
};

console.log(Object.keys(user));
console.log(Object.values(user));
console.log(Object.entries(user));
```

Output:

```js
["name", "age"][("Alice", 25)][(["name", "Alice"], ["age", 25])];
```

---

# 1. `Object.keys()`

Returns an array containing all enumerable property names.

```js
const person = {
  name: "John",
  age: 30,
  city: "Delhi",
};

console.log(Object.keys(person));
```

Output:

```js
["name", "age", "city"];
```

### Common Use Cases

- Counting properties
- Iterating over keys
- Checking if a key exists

```js
const keys = Object.keys(person);

console.log(keys.length); // 3
```

Looping:

```js
Object.keys(person).forEach((key) => {
  console.log(key, person[key]);
});
```

---

# 2. `Object.values()`

Returns an array of property values.

```js
const person = {
  name: "John",
  age: 30,
  city: "Delhi",
};

console.log(Object.values(person));
```

Output:

```js
["John", 30, "Delhi"];
```

### Common Use Cases

Summing values:

```js
const scores = {
  math: 90,
  science: 85,
  english: 95,
};

const total = Object.values(scores).reduce((sum, score) => sum + score, 0);

console.log(total);
```

Output:

```js
270;
```

---

# 3. `Object.entries()`

Returns an array of key-value pairs.

```js
const person = {
  name: "John",
  age: 30,
};

console.log(Object.entries(person));
```

Output:

```js
[
  ["name", "John"],
  ["age", 30],
];
```

### Common Use Cases

#### Iterating over key-value pairs

```js
for (const [key, value] of Object.entries(person)) {
  console.log(key, value);
}
```

Output:

```js
name John
age 30
```

---

# 4. Converting Objects to Arrays

`Object.entries()` is useful when you want array methods like `map()`, `filter()`, or `reduce()`.

```js
const prices = {
  apple: 100,
  banana: 50,
  mango: 120,
};

const expensive = Object.entries(prices).filter(([key, value]) => value > 80);

console.log(expensive);
```

Output:

```js
[
  ["apple", 100],
  ["mango", 120],
];
```

---

# 5. Converting Back to an Object

ES2019 introduced `Object.fromEntries()`.

```js
const entries = [
  ["name", "Alice"],
  ["age", 25],
];

const obj = Object.fromEntries(entries);

console.log(obj);
```

Output:

```js
{
  name: "Alice",
  age: 25
}
```

A common pattern:

```js
const user = {
  name: "Alice",
  age: 25,
};

const transformed = Object.fromEntries(
  Object.entries(user).map(([key, value]) => [key, String(value)]),
);

console.log(transformed);
```

---

# 6. Property Order

All three methods return properties in the same order:

```js
const obj = {
  a: 1,
  b: 2,
  c: 3,
};

Object.keys(obj);
Object.values(obj);
Object.entries(obj);
```

Results follow:

```js
a → b → c
```

---

# 7. What Properties Are Included?

These methods return only:

- Own properties
- Enumerable properties

They do **not** include inherited properties.

```js
const parent = {
  role: "admin",
};

const user = Object.create(parent);
user.name = "John";

console.log(Object.keys(user));
```

Output:

```js
["name"];
```

`role` is inherited, so it is excluded.

---

# 8. Difference from `for...in`

```js
for (const key in user) {
  console.log(key);
}
```

`for...in` iterates over inherited enumerable properties as well.

```js
Object.keys(user);
```

returns only own properties.

### Interview Tip

Prefer `Object.keys()`, `Object.values()`, or `Object.entries()` when you only want an object's own properties.

---

# 9. Common Interview Example

Transform object values:

```js
const prices = {
  apple: 10,
  banana: 20,
};

const doubled = Object.fromEntries(
  Object.entries(prices).map(([key, value]) => [key, value * 2]),
);

console.log(doubled);
```

Output:

```js
{
  apple: 20,
  banana: 40
}
```

This demonstrates how `Object.entries()` enables functional programming patterns with objects.

---

# Interview Summary

```js
Object.keys(obj); // ["key1", "key2"]

Object.values(obj); // ["value1", "value2"]

Object.entries(obj); // [["key1", "value1"], ["key2", "value2"]]
```

- **`Object.keys()`** → returns property names.
- **`Object.values()`** → returns property values.
- **`Object.entries()`** → returns key-value pairs.
- All return arrays of an object's **own enumerable properties**.
- `Object.entries()` is especially useful with `map()`, `filter()`, and `reduce()`.
- `Object.fromEntries()` can convert key-value pairs back into an object.

## Question 3. Difference between `for...of` and `forEach()` for arrays

### Direct Answer

- **`for...of`** is a loop statement that iterates over iterable values and supports `break`, `continue`, and `await`.
- **`forEach()`** is an array method that executes a callback for each element but **cannot be broken early** and does not work well with async control flow.

---

# 1. `for...of` (Iterable-based loop)

### Syntax

```js
for (const item of array) {
  console.log(item);
}
```

### Example

```js
const nums = [1, 2, 3];

for (const num of nums) {
  console.log(num);
}
```

### Output

```
1
2
3
```

---

## Key Characteristics

### ✅ Works with any iterable

- Arrays
- Strings
- Maps
- Sets
- NodeLists

```js
for (const char of "hello") {
  console.log(char);
}
```

---

### ✅ Supports `break` and `continue`

```js
const nums = [1, 2, 3, 4, 5];

for (const num of nums) {
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

### ✅ Supports `await` (very important in interviews)

```js
async function process(arr) {
  for (const item of arr) {
    await delay(item);
  }
}
```

---

# 2. `forEach()` (Array method)

### Syntax

```js
array.forEach((item, index, array) => {
  console.log(item);
});
```

### Example

```js
const nums = [1, 2, 3];

nums.forEach((num) => {
  console.log(num);
});
```

---

## Key Characteristics

### ❌ Cannot break or continue

```js
nums.forEach((num) => {
  if (num === 2) {
    // break ❌ not possible
  }
});
```

To simulate early exit, you'd need exceptions (not recommended).

---

### ❌ Does NOT work well with async/await

```js
async function test() {
  [1, 2, 3].forEach(async (num) => {
    await delay(num);
  });

  console.log("Done");
}
```

### Problem:

- `forEach` does not wait for async callbacks
- "Done" prints immediately

---

# 3. Key Differences (Interview Table)

| Feature          | `for...of`     | `forEach()`  |
| ---------------- | -------------- | ------------ |
| Type             | Loop statement | Array method |
| Works on         | Any iterable   | Arrays only  |
| `break/continue` | ✅ Yes         | ❌ No        |
| `await` support  | ✅ Yes         | ❌ Poor      |
| Return value     | Not applicable | `undefined`  |
| Control flow     | Flexible       | Limited      |

---

# 4. Execution Behavior Difference

## `for...of` (synchronous control flow)

```js
for (const num of [1, 2, 3]) {
  console.log(num);
}
```

Runs like a normal loop — step-by-step execution.

---

## `forEach()` (callback-based execution)

```js
[1, 2, 3].forEach((num) => {
  console.log(num);
});
```

Internally behaves like:

```js
for (let i = 0; i < arr.length; i++) {
  callback(arr[i], i, arr);
}
```

But without control over flow interruption.

---

# 5. Async Behavior (Very Important Interview Point)

## ❌ Wrong with `forEach`

```js
async function run() {
  [1, 2, 3].forEach(async (num) => {
    await delay(num);
    console.log(num);
  });

  console.log("Finished");
}
```

Output order (unexpected):

```
Finished
1
2
3
```

---

## ✅ Correct with `for...of`

```js
async function run() {
  for (const num of [1, 2, 3]) {
    await delay(num);
    console.log(num);
  }

  console.log("Finished");
}
```

Output:

```
1
2
3
Finished
```

---

# 6. Performance Considerations

- `forEach()` can be slightly optimized in engine internals for simple iteration
- `for...of` is more flexible but may be marginally slower in micro-benchmarks
- In real-world apps: **difference is negligible**

---

# 7. When to Use What?

## Use `for...of` when:

- You need `break` or `continue`
- You are working with async/await
- You are iterating over non-array iterables
- You need full control over flow

## Use `forEach()` when:

- You want simple iteration
- No early exit needed
- No async control flow required
- You prefer functional style for readability

---

# 8. Common Interview Trap

### Question:

> Why doesn’t `forEach` support `await` properly?

### Answer:

Because `forEach` does not return a promise chain and does not await the callback execution. Each callback runs independently in its own microtask, so the loop itself does not pause.

---

# 9. Bonus: Breaking out of `forEach` (Hacky way)

```js
try {
  [1, 2, 3].forEach((num) => {
    if (num === 2) throw new Error("break");
    console.log(num);
  });
} catch (e) {}
```

👉 Not recommended in production — shows why `for...of` exists.

---

# Final Interview Summary

- `for...of` = modern loop, supports control flow + async, works on iterables.
- `forEach()` = array utility method, cleaner for simple iteration but limited.
- For real-world and interview-quality code, **prefer `for...of` when control matters**, and `forEach()` for simple, synchronous iteration.

## Question 4. How to remove duplicates from an array without using Set

## Question 5. Difference between `find()` and `filter()` in arrays

## Question 6. Difference between `some()` and `every()` in arrays

## Question 7. How to sort an array of objects by a key

## Question 8. How to flatten nested arrays using recursion

## Question 9. How to implement a simple stack using arrays

## Question 10. How to implement a queue using arrays

## Question 11. Difference between `bind()`, `call()`, and `apply()`

## Question 12. How to create a closure and use it in a function

## Question 13. How to implement a private variable using closures

## Question 14. What is the difference between `this` in arrow functions and regular functions

## Question 15. How to implement a Singleton pattern in JavaScript

## Question 16. Difference between nullish coalescing (`??`) and OR (`||`) operator

## Question 17. How to use optional chaining (`?.`) in JS

## Question 18. Difference between `let` in block scope vs `var` in function scope

## Question 19. How to use default parameters in functions

## Question 20. Difference between synchronous and asynchronous code
