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

## Short Answer

**Classical inheritance** is based on **classes** that define the structure and behavior of objects. Objects are instances of classes.

**Prototypal inheritance** is based on **objects inheriting directly from other objects**. There are no true classes at the language level; objects are linked through prototypes.

JavaScript uses **prototypal inheritance**, although ES6 `class` syntax makes it look similar to classical inheritance.

---

# Detailed Explanation

## 1. Classical Inheritance

Languages like **Java, C++, C#, and Python** primarily use classical inheritance.

You define a class and create objects from it.

### Example (Java-like)

```java
class Animal {
    void speak() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Bark");
    }
}
```

Usage:

```java
Dog d = new Dog();
d.speak();
d.bark();
```

### Characteristics

- Class-based
- Blueprint → Object
- Inheritance defined between classes
- Typically supports access modifiers (`public`, `private`, etc.)
- Hierarchy designed before runtime

---

# 2. Prototypal Inheritance

In prototypal inheritance, objects inherit directly from other objects.

### Example

```javascript id="o3q03o"
const animal = {
  speak() {
    console.log("Animal sound");
  },
};

const dog = Object.create(animal);

dog.bark = function () {
  console.log("Bark");
};

dog.speak();
dog.bark();
```

Output:

```javascript id="s35bvt"
Animal sound
Bark
```

No class was required.

`dog` inherits directly from `animal`.

---

# How JavaScript Inheritance Works

```javascript id="t6cn2e"
const animal = {
  eats: true,
};

const dog = Object.create(animal);

console.log(dog.eats);
```

JavaScript looks up the prototype chain:

```text
dog
 ↓
animal
 ↓
Object.prototype
 ↓
null
```

Output:

```javascript id="1tmg7s"
true;
```

---

# ES6 Classes: Are They Classical Inheritance?

Consider:

```javascript id="5h8dys"
class Animal {
  speak() {
    console.log("Animal sound");
  }
}

class Dog extends Animal {
  bark() {
    console.log("Bark");
  }
}
```

Looks like Java or C#.

However, internally JavaScript converts this into prototype-based behavior.

```javascript id="25t0te"
console.log(Dog.prototype.__proto__ === Animal.prototype);
// true
```

So ES6 classes are **syntactic sugar** over prototypes.

---

# Key Differences

| Feature              | Classical Inheritance | Prototypal Inheritance         |
| -------------------- | --------------------- | ------------------------------ |
| Based on             | Classes               | Objects                        |
| Inheritance between  | Classes               | Objects                        |
| Object creation      | Instance of class     | Clone/link from another object |
| Flexibility          | More rigid            | More dynamic                   |
| Runtime modification | Limited               | Easy                           |
| Method sharing       | Via class methods     | Via prototypes                 |
| Used by              | Java, C#, C++         | JavaScript                     |

---

# Runtime Flexibility

One major advantage of prototypal inheritance is that behavior can be changed dynamically.

### Classical Style

Typically fixed after compilation.

### Prototypal Style

```javascript id="r8bq7s"
const animal = {
  speak() {
    console.log("Animal sound");
  },
};

const dog = Object.create(animal);

animal.run = function () {
  console.log("Running");
};

dog.run();
```

Output:

```javascript id="g1bc7r"
Running;
```

The new method becomes immediately available through the prototype chain.

---

# Method Sharing Comparison

### Classical

```java
class Person {
    void greet() {}
}
```

Instances share methods defined in the class.

---

### Prototypal

```javascript id="sx4q1h"
function Person() {}

Person.prototype.greet = function () {
  console.log("Hello");
};
```

All instances share the same prototype method.

---

# Memory Efficiency

### Bad Approach

```javascript id="ewvp72"
function Person(name) {
  this.name = name;

  this.greet = function () {
    console.log("Hello");
  };
}
```

Each object gets its own copy of `greet`.

---

### Better Approach

```javascript id="74zzga"
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  console.log("Hello");
};
```

One shared method for all instances.

This is how prototypal inheritance achieves memory efficiency.

---

# Common Interview Question

### Does JavaScript support classical inheritance?

**Answer:**

Not natively.

JavaScript's inheritance model is fundamentally **prototypal**.

ES6 introduced `class` syntax:

```javascript id="y0vx1i"
class Person {}
```

but this is only syntactic sugar over prototypes and does not change the underlying inheritance mechanism.

---

# Real-World Analogy

### Classical Inheritance

```text
Blueprint (Class)
       ↓
 Create Object
       ↓
    Instance
```

Like building multiple houses from the same architectural blueprint.

---

### Prototypal Inheritance

```text
Object A
   ↓
Object B inherits from A
   ↓
Object C inherits from B
```

Like copying an existing house and modifying it instead of using a blueprint.

---

# Interview Summary

**Classical inheritance** creates objects from classes and defines inheritance between classes.

**Prototypal inheritance** allows objects to inherit directly from other objects through the prototype chain.

**JavaScript is fundamentally prototype-based**, and ES6 classes are simply a cleaner syntax built on top of the prototype system.

## Question 5. What are JavaScript classes? How are they different from functions?

