# Set 3

| S.No. | Question                                                                                                                                                                                       |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What are IIFEs (Immediately Invoked Function Expressions)?](#question-1-what-are-iifes-immediately-invoked-function-expressions)                                                              |
| 2.    | [Difference between shallow and deep copy of objects](#question-2-difference-between-shallow-and-deep-copy-of-objects)                                                                         |
| 3.    | [Explain prototypes and prototype chain](#question-3-explain-prototypes-and-prototype-chain)                                                                                                   |
| 4.    | [Difference between classical inheritance and prototypal inheritance](#question-4-difference-between-classical-inheritance-and-prototypal-inheritance)                                         |
| 5.    | [What are JavaScript classes? How are they different from functions?](#question-5-what-are-javascript-classes-how-are-they-different-from-functions)                                           |
| 6.    | [What is the difference between static and instance methods?](#question-6-what-is-the-difference-between-static-and-instance-methods)                                                          |
| 7.    | [Explain modules in JavaScript. How to export and import?](#question-7-explain-modules-in-javascript-how-to-export-and-import)                                                                 |
| 8.    | [Explain modules in JavaScript. How to export and import?](#question-8-explain-modules-in-javascript-how-to-export-and-import)                                                                 |
| 9.    | [Difference between synchronous and asynchronous functions](#question-9-difference-between-synchronous-and-asynchronous-functions)                                                             |
| 10.   | [Explain callback functions with examples](#question-10-explain-callback-functions-with-examples)                                                                                              |
| 11.   | [What are Promises? How do they work?](#question-11-what-are-promises-how-do-they-work)                                                                                                        |
| 12.   | [Difference between then chaining and async/await](#question-12-difference-between-then-chaining-and-asyncawait)                                                                               |
| 13.   | [What is the Event Loop? Explain call stack, microtasks, and macrotasks](#question-13-what-is-the-event-loop-explain-call-stack-microtasks-and-macrotasks)                                     |
| 14.   | [What is a JavaScript timer? Difference between setTimeout and setInterval](#question-14-what-is-a-javascript-timer-difference-between-settimeout-and-setinterval)                             |
| 15.   | [What are errors in JavaScript? Difference between SyntaxError, TypeError, ReferenceError](#question-15-what-are-errors-in-javascript-difference-between-syntaxerror-typeerror-referenceerror) |
| 16.   | [How to handle errors using try…catch…finally?](#question-16-how-to-handle-errors-using-trycatchfinally)                                                                                       |
| 17.   | [Difference between for…in and for…of for arrays](#question-17-difference-between-forin-and-forof-for-arrays)                                                                                  |
| 18.   | [What are JavaScript Symbols?](#question-18-what-are-javascript-symbols)                                                                                                                       |
| 19.   | [Explain Object.freeze(), Object.seal(), Object.preventExtensions()](#question-19-explain-objectfreeze-objectseal-objectpreventextensions)                                                     |
| 20.   | [Difference between spread operator (…) and rest operator](#question-20-difference-between-spread-operator--and-rest-operator)                                                                 |

## Question 1. What are IIFEs (Immediately Invoked Function Expressions)?

> An IIFE (Immediately Invoked Function Expression) is a JavaScript function that is defined and executed immediately after it is created.

### Detailed Explanation

An **IIFE** is a function expression that runs as soon as it is defined. It is commonly used to create a **private scope** and avoid polluting the global namespace.

### Basic Syntax

```javascript
(function () {
  console.log("I run immediately!");
})();
```

Or using arrow functions:

```javascript
(() => {
  console.log("I run immediately too!");
})();
```

The key idea is:

- The function is wrapped in parentheses → turns it into an expression
- The final `()` immediately invokes it

### Why IIFEs are used

#### 1. Creating a private scope (data encapsulation)

Before ES6 modules, IIFEs were used to avoid global variable pollution.

```javascript
(function () {
  let secret = "hidden";
  console.log(secret);
})();

console.log(secret); // ❌ ReferenceError: secret is not defined
```

`secret` is not accessible outside the function.

#### 2. Avoiding global namespace pollution

```javascript
(function () {
  let counter = 0;

  function increment() {
    counter++;
    console.log(counter);
  }

  increment();
  increment();
})();
```

Here, `counter` and `increment` do not leak into the global scope.

#### 3. Module pattern (pre-ES6)

IIFEs were widely used to simulate modules:

```javascript
const module = (function () {
  let privateValue = 10;

  function getValue() {
    return privateValue;
  }

  return {
    getValue,
  };
})();

console.log(module.getValue()); // 10
```

### How it works (conceptually)

JavaScript treats:

- `function foo() {}` → function declaration
- `(function foo() {})` → function expression

Only expressions can be immediately executed, so we wrap it in `()` to convert it.

### Common Pitfalls

#### 1. Forgetting parentheses

```javascript
function () {
  console.log("error");
}();
```

❌ Syntax Error — function declaration cannot be invoked directly.

#### 2. Misunderstanding scope

Variables declared inside IIFE are not accessible outside:

```javascript
(function () {
  var x = 10;
})();

console.log(x); // ❌ ReferenceError
```

### Modern JavaScript perspective

With ES6+, IIFEs are less necessary because we now have:

- `let` and `const` (block scope)
- ES Modules (`import/export`)
- Block scopes `{}`

Example replacement:

```javascript
{
  let x = 10;
  console.log(x);
}

console.log(x); // ❌ not accessible
```

### IIFE vs Modules

| Feature         | IIFE    | ES Modules |
| --------------- | ------- | ---------- |
| Scope isolation | Yes     | Yes        |
| Reusability     | Limited | High       |
| Imports/exports | No      | Yes        |
| Modern usage    | Rare    | Standard   |

### Summary

An IIFE is:

- A function that runs immediately after definition
- Used for creating private scope
- Helpful in avoiding global variables
- Mostly replaced today by ES modules and block scope

## Question 2. Difference between shallow and deep copy of objects

## Short Answer

A **shallow copy** copies only the top-level properties of an object. If a property contains another object or array, the copy and the original still share the same reference.

A **deep copy** recursively copies all nested objects and arrays, creating completely independent copies.

---

# Detailed Explanation

JavaScript objects and arrays are **reference types**. When you assign one object to another variable, you're copying the reference, not the actual object.

```javascript
const original = {
  name: "John",
};

const copy = original;

copy.name = "Mike";

console.log(original.name); // Mike
```

Both variables point to the same object in memory.

---

# What is a Shallow Copy?

A shallow copy creates a new object, but nested objects are still shared.

### Example

```javascript
const original = {
  name: "John",
  address: {
    city: "Delhi",
  },
};

const shallowCopy = { ...original };

shallowCopy.name = "Mike";
shallowCopy.address.city = "Mumbai";

console.log(original.name); // John
console.log(original.address.city); // Mumbai
```

### Memory Representation

```text
original
 ├─ name → "John"
 └─ address ─┐
             ▼
          { city: "Mumbai" }

shallowCopy
 ├─ name → "Mike"
 └─ address ─┘
```

The `address` object is shared between both objects.

---

# Common Ways to Create a Shallow Copy

### 1. Spread Operator

```javascript
const copy = { ...obj };
```

### 2. Object.assign()

```javascript
const copy = Object.assign({}, obj);
```

### 3. Array Spread

```javascript
const copy = [...arr];
```

### 4. Array.slice()

```javascript
const copy = arr.slice();
```

All of these perform **shallow copies**.

---

# What is a Deep Copy?

A deep copy recursively copies all nested structures.

### Example

```javascript
const original = {
  name: "John",
  address: {
    city: "Delhi",
  },
};

const deepCopy = structuredClone(original);

deepCopy.address.city = "Mumbai";

console.log(original.address.city); // Delhi
console.log(deepCopy.address.city); // Mumbai
```

Now both objects are completely independent.

---

# Modern Deep Copy: structuredClone()

The recommended modern approach is:

```javascript
const clone = structuredClone(original);
```

### Supports

- Objects
- Arrays
- Maps
- Sets
- Dates
- Typed Arrays
- Circular references

Example:

```javascript
const obj = {
  user: {
    name: "John",
  },
};

const clone = structuredClone(obj);

clone.user.name = "Mike";

console.log(obj.user.name); // John
```

---

# JSON-Based Deep Copy (Older Technique)

```javascript
const deepCopy = JSON.parse(JSON.stringify(obj));
```

### Works for

```javascript
const obj = {
  name: "John",
  age: 30,
};
```

### Fails for

```javascript
const obj = {
  date: new Date(),
  fn: () => {},
  map: new Map(),
  set: new Set(),
  undefinedValue: undefined,
};
```

Problems:

- Removes functions
- Loses `Date` objects
- Loses `Map` and `Set`
- Removes `undefined`
- Cannot handle circular references

Therefore, `structuredClone()` is preferred in modern JavaScript.

---

# Interview Comparison

| Feature                      | Shallow Copy        | Deep Copy           |
| ---------------------------- | ------------------- | ------------------- |
| Creates new top-level object | ✅                  | ✅                  |
| Copies nested objects        | ❌ Reference shared | ✅ Fully copied     |
| Faster                       | ✅                  | ❌ Usually slower   |
| Uses less memory             | ✅                  | ❌ Uses more memory |
| Safe for nested updates      | ❌                  | ✅                  |
| Spread operator              | ✅                  | ❌                  |
| Object.assign()              | ✅                  | ❌                  |
| structuredClone()            | ❌                  | ✅                  |

---

# Common Interview Trap

```javascript
const obj1 = {
  user: {
    name: "John",
  },
};

const obj2 = { ...obj1 };

obj2.user.name = "Mike";

console.log(obj1.user.name);
```

**Output:**

```javascript
Mike;
```

Many candidates expect `"John"`, but spread syntax performs only a **shallow copy**, so both objects share the same nested `user` object.

---

# Best Practices

- Use **spread syntax** or **Object.assign()** when objects are flat.
- Use **structuredClone()** for true deep cloning.
- Be cautious when copying objects with nested arrays or objects.
- Avoid `JSON.parse(JSON.stringify())` for complex data structures.

---

## Interview One-Liner

**"A shallow copy duplicates only the first level of an object and shares references to nested objects, whereas a deep copy recursively clones all nested structures, making the copy completely independent from the original."**

## Question 3. Explain prototypes and prototype chain

## Short Answer

A **prototype** is an object from which other objects inherit properties and methods. The **prototype chain** is JavaScript's mechanism for looking up properties by traversing linked prototype objects until the property is found or the chain ends.

JavaScript uses **prototypal inheritance**, not classical inheritance.

---

# Detailed Explanation

In JavaScript, every object has an internal link to another object called its **prototype** (`[[Prototype]]`).

When you try to access a property:

1. JavaScript checks the object itself.
2. If not found, it checks the object's prototype.
3. Then the prototype's prototype.
4. This continues until `null` is reached.

This lookup path is called the **prototype chain**.

---

# Basic Example

```javascript
const person = {
  greet() {
    console.log("Hello");
  },
};

const user = Object.create(person);

user.name = "John";

console.log(user.name); // John
user.greet(); // Hello
```

### What Happens?

```text
user
 └── prototype → person
                    └── prototype → Object.prototype
                                          └── null
```

When `user.greet()` is called:

- JavaScript checks `user`
- Doesn't find `greet`
- Looks in `person`
- Finds `greet`
- Executes it

---

# Constructor Functions and Prototypes

Before ES6 classes, constructor functions were commonly used.

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hello, I'm ${this.name}`);
};

const john = new Person("John");

john.sayHello();
```

Output:

```javascript
Hello, I'm John
```

---

# Why Use Prototypes?

Without prototypes:

```javascript
function Person(name) {
  this.name = name;

  this.sayHello = function () {
    console.log("Hello");
  };
}
```

Every object gets its own copy of `sayHello`.

```javascript
const p1 = new Person("A");
const p2 = new Person("B");

console.log(p1.sayHello === p2.sayHello);
// false
```

Using prototypes:

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log("Hello");
};
```

```javascript
const p1 = new Person("A");
const p2 = new Person("B");

console.log(p1.sayHello === p2.sayHello);
// true
```

Now all instances share a single method.

---

# Understanding the Prototype Chain

```javascript
const arr = [1, 2, 3];
```

Prototype chain:

```text
arr
 ↓
Array.prototype
 ↓
Object.prototype
 ↓
null
```

Therefore:

```javascript
arr.push(4);
```

works because `push()` exists on `Array.prototype`.

Similarly:

```javascript
arr.toString();
```

works because `toString()` exists higher up in the chain.

---

# Visual Example

```javascript
const animal = {
  eats: true,
};

const dog = Object.create(animal);

dog.barks = true;

console.log(dog.eats);
```

Lookup process:

```text
dog.eats

1. Check dog → not found
2. Check animal → found (true)
```

Result:

```javascript
true;
```

---

# Prototype vs **proto** vs prototype

This is a very common interview question.

### 1. prototype

Exists on constructor functions.

```javascript
function Person() {}

console.log(Person.prototype);
```

Used when creating instances with `new`.

---

### 2. **proto**

Exists on object instances.

```javascript
const obj = {};

console.log(obj.__proto__);
```

Points to the object's prototype.

---

### Relationship

```javascript
function Person() {}

const p = new Person();

console.log(p.__proto__ === Person.prototype);
```

Output:

```javascript
true;
```

---

# ES6 Classes and Prototypes

Classes are syntactic sugar over prototypes.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello ${this.name}`);
  }
}
```

Internally, JavaScript does something similar to:

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  console.log(`Hello ${this.name}`);
};
```

The inheritance mechanism is still prototype-based.

---

# Method Overriding Through Prototype Chain

```javascript
const animal = {
  speak() {
    console.log("Animal sound");
  },
};

const dog = Object.create(animal);

dog.speak = function () {
  console.log("Bark");
};

dog.speak();
```

Output:

```javascript
Bark;
```

JavaScript finds `speak` on `dog` first and stops searching.

---

# Common Interview Pitfalls

### Pitfall 1: Changing Prototype After Instances Exist

```javascript
function Person() {}

const p1 = new Person();

Person.prototype.sayHi = function () {
  console.log("Hi");
};

p1.sayHi();
```

This works because instances reference the prototype object dynamically.

---

### Pitfall 2: Property Shadowing

```javascript
const parent = {
  value: 10,
};

const child = Object.create(parent);

child.value = 20;

console.log(child.value); // 20
```

The child's property shadows the parent's property.

---

# Performance Considerations

Prototype methods are:

- Shared across instances
- More memory efficient
- Faster than recreating methods for every object

Good:

```javascript
Person.prototype.sayHello = function () {};
```

Less efficient:

```javascript
function Person() {
  this.sayHello = function () {};
}
```

---

# Interview Summary

### Prototype

An object that provides inherited properties and methods to other objects.

### Prototype Chain

A linked series of prototype objects that JavaScript traverses during property lookup.

### Lookup Rule

```text
Object
   ↓
Prototype
   ↓
Prototype's Prototype
   ↓
...
   ↓
null
```

### Key Fact

**"JavaScript implements inheritance through prototypes. When a property or method is not found on an object, JavaScript searches up the prototype chain until it finds the property or reaches `null`."**

## Question 4. Difference between classical inheritance and prototypal inheritance

## Question 5. What are JavaScript classes? How are they different from functions?

## Question 6. What is the difference between static and instance methods?

## Question 7. Explain modules in JavaScript. How to export and import?

## Question 8. Explain modules in JavaScript. How to export and import?

## Question 9. Difference between synchronous and asynchronous functions

## Question 10. Explain callback functions with examples

## Question 11. What are Promises? How do they work?

## Question 12. Difference between then chaining and async/await

## Question 13. What is the Event Loop? Explain call stack, microtasks, and macrotasks

## Question 14. What is a JavaScript timer? Difference between setTimeout and setInterval

## Question 15. What are errors in JavaScript? Difference between SyntaxError, TypeError, ReferenceError

## Question 16. How to handle errors using try…catch…finally?

## Question 17. Difference between for…in and for…of for arrays

## Question 18. What are JavaScript Symbols?

## Question 19. Explain Object.freeze(), Object.seal(), Object.preventExtensions()

## Question 20. Difference between spread operator (…) and rest operator
