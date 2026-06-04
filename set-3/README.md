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

## Short Answer

**Instance methods** belong to objects created from a class and are called on instances.
**Static methods** belong to the class itself and are called on the class, not on instances.

---

# Detailed Explanation (Interview Level)

In JavaScript classes, methods can be defined in two ways:

1. **Instance methods** → accessible via objects (instances)
2. **Static methods** → accessible via the class itself

---

# 1. Instance Methods

## Definition

Instance methods are methods that operate on **individual objects created using `new`**.

### Example

```javascript id="a1b2c3"
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const p1 = new Person("John");

p1.greet(); // Hello, my name is John
```

---

## How it works internally

Instance methods are stored on the **prototype**:

```javascript id="d4e5f6"
Person.prototype.greet = function () {
  console.log(this.name);
};
```

So all instances share the same method.

---

## Key points

- Called on **objects (instances)**
- Can access instance properties via `this`
- Shared across all instances (memory efficient)

---

# 2. Static Methods

## Definition

Static methods belong to the **class itself**, not to instances.

### Example

```javascript id="g7h8i9"
class MathUtils {
  static add(a, b) {
    return a + b;
  }
}

console.log(MathUtils.add(2, 3)); // 5
```

---

## Important rule

❌ You cannot call static methods on instances:

```javascript id="j1k2l3"
const obj = new MathUtils();

obj.add(2, 3); // ❌ TypeError
```

✔ Must call via class:

```javascript id="m4n5o6"
MathUtils.add(2, 3);
```

---

## How it works internally

Static methods are attached directly to the constructor function:

```javascript id="p7q8r9"
MathUtils.add = function (a, b) {
  return a + b;
};
```

They are **not part of the prototype chain**.

---

# 3. Key Differences

| Feature                | Instance Method      | Static Method                                 |
| ---------------------- | -------------------- | --------------------------------------------- |
| Belongs to             | Object instance      | Class itself                                  |
| Called using           | `object.method()`    | `Class.method()`                              |
| Uses `this`            | Refers to instance   | Refers to class (or undefined in strict mode) |
| Memory sharing         | Shared via prototype | Single copy on class                          |
| Inherited by instances | Yes                  | No                                            |

---

# 4. Real-World Analogy

### Instance Method

Like actions performed by a specific person:

```text id="s1t2u3"
John can speak()
Mary can speak()
```

Each person performs the action.

---

### Static Method

Like a utility service:

```text id="v4w5x6"
MathUtils.add()
MathUtils.multiply()
```

Not tied to any individual object.

---

# 5. When to Use Instance Methods

Use instance methods when behavior depends on object state.

```javascript id="y7z8a9"
class User {
  constructor(name) {
    this.name = name;
  }

  getName() {
    return this.name;
  }
}
```

Each user has its own name.

---

# 6. When to Use Static Methods

Use static methods for **utility or helper logic** that does not depend on instance data.

### Example: Factory method

```javascript id="b1c2d3"
class User {
  constructor(name) {
    this.name = name;
  }

  static createAnonymous() {
    return new User("Anonymous");
  }
}

const user = User.createAnonymous();
console.log(user.name); // Anonymous
```

---

### Example: Utility functions

```javascript id="e4f5g6"
class StringUtils {
  static toUpper(str) {
    return str.toUpperCase();
  }
}

console.log(StringUtils.toUpper("hello"));
```

---

# 7. Common Interview Pitfall

## Static method cannot access instance properties

```javascript id="h7i8j9"
class Person {
  constructor(name) {
    this.name = name;
  }

  static greet() {
    console.log(this.name);
  }
}

Person.greet(); // undefined (or class context)
```

Because `this` refers to the class, not an instance.

---

# 8. Prototype Relationship Insight

```javascript id="k1l2m3"
class A {
  instanceMethod() {}
  static staticMethod() {}
}

console.log(A.prototype.instanceMethod); // function
console.log(A.staticMethod); // function
```

- Instance methods → `A.prototype`
- Static methods → `A`

---

# 9. Interview Summary

**Instance methods** operate on object instances and use instance data via `this`.
**Static methods** belong to the class itself and are used for utility or class-level operations.

