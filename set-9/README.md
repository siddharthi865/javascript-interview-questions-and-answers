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

## Short Answer (Interview-ready)

You can implement both **Stack** and **Queue** using JavaScript arrays because arrays already support the required operations:

- **Stack (LIFO)** → use `push()` and `pop()`
- **Queue (FIFO)** → use `push()` and `shift()` (or better `push()` + `head pointer` for performance)

---

# 1. Stack Implementation Using Array

## Concept

A **Stack** follows:

> Last In First Out (LIFO)

Think: browser back button, undo feature, function call stack.

---

## Using Array Methods

### Implementation

```js id="stk1"
class Stack {
  constructor() {
    this.items = [];
  }

  push(element) {
    this.items.push(element);
  }

  pop() {
    if (this.isEmpty()) return "Stack Underflow";
    return this.items.pop();
  }

  peek() {
    return this.items[this.items.length - 1];
  }

  isEmpty() {
    return this.items.length === 0;
  }

  size() {
    return this.items.length;
  }
}
```

---

## Example Usage

```js id="stk2"
const stack = new Stack();

stack.push(10);
stack.push(20);
stack.push(30);

console.log(stack.pop()); // 30
console.log(stack.peek()); // 20
```

---

## Complexity

| Operation | Time Complexity |
| --------- | --------------- |
| push      | O(1)            |
| pop       | O(1)            |
| peek      | O(1)            |

---

## Why Stack is Efficient in Arrays

Because `push` and `pop` operate at the **end of the array**, no shifting is needed.

---

# 2. Queue Implementation Using Array

## Concept

A **Queue** follows:

> First In First Out (FIFO)

Think: printer queue, ticket line, task scheduling.

---

## Naive Implementation (Not Optimal)

```js id="q1"
class Queue {
  constructor() {
    this.items = [];
  }

  enqueue(element) {
    this.items.push(element);
  }

  dequeue() {
    if (this.isEmpty()) return "Queue Underflow";
    return this.items.shift();
  }

  front() {
    return this.items[0];
  }

  isEmpty() {
    return this.items.length === 0;
  }

  size() {
    return this.items.length;
  }
}
```

---

## Example Usage

```js id="q2"
const queue = new Queue();

queue.enqueue(10);
queue.enqueue(20);
queue.enqueue(30);

console.log(queue.dequeue()); // 10
console.log(queue.front()); // 20
```

---

## Problem with This Approach

### `shift()` is expensive

```txt id="q3"
shift() → O(n)
```

Because every element is re-indexed after removal.

So repeated dequeue operations become inefficient.

---

# 3. Optimized Queue (Best Interview Answer)

Instead of shifting, use a **head pointer**.

## Implementation

```js id="q4"
class Queue {
  constructor() {
    this.items = [];
    this.head = 0;
  }

  enqueue(element) {
    this.items.push(element);
  }

  dequeue() {
    if (this.isEmpty()) return "Queue Underflow";

    const item = this.items[this.head];
    this.head++;

    // Optional cleanup to avoid memory leak
    if (this.head * 2 >= this.items.length) {
      this.items = this.items.slice(this.head);
      this.head = 0;
    }

    return item;
  }

  front() {
    return this.items[this.head];
  }

  isEmpty() {
    return this.items.length - this.head === 0;
  }

  size() {
    return this.items.length - this.head;
  }
}
```

---

## Complexity (Optimized)

| Operation | Time Complexity |
| --------- | --------------- |
| enqueue   | O(1)            |
| dequeue   | O(1) amortized  |
| front     | O(1)            |

---

# 4. Stack vs Queue (Quick Comparison)

| Feature | Stack           | Queue                      |
| ------- | --------------- | -------------------------- |
| Order   | LIFO            | FIFO                       |
| Insert  | push            | enqueue (push)             |
| Remove  | pop             | shift / head pointer       |
| Example | undo, recursion | task scheduling, buffering |

---

# 5. Alternative: Using Linked List (Advanced Mention)

In real systems, stacks and queues are often implemented using linked lists for guaranteed O(1) operations without resizing issues.

---

# 6. Common Interview Pitfalls

## ❌ Using shift in high-performance systems

```js id="q5"
arr.shift(); // O(n) - avoid in heavy queues
```

---

## ❌ Confusing stack and queue order

Stack:

```txt id="q6"
1 → 2 → 3 → pop → 3
```

Queue:

```txt id="q7"
1 → 2 → 3 → dequeue → 1
```

---

## ❌ Not handling empty structure

Always guard:

```js id="q8"
if (this.isEmpty()) return null;
```

---

# 7. Senior-Level Interview Answer

> “A stack can be implemented using an array with `push` and `pop`, giving O(1) operations since both act on the end of the array. A queue can be implemented using `push` and `shift`, but `shift` is O(n), so for better performance we use a head pointer or a linked list approach. In production systems, queues are often optimized to avoid array re-indexing overhead and ensure amortized O(1) dequeue operations.”

