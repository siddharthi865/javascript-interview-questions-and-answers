# Set 13

| S.No. | Question                                                                                                                                                                    |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to merge multiple objects into one](#question-1-how-to-merge-multiple-objects-into-one)                                                                                |
| 2.    | [Difference between `Object.keys()`, `Object.values()`, and `Object.entries()`](#question-2-difference-between-objectkeys-objectvalues-and-objectentries)                   |
| 3.    | [Difference between `for...of` and `forEach()` for arrays](#question-3-difference-between-forof-and-foreach-for-arrays)                                                     |
| 4.    | [How to remove duplicates from an array without using Set](#question-4-how-to-remove-duplicates-from-an-array-without-using-set)                                            |
| 5.    | [Difference between `find()` and `filter()` in arrays](#question-5-difference-between-find-and-filter-in-arrays)                                                            |
| 6.    | [Difference between `some()` and `every()` in arrays](#question-6-difference-between-some-and-every-in-arrays)                                                              |
| 7.    | [How to sort an array of objects by a key](#question-7-how-to-sort-an-array-of-objects-by-a-key)                                                                            |
| 8.    | [How to flatten nested arrays using recursion](#question-8-how-to-flatten-nested-arrays-using-recursion)                                                                    |
| 9.    | [How to implement a simple stack using arrays](#question-9-how-to-implement-a-simple-stack-using-arrays)                                                                    |
| 10.   | [How to implement a queue using arrays](#question-10-how-to-implement-a-queue-using-arrays)                                                                                 |
| 11.   | [Difference between `bind()`, `call()`, and `apply()`](#question-11-difference-between-bind-call-and-apply)                                                                 |
| 12.   | [How to create a closure and use it in a function](#question-12-how-to-create-a-closure-and-use-it-in-a-function)                                                           |
| 13.   | [How to implement a private variable using closures](#question-13-how-to-implement-a-private-variable-using-closures)                                                       |
| 14.   | [What is the difference between `this` in arrow functions and regular functions](#question-14-what-is-the-difference-between-this-in-arrow-functions-and-regular-functions) |
| 15.   | [How to implement a Singleton pattern in JavaScript](#question-15-how-to-implement-a-singleton-pattern-in-javascript)                                                       |
| 16.   | [Difference between nullish coalescing (`??`) and OR operator](#question-16-difference-between-nullish-coalescing--and-or--operator)                                        |
| 17.   | [How to use optional chaining (`?.`) in JS](#question-17-how-to-use-optional-chaining--in-js)                                                                               |
| 18.   | [Difference between `let` in block scope vs `var` in function scope](#question-18-difference-between-let-in-block-scope-vs-var-in-function-scope)                           |
| 19.   | [How to use default parameters in functions](#question-19-how-to-use-default-parameters-in-functions)                                                                       |
| 20.   | [Difference between synchronous and asynchronous code](#question-20-difference-between-synchronous-and-asynchronous-code)                                                   |

## Question 1. How to merge multiple objects into one

You can merge multiple objects into one in JavaScript using:

1. **Spread operator (`...`)** — modern and most common
2. **`Object.assign()`**
3. Custom deep merge logic (for nested objects)

Example using spread syntax:

```js
const obj1 = { name: "Alice" };
const obj2 = { age: 25 };
const obj3 = { city: "Delhi" };

const merged = { ...obj1, ...obj2, ...obj3 };

console.log(merged);
// { name: 'Alice', age: 25, city: 'Delhi' }
```

### Detailed Explanation

#### 1. Using the Spread Operator (`...`) — Recommended

The spread operator was introduced in ES6 and is the cleanest way to merge objects.

```js
const user = { name: "John" };
const details = { age: 30 };
const address = { city: "Mumbai" };

const result = {
  ...user,
  ...details,
  ...address,
};

console.log(result);
```

##### Output

```js
{
  name: "John",
  age: 30,
  city: "Mumbai"
}
```

#### 2. Property Conflict Behavior

If multiple objects contain the same key, the **last object's value overwrites earlier ones**.

```js
const obj1 = { name: "Alice", age: 20 };
const obj2 = { age: 30 };

const merged = { ...obj1, ...obj2 };

console.log(merged);
```

##### Output

```js
{
  name: "Alice",
  age: 30
}
```

Because `obj2.age` overwrites `obj1.age`.

> Object merging in JavaScript follows a "last write wins" rule.

#### 3. Using `Object.assign()`

Before spread syntax, `Object.assign()` was commonly used.

```js
const obj1 = { a: 1 };
const obj2 = { b: 2 };

const merged = Object.assign({}, obj1, obj2);

console.log(merged);
```

##### Output

```js
{ a: 1, b: 2 }
```

#### Difference Between Spread and Object.assign

| Feature        | Spread Operator | Object.assign  |
| -------------- | --------------- | -------------- |
| Syntax         | Cleaner         | Verbose        |
| Mutates target | No              | Can mutate     |
| Modern usage   | Preferred       | Older approach |

Example of mutation:

```js
const target = { a: 1 };

Object.assign(target, { b: 2 });

console.log(target);
// target is modified
```

Spread creates a new object more naturally.

#### 4. Merging Multiple Objects Dynamically

If you have an array of objects:

```js
const objects = [{ a: 1 }, { b: 2 }, { c: 3 }];

const merged = Object.assign({}, ...objects);

console.log(merged);
```

Or using `reduce()`:

```js
const merged = objects.reduce((acc, obj) => {
  return { ...acc, ...obj };
}, {});
```

### 5. Important: Shallow Merge vs Deep Merge

Both spread and `Object.assign()` perform a **shallow merge**.

#### Shallow Merge Example

```js
const obj1 = {
  user: {
    name: "Alice",
  },
};

const obj2 = {
  user: {
    age: 25,
  },
};

const merged = { ...obj1, ...obj2 };

console.log(merged);
```

##### Output

```js
{
  user: {
    age: 25;
  }
}
```

The entire `user` object gets replaced.

#### 6. Deep Merge (Nested Objects)

To preserve nested properties, you need custom logic or a utility library like Lodash.

Example:

```js
const obj1 = {
  user: {
    name: "Alice",
  },
};

const obj2 = {
  user: {
    age: 25,
  },
};

const merged = {
  user: {
    ...obj1.user,
    ...obj2.user,
  },
};

console.log(merged);
```

##### Output

```js
{
  user: {
    name: "Alice",
    age: 25
  }
}
```

#### 7. Using Lodash `merge()` for Deep Merge

```js
import _ from "lodash";

const result = _.merge(obj1, obj2);
```

##### Why Use It?

- Handles deeply nested objects
- Easier for complex structures
- Safer for production applications

### 8. Common Interview Pitfalls

#### Pitfall 1: Thinking spread creates deep copies

```js
const obj1 = {
  address: { city: "Delhi" },
};

const copy = { ...obj1 };

copy.address.city = "Mumbai";

console.log(obj1.address.city);
```

##### Output

```js
"Mumbai";
```

Nested objects are still shared references.

#### Pitfall 2: Forgetting overwrite order

```js
{
  ...obj1,
  ...obj2
}
```

`obj2` overrides `obj1`.

Order matters.

### 9. Performance Considerations

For very large objects:

- Spread syntax creates new objects repeatedly
- `Object.assign()` may be slightly faster in some engines
- Deep merging can become expensive

In most frontend/backend applications, spread syntax is perfectly fine.

#### Best Practice

For modern JavaScript:

```js
const merged = { ...obj1, ...obj2, ...obj3 };
```

Use:

- **Spread syntax** for shallow merges
- **Custom logic / Lodash merge** for deep merges

-

### Summary

- Multiple objects can be merged using:
  - Spread operator
  - `Object.assign()`

- Later properties overwrite earlier ones
- Both methods perform shallow merges
- Nested objects require deep merge handling
- Spread syntax is the modern preferred approach

## Question 2. Difference between `Object.keys()`, `Object.values()`, and `Object.entries()`

## Question 3. Difference between `for...of` and `forEach()` for arrays

## Question 4. How to remove duplicates from an array without using Set

## Question 5. Difference between `find()` and `filter()` in arrays

## Question 6. Difference between `some()` and `every()` in arrays

## Question 7. How to sort an array of objects by a key

## Question 8. How to flatten nested arrays using recursion

## Question 9. How to implement a simple stack using arrays

## Question 10. How to implement a queue using arrays

## Question 11. Difference between `bind()`, `call()`, and `apply()`

## Question 12. How to create a closure and use it in a function

## Question 13. How to implement a private variable using closures

## Question 14. What is the difference between `this` in arrow functions and regular functions

## Question 15. How to implement a Singleton pattern in JavaScript

## Question 16. Difference between nullish coalescing (`??`) and OR (`||`) operator

## Question 17. How to use optional chaining (`?.`) in JS

## Question 18. Difference between `let` in block scope vs `var` in function scope

## Question 19. How to use default parameters in functions

## Question 20. Difference between synchronous and asynchronous code