---

## One-liner (Interview-ready)

👉 _“Instance methods belong to objects created from a class and operate on instance data, while static methods belong to the class itself and are used for utility or class-level functionality, not tied to any specific instance.”_

## Question 7. Explain modules in JavaScript. How to export and import?

## Short Answer

**JavaScript modules** are reusable pieces of code that live in separate files and communicate with each other using `export` and `import`. They help organize code, avoid global scope pollution, and enable dependency management.

There are two main systems:

- **ES Modules (ESM)** → modern standard (`import` / `export`)
- **CommonJS (CJS)** → older Node.js system (`require` / `module.exports`)

---

# 1. What are Modules?

A module is simply a **file that encapsulates code** (variables, functions, classes) and exposes only what it wants to share.

### Without modules (bad practice)

```javascript id="a1b2c3"
var name = "John";

function greet() {
  console.log(name);
}
```

Everything is in global scope → risk of conflicts.

---

### With modules (good practice)

Each file has its own scope:

```javascript id="d4e5f6"
// user.js
const name = "John";

export function greet() {
  console.log(name);
}
```

---

# 2. ES Modules (Modern JavaScript)

ES Modules use:

- `export` → to expose code
- `import` → to use code

They are supported in modern browsers and Node.js (with `"type": "module"`).

---

## A. Named Exports

### Export

```javascript id="g1h2i3"
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
```

### Import

```javascript id="j4k5l6"
import { add, subtract } from "./math.js";

console.log(add(2, 3)); // 5
```

---

## B. Default Export

A module can have **one default export**.

### Export

```javascript id="m7n8o9"
// user.js
export default function greet(name) {
  return `Hello ${name}`;
}
```

### Import

```javascript id="p1q2r3"
import greet from "./user.js";

console.log(greet("John"));
```

👉 No curly braces for default imports.

---

## C. Mixing Default and Named Exports

```javascript id="s4t5u6"
// utils.js
export const sum = (a, b) => a + b;

export default function multiply(a, b) {
  return a * b;
}
```

### Import

```javascript id="v7w8x9"
import multiply, { sum } from "./utils.js";
```

---

## D. Renaming Imports/Exports

### Rename during import

```javascript id="a9b8c7"
import { add as sum } from "./math.js";

sum(2, 3);
```

### Rename during export

```javascript id="d6e5f4"
export { add as sum };
```

---

## E. Import Everything

```javascript id="g3h2i1"
import * as math from "./math.js";

console.log(math.add(2, 3));
```

---

# 3. CommonJS Modules (Node.js Old System)

Used in older Node.js applications.

### Export

```javascript id="j9k8l7"
// math.js
function add(a, b) {
  return a + b;
}

module.exports = { add };
```

### Import

```javascript id="m6n5o4"
const math = require("./math");

console.log(math.add(2, 3));
```

---

# 4. Key Differences: ES Modules vs CommonJS

| Feature         | ES Modules                | CommonJS                 |
| --------------- | ------------------------- | ------------------------ |
| Syntax          | `import/export`           | `require/module.exports` |
| Loading         | Static (compile-time)     | Dynamic (runtime)        |
| Async support   | Yes (better for browsers) | No                       |
| Tree shaking    | Supported                 | Not supported            |
| Browser support | Native                    | Not native               |
| Node.js usage   | Modern default            | Legacy                   |

---

# 5. Module Scope

Each module has its own scope:

```javascript id="p3q2r1"
// a.js
let count = 0;
```

```javascript id="s1t2u3"
// b.js
console.log(count); // ❌ ReferenceError
```

No global pollution.

---

# 6. How Modules Work Internally

When you import a module:

1. The module is executed once
2. Its exports are cached
3. All imports share the same instance

### Example

```javascript id="u4v5w6"
// counter.js
export let count = 0;

export function increment() {
  count++;
}
```

```javascript id="x7y8z9"
import { count, increment } from "./counter.js";

increment();
console.log(count); // 1
```

All imports share the same memory reference.

---

# 7. Important Concepts

## A. Live Bindings (ESM Feature)

Imports are **live references**, not copies:

