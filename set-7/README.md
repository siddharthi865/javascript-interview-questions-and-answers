# Set 7

| S.No. | Question                                                                                                                                                          |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [Explain parseInt() vs Number()](#question-1-explain-parseint-vs-number)                                                                                          |
| 2.    | [What are template literals? Give examples with variables](#question-2-what-are-template-literals-give-examples-with-variables)                                   |
| 3.    | [Difference between alert(), prompt(), and confirm()](#question-3-difference-between-alert-prompt-and-confirm)                                                    |
| 4.    | [What is the difference between innerHTML and textContent?](#question-4-what-is-the-difference-between-innerhtml-and-textcontent)                                 |
| 5.    | [How to change the style of an element using JavaScript?](#question-5-how-to-change-the-style-of-an-element-using-javascript)                                     |
| 6.    | [Difference between appendChild() and insertBefore()](#question-6-difference-between-appendchild-and-insertbefore)                                                |
| 7.    | [What is an HTMLCollection vs NodeList?](#question-7-what-is-an-htmlcollection-vs-nodelist)                                                                       |
| 8.    | [Difference between document.querySelectorAll() and getElementsByClassName()](#question-8-difference-between-documentqueryselectorall-and-getelementsbyclassname) |
| 9.    | [How to prevent default behavior of events?](#question-9-how-to-prevent-default-behavior-of-events)                                                               |
| 10.   | [What is event delegation?](#question-10-what-is-event-delegation)                                                                                                |
| 11.   | [How to remove an event listener?](#question-11-how-to-remove-an-event-listener)                                                                                  |
| 12.   | [How does setTimeout work?](#question-12-how-does-settimeout-work)                                                                                                |
| 13.   | [Difference between alert() and console.log()](#question-13-difference-between-alert-and-consolelog)                                                              |
| 14.   | [How to check if a number is even or odd in JavaScript?](#question-14-how-to-check-if-a-number-is-even-or-odd-in-javascript)                                      |
| 15.   | [What is the difference between Math.floor(), Math.ceil(), and Math.round()?](#question-15-what-is-the-difference-between-mathfloor-mathceil-and-mathround)       |
| 16.   | [Explain the difference between synchronous and asynchronous loops](#question-16-explain-the-difference-between-synchronous-and-asynchronous-loops)               |
| 17.   | [How to flatten a nested array?](#question-17-how-to-flatten-a-nested-array)                                                                                      |
| 18.   | [Explain the difference between shallow copy and deep copy with examples](#question-18-explain-the-difference-between-shallow-copy-and-deep-copy-with-examples)   |
| 19.   | [Difference between Object.assign() and spread operator for objects](#question-19-difference-between-objectassign-and-spread-operator-for-objects)                |
| 20.   | [How to merge two objects in JavaScript?](#question-20-how-to-merge-two-objects-in-javascript)                                                                    |

## Question 1. Explain parseInt() vs Number()

> `parseInt()` converts a string to an integer by parsing from left to right until it hits a non-numeric character, while `Number()` converts the entire value strictly into a number (integer or float). `Number()` is more strict; `parseInt()` is more permissive but can lead to unexpected results.

### Detailed Explanation

In JavaScript, both `parseInt()` and `Number()` are used for type conversion from strings (or other types) into numbers, but they behave very differently in terms of parsing strategy, strictness, and use cases.

### 1. `parseInt()`

#### What it does

- Parses a string **character by character from left to right**
- Stops when it encounters a non-numeric character
- Returns an **integer only**

#### Syntax

```js
parseInt(string, radix);
```

#### Example

```js
parseInt("42"); // 42
parseInt("42px"); // 42  (stops at 'p')
parseInt("10.99"); // 10  (stops at '.')
parseInt("  56abc"); // 56
```

#### Radix (important point)

Always specify radix (base), especially for older browsers:

```js
parseInt("101", 2); // 5 (binary)
parseInt("10", 10); // 10 (decimal)
```

#### Key characteristics of `parseInt()`

- Converts to **integer only**
- Ignores trailing non-numeric characters
- Can produce unexpected results if input is malformed
- Allows partial parsing

#### Pitfall

```js
parseInt("08"); // In old JS engines could behave unexpectedly without radix
```

Best practice:

```js
parseInt("08", 10);
```

### 2. `Number()`

#### What it does

- Converts the **entire value strictly into a number**
- Supports integers, floats, exponential notation
- Returns `NaN` if conversion fails

#### Example

```js
Number("42"); // 42
Number("10.99"); // 10.99
Number("42px"); // NaN
Number("  56  "); // 56
Number(true); // 1
Number(false); // 0
```

#### Key characteristics of `Number()`

- Strict conversion (no partial parsing)
- Supports decimals
- Converts booleans and other types too
- Returns `NaN` if the entire value is not valid

#### Pitfall

```js
Number("42px"); // NaN (unlike parseInt)
```

### 3. Key Differences

| Feature                         | parseInt()                 | Number()                  |
| ------------------------------- | -------------------------- | ------------------------- |
| Parsing style                   | Stops at invalid character | Strict full conversion    |
| Decimal support                 | ❌ (truncates)             | ✅                        |
| Returns NaN for partial strings | ❌                         | ✅                        |
| Accepts "42px"                  | ✅ → 42                    | ❌ → NaN                  |
| Boolean conversion              | ❌                         | ✅                        |
| Recommended for                 | extracting integers        | strict numeric conversion |

### 4. When to use what?

#### Use `parseInt()` when

- You are extracting numbers from strings
- You expect mixed input like `"100px"`
- You only need integers

```js
parseInt("200px", 10); // 200
```

#### Use `Number()` when

- You need strict validation
- You expect clean numeric input
- You need decimal support

```js
Number("123.45"); // 123.45
Number("123px"); // NaN (good for validation)
```

### 5. Edge Case Comparison

```js
parseInt("12.34"); // 12
Number("12.34"); // 12.34
```

```js
parseInt("abc"); // NaN
Number("abc"); // NaN
```

```js
parseInt(true); // NaN
Number(true); // 1
```

### 6. Best Practices (Interview Tip)

- Always pass radix to `parseInt`
- Prefer `Number()` for clean numeric validation
- Avoid `parseInt()` for decimals or precise numeric parsing
- Be aware of implicit coercion pitfalls

### Final Summary

- `parseInt()` = **loose parsing, integer-focused, stops early**
- `Number()` = **strict conversion, supports full numeric types**

## Question 2. What are template literals? Give examples with variables

## Short Answer

**Template literals** are a feature introduced in ES6 that allow you to create strings using **backticks (` `)** instead of quotes. They support:

- **String interpolation** (embedding variables and expressions)
- **Multi-line strings**
- **Embedded expressions**
- **Tagged templates**

The most common use is inserting variables into strings using `${}`.

---

# Basic Syntax

```js
const name = "John";

const message = `Hello, ${name}!`;

console.log(message);
```

**Output:**

```js
Hello, John!
```

---

# Why Use Template Literals?

Before ES6, string concatenation was commonly done with `+`:

```js
const name = "John";
const age = 25;

const msg = "My name is " + name + " and I am " + age + " years old.";

console.log(msg);
```

Using template literals:

```js
const name = "John";
const age = 25;

const msg = `My name is ${name} and I am ${age} years old.`;

console.log(msg);
```

This is cleaner and easier to read.

---

# Example 1: Inserting Variables

```js
const firstName = "Alice";
const city = "London";

console.log(`Hello ${firstName}, welcome to ${city}!`);
```

**Output:**

```js
Hello Alice, welcome to London!
```

---

# Example 2: Using Expressions

Anything inside `${}` is evaluated as JavaScript.

```js
const a = 10;
const b = 20;

console.log(`Sum = ${a + b}`);
```

**Output:**

```js
Sum = 30;
```

---

# Example 3: Calling Functions

```js
function greet(name) {
  return `Hello ${name}`;
}

console.log(`${greet("David")}!`);
```

**Output:**

```js
Hello David!
```

---

# Example 4: Accessing Object Properties

```js
const user = {
  name: "Sarah",
  age: 28,
};

console.log(`${user.name} is ${user.age} years old.`);
```

**Output:**

```js
Sarah is 28 years old.
```

---

# Example 5: Multi-line Strings

Before ES6:

```js
const text = "Line 1\n" + "Line 2\n" + "Line 3";
```

With template literals:

```js
const text = `
Line 1
Line 2
Line 3
`;

console.log(text);
```

---

# Example 6: HTML Generation

A common frontend use case:

```js
const user = {
  name: "John",
  role: "Developer",
};

const html = `
  <div>
    <h2>${user.name}</h2>
    <p>${user.role}</p>
  </div>
`;

console.log(html);
```

---

# Nested Template Literals

```js
const user = "Alice";
const isAdmin = true;

const message = `Welcome ${user}. ${
  isAdmin ? "You have admin access." : "You are a regular user."
}`;

console.log(message);
```

**Output:**

```js
Welcome Alice. You have admin access.
```

---

# Common Interview Question

### What is the difference between template literals and string concatenation?

| String Concatenation              | Template Literals                     |
| --------------------------------- | ------------------------------------- |
| Uses `+` operator                 | Uses backticks `` ` ` ``              |
| Harder to read for long strings   | More readable                         |
| Multi-line strings require `\n`   | Supports multi-line strings naturally |
| Expressions require concatenation | Expressions inside `${}`              |

---

# Best Practices

### ✅ Use template literals for dynamic strings

```js
const product = "Laptop";
const price = 50000;

const message = `${product} costs ₹${price}`;
```

### ✅ Use for HTML templates

```js
const card = `<div>${title}</div>`;
```

### ❌ Avoid unnecessary template literals

```js
// Not needed
const name = `John`;
```

Prefer:

```js
const name = "John";
```

---

# Interview-Friendly Summary

> Template literals are ES6 string literals enclosed in backticks (`` ` ``). They allow string interpolation using `${}`, support multi-line strings, and improve readability compared to traditional string concatenation. They are commonly used for dynamic content generation, HTML templates, and embedding JavaScript expressions directly inside strings.

## Question 3. Difference between alert(), prompt(), and confirm()

## Short Answer

- **`alert()`** → Shows a message with an **OK button only**
- **`prompt()`** → Takes **user input** and returns it
- **`confirm()`** → Asks a **yes/no question (OK/Cancel)** and returns a boolean

All three are **blocking (synchronous)** browser dialogs used for simple user interaction.

---

# 1. `alert()`

### What it does:

Displays a simple message popup to the user.

### Syntax:

```js id="a1b2c3"
alert("Hello World");
```

### Behavior:

- Shows message
- Only **OK button**
- No return value (`undefined`)
- Execution pauses until user clicks OK

### Example:

```js id="d4e5f6"
console.log("Start");

alert("This is an alert!");

console.log("End");
```

### Output order:

```
Start
(alert popup)
End
```

---

# 2. `prompt()`

### What it does:

Asks the user for input and returns the value.

### Syntax:

```js id="g7h8i9"
const name = prompt("Enter your name:");
```

### Behavior:

- Takes user input as string
- Returns:
  - entered string
  - `null` if user clicks Cancel

- Execution pauses until input is given

---

### Example:

```js id="j1k2l3"
const age = prompt("Enter your age:");

console.log(age);
```

### Possible outputs:

```
"25"   (string)
null   (if Cancel clicked)
```

---

### Important Interview Point:

Even numbers are returned as strings:

```js id="m4n5o6"
const num = prompt("Enter number:");
console.log(typeof num); // "string"
```

To convert:

```js id="p7q8r9"
const num = Number(prompt("Enter number:"));
```

---

# 3. `confirm()`

### What it does:

Asks user to confirm an action.

### Syntax:

```js id="s1t2u3"
const result = confirm("Are you sure?");
```

### Behavior:

- Shows OK and Cancel buttons
- Returns:
  - `true` → OK clicked
  - `false` → Cancel clicked

---

### Example:

```js id="v4w5x6"
const isDeleted = confirm("Do you want to delete this file?");

console.log(isDeleted);
```

### Output:

```
true  → OK clicked
false → Cancel clicked
```

---

# 🔥 Key Differences (Interview Table)

| Feature       | alert()      | prompt()       | confirm()        |
| ------------- | ------------ | -------------- | ---------------- |
| Purpose       | Show message | Get input      | Ask confirmation |
| Buttons       | OK           | OK + Cancel    | OK + Cancel      |
| Return value  | undefined    | string or null | boolean          |
| Input support | ❌           | ✅             | ❌               |
| Blocking      | Yes          | Yes            | Yes              |

---

# 🔥 Execution Behavior (Important Concept)

All three are:

- **Synchronous**
- **Blocking UI**
- Pause JavaScript execution until closed

Example:

```js id="y7z8a9"
console.log("A");

alert("Hello");

console.log("B");
```

Execution order:

```
A → alert blocks → B
```

---

# Common Interview Traps

---

## ❓ 1. What does prompt return?

```js id="b1c2d3"
prompt("Enter value");
```

### Answer:

- Always returns **string or null**

---

## ❓ 2. What happens if Cancel is clicked?

```js id="e4f5g6"
const value = prompt("Enter name:");
```

### Answer:

```js id="h7i8j9"
null;
```

---

## ❓ 3. Is confirm() type boolean?

Yes:

```js id="k1l2m3"
true; // OK
false; // Cancel
```

---

## ❓ 4. Why are these not used in modern apps?

### Reasons:

- Blocking UI (bad UX)
- Not customizable
- Not mobile-friendly
- Breaks async flow

### Modern alternatives:

- Custom modal components
- UI libraries (React modals, Bootstrap modals)

---

# Best Practice (Interview Answer)

> `alert()`, `prompt()`, and `confirm()` are built-in browser dialog methods used for simple interactions. However, they are synchronous and block the main thread, which makes them unsuitable for modern production UI. Today, developers prefer custom modal components for better UX and control.

---

# Final Summary

- `alert()` → message only
- `prompt()` → input → string/null
- `confirm()` → decision → boolean

## Question 4. What is the difference between innerHTML and textContent?

## Question 5. How to change the style of an element using JavaScript?

## Question 6. Difference between appendChild() and insertBefore()

## Question 7. What is an HTMLCollection vs NodeList?

## Question 8. Difference between document.querySelectorAll() and getElementsByClassName()

## Question 9. How to prevent default behavior of events?

## Question 10. What is event delegation?

## Question 11. How to remove an event listener?

## Question 12. How does setTimeout work?

## Question 13. Difference between alert() and console.log()

## Question 14. How to check if a number is even or odd in JavaScript?

## Question 15. What is the difference between Math.floor(), Math.ceil(), and Math.round()?

## Question 16. Explain the difference between synchronous and asynchronous loops

## Question 17. How to flatten a nested array?

## Question 18. Explain the difference between shallow copy and deep copy with examples

## Question 19. Difference between Object.assign() and spread operator for objects

## Question 20. How to merge two objects in JavaScript?
