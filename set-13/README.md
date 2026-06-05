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

### Direct Answer

You can remove duplicates from an array **without using `Set`** by using:

1. `filter()` + `indexOf()`
2. `reduce()`
3. An object/hash map lookup
4. `Map`
5. Nested loops (least efficient)

The most common interview solution is:

```js
const arr = [1, 2, 2, 3, 4, 4, 5];

const unique = arr.filter((item, index) => {
  return arr.indexOf(item) === index;
});

console.log(unique);
```

Output:

```js
[1, 2, 3, 4, 5];
```

---

# 1. Using `filter()` + `indexOf()`

### Interview Favorite

```js
const arr = [1, 2, 2, 3, 4, 4, 5];

const unique = arr.filter((item, index) => arr.indexOf(item) === index);

console.log(unique);
```

### How It Works

For each element:

```js
arr.indexOf(item);
```

returns the first occurrence index.

Example:

```js
[1, 2, 2, 3];
```

For second `2`:

```js
index = 2
indexOf(2) = 1
```

Since:

```js
2 !== 1;
```

it gets removed.

---

## Time Complexity

```text
filter -> O(n)
indexOf -> O(n)

Total = O(n²)
```

### Interview Note

Good for small arrays but inefficient for large datasets.

---

# 2. Using `reduce()`

```js
const arr = [1, 2, 2, 3, 4, 4];

const unique = arr.reduce((acc, curr) => {
  if (!acc.includes(curr)) {
    acc.push(curr);
  }
  return acc;
}, []);

console.log(unique);
```

Output:

```js
[1, 2, 3, 4];
```

---

## Complexity

```text
reduce -> O(n)
includes -> O(n)

Total = O(n²)
```

---

# 3. Using an Object as a Lookup Table

### Best Interview Solution

```js
const arr = [1, 2, 2, 3, 4, 4, 5];

const seen = {};
const unique = [];

for (const item of arr) {
  if (!seen[item]) {
    seen[item] = true;
    unique.push(item);
  }
}

console.log(unique);
```

Output:

```js
[1, 2, 3, 4, 5];
```

---

## Complexity

```text
Lookup = O(1)

Overall = O(n)
```

This is usually what interviewers want when they say:

> "Remove duplicates without Set and optimize."

---

# 4. Using `Map`

```js
const arr = [1, 2, 2, 3, 4, 4];

const map = new Map();

for (const item of arr) {
  map.set(item, true);
}

const unique = [...map.keys()];

console.log(unique);
```

Output:

```js
[1, 2, 3, 4];
```

---

## Complexity

```text
O(n)
```

---

# 5. Using Nested Loops

### Traditional Approach

```js
const arr = [1, 2, 2, 3, 4, 4];

const unique = [];

for (let i = 0; i < arr.length; i++) {
  let found = false;

  for (let j = 0; j < unique.length; j++) {
    if (arr[i] === unique[j]) {
      found = true;
      break;
    }
  }

  if (!found) {
    unique.push(arr[i]);
  }
}

console.log(unique);
```

Output:

```js
[1, 2, 3, 4];
```

---

## Complexity

```text
O(n²)
```

Usually asked in DSA-style interviews.

---

# 6. Removing Duplicate Objects

### Problem

```js
const users = [
  { id: 1, name: "John" },
  { id: 1, name: "John" },
  { id: 2, name: "Alice" },
];
```

`indexOf()` won't work because objects are compared by reference.

---

### Solution Using Lookup

```js
const seen = {};
const unique = [];

for (const user of users) {
  if (!seen[user.id]) {
    seen[user.id] = true;
    unique.push(user);
  }
}

console.log(unique);
```

Output:

```js
[
  { id: 1, name: "John" },
  { id: 2, name: "Alice" },
];
```

---

# Common Interview Pitfalls

## Pitfall 1: `indexOf()` with Objects

```js
const arr = [{ a: 1 }, { a: 1 }];

console.log(arr.indexOf({ a: 1 }));
```

Output:

```js
-1;
```

Because objects are reference types.

---

## Pitfall 2: Object Lookup with Falsy Values

```js
if (!seen[item])
```

Can be problematic if keys are `"0"`, `false`, etc.

Safer:

```js
if (!(item in seen))
```

Example:

```js
const seen = {};

for (const item of arr) {
  if (!(item in seen)) {
    seen[item] = true;
    unique.push(item);
  }
}
```

---

# Best Practice (Interview Answer)