```javascript id="a2b3c4"
export let value = 10;

export function update() {
  value++;
}
```

```javascript id="d5e6f7"
import { value, update } from "./mod.js";

update();
console.log(value); // 11
```

---

## B. Top-Level Scope Only

You cannot conditionally import like this:

```javascript id="g8h9i1"
if (true) {
  import { add } from "./math.js"; // ❌ Not allowed
}
```

Instead use dynamic import:

```javascript id="j2k3l4"
if (true) {
  const module = await import("./math.js");
}
```

---

# 8. Script Type Requirement (Browser)

To use ES Modules in browser:

```html id="m5n6o7"
<script type="module" src="app.js"></script>
```

Without `type="module"`, imports will fail.

---

# 9. Common Interview Pitfalls

### 1. Forgetting file extension (in browser ESM)

```javascript
import { add } from "./math"; // ❌ often fails in browsers
```

---

### 2. Default vs Named confusion

```javascript
import add from "./math.js"; // works only for default export
import { add } from "./math.js"; // named export
```

---

### 3. Modules are singletons

Same module is not re-executed on multiple imports.

---

# 10. Interview Summary

👉 JavaScript modules allow code separation and reuse using `export` and `import`.

- ES Modules are the modern standard
- Support named and default exports
- Provide scope isolation
- Enable tree shaking and better performance
- CommonJS is older Node.js module system

---

## One-liner (Interview-ready)

👉 _“JavaScript modules are independent files that encapsulate code and expose functionality using export and import; ES Modules provide static, standardized module loading with support for named and default exports, replacing older CommonJS in modern JavaScript development.”_

## Question 8. Explain modules in JavaScript. How to export and import?

## Short Answer

**JavaScript modules** are reusable pieces of code that live in separate files and communicate with each other using `export` and `import`. They help organize code, avoid global scope pollution, and enable dependency management.

There are two main systems:

- **ES Modules (ESM)** → modern standard (`import` / `export`)
- **CommonJS (CJS)** → older Node.js system (`require` / `module.exports`)

---

# 1. What are Modules?

A module is simply a **file that encapsulates code** (variables, functions, classes) and exposes only what it wants to share.

### Without modules (bad practice)

```javascript id="a1b2c3"
var name = "John";

function greet() {
  console.log(name);
}
```

Everything is in global scope → risk of conflicts.

---

### With modules (good practice)

Each file has its own scope:

```javascript id="d4e5f6"
// user.js
const name = "John";

export function greet() {
  console.log(name);
}
```

---

# 2. ES Modules (Modern JavaScript)

ES Modules use:

- `export` → to expose code
- `import` → to use code

They are supported in modern browsers and Node.js (with `"type": "module"`).

---

## A. Named Exports

### Export

```javascript id="g1h2i3"
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
```

### Import

```javascript id="j4k5l6"
import { add, subtract } from "./math.js";

console.log(add(2, 3)); // 5
```

---

## B. Default Export

A module can have **one default export**.

### Export

```javascript id="m7n8o9"
// user.js
export default function greet(name) {
  return `Hello ${name}`;
}
```

### Import

```javascript id="p1q2r3"
import greet from "./user.js";

console.log(greet("John"));
```

👉 No curly braces for default imports.

---

## C. Mixing Default and Named Exports

```javascript id="s4t5u6"
// utils.js
export const sum = (a, b) => a + b;

export default function multiply(a, b) {
  return a * b;
}
```

### Import

```javascript id="v7w8x9"
import multiply, { sum } from "./utils.js";
```

---

## D. Renaming Imports/Exports

### Rename during import

```javascript id="a9b8c7"
import { add as sum } from "./math.js";

sum(2, 3);
```

### Rename during export

```javascript id="d6e5f4"
export { add as sum };
```

---

## E. Import Everything

```javascript id="g3h2i1"
import * as math from "./math.js";

console.log(math.add(2, 3));
```

---

# 3. CommonJS Modules (Node.js Old System)

Used in older Node.js applications.

### Export

```javascript id="j9k8l7"
// math.js
function add(a, b) {
  return a + b;
}

module.exports = { add };
```

### Import