## Short Answer

**JavaScript classes** are a syntactic sugar over prototype-based inheritance that provide a cleaner, more structured way to create objects and manage inheritance.

A **function** is a general-purpose callable block of code, while a **class** is specifically designed as a blueprint for creating objects using `new`, with built-in support for constructors, methods, and inheritance.

Under the hood, classes in JavaScript are still functions + prototypes.

---

# Detailed Explanation (Interview Level)

## 1. What is a JavaScript Class?

A class is a **template for creating objects** with shared structure and behavior.

### Example

```javascript id="c9gq2q"
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, ${this.name}`);
  }
}

const p1 = new Person("John");
p1.greet();
```

### Key idea:

- `Person` is a blueprint
- `p1` is an instance
- Methods are shared via prototype

---

## 2. Internally, Classes Are Functions

Even though it looks different, JavaScript classes are actually functions under the hood.

```javascript id="z5h3vn"
class Person {}
```

is essentially:

```javascript id="9bq2xk"
function Person() {}
```

But with additional behavior:

```javascript id="v8c1lm"
console.log(typeof Person); // "function"
```

---

## 3. Class Methods Are Stored in Prototype

```javascript id="q7m2dp"
class Person {
  greet() {
    console.log("Hello");
  }
}
```

Internally becomes:

```javascript id="p4n8xz"
function Person() {}

Person.prototype.greet = function () {
  console.log("Hello");
};
```

So all instances share the same method.

---

## 4. Key Features of Classes

### 1. Constructor

Used for initializing object properties.

```javascript id="t2k9rq"
class Car {
  constructor(brand) {
    this.brand = brand;
  }
}
```

---

### 2. Methods

```javascript id="f1m7xz"
class Car {
  drive() {
    console.log("Driving");
  }
}
```

---

### 3. Inheritance

```javascript id="k8d3wp"
class Animal {
  speak() {
    console.log("Animal sound");
  }
}

class Dog extends Animal {
  bark() {
    console.log("Bark");
  }
}
```

---

### 4. super keyword

```javascript id="x3p9lm"
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
}
```

---

## 5. Difference Between Class and Function

### A. Purpose

| Feature           | Function            | Class               |
| ----------------- | ------------------- | ------------------- |
| Purpose           | General logic       | Object blueprint    |
| Callable directly | Yes                 | No (must use `new`) |
| Used for OOP      | No (not inherently) | Yes                 |

---

### B. Instantiation

#### Function (constructor function style)

```javascript id="r2v8qp"
function Person(name) {
  this.name = name;
}

const p1 = new Person("John");
```

#### Class

```javascript id="s9k3lm"
class Person {
  constructor(name) {
    this.name = name;
  }
}

const p1 = new Person("John");
```

Both behave similarly, but class syntax is stricter.

---

### C. Hoisting Behavior

#### Functions

```javascript id="w8n4qx"
sayHi();

function sayHi() {
  console.log("Hi");
}
```

✔ Works (function hoisted)

---

#### Classes

```javascript id="d3p9lx"
const p = new Person(); // ❌ ReferenceError

class Person {}
```

❌ Classes are not usable before declaration (Temporal Dead Zone).

---

### D. Strict Mode Behavior

Classes are always in **strict mode**.

```javascript id="c6m8qz"
class Person {
  constructor() {
    this.name = name; // ReferenceError if name not defined
  }
}
```

---

### E. Prototype Handling

#### Function-based:

```javascript id="u7k2pw"
function Person() {}
Person.prototype.sayHi = function () {};
```

#### Class-based:

```javascript id="x1m9zd"
class Person {
  sayHi() {}
}
```

Both use prototype internally, but class syntax is cleaner.

---

## 6. Important Differences Summary

| Feature                     | Function              | Class                |
| --------------------------- | --------------------- | -------------------- |
| Can be called without `new` | Yes                   | No                   |
| Hoisting                    | Yes                   | No (TDZ)             |
| Strict mode default         | No                    | Yes                  |
| Syntax complexity           | Flexible but verbose  | Clean and structured |
| Inheritance support         | Manual via prototypes | Built-in (`extends`) |
| Intended usage              | General logic         | OOP design           |

---

## 7. Common Interview Trap

### Are classes a new type in JavaScript?

❌ No.

They are just **special functions with prototype-based behavior underneath**.

```javascript id="kq7x2m"
typeof class A {}; // "function"
```

---

## 8. Why Use Classes If Functions Already Work?

### Advantages of classes:

- Cleaner syntax for OOP
- Easier inheritance (`extends`)
- Better readability
- Standardized structure
- Easier to maintain large applications

---

## 9. Real-world Analogy

### Function (constructor function)

Like manually building each object step-by-step.

### Class

Like a blueprint factory:

- You define once
- Create multiple consistent objects

---

## 10. Interview Summary

**JavaScript classes are syntactic sugar over prototypes that provide a structured way to create objects, define methods, and implement inheritance.**

Key points:

- Classes are still functions internally
- Methods are stored in prototypes
- Must use `new`
- Not hoisted like functions
- Provide cleaner OOP syntax than constructor functions

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