## Question 4. Difference between event.target and event.currentTarget

## Short Answer (Interview-ready)

- **`event.target`** → the _actual element_ that triggered the event.
- **`event.currentTarget`** → the _element to which the event listener is attached_.

They are often different in **event bubbling scenarios**.

---

# 🧠 Detailed Explanation (FAANG-level)

In JavaScript event handling, events travel in two main phases:

1. **Capturing phase**
2. **Bubbling phase (most commonly used)**

During bubbling, an event triggered on a child element propagates up to its parents.

---

## 🔹 `event.target` (Origin of event)

👉 The element where the event **actually happened**

Example:

- Click on a button inside a div → button is the target

---

## 🔹 `event.currentTarget` (Handler owner)

👉 The element that the **event listener is attached to**

Even if the event originated from a child, this remains fixed.

---

# ⚡ Example (Very Important for Interviews)

```html
<div id="parent">
  <button id="child">Click me</button>
</div>
```

```js id="evt1"
document.getElementById("parent").addEventListener("click", function (event) {
  console.log("target:", event.target.id);
  console.log("currentTarget:", event.currentTarget.id);
});
```

---

## 👉 If you click the button:

### Output:

```
target: child
currentTarget: parent
```

---

# 🧠 Why this happens

- Click happened on **button** → so `target = button`
- Listener is on **div#parent** → so `currentTarget = parent`

---

# 🔥 If you click the parent div directly

```
target: parent
currentTarget: parent
```

Now both are same.

---

# 📌 Key Difference Table

| Feature                   | event.target           | event.currentTarget        |
| ------------------------- | ---------------------- | -------------------------- |
| Meaning                   | Where event originated | Where listener is attached |
| Changes during bubbling   | ❌ No                  | ❌ No                      |
| Depends on click position | ✅ Yes                 | ❌ No                      |
| Common use                | Delegation logic       | Stable reference           |

---

# 🧠 Real-world Use Case: Event Delegation

This is where interviewers LOVE this question.

```js id="evt2"
document.getElementById("list").addEventListener("click", function (e) {
  if (e.target.tagName === "LI") {
    console.log("Clicked item:", e.target.textContent);
  }
});
```

### Why `target` is used:

Because we want the actual clicked `<li>`, not the parent `<ul>`.

---

# ⚠️ Common Pitfalls

## ❌ Using currentTarget for delegation

```js id="evt3"
e.currentTarget.tagName; // always UL in this case
```

This breaks logic for child detection.

---

## ❌ Assuming both are same

They are only same when:

- event is triggered directly on the element with listener

---

## ❌ Arrow function confusion

```js id="evt4"
element.addEventListener("click", (e) => {
  console.log(this); // ❌ not currentTarget
});
```

Arrow functions do NOT bind `this`.

Use:

```js id="evt5"
function (e) {
  console.log(this === e.currentTarget); // true
}
```

---

# 🔥 Bonus: Relationship with `this`

In non-arrow functions inside event handlers:

```js id="evt6"
element.addEventListener("click", function (e) {
  console.log(this === e.currentTarget); // true
});
```

So:

| Concept  | Value                   |
| -------- | ----------------------- |
| `this`   | same as `currentTarget` |
| `target` | actual clicked element  |

---

# 🧠 Senior-Level Interview Answer

> “`event.target` refers to the element that actually triggered the event, while `event.currentTarget` refers to the element on which the event listener is registered. In event bubbling scenarios, these often differ. This distinction is crucial for event delegation patterns, where we attach a single listener on a parent but handle events from dynamically generated child elements using `event.target`.”

## Question 5. What are the differences between classical object-oriented programming and JS prototypal inheritance?

## Short Answer (Interview-ready)