```javascript id="m6n5o4"
const math = require("./math");

console.log(math.add(2, 3));
```

---

# 4. Key Differences: ES Modules vs CommonJS

| Feature         | ES Modules                | CommonJS                 |
| --------------- | ------------------------- | ------------------------ |
| Syntax          | `import/export`           | `require/module.exports` |
| Loading         | Static (compile-time)     | Dynamic (runtime)        |
| Async support   | Yes (better for browsers) | No                       |
| Tree shaking    | Supported                 | Not supported            |
| Browser support | Native                    | Not native               |
| Node.js usage   | Modern default            | Legacy                   |

---

# 5. Module Scope

Each module has its own scope:

```javascript id="p3q2r1"
// a.js
let count = 0;
```

```javascript id="s1t2u3"
// b.js
console.log(count); // ❌ ReferenceError
```

No global pollution.

---

# 6. How Modules Work Internally

When you import a module:

1. The module is executed once
2. Its exports are cached
3. All imports share the same instance

### Example

```javascript id="u4v5w6"
// counter.js
export let count = 0;

export function increment() {
  count++;
}
```

```javascript id="x7y8z9"
import { count, increment } from "./counter.js";

increment();
console.log(count); // 1
```

All imports share the same memory reference.

---

# 7. Important Concepts

## A. Live Bindings (ESM Feature)

Imports are **live references**, not copies:

```javascript id="a2b3c4"
export let value = 10;

export function update() {
  value++;
}
```

```javascript id="d5e6f7"
import { value, update } from "./mod.js";

update();
console.log(value); // 11
```

---

## B. Top-Level Scope Only

You cannot conditionally import like this:

```javascript id="g8h9i1"
if (true) {
  import { add } from "./math.js"; // ❌ Not allowed
}
```

Instead use dynamic import:

```javascript id="j2k3l4"
if (true) {
  const module = await import("./math.js");
}
```

---

# 8. Script Type Requirement (Browser)

To use ES Modules in browser:

```html id="m5n6o7"
<script type="module" src="app.js"></script>
```

Without `type="module"`, imports will fail.

---

# 9. Common Interview Pitfalls

### 1. Forgetting file extension (in browser ESM)

```javascript
import { add } from "./math"; // ❌ often fails in browsers
```

---

### 2. Default vs Named confusion

```javascript
import add from "./math.js"; // works only for default export
import { add } from "./math.js"; // named export
```

---

### 3. Modules are singletons

Same module is not re-executed on multiple imports.

---

# 10. Interview Summary

👉 JavaScript modules allow code separation and reuse using `export` and `import`.

- ES Modules are the modern standard
- Support named and default exports
- Provide scope isolation
- Enable tree shaking and better performance
- CommonJS is older Node.js module system

---

## One-liner (Interview-ready)

👉 _“JavaScript modules are independent files that encapsulate code and expose functionality using export and import; ES Modules provide static, standardized module loading with support for named and default exports, replacing older CommonJS in modern JavaScript development.”_

## Question 9. Difference between synchronous and asynchronous functions

## Short Answer

A **synchronous function** executes code **line by line and blocks further execution until it completes**.
An **asynchronous function** executes code **without blocking the main thread**, allowing other operations to continue while it completes in the background.

---

# 1. Synchronous Functions

## Definition

Synchronous code runs in a **sequential, blocking manner**. Each line waits for the previous one to finish.

---

## Example

```javascript id="a1b2c3"
console.log("Start");

function syncTask() {
  console.log("Task executing...");
}

syncTask();

console.log("End");
```

### Output:

```
Start
Task executing...
End
```

---

## Key Characteristics

- Executes **one at a time**
- Blocks further execution until completion
- Easier to understand and debug
- Can cause performance issues if tasks are long-running

---

## Problem with Sync Code (Blocking)

```javascript id="d4e5f6"
function heavyTask() {
  let start = Date.now();
  while (Date.now() - start < 5000) {
    // blocking for 5 seconds
  }
  console.log("Heavy task done");
}

console.log("Before");
heavyTask();
console.log("After");
```

### Output:

```
Before
(5 seconds delay)
Heavy task done
After
```

