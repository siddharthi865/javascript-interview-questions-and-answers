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

## Short Answer (Interview-ready)

The `new` keyword in JavaScript creates a new object, links it to a constructor’s prototype, binds `this` to that object, executes the constructor, and finally returns the object (unless the constructor explicitly returns another object).

---

# 🧠 Detailed Explanation (Senior Interview Level)

When you write:

```js id="n1"
function Person(name) {
  this.name = name;
}

const p = new Person("Alice");
```

JavaScript internally performs **4 main steps**.

---

# ⚙️ Internal Working of `new`

## Step 1: Create a new empty object

```js id="n2"
const obj = {};
```

---

## Step 2: Link the object to the constructor prototype

```js id="n3"
obj.__proto__ = Person.prototype;
```

👉 This creates the **prototype chain**

---

## Step 3: Bind `this` inside constructor to the new object

```js id="n4"
Person.call(obj, "Alice");
```

So inside constructor:

```js id="n5"
this === obj;
```

---

## Step 4: Return the object

If constructor does NOT return an object:

```js id="n6"
return obj;
```

---

# 🧠 Final Equivalent Implementation of `new`

This is exactly how it behaves internally:

```js id="n7"
function myNew(Constructor, ...args) {
  const obj = Object.create(Constructor.prototype);

  const result = Constructor.apply(obj, args);

  return result !== null && typeof result === "object" ? result : obj;
}
```

---

# 🔥 Example Breakdown

```js id="n8"
function Person(name) {
  this.name = name;
}

const p = new Person("John");
```

Internally becomes:

```js id="n9"
const obj = {};
obj.__proto__ = Person.prototype;
Person.call(obj, "John");
return obj;
```

---

# 🧠 Prototype Link (Very Important)

```txt id="n10"
p → Person.prototype → Object.prototype → null
```

So:

```js id="n11"
p instanceof Person; // true
```

---

# ⚠️ Important Edge Cases (FAANG favorite)

---

## 1. Constructor returns primitive → ignored

```js id="n12"
function A() {
  this.x = 10;
  return 100;
}

const a = new A();
console.log(a);
```

### Output:

```txt id="n13"
{ x: 10 }
```

👉 Primitive return is ignored

---

## 2. Constructor returns object → overrides `this`

```js id="n14"
function A() {
  this.x = 10;
  return { y: 20 };
}

const a = new A();
console.log(a);
```

### Output:

```txt id="n15"
{ y: 20 }
```

👉 Returned object replaces `this`

---

## 3. Missing `new` causes bug

```js id="n16"
function Person(name) {
  this.name = name;
}

const p = Person("Alice");
```

### Problem:

- `this` becomes `window` (or undefined in strict mode)
- leads to bugs

---

# 🔥 How `new` ties to Prototypes

```js id="n17"
function Animal() {}

Animal.prototype.speak = function () {
  console.log("sound");
};

const a = new Animal();
a.speak();
```

### Lookup:

```txt id="n18"
a → Animal.prototype → Object.prototype
```

---

# 🧠 Why `new` exists (Design reason)

It enables:

- object creation pattern
- shared methods via prototype (memory efficient)
- constructor-based modeling

---

# ⚖️ `new` vs Object.create

| Feature               | new | Object.create |
| --------------------- | --- | ------------- |
| Constructor execution | Yes | No            |
| Prototype linking     | Yes | Yes           |
| Initialization logic  | Yes | No            |

---

# 🔥 Common Interview Traps

## ❌ Thinking `new` only creates objects

👉 It also:

- sets prototype
- binds `this`
- handles return logic

---

## ❌ Forgetting prototype linkage

Many assume:

```js
obj = {};
```

But actual:

```js
obj → Constructor.prototype
```

---

## ❌ Misunderstanding return behavior

Constructor return overrides `this` ONLY if it's an object.

---

# 🧠 Senior-Level Interview Answer

> “The `new` keyword in JavaScript performs four operations: it creates a new empty object, sets its internal prototype to the constructor’s prototype, binds `this` inside the constructor to the new object, and finally returns the object unless the constructor explicitly returns another object. This mechanism enables prototype-based inheritance and is the foundation of constructor functions in JavaScript.”

## Question 7. Explain super() in classes

## Short Answer (Interview-ready)

`super()` in JavaScript classes is used to **call the parent class constructor**. It must be called in a child class before accessing `this`, and it ensures the parent class is properly initialized.