### Simple Solution

```js
const unique = arr.filter((item, index) => arr.indexOf(item) === index);
```

### Optimized Solution (No Set)

```js
const seen = Object.create(null);
const unique = [];

for (const item of arr) {
  if (!(item in seen)) {
    seen[item] = true;
    unique.push(item);
  }
}
```

### Complexity

| Approach            | Time  | Space |
| ------------------- | ----- | ----- |
| `filter + indexOf`  | O(n²) | O(n)  |
| `reduce + includes` | O(n²) | O(n)  |
| Object lookup       | O(n)  | O(n)  |
| `Map` lookup        | O(n)  | O(n)  |
| Nested loops        | O(n²) | O(n)  |

---

# Interview Summary

If asked:

> **"Remove duplicates from an array without using Set and make it efficient."**

Use a hash lookup:

```js
const seen = Object.create(null);

const unique = arr.filter((item) => {
  if (item in seen) return false;
  seen[item] = true;
  return true;
});
```

- No `Set`
- Preserves order
- Time Complexity: **O(n)**
- Space Complexity: **O(n)**
- Works well in real-world interviews and production code.

## Question 5. Difference between `find()` and `filter()` in arrays

### Direct Answer

Both `find()` and `filter()` search an array based on a condition, but they return different results:

| Method     | Returns                                  |
| ---------- | ---------------------------------------- |
| `find()`   | The **first matching element**           |
| `filter()` | **All matching elements** as a new array |

Example:

```js
const numbers = [1, 2, 3, 4, 5, 6];

console.log(numbers.find((num) => num > 3));
console.log(numbers.filter((num) => num > 3));
```

Output:

```js
(4)[(4, 5, 6)];
```

---

# 1. `find()`

`find()` returns the **first element** that satisfies the condition.

### Syntax

```js
array.find((element, index, array) => {
  return condition;
});
```

### Example

```js
const users = [
  { id: 1, name: "John" },
  { id: 2, name: "Alice" },
  { id: 3, name: "Bob" },
];

const user = users.find((u) => u.id === 2);

console.log(user);
```

Output:

```js
{
  id: 2,
  name: "Alice"
}
```

---

## If No Match Exists

```js
const result = [1, 2, 3].find((x) => x > 10);

console.log(result);
```

Output:

```js
undefined;
```

---

## Important Behavior

`find()` **stops immediately** after finding the first match.

```js
const arr = [1, 2, 3, 4, 5];

const result = arr.find((num) => {
  console.log(num);
  return num > 3;
});
```

Output:

```js
1;
2;
3;
4;
```

Stops after `4`.

---

# 2. `filter()`

`filter()` returns **all elements** matching the condition.

### Syntax

```js
array.filter((element, index, array) => {
  return condition;
});
```

### Example

```js
const numbers = [1, 2, 3, 4, 5, 6];

const result = numbers.filter((num) => num > 3);

console.log(result);
```

Output:

```js
[4, 5, 6];
```

---

## If No Match Exists

```js
const result = [1, 2, 3].filter((x) => x > 10);

console.log(result);
```

Output:

```js
[];
```

Returns an empty array, never `undefined`.

---

# 3. Key Difference

### `find()`

```js
const result = [10, 20, 30, 40].find((x) => x > 15);

console.log(result);
```

Output:

```js
20;
```

---

### `filter()`

```js
const result = [10, 20, 30, 40].filter((x) => x > 15);

console.log(result);
```

Output:

```js
[20, 30, 40];
```

---

# 4. Performance Difference

## `find()`

Stops at the first match.

```text
Best case: O(1)
Worst case: O(n)
```

Example:

```js
const result = arr.find((x) => x === target);
```

As soon as target is found, iteration ends.

---

## `filter()`

Always traverses the entire array.

```text
Always O(n)
```

Even if the first element matches.

```js
const result = arr.filter((x) => x === target);
```

The whole array must be checked.

---

# 5. Practical Example

### Find One User

```js
const user = users.find((user) => user.id === 5);
```

Because IDs are unique, use `find()`.

---

### Find All Admins

```js
const admins = users.filter((user) => user.role === "admin");
```

Because multiple users may match.

---

# 6. Return Types

```js
const arr = [1, 2, 3];
```

### `find()`

```js
arr.find((x) => x > 1);
```

Returns:

```js
2;
```

Type:

```js
number | undefined;
```

---

### `filter()`

```js
arr.filter((x) => x > 1);
```

