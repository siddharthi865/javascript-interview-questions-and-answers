# Set 6

| S.No. | Question                                                                                                                                                    |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is a JavaScript engine? Give examples](#question-1-what-is-a-javascript-engine-give-examples)                                                         |
| 2.    | [What is the difference between client-side and server-side JavaScript?](#question-2-what-is-the-difference-between-client-side-and-server-side-javascript) |
| 3.    | [How does typeof operator work? Give examples](#question-3-how-does-typeof-operator-work-give-examples)                                                     |
| 4.    | [Explain the difference between undefined and undeclared variables](#question-4-explain-the-difference-between-undefined-and-undeclared-variables)          |
| 5.    | [What is the difference between primitive and reference data types?](#question-5-what-is-the-difference-between-primitive-and-reference-data-types)         |
| 6.    | [How does Infinity work in JavaScript?](#question-6-how-does-infinity-work-in-javascript)                                                                   |
| 7.    | [Difference between mutable and immutable objects](#question-7-difference-between-mutable-and-immutable-objects)                                            |
| 8.    | [What is the difference between a function and a method?](#question-8-what-is-the-difference-between-a-function-and-a-method)                               |
| 9.    | [How to check if a variable is an array?](#question-9-how-to-check-if-a-variable-is-an-array)                                                               |
| 10.   | [Difference between isArray() and instanceof Array](#question-10-difference-between-isarray-and-instanceof-array)                                           |
| 11.   | [Explain string methods like split(), join(), includes()](#question-11-explain-string-methods-like-split-join-includes)                                     |
| 12.   | [What is the difference between substr(), substring(), and slice()?](#question-12-what-is-the-difference-between-substr-substring-and-slice)                |
| 13.   | [Difference between push() and concat() in arrays](#question-13-difference-between-push-and-concat-in-arrays)                                               |
| 14.   | [How to reverse an array in JavaScript?](#question-14-how-to-reverse-an-array-in-javascript)                                                                |
| 15.   | [What is the difference between pop() and shift()?](#question-15-what-is-the-difference-between-pop-and-shift)                                              |
| 16.   | [How to find the largest number in an array?](#question-16-how-to-find-the-largest-number-in-an-array)                                                      |
| 17.   | [Difference between indexOf() and findIndex()](#question-17-difference-between-indexof-and-findindex)                                                       |
| 18.   | [Explain truthy and falsy values with examples](#question-18-explain-truthy-and-falsy-values-with-examples)                                                 |
| 19.   | [How to convert a string to number in JavaScript?](#question-19-how-to-convert-a-string-to-number-in-javascript)                                            |
| 20.   | [How to convert a number to a string in JavaScript?](#question-20-how-to-convert-a-number-to-a-string-in-javascript)                                        |

## Question 1. What is a JavaScript engine? Give examples

> A JavaScript engine is a program or interpreter that executes JavaScript code by converting it into machine code that the computer can understand and run.

### Detailed Explanation

A JavaScript engine is the **core component inside browsers and runtime environments (like Node.js)** that takes your JavaScript source code and executes it efficiently.

Since JavaScript is a high-level language, it cannot run directly on hardware. The engine is responsible for:

#### 1. Parsing

- Converts JavaScript code into an **Abstract Syntax Tree (AST)**
- Checks for syntax errors

#### 2. Compilation (Modern engines use JIT compilation)

- JavaScript is usually **Just-In-Time (JIT) compiled**
- Converts JS into optimized machine code at runtime
- Improves performance significantly

#### 3. Execution

- Executes the compiled machine code
- Manages memory (via garbage collection)

#### 4. Optimization

- Detects frequently used code paths
- Re-optimizes for better performance

### Popular JavaScript Engines

#### In Browsers

- **V8** → Used in Google Chrome, Microsoft Edge
- **SpiderMonkey** → Used in Mozilla Firefox
- **JavaScriptCore (Nitro)** → Used in Safari

#### Outside Browsers

- **Node.js uses V8 engine** (same as Chrome)

### Example (How it works conceptually)

```javascript
function add(a, b) {
  return a + b;
}

console.log(add(2, 3));
```

#### What the engine does

1. Parses the code into AST
2. Compiles it into optimized machine code
3. Executes `add(2, 3)`
4. Outputs `5`

### Key Concepts

#### 1. JIT Compilation

Modern engines don’t just interpret code—they compile it at runtime for speed.

#### 2. Memory Management

- JavaScript engines use **Garbage Collection**
- Automatically frees unused memory

#### 3. Event Loop Integration

The engine works with:

- Call Stack
- Heap
- Callback Queue / Microtask Queue

### Common Pitfalls / Misconceptions

#### ❌ JavaScript engine ≠ JavaScript runtime

- Engine: executes JS (e.g., V8)
- Runtime: includes engine + APIs (e.g., browser APIs or Node.js environment)

#### ❌ JavaScript is not purely interpreted anymore

- Modern engines use **hybrid interpretation + compilation (JIT)**

### Best Practice Insight

Understanding engines helps in:

- Writing **performance-optimized code**
- Avoiding memory leaks
- Understanding async behavior and event loop

## Question 2. What is the difference between client-side and server-side JavaScript?

## Question 3. How does typeof operator work? Give examples

## Question 4. Explain the difference between undefined and undeclared variables

## Question 5. What is the difference between primitive and reference data types?

## Question 6. How does Infinity work in JavaScript?

## Question 7. Difference between mutable and immutable objects

## Question 8. What is the difference between a function and a method?

## Question 9. How to check if a variable is an array?

## Question 10. Difference between isArray() and instanceof Array

## Question 11. Explain string methods like split(), join(), includes()

## Question 12. What is the difference between substr(), substring(), and slice()?

## Question 13. Difference between push() and concat() in arrays

## Question 14. How to reverse an array in JavaScript?

## Question 15. What is the difference between pop() and shift()?

## Question 16. How to find the largest number in an array?

## Question 17. Difference between indexOf() and findIndex()

## Question 18. Explain truthy and falsy values with examples

## Question 19. How to convert a string to number in JavaScript?

## Question 20. How to convert a number to a string in JavaScript?
