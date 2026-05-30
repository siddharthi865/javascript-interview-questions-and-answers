# Set 11

| S.No. | Question                                                                                                                                                |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between document and window objects?](#question-1-what-is-the-difference-between-document-and-window-objects)                   |
| 2.    | [How do you check if a variable is null or undefined?](#question-2-how-do-you-check-if-a-variable-is-null-or-undefined)                                 |
| 3.    | [Difference between for...in and for...of loops](#question-3-difference-between-forin-and-forof-loops)                                                  |
| 4.    | [How to find the type of an object in JavaScript?](#question-4-how-to-find-the-type-of-an-object-in-javascript)                                         |
| 5.    | [What is the difference between primitive and non-primitive types?](#question-5-what-is-the-difference-between-primitive-and-non-primitive-types)       |
| 6.    | [Difference between mutable and immutable data types](#question-6-difference-between-mutable-and-immutable-data-types)                                  |
| 7.    | [How to concatenate arrays in JavaScript?](#question-7-how-to-concatenate-arrays-in-javascript)                                                         |
| 8.    | [How to check if a string contains another string?](#question-8-how-to-check-if-a-string-contains-another-string)                                       |
| 9.    | [How to convert a number to a string in binary, octal, or hexadecimal?](#question-9-how-to-convert-a-number-to-a-string-in-binary-octal-or-hexadecimal) |
| 10.   | [How to round a number to a fixed number of decimal places?](#question-10-how-to-round-a-number-to-a-fixed-number-of-decimal-places)                    |
| 11.   | [Difference between parseFloat and Number()](#question-11-difference-between-parsefloat-and-number)                                                     |
| 12.   | [What is the difference between undefined, null, and an empty string?](#question-12-what-is-the-difference-between-undefined-null-and-an-empty-string)  |
| 13.   | [How to find the length of an object or array?](#question-13-how-to-find-the-length-of-an-object-or-array)                                              |
| 14.   | [How to remove a property from an object?](#question-14-how-to-remove-a-property-from-an-object)                                                        |
| 15.   | [Difference between document.getElementById() and querySelector()](#question-15-difference-between-documentgetelementbyid-and-queryselector)            |
| 16.   | [How to prevent an HTML form from submitting?](#question-16-how-to-prevent-an-html-form-from-submitting)                                                |
| 17.   | [How to add an event listener to multiple elements?](#question-17-how-to-add-an-event-listener-to-multiple-elements)                                    |
| 18.   | [Difference between innerHTML, innerText, and textContent](#question-18-difference-between-innerhtml-innertext-and-textcontent)                         |
| 19.   | [How to check if a key exists in an object?](#question-19-how-to-check-if-a-key-exists-in-an-object)                                                    |
| 20.   | [How to find the largest/smallest number in an array?](#question-20-how-to-find-the-largestsmallest-number-in-an-array)                                 |

## Question 1. What is the difference between document and window objects?

In a browser environment:

- `window` is the **global object** representing the browser window/tab.
- `document` is a property of `window` that represents the **HTML page (DOM)** loaded inside that window.

So the relationship is:

```js
window.document;
```

`window` manages browser-level features, while `document` manages webpage content.

### Detailed Explanation

#### 1. What is the `window` Object?

The `window` object is the **top-level global object** in browsers.

It represents the browser tab/window and provides APIs for:

- Browser control
- Timers
- Navigation
- Storage
- Global variables/functions
- Screen/location/history access

##### Examples

```js
console.log(window);
```

##### Common `window` APIs

```js
window.alert("Hello");
window.setTimeout(() => {}, 1000);
window.location.href;
window.localStorage;
window.history.back();
```

##### Important Interview Point

In browsers, globally declared variables become properties of `window` (with `var`).

```js
var x = 10;

console.log(window.x); // 10
```

But `let` and `const` do NOT attach to `window`.

```js
let y = 20;

console.log(window.y); // undefined
```

#### 2. What is the `document` Object?

The `document` object represents the **DOM (Document Object Model)** of the webpage.

It allows JavaScript to:

- Access HTML elements
- Modify content
- Change styles
- Create/remove elements
- Handle DOM events

##### Examples

```js
console.log(document);
```

##### Common `document` APIs

```js
document.getElementById("title");
document.querySelector(".box");
document.createElement("div");
document.body.style.background = "lightblue";
```

### Relationship Between `window` and `document`

`document` is actually a property of `window`.

```js
console.log(window.document === document); // true
```

Hierarchy:

```text
window
 ├── document
 │    ├── body
 │    ├── forms
 │    └── links
 ├── location
 ├── history
 ├── localStorage
 └── setTimeout()
```

### Key Differences

| Feature      | `window`                   | `document`                           |
| ------------ | -------------------------- | ------------------------------------ |
| Represents   | Browser window/tab         | Loaded HTML page                     |
| Type         | Global browser object      | DOM object                           |
| Scope        | Entire browser environment | Webpage content                      |
| Access Level | Top-level object           | Child of `window`                    |
| Used For     | Browser APIs               | DOM manipulation                     |
| Example APIs | `alert()`, `setTimeout()`  | `querySelector()`, `createElement()` |

### Practical Examples

#### Example 1 — Using `window`

```js
window.alert("Welcome");
```

Controls browser behavior.

#### Example 2 — Using `document`

```js
document.body.style.backgroundColor = "yellow";
```

Manipulates webpage content.

### Browser Global Scope Behavior

Because `window` is global, you can often omit `window`.

These are equivalent:

```js
window.alert("Hi");
alert("Hi");
```

And:

```js
window.document;
document;
```

JavaScript automatically looks on `window`.

### Important Interview Concept

#### DOM vs BOM

##### `document` → DOM (Document Object Model)

Handles webpage structure.

##### `window` → BOM (Browser Object Model)

Handles browser features.

Examples of BOM objects:

- `navigator`
- `screen`
- `history`
- `location`

All accessible through `window`.

### Common Interview Questions

#### Q1: Is `document` part of `window`?

Yes.

```js
window.document;
```

#### Q2: Can `document` exist without `window`?

In browsers, no.

`document` is provided by the browser through `window`.

#### Q3: Which object is global in browsers?

`window`

#### Q4: Which object manipulates HTML elements?

`document`

### Example Combining Both

```html
<button id="btn">Click</button>

<script>
  document.getElementById("btn").addEventListener("click", () => {
    window.alert("Button clicked");
  });
</script>
```

- `document` selects the button
- `window` shows the alert

### Common Pitfalls

#### 1. Confusing Global Scope with DOM

```js
document.myVar; // ❌
```

Variables belong to `window`, not `document`.

#### 2. Assuming Everything is on `document`

Timers are on `window`, not `document`.

```js
setTimeout(() => {}, 1000);
```

Equivalent to:

```js
window.setTimeout(() => {}, 1000);
```

### Best Practices

- Use `document` for DOM manipulation.
- Use `window` only for browser/global APIs.
- Avoid polluting `window` with global variables.
- Prefer modules and block scope (`let`/`const`).

### Summary

> The `window` object represents the browser window and acts as the global object in browsers, providing APIs like `alert`, `setTimeout`, and `localStorage`.
> The `document` object represents the DOM of the loaded webpage and is used to access and manipulate HTML elements.
> `document` is actually a property of `window`, i.e., `window.document`.

## Question 2. How do you check if a variable is null or undefined?

## Question 3. Difference between for...in and for...of loops

## Question 4. How to find the type of an object in JavaScript?

## Question 5. What is the difference between primitive and non-primitive types?

## Question 6. Difference between mutable and immutable data types

## Question 7. How to concatenate arrays in JavaScript?

## Question 8. How to check if a string contains another string?

## Question 9. How to convert a number to a string in binary, octal, or hexadecimal?

## Question 10. How to round a number to a fixed number of decimal places?

## Question 11. Difference between parseFloat and Number()

## Question 12. What is the difference between undefined, null, and an empty string?

## Question 13. How to find the length of an object or array?

## Question 14. How to remove a property from an object?

## Question 15. Difference between document.getElementById() and querySelector()

## Question 16. How to prevent an HTML form from submitting?

## Question 17. How to add an event listener to multiple elements?

## Question 18. Difference between innerHTML, innerText, and textContent

## Question 19. How to check if a key exists in an object?

## Question 20. How to find the largest/smallest number in an array?