Returns:

```js
[2, 3];
```

Type:

```js
number[]
```

---

# 7. Common Interview Trap

### Question

```js
const result = [1, 2, 3].find((x) => x > 10);

console.log(result);
```

Output:

```js
undefined;
```

Many candidates incorrectly answer:

```js
[];
```

That's `filter()`, not `find()`.

---

# 8. Related Methods

### `findIndex()`

Returns the index of the first match.

```js
const index = [10, 20, 30].findIndex((x) => x === 20);

console.log(index);
```

Output:

```js
1;
```

---

### `some()`

Checks whether at least one match exists.

```js
const exists = [1, 2, 3].some((x) => x > 2);

console.log(exists);
```

Output:

```js
true;
```

---

### `every()`

Checks whether all elements match.

```js
const result = [2, 4, 6].every((x) => x % 2 === 0);

console.log(result);
```

Output:

```js
true;
```

---

# Interview Summary

| Feature         | `find()`               | `filter()`           |
| --------------- | ---------------------- | -------------------- |
| Return value    | First matching element | Array of all matches |
| No match        | `undefined`            | `[]`                 |
| Stops early     | ✅ Yes                 | ❌ No                |
| Use case        | Find one item          | Find many items      |
| Time Complexity | O(1)–O(n)              | O(n)                 |
| Return type     | Element or `undefined` | Array                |

### Rule of Thumb

- Use **`find()`** when you need **one matching element**.
- Use **`filter()`** when you need **all matching elements**.
- `find()` can be more efficient because it stops as soon as a match is found.

## Question 6. Difference between `some()` and `every()` in arrays

### Direct Answer

Both `some()` and `every()` test array elements against a condition, but they answer different questions:

- **`some()`** → "Does **at least one** element satisfy the condition?"
- **`every()`** → "Do **all** elements satisfy the condition?"

Example:

```js
const numbers = [2, 4, 6, 8];

console.log(numbers.some((num) => num > 5)); // true
console.log(numbers.every((num) => num > 5)); // false
```

---

# 1. `some()`

Returns `true` if **at least one** element passes the test.

### Syntax

```js
array.some((element, index, array) => {
  return condition;
});
```

### Example

```js
const numbers = [1, 3, 5, 8];

const hasEven = numbers.some((num) => num % 2 === 0);

console.log(hasEven);
```

Output:

```js
true;
```

Because `8` is even.

---

## How `some()` Works

Think of it as a logical **OR (`||`)** across all elements.

```js
[1, 3, 5, 8];
```

Equivalent idea:

```js
false || false || false || true;
```

Result:

```js
true;
```

---

## Short-Circuit Behavior

`some()` stops as soon as it finds a match.

```js
const nums = [1, 2, 3, 4];

nums.some((num) => {
  console.log(num);
  return num === 2;
});
```

Output:

```js
1;
2;
```

Iteration stops after finding `2`.

---

# 2. `every()`

Returns `true` only if **all** elements pass the test.

### Syntax

```js
array.every((element, index, array) => {
  return condition;
});
```

### Example

```js
const numbers = [2, 4, 6, 8];

const allEven = numbers.every((num) => num % 2 === 0);

console.log(allEven);
```

Output:

```js
true;
```

---

## How `every()` Works

Think of it as a logical **AND (`&&`)** across all elements.

```js
[2, 4, 6, 8];
```

Equivalent idea:

```js
true && true && true && true;
```

Result:

```js
true;
```

---

## Short-Circuit Behavior

`every()` stops as soon as it finds a failure.

```js
const nums = [2, 4, 5, 8];

nums.every((num) => {
  console.log(num);
  return num % 2 === 0;
});
```

Output:

```js
2;
4;
5;
```

Stops at `5` because the condition fails.

---

# 3. Key Differences

| Feature              | `some()`           | `every()`               |     |            |
| -------------------- | ------------------ | ----------------------- | --- | ---------- |
| Meaning              | At least one match | All must match          |     |            |
| Logical equivalent   | OR (`              |                         | `)  | AND (`&&`) |
| Returns `true` when  | One element passes | All elements pass       |     |            |
| Stops early          | First success      | First failure           |     |            |
| No matching elements | `false`            | `true` only if all pass |     |            |

---

# 4. Empty Array Behavior (Common Interview Question)

### `some()` on Empty Array

```js
console.log([].some((x) => x > 0));
```

Output:

```js
false;
```