---

# 🧠 Detailed Explanation (Senior Interview Level)

In JavaScript ES6 classes, inheritance is implemented using prototypes, and `super()` is the mechanism that connects the **child class constructor to the parent class constructor**.

---

# 🏗️ Basic Example

```js id="s1"
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name); // calls Animal constructor
  }
}

const d = new Dog("Tommy");
console.log(d.name);
```

### Output:

```
Tommy
```

---

# ⚙️ What `super()` actually does

When you write:

```js id="s2"
super(name);
```

Internally it behaves like:

```js id="s3"
Animal.call(this, name);
```

But with prototype chain properly set up.

---

# 🧠 Why `super()` is required

In child classes, JavaScript does NOT automatically initialize `this`.

### ❌ This will throw error:

```js id="s4"
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name) {
    this.name = name; // ❌ ReferenceError
  }
}
```

### Error:

```
ReferenceError: Must call super constructor in derived class before accessing 'this'
```

---

# 🔥 Rule: `this` is uninitialized until `super()` runs

In derived classes:

```txt id="s5"
super() → initializes this
then → you can use this
```

---

# 🧠 Behind the scenes (important for interviews)

When a class extends another:

```js id="s6"
class Dog extends Animal {}
```

JS creates prototype chain:

```txt id="s7"
Dog.prototype → Animal.prototype → Object.prototype
Dog.__proto__ → Animal
```

And constructor chain:

```txt id="s8"
Dog constructor → must call Animal constructor via super()
```

---

# 🧪 `super()` with methods

`super` is not only for constructors — it can also call parent methods.

---

## Example:

```js id="s9"
class Animal {
  speak() {
    console.log("Animal speaks");
  }
}

class Dog extends Animal {
  speak() {
    super.speak(); // call parent method
    console.log("Dog barks");
  }
}

new Dog().speak();
```

### Output:

```
Animal speaks
Dog barks
```

---

# ⚠️ Important Rules (FAANG favorite)

## 1. `super()` must be first in constructor

```js id="s10"
class Dog extends Animal {
  constructor() {
    super(); // MUST come first
    this.age = 5;
  }
}
```

---

## 2. Cannot use `this` before `super()`

```js id="s11"
class Dog extends Animal {
  constructor() {
    console.log(this); // ❌ error
    super();
  }
}
```

---

## 3. `super` behaves differently in methods vs constructors

| Context     | Behavior                 |
| ----------- | ------------------------ |
| constructor | calls parent constructor |
| method      | calls parent method      |

---

# 🧠 Advanced Behavior (Prototype linkage)

```js id="s12"
class A {}
class B extends A {}

console.log(B.__proto__ === A); // true
console.log(B.prototype.__proto__ === A.prototype); // true
```

---

# 🔥 Real-world analogy

Think of:

- `Animal` = base blueprint
- `Dog` = extended blueprint
- `super()` = “initialize base system first before adding custom features”

---

# ⚠️ Common Interview Pitfalls

## ❌ Forgetting super()

```js id="s13"
class Dog extends Animal {
  constructor() {
    this.name = "x"; // error
  }
}
```

---

## ❌ Thinking super copies properties

👉 It does NOT copy — it executes parent constructor.

---

## ❌ Confusing super vs this

| Keyword | Meaning                |
| ------- | ---------------------- |
| this    | current object         |
| super   | parent class reference |

---

# 🔥 super in static methods

```js id="s14"
class A {
  static hello() {
    console.log("A");
  }
}

class B extends A {
  static hello() {
    super.hello();
    console.log("B");
  }
}

B.hello();
```

### Output:

```
A
B
```

---

# 🧠 Senior-level Interview Answer

> “In JavaScript classes, `super()` is used to invoke the parent class constructor and properly initialize the `this` context in derived classes. It is mandatory before accessing `this` in a subclass constructor. Additionally, `super` can be used inside methods to access parent class methods. Under the hood, it links the prototype chain and ensures proper constructor delegation in JavaScript’s prototypal inheritance model.”

## Question 8. Difference between private and public fields in classes

## Short Answer (Interview-ready)

In JavaScript classes, **public fields** are accessible from outside the class instance, while **private fields** (prefixed with `#`) are only accessible inside the class where they are defined. Private fields provide true encapsulation at runtime.

---

# 🧠 Detailed Explanation (Senior Interview Level)

