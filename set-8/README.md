# Set 8

| S.No. | Question                                                                                                                                                           |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [What are computed property names in ES6?](#question-1-what-are-computed-property-names-in-es6)                                                                    |
| 2.    | [Difference between object literal and constructor function](#question-2-difference-between-object-literal-and-constructor-function)                               |
| 3.    | [What are default parameters in functions?](#question-3-what-are-default-parameters-in-functions)                                                                  |
| 4.    | [How to swap two variables without using a third variable?](#question-4-how-to-swap-two-variables-without-using-a-third-variable)                                  |
| 5.    | [What is rest parameter vs arguments object?](#question-5-what-is-rest-parameter-vs-arguments-object)                                                              |
| 6.    | [Explain dynamic import in ES6](#question-6-explain-dynamic-import-in-es6)                                                                                         |
| 7.    | [What are JavaScript generators? How do they differ from regular functions?](#question-7-what-are-javascript-generators-how-do-they-differ-from-regular-functions) |
| 8.    | [Difference between for…of and forEach() for arrays](#question-8-difference-between-forof-and-foreach-for-arrays)                                                  |
| 9.    | [Explain async/await syntax](#question-9-explain-asyncawait-syntax)                                                                                                |
| 10.   | [Difference between Promise.all and Promise.race](#question-10-difference-between-promiseall-and-promiserace)                                                      |
| 11.   | [How to handle multiple async operations sequentially](#question-11-how-to-handle-multiple-async-operations-sequentially)                                          |
| 12.   | [Explain try…catch in async functions](#question-12-explain-trycatch-in-async-functions)                                                                           |
| 13.   | [Difference between setTimeout(fn, 0) and Promise.resolve().then(fn)](#question-13-difference-between-settimeoutfn-0-and-promiseresolvethenfn)                     |
| 14.   | [What are memory leaks in JavaScript? Examples?](#question-14-what-are-memory-leaks-in-javascript-examples)                                                        |
| 15.   | [Explain closures with practical examples](#question-15-explain-closures-with-practical-examples)                                                                  |
| 16.   | [How to use bind to create partially applied functions](#question-16-how-to-use-bind-to-create-partially-applied-functions)                                        |
| 17.   | [What is function currying? Example in JS](#question-17-what-is-function-currying-example-in-js)                                                                   |
| 18.   | [Explain optional parameters in functions](#question-18-explain-optional-parameters-in-functions)                                                                  |
| 19.   | [Difference between map() and reduce()](#question-19-difference-between-map-and-reduce)                                                                            |

## Question 1. What are computed property names in ES6?

Computed property names in ES6 allow you to define object property keys dynamically using expressions inside square brackets `[]`.

They are useful when the property name is not known until runtime or needs to be generated programmatically.

### Basic Syntax

```js
const key = "username";

const user = {
  [key]: "john_doe",
};

console.log(user.username); // john_doe
```

Here:

```js
[key];
```

is evaluated first, and its result becomes the property name.

### Why Computed Property Names Were Added

Before ES6, dynamic property assignment usually looked like this:

```js
const obj = {};
obj[key] = value;
```

ES6 made it possible to define dynamic keys directly inside object literals.

#### Example 1 — Dynamic Keys

```js
const field = "email";

const formData = {
  [field]: "test@example.com",
};

console.log(formData);
// { email: "test@example.com" }
```

#### Example 2 — Using Expressions

The expression inside `[]` can be any valid JavaScript expression.

```js
const prefix = "user";

const obj = {
  [prefix + "Name"]: "Alice",
  [prefix + "Age"]: 25,
};

console.log(obj);
// { userName: "Alice", userAge: 25 }
```

#### Example 3 — Using Functions

```js
function getKey(id) {
  return `item_${id}`;
}

const data = {
  [getKey(101)]: "Laptop",
};

console.log(data);
// { item_101: "Laptop" }
```

#### Example 4 — Computed Method Names

Computed property names also work with methods.

```js
const methodName = "sayHello";

const person = {
  [methodName]() {
    console.log("Hello!");
  },
};

person.sayHello();
```

### Real-World Use Cases

#### 1. Dynamic API Responses

```js
const apiField = "status";

const response = {
  [apiField]: "success",
};
```

#### 2. Redux / State Updates

```js
const field = "email";

const state = {
  name: "John",
  [field]: "john@example.com",
};
```

Very common in React state management.

#### 3. Configuration Objects

```js
const env = "development";

const config = {
  [`${env}_url`]: "localhost:3000",
};
```

### Difference Between Normal and Computed Properties

#### Normal Property

```js
const obj = {
  name: "John",
};
```

Property key is fixed.

#### Computed Property

```js
const key = "name";

const obj = {
  [key]: "John",
};
```

Property key is evaluated dynamically.

### Important Points

#### 1. Expressions Are Evaluated at Runtime

```js
const a = "x";

const obj = {
  [a + 1]: 10,
};

console.log(obj); // { x1: 10 }
```

#### 2. Without `[]`, It Becomes Literal Text

```js
const key = "age";

const obj1 = {
  key: 25,
};

console.log(obj1.key); // 25
```

This creates a property literally named `"key"`.

Correct dynamic version:

```js
const obj2 = {
  [key]: 25,
};

console.log(obj2.age); // 25
```

#### 3. Duplicate Computed Keys Override Earlier Ones

```js
const obj = {
  ["a"]: 1,
  ["a"]: 2,
};

console.log(obj.a); // 2
```

Last assignment wins.

### Relation to Other ES6 Features

Computed property names are commonly used with:

- Object destructuring
- Spread operator
- Template literals
- Dynamic object creation
- Maps and reducers
- React state updates

Example:

```js
const key = "theme";

const settings = {
  darkMode: true,
  [key]: "dark",
};
```

### Common Pitfalls

#### Pitfall 1 — Forgetting Brackets

```js
const key = "color";

const obj = {
  key: "red",
};

console.log(obj.color); // undefined
```

#### Pitfall 2 — Non-String Keys

Object keys are automatically converted to strings.

```js
const obj = {
  [1 + 2]: "three",
};

console.log(obj);
// { "3": "three" }
```

### Interview-Friendly Definition

> Computed property names in ES6 allow object property keys to be created dynamically using expressions inside square brackets `[]`. The expression is evaluated at runtime, and its result becomes the property name. They are commonly used for dynamic object creation and state management.

## Question 2. Difference between object literal and constructor function

## Direct Answer

Both **object literals** and **constructor functions** are used to create objects in JavaScript, but they serve different purposes:

- **Object Literal (`{}`)**: Best for creating a single object quickly.
- **Constructor Function (`new`)**: Best for creating multiple objects with the same structure and behavior.

---

# 1. Object Literal

An object literal creates a single object directly.

```js
const person = {
  name: "John",
  age: 30,
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  },
};

person.greet();
```

### Characteristics

- Simple and concise.
- Creates one object instance.
- No need for `new`.
- Suitable for configuration objects, JSON-like data, and one-off objects.

### Memory Consideration

If you create many similar objects using literals, methods get duplicated:

```js
const user1 = {
  name: "John",
  greet() {
    console.log("Hello");
  },
};

const user2 = {
  name: "Alice",
  greet() {
    console.log("Hello");
  },
};
```

Each object has its own copy of `greet`.

---

# 2. Constructor Function

A constructor function acts as a blueprint for creating objects.

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const p1 = new Person("John", 30);
const p2 = new Person("Alice", 25);

console.log(p1);
console.log(p2);
```

### What `new` Does

```js
const p1 = new Person("John", 30);
```

Internally:

1. Creates a new empty object.
2. Sets its prototype to `Person.prototype`.
3. Binds `this` to the new object.
4. Executes the constructor.
5. Returns the object.

---

# Sharing Methods via Prototype

Instead of creating methods per object:

```js
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  console.log(`Hello, I'm ${this.name}`);
};
```

Now all instances share the same method:

```js
const p1 = new Person("John");
const p2 = new Person("Alice");

p1.greet();
p2.greet();
```

This is more memory-efficient.

---

# Comparison Table

| Feature           | Object Literal        | Constructor Function     |
| ----------------- | --------------------- | ------------------------ |
| Syntax            | `{}`                  | `function + new`         |
| Purpose           | Single object         | Multiple similar objects |
| Reusability       | Low                   | High                     |
| Prototype support | Uses Object.prototype | Custom prototype chain   |
| Memory efficiency | Methods duplicated    | Methods can be shared    |
| Requires `new`    | No                    | Yes                      |
| OOP suitability   | Limited               | Better                   |

---

# Example: Same Problem Solved Both Ways

### Object Literal

```js
const car1 = {
  brand: "Toyota",
  drive() {
    console.log("Driving...");
  },
};

const car2 = {
  brand: "Honda",
  drive() {
    console.log("Driving...");
  },
};
```

Method duplicated in each object.

---

### Constructor Function

```js
function Car(brand) {
  this.brand = brand;
}

Car.prototype.drive = function () {
  console.log("Driving...");
};

const car1 = new Car("Toyota");
const car2 = new Car("Honda");
```

Single shared `drive` method.

---

# Modern Alternative: ES6 Classes

Constructor functions are the foundation of ES6 classes.

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
}
```

Equivalent to:

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.greet = function () {
  console.log(`Hello, I'm ${this.name}`);
};
```

Classes provide cleaner syntax but still use JavaScript's prototype system internally.

---

# Common Interview Questions

### Q: Can an object literal be used as a constructor?

No.

```js
const person = {
  name: "John",
};

new person(); // TypeError
```

Only functions (or classes) can be used with `new`.

---

### Q: What happens if you forget `new`?

```js
function Person(name) {
  this.name = name;
}

const p = Person("John");
```

In non-strict mode, `this` may refer to the global object, causing bugs.

A safer pattern:

```js
function Person(name) {
  if (!(this instanceof Person)) {
    return new Person(name);
  }

  this.name = name;
}
```

---

# Interview-Friendly Summary

> An object literal creates a single object directly using `{}` and is ideal for one-off objects. A constructor function acts as a blueprint for creating multiple similar objects using `new`. Constructor functions support prototype-based method sharing, making them more reusable and memory-efficient for object-oriented programming. ES6 classes are syntactic sugar over constructor functions and prototypes.

## Question 3. What are default parameters in functions?

## Direct Answer

**Default parameters** in JavaScript allow you to assign a default value to a function parameter if no argument (or `undefined`) is passed.

```js
function greet(name = "Guest") {
  return `Hello, ${name}`;
}

greet(); // "Hello, Guest"
greet("John"); // "Hello, John"
```

---

# Detailed Interview Explanation

Before ES6, JavaScript did not have built-in default parameters. Developers had to manually handle missing arguments.

Default parameters solve this cleanly by allowing parameter initialization directly in the function signature.

---

## 1. Basic Syntax

```js
function multiply(a = 1, b = 1) {
  return a * b;
}

console.log(multiply()); // 1
console.log(multiply(5)); // 5
console.log(multiply(5, 2)); // 10
```

If a value is not provided, the default is used.

---

## 2. Important Behavior Rules

### Rule 1: Only `undefined` triggers default value

```js
function test(a = 10) {
  return a;
}

test(undefined); // 10
test(null); // null (NOT defaulted)
test(0); // 0
test(""); // ""
```

👉 Only `undefined` activates the default.

---

### Rule 2: Default parameters can be expressions

```js
function getId() {
  return 100;
}

function user(id = getId()) {
  return id;
}

console.log(user()); // 100
```

The expression is evaluated only when needed.

---

### Rule 3: Parameters are evaluated left to right

```js
function example(a = 1, b = a + 1) {
  return [a, b];
}

console.log(example()); // [1, 2]
```

But reverse reference is NOT allowed:

```js
function example(a = b, b = 2) {
  return [a, b];
}

// ReferenceError: Cannot access 'b' before initialization
```

---

## 3. Default Parameters vs Old Pattern

### Before ES6

```js
function greet(name) {
  name = name || "Guest";
  return `Hello, ${name}`;
}
```

### Problem with old approach:

```js
greet(""); // "Hello, Guest" ❌ wrong behavior
```

Because `""` is falsy.

---

### With default parameters (correct)

```js
function greet(name = "Guest") {
  return `Hello, ${name}`;
}

greet(""); // "Hello, " ✅ correct
```

---

## 4. Using Objects and Destructuring Defaults

Very common in real-world APIs.

```js
function createUser({ name = "Anonymous", age = 18 } = {}) {
  return { name, age };
}

console.log(createUser({ name: "John" }));
// { name: "John", age: 18 }

console.log(createUser());
// { name: "Anonymous", age: 18 }
```

👉 `= {}` ensures function doesn’t break when no argument is passed.

---

## 5. Default Parameters + Function Scope

Default parameters create their own scope.

```js
let x = 10;

function test(y = x + 1) {
  let x = 50;
  return y;
}

console.log(test()); // 11
```

👉 Default values do NOT use inner function scope.

---

## 6. Common Pitfalls

### Pitfall 1: Using `||` incorrectly

```js
function count(n) {
  n = n || 10;
  return n;
}

count(0); // 10 ❌ unexpected
```

Fixed:

```js
function count(n = 10) {
  return n;
}
```

---

### Pitfall 2: Reference errors in ordering

```js
function test(a = b, b = 2) {
  return a + b;
}
```

❌ `b` is not initialized yet.

---

### Pitfall 3: Forgetting default for object parameter

```js
function print({ name }) {
  console.log(name);
}

print(); // ❌ Error
```

Fix:

```js
function print({ name } = {}) {
  console.log(name);
}
```

---

## 7. Real-World Use Cases

### API handlers

```js
function fetchData(url, method = "GET") {
  console.log(url, method);
}
```

---

### Utility functions

```js
function delay(ms = 1000) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}
```

---

### React-style props

```js
function Button({ label = "Click", disabled = false } = {}) {
  return { label, disabled };
}
```

---

## 8. Interview Summary

> Default parameters in JavaScript allow function parameters to have fallback values when arguments are `undefined`. They improve readability, eliminate the need for manual checks like `||`, support expressions, and are evaluated only when needed. Unlike falsy-based defaults, they correctly distinguish between `undefined`, `null`, `0`, and empty strings, making them safer and more predictable in modern JavaScript development.

## Question 4. How to swap two variables without using a third variable?

## Question 5. What is rest parameter vs arguments object?

## Question 6. Explain dynamic import in ES6

## Question 7. What are JavaScript generators? How do they differ from regular functions?

## Question 8. Difference between for…of and forEach() for arrays

## Question 9. Explain async/await syntax

## Question 10. Difference between Promise.all and Promise.race

## Question 11. How to handle multiple async operations sequentially

## Question 12. Explain try…catch in async functions

## Question 13. Difference between setTimeout(fn, 0) and Promise.resolve().then(fn)

## Question 14. What are memory leaks in JavaScript? Examples?

## Question 15. Explain closures with practical examples

## Question 16. How to use bind to create partially applied functions

## Question 17. What is function currying? Example in JS

## Question 18. Explain optional parameters in functions

## Question 19. Difference between map() and reduce()