**Classical OOP** (Java, C++, C#) is based on **classes creating objects**, where inheritance occurs between classes. **JavaScript prototypal inheritance** is based on **objects inheriting directly from other objects** through the prototype chain.

Modern JavaScript has `class` syntax, but under the hood it still uses **prototypes**, not classical inheritance.

---

# 🧠 Detailed Explanation (Senior Interview Level)

The fundamental difference is:

### Classical OOP

```txt
Class → Object
```

### JavaScript

```txt
Object → Object
```

In JavaScript, objects can inherit directly from other objects without requiring a class.

---

# 🏛️ 1. Classical OOP (Java, C++, C#)

Classes act as blueprints.

```java
class Animal {
    void speak() {
        System.out.println("Animal");
    }
}

class Dog extends Animal {
}
```

Usage:

```java
Dog d = new Dog();
d.speak();
```

### Characteristics

- Classes are first-class language constructs
- Objects are instances of classes
- Inheritance happens between classes
- Usually strongly typed

---

# ⚡ 2. JavaScript Prototypal Inheritance

Objects inherit from other objects.

```js
const animal = {
  speak() {
    console.log("Animal");
  },
};

const dog = Object.create(animal);

dog.speak();
```

### Prototype Chain

```txt
dog
 ↓
animal
 ↓
Object.prototype
 ↓
null
```

When JavaScript cannot find a property on an object, it searches up the prototype chain.

---

# 🔥 Example: Property Lookup

```js
const animal = {
  type: "animal",
};

const dog = Object.create(animal);

console.log(dog.type);
```

Output:

```txt
animal
```

JavaScript finds `type` on the prototype object.

---

# ⚖️ Key Differences

| Feature              | Classical OOP     | JavaScript Prototypal  |
| -------------------- | ----------------- | ---------------------- |
| Basis                | Classes           | Objects                |
| Inheritance          | Class → Class     | Object → Object        |
| Blueprint Required   | Yes               | No                     |
| Object Creation      | Instantiate class | Create object directly |
| Property Lookup      | Class hierarchy   | Prototype chain        |
| Runtime Flexibility  | Lower             | Higher                 |
| Dynamic Modification | Limited           | Very flexible          |

---

# 🧠 Classes in Modern JavaScript

Many developers think JavaScript now has classical inheritance because of `class`.

Example:

```js
class Animal {
  speak() {
    console.log("Animal");
  }
}

class Dog extends Animal {}
```

Looks like Java:

```js
const d = new Dog();
d.speak();
```

---

## But internally...

JavaScript converts this into prototype relationships.

```js
console.log(Dog.prototype.__proto__ === Animal.prototype);
```

Output:

```txt
true
```

So:

```txt
Dog instance
      ↓
Dog.prototype
      ↓
Animal.prototype
      ↓
Object.prototype
      ↓
null
```

Still prototypal inheritance.

---

# 🔥 Dynamic Nature of Prototypes

One major difference:

### Classical OOP

Changing a class at runtime is difficult or impossible.

### JavaScript

You can modify prototypes dynamically.

```js
function Animal() {}

Animal.prototype.walk = function () {
  console.log("walking");
};

const dog = new Animal();

dog.walk();
```

Later:

```js
Animal.prototype.run = function () {
  console.log("running");
};

dog.run();
```

Output:

```txt
running
```

Existing objects gain access immediately.

---

# 🧠 Constructor Functions (Pre-ES6)

Before `class` syntax:

```js
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function () {
  console.log(this.name);
};
```

This is the original JavaScript inheritance model.

---

# 🔥 Object Delegation

JavaScript inheritance is often described as:

> Objects delegate behavior to prototypes.

Example:

```js
const animal = {
  speak() {
    console.log("Animal");
  },
};

const dog = Object.create(animal);

dog.speak();
```

`dog` doesn't own `speak`.

It delegates lookup to `animal`.

---

# ⚠️ Common Interview Traps

## Trap 1: "JavaScript is class-based"

❌ Incorrect

JavaScript is fundamentally:

```txt
Prototype-based
```

Even ES6 classes use prototypes internally.

---

## Trap 2: "Classes create copies of methods"

```js
class User {
  greet() {}
}
```

Methods are stored on:

```js
User.prototype;
```

Not copied to every instance.

---

## Trap 3: Confusing prototype with instance

```js
function User() {}

const u = new User();
```

```txt
u → instance
User.prototype → prototype object
```

Different things.

---

# 🔥 Real Internal Relationship

```js
class A {}

const a = new A();
```

Internally:

```txt
a
 ↓
A.prototype
 ↓
Object.prototype
 ↓
null
```

Checking:

```js
console.log(Object.getPrototypeOf(a) === A.prototype);
```

Output:

```txt
true
```

---

# 🧠 Advantages of Prototypal Inheritance

### 1. Dynamic

Modify behavior at runtime.

### 2. Memory Efficient

Methods shared through prototypes.

### 3. Flexible

Objects can inherit directly from objects.

### 4. No Blueprint Requirement

Can create inheritance without classes.

---

# 🧠 Advantages of Classical OOP

### 1. Easier for large teams

Clear hierarchy.

### 2. Strong typing support

(Java/C#/C++)

### 3. Familiar architecture

Common enterprise pattern.

---

# 💣 Senior-Level Interview Answer

> “Classical OOP is based on classes acting as blueprints for objects, with inheritance occurring between classes. JavaScript, however, uses prototypal inheritance, where objects inherit directly from other objects through the prototype chain. Although ES6 introduced class syntax, it is primarily syntactic sugar over the existing prototype-based model. Property and method lookup in JavaScript occurs dynamically through prototype delegation, making the language more flexible and dynamic than traditional class-based OOP systems.”

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
