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

## Question 3. What are JavaScript objects?

## Question 4. How to loop over object properties?

## Question 5. What are arrays in JavaScript? How to loop over them?

## Question 6. Difference between for loop, for…of, and for…in

## Question 7. Explain array methods like push, pop, shift, unshift

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