JavaScript ES2022 introduced **native private class fields** using `#`. Before that, “privacy” was only by convention (`_variable`) or closures.

---

# 🟢 1. Public Fields

## Definition:

Public fields are properties that can be accessed and modified from outside the class.

---

## Example:

```js id="p1"
class User {
  name = "Alice"; // public field

  greet() {
    console.log("Hello " + this.name);
  }
}

const u = new User();

console.log(u.name); // ✅ accessible
u.name = "Bob"; // ✅ can modify
u.greet();
```

---

## Key characteristics:

- Accessible anywhere
- Part of the instance object
- Default behavior in JS

---

# 🔴 2. Private Fields (`#` syntax)

## Definition:

Private fields are only accessible inside the class body.

---

## Example:

```js id="p2"
class User {
  #password = "1234";

  showPassword() {
    console.log(this.#password);
  }
}

const u = new User();

u.showPassword(); // ✅ works
console.log(u.#password); // ❌ SyntaxError
```

---

## Error:

```txt id="err1"
Private field '#password' must be declared in an enclosing class
```

---

# ⚙️ Key Characteristics of Private Fields

- Only accessible inside class
- Not accessible via object instance
- Not in `Object.keys()`
- Truly enforced at runtime (not just convention)

---

# 🧠 Internal Behavior

### Public field:

```txt id="i1"
obj.name → stored directly on object
```

### Private field:

```txt id="i2"
stored in internal slot (not accessible via object lookup)
```

So:

```js id="p3"
Object.keys(u); // does NOT include #password
```

---

# ⚖️ Comparison Table

| Feature              | Public Fields | Private Fields (`#`) |
| -------------------- | ------------- | -------------------- |
| Access outside class | ✅ Yes        | ❌ No                |
| Syntax               | `this.x`      | `this.#x`            |
| Runtime enforcement  | No            | Yes                  |
| Inherited            | Yes           | No                   |
| Visible in object    | Yes           | No                   |
| Security             | Weak          | Strong encapsulation |

---

# 🧠 Example: Encapsulation

## Without private fields (bad design)

```js id="p4"
class Bank {
  balance = 1000;
}

const b = new Bank();
b.balance = -999999; // ❌ can break logic
```

---

## With private fields (safe)

```js id="p5"
class Bank {
  #balance = 1000;

  deposit(amount) {
    this.#balance += amount;
  }

  getBalance() {
    return this.#balance;
  }
}

const b = new Bank();
b.deposit(500);

console.log(b.getBalance()); // 1500
console.log(b.#balance); // ❌ error
```

---

# 🔥 Important Rules (FAANG favorite)

## 1. Private fields must be declared

```js id="p6"
class A {
  #x;
}
```

You cannot dynamically create `#x`.

---

## 2. Cannot access private field outside class

```js id="p7"
obj.#x; // ❌ syntax error
```

---

## 3. Private fields are NOT inherited

```js id="p8"
class A {
  #x = 10;
}

class B extends A {
  show() {
    console.log(this.#x); // ❌ error
  }
}
```

---

## 4. Private fields are per-class, not per-object shape

Each class defines its own private slots.

---

# 🧠 Private Methods (bonus concept)

```js id="p9"
class A {
  #privateMethod() {
    console.log("private");
  }

  publicMethod() {
    this.#privateMethod();
  }
}
```

---

# ⚠️ Common Interview Traps

## ❌ Thinking `_variable` is private

```js id="p10"
class A {
  _x = 10;
}
```

👉 This is ONLY a convention, not real privacy.

---

## ❌ Trying to access private field via bracket notation

```js id="p11"
obj["#x"]; // ❌ does not work
```

---

## ❌ Expecting private fields to appear in JSON

```js id="p12"
JSON.stringify(obj);
```

👉 Private fields are excluded.

---

# 🔥 Why private fields were introduced

Before ES2022:

- closures used for privacy
- weak encapsulation
- prototype hacks possible

Now:

- true encapsulation
- safer APIs
- cleaner OOP design

---

# 🧠 Senior-Level Interview Answer

> “Public fields in JavaScript classes are normal object properties accessible outside the class, while private fields introduced using `#` are truly encapsulated and can only be accessed within the class body. Private fields are not part of the object’s enumerable properties, cannot be accessed or modified externally, and are not inherited by subclasses. Unlike underscore conventions, they provide real runtime enforcement of encapsulation.”

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
