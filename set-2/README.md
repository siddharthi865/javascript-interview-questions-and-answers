# Set 2

| S.No. | Question                                                                                                                                                             |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you select elements using document.querySelector and getElementById?](#question-1-how-do-you-select-elements-using-documentqueryselector-and-getelementbyid) |
| 2.    | [How to add/remove classes dynamically in JS?](#question-2-how-to-addremove-classes-dynamically-in-js)                                                               |
| 3.    | [What are JavaScript objects?](#question-3-what-are-javascript-objects)                                                                                              |
| 4.    | [How to loop over object properties?](#question-4-how-to-loop-over-object-properties)                                                                                |
| 5.    | [What are arrays in JavaScript? How to loop over them?](#question-5-what-are-arrays-in-javascript-how-to-loop-over-them)                                             |
| 6.    | [Difference between for loop, for…of, and for…in](#question-6-difference-between-for-loop-forof-and-forin)                                                           |
| 7.    | [Explain array methods like push, pop, shift, unshift](#question-7-explain-array-methods-like-push-pop-shift-unshift)                                                |
| 8.    | [What is the difference between slice() and splice()?](#question-8-what-is-the-difference-between-slice-and-splice)                                                  |
| 9.    | [What is the difference between map, filter, and forEach?](#question-9-what-is-the-difference-between-map-filter-and-foreach)                                        |
| 10.   | [How to copy an array without affecting the original?](#question-10-how-to-copy-an-array-without-affecting-the-original)                                             |
| 11.   | [Explain JSON. How to parse and stringify JSON?](#question-11-explain-json-how-to-parse-and-stringify-json)                                                          |
| 12.   | [What is this keyword in JavaScript?](#question-12-what-is-this-keyword-in-javascript)                                                                               |
| 13.   | [Difference between call(), apply(), and bind()](#question-13-difference-between-call-apply-and-bind)                                                                |
| 14.   | [What is NaN and how to check it?](#question-14-what-is-nan-and-how-to-check-it)                                                                                     |
| 15.   | [Explain isNaN() vs Number.isNaN()](#question-15-explain-isnan-vs-numberisnan)                                                                                       |
| 16.   | [What is the difference between let and var in terms of hoisting?](#question-16-what-is-the-difference-between-let-and-var-in-terms-of-hoisting)                     |
| 17.   | [Explain the JavaScript execution context](#question-17-explain-the-javascript-execution-context)                                                                    |
| 18.   | [What are the different phases of the execution context?](#question-18-what-are-the-different-phases-of-the-execution-context)                                       |
| 19.   | [What are JavaScript scopes?](#question-19-what-are-javascript-scopes)                                                                                               |
| 20.   | [Explain lexical scoping](#question-20-explain-lexical-scoping)                                                                                                      |

## Question 1. How do you select elements using document.querySelector and getElementById?

> `document.getElementById()` selects an element by its unique `id`, while `document.querySelector()` selects the first element that matches a CSS selector (id, class, tag, etc.).

### **Detailed Explanation**

In JavaScript, DOM selection is fundamental for manipulating HTML elements. The two most commonly used methods are:

#### 1. `document.getElementById()`

##### **What it does**

- Selects a single element by its **unique `id` attribute**
- Returns **one DOM element or `null` if not found**

##### **Syntax**

```javascript
document.getElementById("myId");
```

##### **Example**

```html
<h1 id="title">Hello</h1>
```

```javascript
const heading = document.getElementById("title");
heading.textContent = "Updated Title";
```

##### **Key Points**

- Very fast (optimized internally by browsers)
- Only works with **id**
- Returns a **single element**

#### 2. `document.querySelector()`

##### **What it does**

- Selects the **first element matching a CSS selector**
- Can select by:
  - id (`#id`)
  - class (`.class`)
  - tag (`div`, `p`)
  - complex selectors (`div p`, `.container .item`, etc.)

##### **Syntax**

```javascript
document.querySelector("selector");
```

##### **Examples**

###### By ID

```javascript
const heading = document.querySelector("#title");
```

###### By Class

```javascript
const box = document.querySelector(".box");
```

###### By Tag

```javascript
const firstDiv = document.querySelector("div");
```

###### Complex selector

```javascript
const item = document.querySelector(".menu li.active");
```

### **Key Differences**

| Feature       | getElementById | querySelector          |
| ------------- | -------------- | ---------------------- |
| Selector type | Only `id`      | Any CSS selector       |
| Return value  | Single element | First matching element |
| Performance   | Faster         | Slightly slower        |
| Flexibility   | Low            | Very high              |

### **Practical Comparison Example**

```html
<div id="box1" class="box">Box 1</div>
<div class="box">Box 2</div>
```

```javascript
document.getElementById("box1"); // selects Box 1
document.querySelector("#box1"); // selects Box 1

document.querySelector(".box"); // selects ONLY Box 1 (first match)
```

### **Common Pitfalls**

#### 1. `querySelector` returns only the first match

```javascript
document.querySelector(".box"); // NOT all boxes
```

To select multiple elements:

```javascript
document.querySelectorAll(".box");
```

#### 2. Forgetting `#` or `.` in querySelector

```javascript
document.querySelector("title"); // selects tag, not id
document.querySelector("#title"); // correct for id
```

### **Best Practices**

- Use `getElementById()` when selecting by id → **faster and clearer intent**
- Use `querySelector()` when you need **flexible or complex selections**
- Use `querySelectorAll()` when working with multiple elements

### **Interview Tip**

A strong answer highlights:

- `getElementById` = direct, optimized, id-only
- `querySelector` = flexible CSS-based selector engine
- Trade-off: performance vs flexibility

## Question 2. How to add/remove classes dynamically in JS?

### **Short Answer**

You can add or remove CSS classes dynamically in JavaScript using the `classList` API:

- `element.classList.add("className")` → add class
- `element.classList.remove("className")` → remove class
- `element.classList.toggle("className")` → add if missing, remove if present

---

## **Detailed Explanation (Interview Perspective)**

In JavaScript, dynamically changing classes is one of the most common DOM manipulation tasks. Modern JavaScript provides the **`classList` interface**, which is cleaner and safer than manipulating `className` strings directly.

---

## 1. Using `classList.add()`

### **Purpose**

Adds one or more classes to an element.

### **Syntax**

```javascript
element.classList.add("className");
```

### **Example**

```html
<div id="box"></div>
```

```javascript
const box = document.getElementById("box");
box.classList.add("active");
```

### **Multiple classes**

```javascript
box.classList.add("active", "highlight");
```

---

## 2. Using `classList.remove()`

### **Purpose**

Removes one or more classes.

### **Syntax**

```javascript
element.classList.remove("className");
```

### **Example**

```javascript
box.classList.remove("active");
```

### **Multiple classes**

```javascript
box.classList.remove("active", "highlight");
```

---

## 3. Using `classList.toggle()`

### **Purpose**

Adds the class if it doesn't exist, removes it if it does.

### **Syntax**

```javascript
element.classList.toggle("className");
```

### **Example**

```javascript
box.classList.toggle("active");
```

### **With force parameter**

```javascript
box.classList.toggle("active", true); // always add
box.classList.toggle("active", false); // always remove
```

---

## 4. Using `classList.contains()`

### **Purpose**

Checks if an element has a class.

### **Example**

```javascript
if (box.classList.contains("active")) {
  console.log("Active state enabled");
}
```

---

## 5. Older Approach: `className` (Not Recommended)

Before `classList`, developers used string manipulation:

### **Example**

```javascript
box.className = "active highlight";
```

### **Removing class manually (problematic)**

```javascript
box.className = box.className.replace("active", "");
```

### **Why it’s bad**

- Easy to break existing classes
- Hard to maintain
- No built-in methods for safe manipulation

---

## **Key Differences: `classList` vs `className`**

| Feature             | classList        | className              |
| ------------------- | ---------------- | ---------------------- |
| Ease of use         | High             | Low                    |
| Safety              | Safe             | Error-prone            |
| Multiple operations | Built-in methods | Manual string handling |
| Modern standard     | Yes              | Legacy                 |

---

## **Real-world Example (UI Toggle)**

```html
<button id="btn">Toggle Dark Mode</button>
<div id="app">Hello</div>
```

```javascript
const btn = document.getElementById("btn");
const app = document.getElementById("app");

btn.addEventListener("click", () => {
  app.classList.toggle("dark");
});
```

---

## **Common Pitfalls**

### 1. Forgetting multiple class support

```javascript
element.classList.add("a b"); // ❌ wrong
element.classList.add("a", "b"); // ✅ correct
```

---

### 2. Confusing `classList` with `className`

```javascript
element.className.add("test"); // ❌ doesn't exist
```

---

### 3. Overusing `className`

Can accidentally overwrite existing classes:

```javascript
element.className = "new-class"; // removes all previous classes
```

---

## **Best Practices**

- Always prefer `classList` over `className`
- Use `toggle()` for UI state changes (menus, themes, modals)
- Use `contains()` before conditional logic when needed
- Keep class names meaningful and state-driven (e.g., `is-active`, `hidden`, `open`)

---

## **Interview Tip**

A strong senior-level answer should emphasize:

- `classList` is a modern DOMTokenList API
- It avoids string parsing issues of `className`
- It supports atomic class operations (add/remove/toggle/contains)

---

If you want, I can next explain **event delegation vs direct event listeners**, which is a very common follow-up in DOM interviews.

## Question 3. What are JavaScript objects?

### **Short Answer**

JavaScript objects are **non-primitive data structures** used to store **key–value pairs**, where keys are strings (or symbols) and values can be any data type—including other objects, functions, or primitives.

---

## **Detailed Explanation (Interview Perspective)**

In JavaScript, an object is a fundamental building block used to model real-world entities and complex data structures. Almost everything in JavaScript (except primitives) is either an object or behaves like one.

Objects are **reference types**, meaning they are stored and passed by reference, not by value.

---

## **1. Creating Objects**

### **Object Literal (most common)**

```javascript id="obj1"
const user = {
  name: "Alice",
  age: 25,
  isAdmin: true,
};
```

---

### **Using `new Object()`**

```javascript id="obj2"
const user = new Object();
user.name = "Alice";
user.age = 25;
```

---

### **Using Constructor Function**

```javascript id="obj3"
function User(name, age) {
  this.name = name;
  this.age = age;
}

const user = new User("Alice", 25);
```

---

### **Using `Object.create()`**

```javascript id="obj4"
const proto = {
  greet() {
    console.log("Hello!");
  },
};

const user = Object.create(proto);
user.name = "Alice";
```

---

## **2. Accessing Object Properties**

### **Dot Notation**

```javascript id="access1"
console.log(user.name);
```

### **Bracket Notation**

```javascript id="access2"
console.log(user["name"]);
```

### **When to use bracket notation**

- When key is dynamic
- When key has spaces or special characters

```javascript id="access3"
const key = "name";
console.log(user[key]);
```

---

## **3. Adding / Updating Properties**

```javascript id="update1"
user.city = "Delhi"; // add
user.age = 26; // update
```

---

## **4. Deleting Properties**

```javascript id="delete1"
delete user.isAdmin;
```

---

## **5. Objects Can Contain Functions (Methods)**

```javascript id="method1"
const user = {
  name: "Alice",
  greet: function () {
    console.log("Hello " + this.name);
  },
};

user.greet();
```

### ES6 shorthand method syntax

```javascript id="method2"
const user = {
  name: "Alice",
  greet() {
    console.log(`Hello ${this.name}`);
  },
};
```

---

## **6. Key Characteristics of JavaScript Objects**

### ✔ Dynamic

You can add/remove properties anytime:

```javascript id="dynamic1"
const obj = {};
obj.newProp = "value";
```

---

### ✔ Reference Type

```javascript id="ref1"
const a = { x: 10 };
const b = a;

b.x = 20;

console.log(a.x); // 20 (same reference)
```

---

### ✔ Heterogeneous Values

Objects can hold any type:

```javascript id="hetero1"
const data = {
  id: 1,
  name: "Alice",
  isActive: true,
  skills: ["JS", "React"],
  address: { city: "Delhi" },
  greet() {
    console.log("Hi");
  },
};
```

---

## **7. Objects vs Primitive Types**

| Feature    | Objects           | Primitives              |
| ---------- | ----------------- | ----------------------- |
| Type       | Reference type    | Value type              |
| Mutability | Mutable           | Immutable               |
| Examples   | {}, [], functions | string, number, boolean |

---

## **8. Important Concept: Object Keys**

- Keys are always strings or symbols internally

```javascript id="key1"
const obj = {
  1: "one",
};

console.log(obj["1"]); // "one"
```

---

## **9. Common Pitfalls**

### 1. Accidental shared references

```javascript id="pit1"
const a = { x: 1 };
const b = a;

b.x = 99;
console.log(a.x); // 99
```

---

### 2. Losing `this` context

```javascript id="pit2"
const user = {
  name: "Alice",
  greet() {
    console.log(this.name);
  },
};

const fn = user.greet;
fn(); // undefined (or error in strict mode)
```

---

### 3. Using objects when primitives are enough

Over-engineering simple data structures.

---

## **10. Best Practices**

- Prefer object literals `{}` over constructors for simplicity
- Use `const` for object references (still mutable internally)
- Avoid deep nesting when possible
- Use destructuring for cleaner access:

```javascript id="bp1"
const user = { name: "Alice", age: 25 };

const { name, age } = user;
```

---

## **Interview Tip**

A strong answer should emphasize:

- Objects are **key–value collections**
- They are **reference types**
- They support **dynamic structure**
- They are central to JavaScript’s design (everything non-primitive is object-like)

## Question 4. How to loop over object properties?

### **Short Answer**

You can loop over JavaScript object properties using:

- `for...in` loop (traditional)
- `Object.keys()` + `forEach`
- `Object.values()` (for values only)
- `Object.entries()` (key–value pairs, most commonly used in modern JS)

---

## **Detailed Explanation (Interview Perspective)**

Objects in JavaScript are not directly iterable like arrays. So we use built-in utilities or specific loops to access their properties.

---

# **1. `for...in` loop (classic approach)**

### **What it does**

Iterates over all **enumerable properties** of an object (including inherited ones).

### **Syntax**

```javascript id="forin1"
for (let key in obj) {
  console.log(key, obj[key]);
}
```

### **Example**

```javascript id="forin2"
const user = {
  name: "Alice",
  age: 25,
  city: "Delhi",
};

for (let key in user) {
  console.log(key, user[key]);
}
```

### **Output**

```
name Alice
age 25
city Delhi
```

---

### ⚠️ Important Pitfall

`for...in` also iterates over inherited properties, so you should filter with `hasOwnProperty()`:

```javascript id="forin3"
for (let key in user) {
  if (user.hasOwnProperty(key)) {
    console.log(key, user[key]);
  }
}
```

---

# **2. `Object.keys()` (most common in modern JS)**

### **What it does**

Returns an array of object’s own keys.

### **Example**

```javascript id="keys1"
const user = {
  name: "Alice",
  age: 25,
};

Object.keys(user).forEach((key) => {
  console.log(key, user[key]);
});
```

### **Why it's preferred**

- Safer than `for...in`
- No inherited properties
- Works naturally with array methods

---

# **3. `Object.values()` (values only)**

### **What it does**

Returns an array of values.

```javascript id="values1"
const user = {
  name: "Alice",
  age: 25,
};

Object.values(user).forEach((value) => {
  console.log(value);
});
```

### **Output**

```
Alice
25
```

---

# **4. `Object.entries()` (best modern approach)**

### **What it does**

Returns array of `[key, value]` pairs.

### **Example**

```javascript id="entries1"
const user = {
  name: "Alice",
  age: 25,
};

Object.entries(user).forEach(([key, value]) => {
  console.log(key, value);
});
```

### **Output**

```
name Alice
age 25
```

---

## **Why `Object.entries()` is powerful**

You can easily transform or map objects:

```javascript id="entries2"
const user = {
  name: "Alice",
  age: 25,
};

const result = Object.entries(user).map(([key, value]) => {
  return `${key.toUpperCase()}: ${value}`;
});

console.log(result);
```

---

# **Comparison of Methods**

| Method             | Returns                    | Best Use Case      | Modern?        |
| ------------------ | -------------------------- | ------------------ | -------------- |
| `for...in`         | keys (including inherited) | legacy code        | ❌             |
| `Object.keys()`    | array of keys              | safe key iteration | ✅             |
| `Object.values()`  | array of values            | value-only logic   | ✅             |
| `Object.entries()` | key-value pairs            | most flexible      | ⭐ Recommended |

---

# **Real-world Example**

```javascript id="real1"
const prices = {
  apple: 100,
  banana: 60,
  mango: 150,
};

Object.entries(prices).forEach(([fruit, price]) => {
  console.log(`${fruit} costs ${price}`);
});
```

---

# **Common Pitfalls**

### 1. Using `for...in` without filtering

Can accidentally include prototype properties.

---

### 2. Expecting order guarantee

Object property order is mostly stable now (ES6+), but:

- Numeric keys are sorted
- String keys follow insertion order

---

### 3. Trying to use `for...of` directly on objects

```javascript id="pit1"
for (let item of user) {
} // ❌ TypeError
```

Objects are not iterable unless converted.

---

# **Best Practices (Interview Answer Summary)**

- Use `Object.entries()` for most cases (clean + flexible)
- Use `Object.keys()` when only keys are needed
- Avoid `for...in` unless working with legacy code or prototype-aware logic
- Always prefer explicit iteration methods over raw loops

---

## **Interview Tip**

A strong senior-level answer highlights:

- Objects are not directly iterable
- ES6 introduced safer utility methods (`keys`, `values`, `entries`)
- `Object.entries()` is the most powerful and commonly used modern approach

## Question 5. What are arrays in JavaScript? How to loop over them?

### **Short Answer**

An **array in JavaScript** is an ordered, indexed collection of values that can hold any data type. You can loop over arrays using methods like `for`, `for...of`, `forEach`, `map`, `filter`, and more modern functional iteration methods.

---

# **Detailed Explanation (Interview Perspective)**

## **1. What are Arrays in JavaScript?**

An array is a **special object type** used to store multiple values in a single variable. Each value is stored at a numeric index starting from `0`.

### **Example**

```javascript id="arr1"
const fruits = ["apple", "banana", "mango"];
```

### **Accessing elements**

```javascript id="arr2"
console.log(fruits[0]); // apple
console.log(fruits[2]); // mango
```

---

## **2. Key Characteristics of Arrays**

### ✔ Ordered

Elements maintain insertion order.

### ✔ Indexed

Starts from `0`.

### ✔ Dynamic

Can grow or shrink:

```javascript id="arr3"
const arr = [1, 2];
arr.push(3);
```

### ✔ Heterogeneous

Can store mixed types:

```javascript id="arr4"
const data = [1, "hello", true, { name: "Alice" }];
```

---

## **3. How Arrays Work Internally**

Arrays in JavaScript are **objects with numeric keys**, but optimized internally by engines (like V8) for performance.

```javascript id="arr5"
const arr = ["a", "b"];
console.log(typeof arr); // "object"
```

---

# **4. Ways to Loop Over Arrays**

---

## **1. Traditional `for` loop**

### **Most basic and fully controlled**

```javascript id="loop1"
const nums = [10, 20, 30];

for (let i = 0; i < nums.length; i++) {
  console.log(nums[i]);
}
```

### ✔ Pros

- Full control over index
- Can break/continue

### ❌ Cons

- Verbose

---

## **2. `for...of` (modern and recommended)**

### **Best for simple iteration**

```javascript id="loop2"
const nums = [10, 20, 30];

for (const num of nums) {
  console.log(num);
}
```

### ✔ Pros

- Clean and readable
- Works with iterables

### ❌ Cons

- No direct index unless manually tracked

---

## **3. `forEach()` (functional approach)**

```javascript id="loop3"
const nums = [10, 20, 30];

nums.forEach((num, index) => {
  console.log(index, num);
});
```

### ✔ Pros

- Clean callback-based style
- Provides index

### ❌ Cons

- Cannot use `break` or `continue`

---

## **4. `map()` (for transformation)**

### **Used when you want a new array**

```javascript id="loop4"
const nums = [1, 2, 3];

const doubled = nums.map((num) => num * 2);

console.log(doubled); // [2, 4, 6]
```

### ✔ Key idea:

- Always returns a new array

---

## **5. `filter()` (for filtering values)**

```javascript id="loop5"
const nums = [1, 2, 3, 4];

const even = nums.filter((num) => num % 2 === 0);

console.log(even); // [2, 4]
```

---

## **6. `reduce()` (for aggregation)**

```javascript id="loop6"
const nums = [1, 2, 3, 4];

const sum = nums.reduce((acc, curr) => acc + curr, 0);

console.log(sum); // 10
```

---

## **7. `entries()` with `for...of`**

If you need index + value:

```javascript id="loop7"
const arr = ["a", "b", "c"];

for (const [index, value] of arr.entries()) {
  console.log(index, value);
}
```

---

# **Comparison of Looping Methods**

| Method     | Use Case             | Can Break? | Returns New Array? |
| ---------- | -------------------- | ---------- | ------------------ |
| `for`      | full control         | Yes        | No                 |
| `for...of` | simple iteration     | Yes        | No                 |
| `forEach`  | functional iteration | No         | No                 |
| `map`      | transform data       | No         | Yes                |
| `filter`   | filtering data       | No         | Yes                |
| `reduce`   | aggregation          | No         | Yes                |

---

# **Common Pitfalls**

## 1. Using `forEach` when you need break

```javascript id="pit1"
nums.forEach((num) => {
  if (num === 2) return; // ❌ does NOT break loop
});
```

---

## 2. Using `map()` without return

```javascript id="pit2"
nums.map((num) => {
  num * 2; // ❌ missing return
});
```

---

## 3. Mutating arrays during iteration

```javascript id="pit3"
nums.forEach((num, i) => {
  nums.push(10); // ❌ risky behavior
});
```

---

# **Best Practices**

- Use `for...of` for simple loops
- Use `map`, `filter`, `reduce` for functional programming style
- Avoid mutating arrays during iteration
- Prefer immutability for predictable code
- Avoid `forEach` when control flow (`break/continue`) is needed

---

# **Interview Tip**

A strong answer should highlight:

- Arrays are **ordered, indexed collections**
- Internally they are **object-like but optimized**
- Multiple iteration methods exist depending on intent:
  - `for/for...of` → control flow
  - `map/filter/reduce` → functional programming
  - `forEach` → side effects

## Question 6. Difference between for loop, for…of, and for…in

### **Short Answer**

- **`for` loop** → Iterates using an index; works with arrays, strings, or any sequence where you control the counter.
- **`for...of`** → Iterates over **values** of an iterable (arrays, strings, maps, sets, etc.).
- **`for...in`** → Iterates over **property keys** of an object.

---

# **Detailed Explanation (Interview Perspective)**

These three loops serve different purposes and are often confused in interviews.

---

## **1. Traditional `for` Loop**

### **Syntax**

```javascript
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}
```

### **Example**

```javascript
const fruits = ["apple", "banana", "mango"];

for (let i = 0; i < fruits.length; i++) {
  console.log(i, fruits[i]);
}
```

### **Output**

```text
0 apple
1 banana
2 mango
```

### **Advantages**

- Full control over the loop.
- Can access both index and value.
- Supports `break`, `continue`, and custom step sizes.

### **Use When**

- You need the index.
- You need to skip elements or iterate in reverse.

---

## **2. `for...of` Loop**

### **Syntax**

```javascript
for (const value of iterable) {
  console.log(value);
}
```

### **Example**

```javascript
const fruits = ["apple", "banana", "mango"];

for (const fruit of fruits) {
  console.log(fruit);
}
```

### **Output**

```text
apple
banana
mango
```

### **Works With**

- Arrays
- Strings
- Sets
- Maps
- Other iterables

```javascript
for (const char of "Hello") {
  console.log(char);
}
```

### **Advantages**

- Cleaner and more readable.
- No need to manage indexes manually.
- Supports `break` and `continue`.

### **Use When**

- You only need the values.
- You're working with iterable objects.

---

## **3. `for...in` Loop**

### **Syntax**

```javascript
for (const key in object) {
  console.log(key);
}
```

### **Example**

```javascript
const user = {
  name: "Alice",
  age: 25,
};

for (const key in user) {
  console.log(key, user[key]);
}
```

### **Output**

```text
name Alice
age 25
```

### **Important**

`for...in` iterates over **property names (keys)**, not values.

### **Arrays with `for...in`**

```javascript
const fruits = ["apple", "banana", "mango"];

for (const index in fruits) {
  console.log(index);
}
```

Output:

```text
0
1
2
```

Notice it returns the **indexes**, not the values.

### **Why it's usually avoided for arrays**

- Returns keys as strings.
- Can include inherited enumerable properties.
- Less predictable than `for...of`.

---

# **Comparison Table**

| Feature                 | `for`   | `for...of`           | `for...in`           |
| ----------------------- | ------- | -------------------- | -------------------- |
| Iterates over           | Indexes | Values               | Keys/Property names  |
| Works on arrays         | ✅      | ✅                   | ✅ (not recommended) |
| Works on objects        | ❌      | ❌ (unless iterable) | ✅                   |
| Access index directly   | ✅      | ❌                   | ✅                   |
| Supports break/continue | ✅      | ✅                   | ✅                   |
| Best for objects        | ❌      | ❌                   | ✅                   |
| Best for arrays         | ✅      | ✅                   | ❌                   |

---

# **Example Showing All Three**

```javascript
const fruits = ["apple", "banana", "mango"];
```

### `for`

```javascript
for (let i = 0; i < fruits.length; i++) {
  console.log(i, fruits[i]);
}
```

Output:

```text
0 apple
1 banana
2 mango
```

### `for...of`

```javascript
for (const fruit of fruits) {
  console.log(fruit);
}
```

Output:

```text
apple
banana
mango
```

### `for...in`

```javascript
for (const index in fruits) {
  console.log(index);
}
```

Output:

```text
0
1
2
```

---

# **Common Interview Pitfall**

```javascript
const arr = ["a", "b", "c"];

for (const item in arr) {
  console.log(item);
}
```

Many candidates expect:

```text
a
b
c
```

Actual output:

```text
0
1
2
```

Because `for...in` returns **keys/indexes**, not values.

---

# **Best Practices**

### Arrays

Use:

```javascript
for (const item of array) {
  // ...
}
```

or

```javascript
array.forEach((item) => {
  // ...
});
```

### Objects

Use:

```javascript
for (const key in object) {
  console.log(key, object[key]);
}
```

or modern alternatives:

```javascript
Object.entries(object).forEach(([key, value]) => {
  console.log(key, value);
});
```

---

## **Interview Tip**

A concise interview answer:

> "`for` gives full control with an index, `for...of` iterates over values of iterable objects, and `for...in` iterates over property keys of objects. For arrays, `for...of` is generally preferred, while `for...in` is mainly used for objects."

## Question 7. Explain array methods like push, pop, shift, unshift

### **Short Answer**

These are basic array manipulation methods:

- **`push()`** → Adds element(s) to the **end** of an array.
- **`pop()`** → Removes and returns the **last** element.
- **`shift()`** → Removes and returns the **first** element.
- **`unshift()`** → Adds element(s) to the **beginning** of an array.

All four methods **mutate (modify) the original array**.

---

# **Detailed Explanation (Interview Perspective)**

## **1. `push()`**

### **Purpose**

Adds one or more elements to the end of an array.

### **Syntax**

```javascript
array.push(element1, element2, ...);
```

### **Example**

```javascript
const fruits = ["apple", "banana"];

fruits.push("mango");

console.log(fruits);
```

### **Output**

```javascript
["apple", "banana", "mango"];
```

### **Return Value**

Returns the **new length** of the array.

```javascript
const arr = [1, 2];

const length = arr.push(3);

console.log(length); // 3
```

---

## **2. `pop()`**

### **Purpose**

Removes the last element from an array.

### **Syntax**

```javascript
array.pop();
```

### **Example**

```javascript
const fruits = ["apple", "banana", "mango"];

const removed = fruits.pop();

console.log(removed);
console.log(fruits);
```

### **Output**

```javascript
mango[("apple", "banana")];
```

### **Return Value**

Returns the removed element.

---

## **3. `shift()`**

### **Purpose**

Removes the first element from an array.

### **Syntax**

```javascript
array.shift();
```

### **Example**

```javascript
const fruits = ["apple", "banana", "mango"];

const removed = fruits.shift();

console.log(removed);
console.log(fruits);
```

### **Output**

```javascript
apple[("banana", "mango")];
```

### **Return Value**

Returns the removed element.

---

## **4. `unshift()`**

### **Purpose**

Adds one or more elements to the beginning of an array.

### **Syntax**

```javascript
array.unshift(element1, element2, ...);
```

### **Example**

```javascript
const fruits = ["banana", "mango"];

fruits.unshift("apple");

console.log(fruits);
```

### **Output**

```javascript
["apple", "banana", "mango"];
```

### **Return Value**

Returns the new length of the array.

---

# **Visual Representation**

### Initial Array

```javascript
["apple", "banana"];
```

### `push("mango")`

```javascript
["apple", "banana", "mango"]
                     ↑
                  added
```

### `pop()`

```javascript
["apple", "banana"]
                     ↑
                 removed
```

### `unshift("orange")`

```javascript
["orange", "apple", "banana"]
   ↑
 added
```

### `shift()`

```javascript
["apple", "banana"]
   ↑
 removed
```

---

# **Time Complexity (Important Interview Point)**

| Method      | Operation       | Complexity |
| ----------- | --------------- | ---------- |
| `push()`    | End insertion   | **O(1)**   |
| `pop()`     | End removal     | **O(1)**   |
| `shift()`   | Front removal   | **O(n)**   |
| `unshift()` | Front insertion | **O(n)**   |

### Why?

For `shift()` and `unshift()`, all remaining elements must be re-indexed.

Example:

```javascript
["a", "b", "c"];
```

After:

```javascript
shift();
```

Engine must move:

```javascript
b → index 0
c → index 1
```

Hence **O(n)**.

---

# **Common Pitfalls**

## 1. Forgetting These Methods Mutate the Array

```javascript
const arr = [1, 2, 3];

arr.pop();

console.log(arr);
```

Output:

```javascript
[1, 2];
```

The original array changed.

---

## 2. Calling on Empty Arrays

```javascript
const arr = [];

console.log(arr.pop());
console.log(arr.shift());
```

Output:

```javascript
undefined;
undefined;
```

No error is thrown.

---

## 3. Using `unshift()` Frequently on Large Arrays

```javascript
for (let i = 0; i < 100000; i++) {
  arr.unshift(i);
}
```

This can become expensive because every insertion shifts existing indexes.

---

# **Real-World Examples**

### Stack (LIFO)

Use `push()` and `pop()`:

```javascript
const stack = [];

stack.push("A");
stack.push("B");

console.log(stack.pop()); // B
```

---

### Queue (FIFO)

Use `push()` and `shift()`:

```javascript
const queue = [];

queue.push("Task1");
queue.push("Task2");

console.log(queue.shift()); // Task1
```

---

# **Best Practices**

- Use **`push()`** and **`pop()`** when possible for better performance.
- Avoid excessive use of **`shift()`** and **`unshift()`** on very large arrays.
- Remember these methods mutate the original array.
- For immutable updates in React or functional programming, prefer:

```javascript
const newArray = [...arr, newItem];
```

instead of:

```javascript
arr.push(newItem);
```

---

## **Interview Tip**

A strong interview answer should mention:

> "`push()` and `pop()` work at the end of the array and are generally O(1). `shift()` and `unshift()` work at the beginning and are O(n) because elements must be re-indexed. All four methods mutate the original array."

## Question 8. What is the difference between slice() and splice()?

## Question 9. What is the difference between map, filter, and forEach?

## Question 10. How to copy an array without affecting the original?

## Question 11. Explain JSON. How to parse and stringify JSON?

## Question 12. What is this keyword in JavaScript?

## Question 13. Difference between call(), apply(), and bind()

## Question 14. What is NaN and how to check it?

## Question 15. Explain isNaN() vs Number.isNaN()

## Question 16. What is the difference between let and var in terms of hoisting?

## Question 17. Explain the JavaScript execution context

## Question 18. What are the different phases of the execution context?

## Question 19. What are JavaScript scopes?

## Question 20. Explain lexical scoping