👉 The UI or program is blocked during execution.

---

# 2. Asynchronous Functions

## Definition

Asynchronous functions allow code to **start a task and continue execution without waiting for it to finish**.

They are commonly used for:

- API calls
- File operations
- Timers
- Database queries

---

## Example with setTimeout

```javascript id="g7h8i9"
console.log("Start");

setTimeout(() => {
  console.log("Async Task");
}, 2000);

console.log("End");
```

### Output:

```
Start
End
Async Task
```

---

## Key Characteristics

- Non-blocking execution
- Uses callbacks, promises, or async/await
- Improves performance and responsiveness
- Handled via **event loop**

---

# 3. How Async Works (Event Loop Concept)

```text id="j1k2l3"
Call Stack → Web APIs → Callback Queue → Event Loop → Call Stack
```

Example:

```javascript id="m4n5o6"
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

console.log("3");
```

### Output:

```
1
3
2
```

Even with `0ms`, async code runs later.

---

# 4. Promises (Async Modern Approach)

```javascript id="p7q8r9"
const fetchData = new Promise((resolve) => {
  setTimeout(() => {
    resolve("Data loaded");
  }, 2000);
});

fetchData.then((data) => {
  console.log(data);
});
```

---

# 5. Async/Await (Clean Syntax)

```javascript id="s1t2u3"
async function getData() {
  console.log("Fetching...");

  const data = await new Promise((resolve) =>
    setTimeout(() => resolve("Data received"), 2000),
  );

  console.log(data);
}

getData();
```

---

# 6. Synchronous vs Asynchronous Comparison

| Feature     | Synchronous            | Asynchronous        |
| ----------- | ---------------------- | ------------------- |
| Execution   | Blocking               | Non-blocking        |
| Flow        | Sequential             | Concurrent          |
| Performance | Slower for heavy tasks | Better scalability  |
| Complexity  | Simple                 | More complex        |
| UI impact   | Freezes UI             | Keeps UI responsive |
| Examples    | loops, calculations    | API calls, timers   |

---

# 7. Real-World Analogy

### Synchronous (Restaurant with one chef)

```text id="v1w2x3"
Order 1 → Cook → Serve → Order 2 → Cook → Serve
```

Everyone waits in line.

---

### Asynchronous (Multiple chefs / system)

```text id="y4z5a6"
Order 1 → cooking (background)
Order 2 → cooking (background)
Serve when ready
```

No waiting for others.

---

# 8. Common Interview Pitfalls

## 1. Thinking async means “parallel execution”

❌ Incorrect
JavaScript is still **single-threaded**.

✔ Correct
Async means **non-blocking**, not parallel (unless using workers).

---

## 2. setTimeout(0) runs immediately

```javascript id="b1c2d3"
setTimeout(() => console.log("A"), 0);
console.log("B");
```

Output:

```
B
A
```

Because async callbacks go to the event loop.

---

## 3. Async functions always return Promises

```javascript id="e4f5g6"
async function test() {
  return 10;
}

console.log(test()); // Promise { 10 }
```

---

# 9. When to Use Each

## Use synchronous when:

- Simple calculations
- Small, fast operations
- No waiting involved

## Use asynchronous when:

- API calls
- Database operations
- File reading/writing
- Timers and delays
- User input events

---

# 10. Interview Summary

👉 **Synchronous functions block execution and run sequentially, while asynchronous functions allow non-blocking execution using callbacks, promises, or async/await, enabling JavaScript to handle long-running operations efficiently.**

---

## One-liner (Interview-ready)

👉 _“Synchronous functions execute line by line and block further execution, whereas asynchronous functions allow the program to continue running while operations complete in the background, typically using callbacks, promises, or async/await.”_

## Question 10. Explain callback functions with examples

## Short Answer

A **callback function** is a function that is passed as an argument to another function and is executed later, either after some operation completes or when a specific event occurs.

---

# 1. What is a Callback Function?

In JavaScript, functions are **first-class citizens**, meaning they can:

- Be passed as arguments
- Be returned from other functions
- Be stored in variables

A **callback** is simply a function passed into another function to be “called back” later.

