# Set 12

| S.No. | Question                                                                                                                                                     |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How to reverse a string in JavaScript?](#question-1-how-to-reverse-a-string-in-javascript)                                                                  |
| 2.    | [Difference between `Math.max.apply()` and `Math.max(...array)`](#question-2-difference-between-mathmaxapply-and-mathmaxarray)                               |
| 3.    | [How to merge multiple arrays into one?](#question-3-how-to-merge-multiple-arrays-into-one)                                                                  |
| 4.    | [Difference between shallow copy and reference copy of arrays](#question-4-difference-between-shallow-copy-and-reference-copy-of-arrays)                     |
| 5.    | [How to empty an array in JavaScript?](#question-5-how-to-empty-an-array-in-javascript)                                                                      |
| 6.    | [Difference between `Array.isArray()` and `instanceof Array`](#question-6-difference-between-arrayisarray-and-instanceof-array)                              |
| 7.    | [What is the difference between `document.createElement()` and `innerHTML`?](#question-7-what-is-the-difference-between-documentcreateelement-and-innerhtml) |
| 8.    | [How to get attributes of an HTML element using JS?](#question-8-how-to-get-attributes-of-an-html-element-using-js)                                          |
| 9.    | [Difference between event bubbling and event capturing](#question-9-difference-between-event-bubbling-and-event-capturing)                                   |
| 10.   | [How to get the current timestamp in JavaScript?](#question-10-how-to-get-the-current-timestamp-in-javascript)                                               |
| 11.   | [Difference between `Date.now()` and `new Date()`](#question-11-difference-between-datenow-and-new-date)                                                     |
| 12.   | [How to calculate the difference between two dates](#question-12-how-to-calculate-the-difference-between-two-dates)                                          |
| 13.   | [How to convert a string to a Date object](#question-13-how-to-convert-a-string-to-a-date-object)                                                            |
| 14.   | [What are regular expressions in JavaScript?](#question-14-what-are-regular-expressions-in-javascript)                                                       |
| 15.   | [How to test a string using regex](#question-15-how-to-test-a-string-using-regex)                                                                            |
| 16.   | [Difference between `setTimeout` and `setInterval`](#question-16-difference-between-settimeout-and-setinterval)                                              |
| 17.   | [How to cancel a `setTimeout` or `setInterval`](#question-17-how-to-cancel-a-settimeout-or-setinterval)                                                      |
| 18.   | [How to debounce a function call](#question-18-how-to-debounce-a-function-call)                                                                              |
| 19.   | [How to throttle a function call](#question-19-how-to-throttle-a-function-call)                                                                              |
| 20.   | [Difference between deep copy and shallow copy of objects](#question-20-difference-between-deep-copy-and-shallow-copy-of-objects)                            |

## Question 1. How to reverse a string in JavaScript?

A string can be reversed in JavaScript in several ways, but the most common interview answer is:

```js
const str = "hello";

const reversed = str.split("").reverse().join("");

console.log(reversed); // "olleh"
```

### Detailed Explanation

JavaScript strings are **immutable**, meaning you cannot directly modify characters inside a string.
So reversing a string usually involves:

1. Converting the string into an array
2. Reversing the array
3. Converting it back into a string

#### Method 1 — Using `split()`, `reverse()`, and `join()` (Most Common)

```js
function reverseString(str) {
  return str.split("").reverse().join("");
}

console.log(reverseString("JavaScript"));
// "tpircSavaJ"
```

##### How it works

###### 1. `split("")`

Converts the string into an array of characters.

```js
"hello".split("");
// ["h", "e", "l", "l", "o"]
```

###### 2. `reverse()`

Reverses the array in place.

```js
["h", "e", "l", "l", "o"].reverse();
// ["o", "l", "l", "e", "h"]
```

###### 3. `join("")`

Converts the array back into a string.

```js
["o", "l", "l", "e", "h"].join("");
// "olleh"
```

#### Time and Space Complexity

| Operation | Complexity |
| --------- | ---------- |
| split()   | O(n)       |
| reverse() | O(n)       |
| join()    | O(n)       |

Overall:

- **Time Complexity:** O(n)
- **Space Complexity:** O(n)

#### Method 2 — Using a Loop

Interviewers sometimes ask you to reverse a string **without using `reverse()`**.

```js
function reverseString(str) {
  let reversed = "";

  for (let i = str.length - 1; i >= 0; i--) {
    reversed += str[i];
  }

  return reversed;
}

console.log(reverseString("hello"));
// "olleh"
```

#### Method 3 — Using Recursion

```js
function reverseString(str) {
  if (str === "") {
    return "";
  }

  return reverseString(str.substr(1)) + str[0];
}

console.log(reverseString("hello"));
// "olleh"
```

##### Important Note

This is elegant but not ideal for large strings because recursion can cause:

- Higher memory usage
- Stack overflow for very long strings

#### Method 4 — Using `reduce()`

```js
function reverseString(str) {
  return str.split("").reduce((rev, char) => char + rev, "");
}

console.log(reverseString("hello"));
// "olleh"
```

#### Unicode / Emoji Edge Case

A common issue with `split("")` is that it may break Unicode surrogate pairs like emojis.

Example:

```js
const str = "😊👍";

console.log(str.split("").reverse().join(""));
// Incorrect result
```

Better approach:

```js
function reverseString(str) {
  return [...str].reverse().join("");
}

console.log(reverseString("😊👍"));
// 👍😊
```

Using the spread operator (`...str`) correctly handles many Unicode characters.

### Modern ES6+ Version

```js
const reverseString = (str) => [...str].reverse().join("");
```

### Common Interview Follow-Ups

#### Q1: Why can't we directly reverse a string?

Because JavaScript strings are immutable.

```js
let str = "hello";
str[0] = "H";

console.log(str); // still "hello"
```

#### Q2: Does `reverse()` work on strings?

No. `reverse()` is an Array method.

```js
"hello".reverse(); // Error
```

#### Q3: Which method is best?

Usually:

```js
str.split("").reverse().join("");
```

is considered the standard interview solution.

For Unicode-safe implementations:

```js
[...str].reverse().join("");
```

is better.

### Best Practice Recommendation

For production-quality modern JavaScript:

```js
const reverseString = (str) => [...str].reverse().join("");
```

because it is:

- Clean
- Readable
- ES6+
- More Unicode-friendly than `split("")`

## Question 2. Difference between `Math.max.apply()` and `Math.max(...array)`

## Question 3. How to merge multiple arrays into one?

## Question 4. Difference between shallow copy and reference copy of arrays

## Question 5. How to empty an array in JavaScript?

## Question 6. Difference between `Array.isArray()` and `instanceof Array`

## Question 7. What is the difference between `document.createElement()` and `innerHTML`?

## Question 8. How to get attributes of an HTML element using JS?

## Question 9. Difference between event bubbling and event capturing

## Question 10. How to get the current timestamp in JavaScript?

## Question 11. Difference between `Date.now()` and `new Date()`

## Question 12. How to calculate the difference between two dates

## Question 13. How to convert a string to a Date object

## Question 14. What are regular expressions in JavaScript?

## Question 15. How to test a string using regex

## Question 16. Difference between `setTimeout` and `setInterval`

## Question 17. How to cancel a `setTimeout` or `setInterval`

## Question 18. How to debounce a function call

## Question 19. How to throttle a function call

## Question 20. Difference between deep copy and shallow copy of objects
