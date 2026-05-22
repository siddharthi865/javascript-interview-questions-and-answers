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

## Question 3. Explain prototypes and prototype chain

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