No element satisfies the condition.

---

### `every()` on Empty Array

```js
console.log([].every((x) => x > 0));
```

Output:

```js
true;
```

This surprises many developers.

### Why?

Mathematically, this is called **vacuous truth**.

There is no element that violates the condition.

---

# 5. Practical Examples

## Check if User Has Any Admin Role

```js
const roles = ["user", "editor", "admin"];

const isAdmin = roles.some((role) => role === "admin");

console.log(isAdmin);
```

Output:

```js
true;
```

---

## Check if All Users Are Active

```js
const users = [{ active: true }, { active: true }, { active: true }];

const allActive = users.every((user) => user.active);

console.log(allActive);
```

Output:

```js
true;
```

---

# 6. Performance

Both methods are:

```text
Worst Case: O(n)
```

However:

### `some()`

Best case:

```text
O(1)
```

if the first element matches.

### `every()`

Best case:

```text
O(1)
```

if the first element fails.

Because both short-circuit.

---

# 7. Common Interview Patterns

## Validation Using `every()`

```js
const ages = [20, 25, 30];

const valid = ages.every((age) => age >= 18);

console.log(valid);
```

Output:

```js
true;
```

---

## Permission Check Using `some()`

```js
const permissions = ["read", "write", "delete"];

const canDelete = permissions.some((p) => p === "delete");

console.log(canDelete);
```

Output:

```js
true;
```

---

# 8. Common Interview Trap

### Question

```js
console.log([].every(() => false));
```

Output:

```js
true;
```

Many candidates answer:

```js
false;
```

Incorrect.

---

### Question

```js
console.log([].some(() => true));
```

Output:

```js
false;
```

Because there are no elements to satisfy the condition.

---

# Relationship with Other Array Methods

```js
find(); // Returns first matching element
filter(); // Returns all matching elements
some(); // Returns true if any match
every(); // Returns true if all match
findIndex(); // Returns index of first match
```

---

# Interview Summary

```js
array.some(predicate);
```

- Returns `true` if **at least one** element matches.
- Behaves like logical **OR**.
- Stops on first success.

```js
array.every(predicate);
```

- Returns `true` if **all** elements match.
- Behaves like logical **AND**.
- Stops on first failure.

### Quick Memory Trick

```text
some()  → ANY?
every() → ALL?
```

- **`some()` = "Is there at least one?"**
- **`every()` = "Do all satisfy the condition?"**

## Question 7. How to sort an array of objects by a key

### Direct Answer

You can sort an array of objects by a specific key using `Array.prototype.sort()` and a comparison function.

```js
const users = [
  { name: "John", age: 30 },
  { name: "Alice", age: 25 },
  { name: "Bob", age: 35 },
];

users.sort((a, b) => a.age - b.age);

console.log(users);
```

Output:

```js
[
  { name: "Alice", age: 25 },
  { name: "John", age: 30 },
  { name: "Bob", age: 35 },
];
```

---

# How `sort()` Works

The `sort()` method expects a comparator function:

```js
array.sort((a, b) => {
  // negative => a before b
  // positive => b before a
  // 0 => keep order
});
```

For numbers:

```js
a.age - b.age;
```

- Negative → `a` comes first
- Positive → `b` comes first
- Zero → order unchanged

---

# 1. Sort by Numeric Property

### Ascending Order

```js
const products = [
  { name: "Laptop", price: 1000 },
  { name: "Phone", price: 500 },
  { name: "Tablet", price: 750 },
];

products.sort((a, b) => a.price - b.price);
```

Output:

```js
[
  { name: "Phone", price: 500 },
  { name: "Tablet", price: 750 },
  { name: "Laptop", price: 1000 },
];
```

---

### Descending Order

```js
products.sort((a, b) => b.price - a.price);
```

Output:

```js
[
  { name: "Laptop", price: 1000 },
  { name: "Tablet", price: 750 },
  { name: "Phone", price: 500 },
];
```

---

# 2. Sort by String Property

For strings, use `localeCompare()`.

```js
const users = [{ name: "John" }, { name: "Alice" }, { name: "Bob" }];

users.sort((a, b) => a.name.localeCompare(b.name));
```

Output:

```js
[{ name: "Alice" }, { name: "Bob" }, { name: "John" }];
```

---

### Descending String Sort

```js
users.sort((a, b) => b.name.localeCompare(a.name));
```

---

# Why Not Use `>` and `<` for Strings?

Instead of:

```js
users.sort((a, b) => {
  if (a.name > b.name) return 1;
  if (a.name < b.name) return -1;
  return 0;
});
```

Prefer:

```js
a.name.localeCompare(b.name);
```

Benefits:

- Cleaner
- Handles international characters
- Better Unicode support

---

# 3. Sort by Date Property

```js
const events = [
  { title: "Event A", date: "2025-03-01" },
  { title: "Event B", date: "2025-01-15" },
  { title: "Event C", date: "2025-02-10" },
];

events.sort((a, b) => new Date(a.date) - new Date(b.date));
```

Output:

```js
[
  { title: "Event B", date: "2025-01-15" },
  { title: "Event C", date: "2025-02-10" },
  { title: "Event A", date: "2025-03-01" },
];
```

---

# 4. Sort by Multiple Keys

A common interview question.

### Example: Age then Name

```js
const users = [
  { name: "John", age: 25 },
  { name: "Alice", age: 25 },
  { name: "Bob", age: 30 },
];

users.sort((a, b) => {
  if (a.age !== b.age) {
    return a.age - b.age;
  }

  return a.name.localeCompare(b.name);
});
```

Output:

```js
[
  { name: "Alice", age: 25 },
  { name: "John", age: 25 },
  { name: "Bob", age: 30 },
];
```

---

# 5. Sorting Nested Object Properties

```js
const users = [
  { profile: { age: 30 } },
  { profile: { age: 20 } },
  { profile: { age: 25 } },
];

users.sort((a, b) => a.profile.age - b.profile.age);
```

Output:

```js
[{ profile: { age: 20 } }, { profile: { age: 25 } }, { profile: { age: 30 } }];
```

---

# 6. Handling Missing Values

Suppose some objects don't have the key.

```js
const users = [
  { name: "John", age: 30 },
  { name: "Alice" },
  { name: "Bob", age: 25 },
];
```

Safe sorting:

```js
users.sort((a, b) => {
  return (a.age ?? Infinity) - (b.age ?? Infinity);
});
```

Output:

```js
[{ name: "Bob", age: 25 }, { name: "John", age: 30 }, { name: "Alice" }];
```

---

# 7. Avoid Mutating the Original Array

### Important Interview Point

`sort()` mutates the original array.

```js
const arr = [3, 1, 2];

arr.sort();

console.log(arr);
```

Output:

```js
[1, 2, 3];
```

Original array changed.

---

### Immutable Sorting

```js
const sortedUsers = [...users].sort((a, b) => a.age - b.age);
```

or (ES2023):

```js
const sortedUsers = users.toSorted((a, b) => a.age - b.age);
```

`toSorted()` returns a new sorted array without mutation.

---

# Common Interview Pitfalls

## Pitfall 1: Sorting Numbers Without Comparator

```js
[10, 2, 5].sort();
```

Output:

```js
[10, 2, 5];
```

Because values are converted to strings and sorted lexicographically.

Correct:

```js
[10, 2, 5].sort((a, b) => a - b);
```

Output:

```js
[2, 5, 10];
```

---

## Pitfall 2: Forgetting That `sort()` Mutates

```js
const original = users;

users.sort(...);
```

Now both references point to the sorted array.

---

# Reusable Utility Function

```js
function sortBy(arr, key) {
  return [...arr].sort((a, b) => {
    if (typeof a[key] === "string") {
      return a[key].localeCompare(b[key]);
    }

    return a[key] - b[key];
  });
}
```

Usage:

```js
const sorted = sortBy(users, "age");
```

---

# Interview Summary

### Numeric Sort

```js
users.sort((a, b) => a.age - b.age);
```

### Descending Numeric Sort

```js
users.sort((a, b) => b.age - a.age);
```

### String Sort

```js
users.sort((a, b) => a.name.localeCompare(b.name));
```

### Multiple Keys

```js
users.sort((a, b) => {
  if (a.age !== b.age) {
    return a.age - b.age;
  }
  return a.name.localeCompare(b.name);
});
```

### Key Points

- `sort()` mutates the original array.
- Use `localeCompare()` for strings.
- Use subtraction for numeric keys.
- Use `toSorted()` or `[...arr].sort()` for immutable sorting.
- Multi-key sorting is a common senior-level interview topic.

## Question 8. How to flatten nested arrays using recursion

### Direct Answer

You can flatten a nested array using **recursion** by iterating through each element and recursively flattening it if it is an array.

