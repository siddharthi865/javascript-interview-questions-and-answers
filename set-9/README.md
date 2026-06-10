# Set 9

| S.No. | Question                                                                                                                                                                                                                  |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to remove duplicates from an array without using Set](#question-1-how-to-remove-duplicates-from-an-array-without-using-set)                                                                                          |
| 2.    | [Explain flatMap() method](#question-2-explain-flatmap-method)                                                                                                                                                            |
| 3.    | [How to implement a stack and queue using arrays](#question-3-how-to-implement-a-stack-and-queue-using-arrays)                                                                                                            |
| 4.    | [Difference between event.target and event.currentTarget](#question-4-difference-between-eventtarget-and-eventcurrenttarget)                                                                                              |
| 5.    | [What are the differences between classical object-oriented programming and JS prototypal inheritance?](#question-5-what-are-the-differences-between-classical-object-oriented-programming-and-js-prototypal-inheritance) |
| 6.    | [How does new keyword work internally?](#question-6-how-does-new-keyword-work-internally)                                                                                                                                 |
| 7.    | [Explain super() in classes](#question-7-explain-super-in-classes)                                                                                                                                                        |
| 8.    | [Difference between private and public fields in classes](#question-8-difference-between-private-and-public-fields-in-classes)                                                                                            |
| 9.    | [Explain the difference between static and instance properties in classes](#question-9-explain-the-difference-between-static-and-instance-properties-in-classes)                                                          |
| 10.   | [How to create a singleton object in JavaScript?](#question-10-how-to-create-a-singleton-object-in-javascript)                                                                                                            |
| 11.   | [How does the event loop handle microtasks and macrotasks?](#question-11-how-does-the-event-loop-handle-microtasks-and-macrotasks)                                                                                        |
| 12.   | [Difference between process.nextTick (Node.js) and Promise.then](#question-12-difference-between-processnexttick-nodejs-and-promisethen)                                                                                  |
| 13.   | [How to implement memoization in JavaScript?](#question-13-how-to-implement-memoization-in-javascript)                                                                                                                    |
| 14.   | [Difference between synchronous and asynchronous iterators](#question-14-difference-between-synchronous-and-asynchronous-iterators)                                                                                       |
| 15.   | [How to create a custom iterable object using [Symbol.iterator]](#question-15-how-to-create-a-custom-iterable-object-using-symboliterator)                                                                                |
| 16.   | [Difference between shallow equality vs deep equality check](#question-16-difference-between-shallow-equality-vs-deep-equality-check)                                                                                     |
| 17.   | [Explain the concept of immutability in JS](#question-17-explain-the-concept-of-immutability-in-js)                                                                                                                       |
| 18.   | [How to implement a polyfill for Array.prototype.map?](#question-18-how-to-implement-a-polyfill-for-arrayprototypemap)                                                                                                    |
| 19.   | [How to create a custom Promise implementation?](#question-19-how-to-create-a-custom-promise-implementation)                                                                                                              |
| 20.   | [Difference between Web APIs, JS engine, and Event Loop](#question-20-difference-between-web-apis-js-engine-and-event-loop)                                                                                               |

## Question 1. How to remove duplicates from an array without using Set

A common way to remove duplicates from an array **without using `Set`** is to use:

- `filter()` with `indexOf()`
- `reduce()`
- a temporary object/map for lookup
- traditional loops

The best approach depends on readability, performance, and the type of values in the array.

### 1. Using `filter()` + `indexOf()` (Most Common)

```js
const arr = [1, 2, 2, 3, 4, 4, 5];

const unique = arr.filter((item, index) => {
  return arr.indexOf(item) === index;
});

console.log(unique); // [1, 2, 3, 4, 5]
```

#### How it works

- `indexOf(item)` returns the **first occurrence** of the item.
- During filtering:
  - If the current index matches the first occurrence → keep it.
  - Otherwise → it's a duplicate.

##### Example

For array:

```js
[1, 2, 2, 3];
```

At second `2`:

```js
arr.indexOf(2) // 1
current index  // 2
```

Since `1 !== 2`, it gets removed.

### 2. Using `reduce()`

```js
const arr = [1, 2, 2, 3, 4, 4];

const unique = arr.reduce((acc, current) => {
  if (!acc.includes(current)) {
    acc.push(current);
  }
  return acc;
}, []);

console.log(unique);
```

#### Explanation

- `acc` stores unique values.
- `includes()` checks whether value already exists.
- If not present → add it.

### 3. Using a Temporary Object (Better Performance)

```js
const arr = [1, 2, 2, 3, 4, 4];

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

#### Why this is faster

- Object lookups are approximately **O(1)**.
- Total complexity becomes roughly **O(n)**.

Whereas:

```js
includes();
indexOf();
```

inside loops make complexity closer to **O(n²)**.

### 4. Using `Map` (Without `Set`)

```js
const arr = [1, 2, 2, 3];

const map = new Map();

for (const item of arr) {
  map.set(item, true);
}

const unique = [...map.keys()];

console.log(unique);
```

This preserves insertion order and works well for primitive values.

### Time Complexity

| Method              | Complexity | Notes                |
| ------------------- | ---------- | -------------------- |
| `filter + indexOf`  | O(n²)      | Easy to read         |
| `reduce + includes` | O(n²)      | Functional style     |
| Object lookup       | O(n)       | Fast for primitives  |
| `Map`               | O(n)       | Modern and efficient |

### Important Edge Cases

#### 1. Objects in Arrays

```js
const arr = [{ a: 1 }, { a: 1 }];
```

These are different references:

```js
{a:1} !== {a:1}
```

So simple duplicate removal will NOT work as expected.

Example:

```js
const unique = arr.filter((item, index) => arr.indexOf(item) === index);

console.log(unique);
// both objects remain
```

#### Removing Duplicate Objects

Usually done using a unique property:

```js
const users = [
  { id: 1, name: "A" },
  { id: 2, name: "B" },
  { id: 1, name: "A" },
];

const unique = users.filter(
  (user, index, self) => index === self.findIndex((u) => u.id === user.id),
);

console.log(unique);
```

### Modern Best Practice

In real projects, developers usually use:

```js
[...new Set(arr)];
```

because it is concise and performant.

But in interviews, companies often ask for alternatives to test understanding of:

- array iteration
- lookup logic
- time complexity
- equality behavior
- algorithmic thinking

### Common Interview Follow-up Questions

#### Why is `indexOf` solution inefficient?

Because `indexOf` scans the array every iteration.

Nested scanning:

```txt
n × n = O(n²)
```

#### Which solution is best for large arrays?

Using:

- object lookup
- `Map`
- or `Set`

because they provide near O(1) lookup time.

### Summary

> “Without using `Set`, duplicates can be removed using `filter + indexOf`, `reduce`, or lookup-based approaches like objects or `Map`. For small arrays, `filter + indexOf` is readable and common. For large datasets, lookup structures are preferable because they reduce time complexity from O(n²) to O(n). Handling objects requires custom comparison logic since object equality in JavaScript is reference-based.”

## Question 2. Explain flatMap() method

The `flatMap()` method in JavaScript is used to:

1. **map** each element of an array
2. then **flatten** the result by one level

It is essentially equivalent to:

```js
array.map(...).flat(1)
```

but more efficient and cleaner.

---

# Syntax

```js
array.flatMap(callback(currentValue, index, array));
```

---

# Basic Example

```js
const arr = [1, 2, 3];

const result = arr.flatMap((num) => [num, num * 2]);

console.log(result);
```

Output:

```js
[1, 2, 2, 4, 3, 6];
```

---

# What Happens Internally

Step 1 — `map()`:

```js
[
  [1, 2],
  [2, 4],
  [3, 6],
];
```

Step 2 — flatten one level:

```js
[1, 2, 2, 4, 3, 6];
```

---

# Equivalent Without `flatMap()`

```js
const result = arr.map((num) => [num, num * 2]).flat();
```

`flatMap()` combines both operations into one.

---

# Why `flatMap()` is Useful

It is commonly used when:

- one input item produces multiple output items
- some items should produce no output
- transforming nested arrays
- data normalization
- parsing APIs

---

# Example: Splitting Words

```js
const sentences = ["Hello world", "JavaScript is awesome"];

const words = sentences.flatMap((sentence) => sentence.split(" "));

console.log(words);
```

Output:

```js
["Hello", "world", "JavaScript", "is", "awesome"];
```

---

# Example: Removing Items While Mapping

You can return:

- empty array → remove item
- single item → keep one
- multiple items → expand

```js
const numbers = [1, 2, 3, 4];

const result = numbers.flatMap((num) => {
  if (num % 2 === 0) {
    return []; // remove even numbers
  }

  return [num, num * 10];
});

console.log(result);
```

Output:

```js
[1, 10, 3, 30];
```

This is harder to do cleanly with just `map()`.

---

# Difference Between `map()` and `flatMap()`

| Method      | Output                           |
| ----------- | -------------------------------- |
| `map()`     | Nested array possible            |
| `flatMap()` | Automatically flattens one level |

Example:

```js
const arr = [1, 2, 3];

console.log(arr.map((x) => [x * 2]));
```

Output:

```js
[[2], [4], [6]];
```

Using `flatMap()`:

```js
console.log(arr.flatMap((x) => [x * 2]));
```

Output:

```js
[2, 4, 6];
```

---

# Important Limitation: Only Flattens One Level

```js
const arr = [1, 2];

const result = arr.flatMap((x) => [[x * 2]]);

console.log(result);
```

Output:

```js
[[2], [4]];
```

Only one nesting level is flattened.

---

# `flatMap()` with Objects

```js
const users = [
  { name: "A", skills: ["JS", "React"] },
  { name: "B", skills: ["Node"] },
];

const skills = users.flatMap((user) => user.skills);

console.log(skills);
```

Output:

```js
["JS", "React", "Node"];
```

Very common in API/data transformation tasks.

---

# Sparse Arrays Behavior

`flatMap()` skips empty slots like `map()` does.

```js
const arr = [1, , 3];

const result = arr.flatMap((x) => [x]);

console.log(result);
```

Output:

```js
[1, 3];
```

---

# Time Complexity

Typically:

```txt
O(n)
```

But actual cost depends on:

- callback complexity
- flattening size
- returned array sizes

---

# Common Interview Pitfalls

## 1. Assuming Deep Flattening

Wrong expectation:

```js
flatMap(); // deep flatten
```

Reality:

```js
flatMap() === flat(1);
```

Only one level.

---

## 2. Returning Non-Arrays

```js
[1, 2, 3].flatMap((x) => x * 2);
```

Works fine.

Output:

```js
[2, 4, 6];
```

Because non-array values are appended directly.

---

# Browser Support

`flatMap()` was introduced in:

- ES2019 (ES10)

Supported in modern browsers and Node.js versions.

---

# Real-World Use Cases

## API Data Transformation

```js
const orders = [
  {
    id: 1,
    items: ["Phone", "Laptop"],
  },
  {
    id: 2,
    items: ["Tablet"],
  },
];

const allItems = orders.flatMap((order) => order.items);
```

---

## Routing/Menu Generation

```js
const menus = [
  {
    category: "Admin",
    links: ["Users", "Settings"],
  },
  {
    category: "User",
    links: ["Profile"],
  },
];

const routes = menus.flatMap((menu) => menu.links);
```

---

# Senior-Level Interview Answer

> “`flatMap()` combines mapping and flattening into a single operation. It transforms each element and then flattens the result by one level. It’s equivalent to `map().flat(1)` but cleaner and slightly more efficient. It’s especially useful when one input maps to zero, one, or many output elements, such as splitting strings, flattening nested API responses, or filtering while transforming data.”

## Question 3. How to implement a stack and queue using arrays

## Question 4. Difference between event.target and event.currentTarget

## Question 5. What are the differences between classical object-oriented programming and JS prototypal inheritance?

## Question 6. How does new keyword work internally?

## Question 7. Explain super() in classes

## Question 8. Difference between private and public fields in classes

## Question 9. Explain the difference between static and instance properties in classes

## Question 10. How to create a singleton object in JavaScript?

## Question 11. How does the event loop handle microtasks and macrotasks?

## Question 12. Difference between process.nextTick (Node.js) and Promise.then

## Question 13. How to implement memoization in JavaScript?

## Question 14. Difference between synchronous and asynchronous iterators

## Question 15. How to create a custom iterable object using [Symbol.iterator]

## Question 16. Difference between shallow equality vs deep equality check

## Question 17. Explain the concept of immutability in JS

## Question 18. How to implement a polyfill for Array.prototype.map?

## Question 19. How to create a custom Promise implementation?

## Question 20. Difference between Web APIs, JS engine, and Event Loop