---

# 2. Basic Example

```javascript id="a1b2c3"
function greet(name, callback) {
  console.log("Hello " + name);
  callback();
}

function sayBye() {
  console.log("Goodbye!");
}

greet("John", sayBye);
```

### Output:

```
Hello John
Goodbye!
```

👉 Here, `sayBye` is a callback function.

---

# 3. Why Callbacks are Used

Callbacks are mainly used for:

- Asynchronous operations
- Event handling
- Code reusability
- Custom behavior injection

---

# 4. Synchronous Callback Example

Even synchronous functions can use callbacks.

```javascript id="d4e5f6"
function calculate(a, b, callback) {
  return callback(a, b);
}

function add(x, y) {
  return x + y;
}

function multiply(x, y) {
  return x * y;
}

console.log(calculate(2, 3, add)); // 5
console.log(calculate(2, 3, multiply)); // 6
```

👉 Same function behaves differently depending on callback.

---

# 5. Asynchronous Callback Example

## setTimeout Example

```javascript id="g7h8i9"
console.log("Start");

setTimeout(() => {
  console.log("This is a callback after 2 seconds");
}, 2000);

console.log("End");
```

### Output:

```
Start
End
This is a callback after 2 seconds
```

👉 The function passed to `setTimeout` is a callback executed later.

---

# 6. Real-world Async Callback (API Simulation)

```javascript id="j1k2l3"
function fetchData(callback) {
  setTimeout(() => {
    const data = "User Data Loaded";
    callback(data);
  }, 2000);
}

fetchData(function (result) {
  console.log(result);
});
```

### Output:

```
User Data Loaded
```

---

# 7. Event Listener Callback

```javascript id="m4n5o6"
document.addEventListener("click", function () {
  console.log("Document clicked!");
});
```

👉 Function runs only when the event occurs.

---

# 8. Callback Hell (Important Interview Topic)

When callbacks are nested too deeply, code becomes hard to read and maintain.

```javascript id="p7q8r9"
doTask1(function (result1) {
  doTask2(result1, function (result2) {
    doTask3(result2, function (result3) {
      doTask4(result3, function (result4) {
        console.log(result4);
      });
    });
  });
});
```

### Problems:

- Hard to read
- Hard to debug
- Difficult to maintain

👉 This led to Promises and async/await.

---

# 9. Callback vs Promise (Comparison)

| Feature        | Callback        | Promise              |
| -------------- | --------------- | -------------------- |
| Readability    | Poor in nesting | Better               |
| Error handling | Difficult       | Structured (`catch`) |
| Chaining       | Hard            | Easy                 |
| Modern usage   | Less preferred  | Preferred            |

---

# 10. Error Handling in Callbacks

```javascript id="s1t2u3"
function fetchData(callback) {
  const error = false;

  if (error) {
    callback("Error occurred", null);
  } else {
    callback(null, "Data loaded");
  }
}

fetchData(function (err, data) {
  if (err) {
    console.log(err);
  } else {
    console.log(data);
  }
});
```

👉 Node.js often uses this **error-first callback pattern**.

---

# 11. Real-World Analogy

### Callback = “Call me when done”

Imagine ordering food:

1. You place an order
2. You give your phone number (callback)
3. Restaurant calls you when food is ready

---

# 12. Common Interview Pitfalls

### 1. Thinking callbacks are only async

❌ Wrong
Callbacks can be both synchronous and asynchronous.

---

### 2. Forgetting execution timing

```javascript id="v3w4x5"
function test(callback) {
  console.log("Before");
  callback();
  console.log("After");
}

test(() => console.log("Inside callback"));
```

### Output:

```
Before
Inside callback
After
```

---

### 3. Callback is not invoked immediately

It is executed **only when called explicitly** or by async API.

---

# 13. Interview Summary

👉 A callback function is a function passed into another function as an argument and executed later, either synchronously or asynchronously, to allow flexible and reusable code execution.

---

## One-liner (Interview-ready)

👉 _“A callback function is a function passed as an argument to another function, which gets executed later either after a task completes or when an event occurs, enabling asynchronous and event-driven programming in JavaScript.”_

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