```js id="flat1"
function flattenArray(arr) {
  let result = [];

  for (const item of arr) {
    if (Array.isArray(item)) {
      result = result.concat(flattenArray(item));
    } else {
      result.push(item);
    }
  }

  return result;
}

console.log(flattenArray([1, [2, [3, [4]], 5]]));
```

Output:

```js id="flat2"
[1, 2, 3, 4, 5];
```

---

# 1. Understanding the Problem

A nested array can have arbitrary depth:

```js id="flat3"
[1, [2, [3, [4]]]];
```

Goal → convert it into:

```js id="flat4"
[1, 2, 3, 4];
```

This is a classic **recursion + tree traversal problem**.

---

# 2. Recursive Idea

At each step:

- If element is **not an array** → push it to result
- If element **is an array** → recursively flatten it

Think of it like traversing a tree:

```
[1, [2, [3]]]
     └── deeper arrays
```

---

# 3. Step-by-Step Execution

Input:

```js id="flat5"
[1, [2, [3, 4]], 5];
```

### Breakdown:

- 1 → push
- [2, [3, 4]] → recurse
  - 2 → push
  - [3, 4] → recurse
    - 3 → push
    - 4 → push

- 5 → push

Final:

```js id="flat6"
[1, 2, 3, 4, 5];
```

---

# 4. Cleaner Version Using Spread Operator

You can also write it more functionally:

```js id="flat7"
function flattenArray(arr) {
  return arr.reduce((acc, item) => {
    return acc.concat(Array.isArray(item) ? flattenArray(item) : item);
  }, []);
}
```

---

# 5. Alternative: Using `for...of` (More Readable for Interviews)

```js id="flat8"
function flattenArray(arr) {
  const result = [];

  for (const item of arr) {
    if (Array.isArray(item)) {
      result.push(...flattenArray(item));
    } else {
      result.push(item);
    }
  }

  return result;
}
```

---

# 6. Complexity Analysis

### Time Complexity

```text id="flat9"
O(n)
```

Every element is visited once.

### Space Complexity

```text id="flat10"
O(n) + recursion stack
```

Worst-case recursion depth = array depth.

---

# 7. Edge Cases

## Empty array

```js id="flat11"
flattenArray([]);
```

Output:

```js id="flat12"
[];
```

---

## Already flat array

```js id="flat13"
flattenArray([1, 2, 3]);
```

Output:

```js id="flat14"
[1, 2, 3];
```

---

## Deep nesting

```js id="flat15"
flattenArray([1, [2, [3, [4, [5]]]]]);
```

Works regardless of depth because recursion handles it dynamically.

---

# 8. Common Interview Pitfalls

## Pitfall 1: Forgetting to return recursive result

```js id="flat16"
result.concat(flattenArray(item)); // ❌ not stored
```

Correct:

```js id="flat17"
result = result.concat(flattenArray(item));
```

---

## Pitfall 2: Mutating input accidentally

Always create a new array; avoid modifying original input unless asked.

---

## Pitfall 3: Using shallow flatten logic

Wrong approach:

```js id="flat18"
arr.flat(); // only depth 1
```

Does not handle deep nesting unless:

```js id="flat19"
arr.flat(Infinity);
```

But recursion is often preferred in interviews.

---

# 9. Recursive Pattern Insight (Important Interview Concept)

This problem is a classic example of:

> **Divide and conquer + DFS traversal**

Each array is treated like a subtree.

---

# 10. Bonus: Iterative Stack Version (Advanced Alternative)

Sometimes asked in senior interviews.

```js id="flat20"
function flattenArray(arr) {
  const stack = [...arr];
  const result = [];

  while (stack.length) {
    const item = stack.pop();

    if (Array.isArray(item)) {
      stack.push(...item);
    } else {
      result.push(item);
    }
  }

  return result.reverse();
}
```

---

# Interview Summary

### Recursive solution (best conceptual answer)

```js id="flat21"
function flattenArray(arr) {
  const result = [];

  for (const item of arr) {
    if (Array.isArray(item)) {
      result.push(...flattenArray(item));
    } else {
      result.push(item);
    }
  }

  return result;
}
```

---

### Key Points

- Use recursion when depth is unknown
- Base case: non-array element
- Recursive case: flatten nested arrays
- Time complexity: **O(n)**
- Space complexity: **O(n)** (plus recursion stack)
- Alternative: `Array.flat(Infinity)` (built-in but less interview-revealing)

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
