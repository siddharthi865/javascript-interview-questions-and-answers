# Set 4

| S.No. | Question                                                                                                                                                                |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to clone an object using spread operator](#question-1-how-to-clone-an-object-using-spread-operator)                                                                |
| 2.    | [Explain destructuring of arrays and objects](#question-2-explain-destructuring-of-arrays-and-objects)                                                                  |
| 3.    | [Difference between nullish coalescing operator ?? and logical OR \|\|](#question-3-difference-between-nullish-coalescing-operator--and-logical-or-)                    |
| 4.    | [Difference between optional chaining ?. and normal property access](#question-4-difference-between-optional-chaining--and-normal-property-access)                      |
| 5.    | [Explain Map vs Object](#question-5-explain-map-vs-object)                                                                                                              |
| 6.    | [Explain Set in JavaScript](#question-6-explain-set-in-javascript)                                                                                                      |
| 7.    | [How to remove duplicates from an array using Set](#question-7-how-to-remove-duplicates-from-an-array-using-set)                                                        |
| 8.    | [What are WeakMap and WeakSet?](#question-8-what-are-weakmap-and-weakset)                                                                                               |
| 9.    | [Explain currying in JavaScript](#question-9-explain-currying-in-javascript)                                                                                            |
| 10.   | [What is memoization? Give example](#question-10-what-is-memoization-give-example)                                                                                      |
| 11.   | [Difference between var, let, const in closures](#question-11-difference-between-var-let-const-in-closures)                                                             |
| 12.   | [Explain function hoisting with examples](#question-12-explain-function-hoisting-with-examples)                                                                         |
| 13.   | [Explain how this behaves differently in arrow functions vs normal functions](#question-13-explain-how-this-behaves-differently-in-arrow-functions-vs-normal-functions) |
| 14.   | [How does JavaScript handle asynchronous code internally?](#question-14-how-does-javascript-handle-asynchronous-code-internally)                                        |
| 15.   | [What is microtask and macrotask queue?](#question-15-what-is-microtask-and-macrotask-queue)                                                                            |
| 16.   | [How to implement debouncing and throttling?](#question-16-how-to-implement-debouncing-and-throttling)                                                                  |
| 17.   | [Difference between == vs === in depth](#question-17-difference-between--vs--in-depth)                                                                                  |
| 18.   | [What is garbage collection in JavaScript?](#question-18-what-is-garbage-collection-in-javascript)                                                                      |
| 19.   | [How to prevent memory leaks in JS?](#question-19-how-to-prevent-memory-leaks-in-js)                                                                                    |
| 20.   | [Explain JavaScript modules in ES6 vs CommonJS](#question-20-explain-javascript-modules-in-es6-vs-commonjs)                                                             |

## Question 1. How to clone an object using spread operator

> You can clone a **shallow copy** of an object in JavaScript using the spread operator like this:

```javascript
const clonedObj = { ...originalObj };
```

### Detailed Explanation

#### 1. What does cloning mean in JavaScript?

Cloning an object means creating a **new object with the same properties and values** as an existing object.

There are two types of cloning:

- **Shallow copy** → copies only top-level properties
- **Deep copy** → copies nested objects as well

The spread operator (`...`) performs a **shallow copy**.

#### 2. How spread operator cloning works

```javascript
const user = {
  name: "John",
  age: 30,
};

const clonedUser = { ...user };

console.log(clonedUser); // { name: "John", age: 30 }
console.log(clonedUser === user); // false (different references)
```

##### Key point

- A **new object is created**
- Top-level properties are copied
- Reference equality is broken at the root level

#### 3. Important limitation: Shallow copy behavior

If the object contains nested objects, only references are copied:

```javascript
const user = {
  name: "John",
  address: {
    city: "Delhi",
  },
};

const clonedUser = { ...user };

clonedUser.address.city = "Mumbai";

console.log(user.address.city); // "Mumbai" ❗ changed in original too
```

##### Why this happens

- `address` is an object → copied by reference, not value

#### 4. When spread operator cloning is useful

Best for:

- Flat objects
- Simple state updates (React, Redux patterns)
- Quick object duplication

Example in state update:

```javascript
const state = { count: 1 };

const newState = { ...state, count: state.count + 1 };
```

#### 5. Common alternatives

##### 1. `Object.assign()`

```javascript
const clone = Object.assign({}, originalObj);
```

Works same as spread operator (shallow copy).

##### 2. Deep copy (modern approach)

For nested objects:

```javascript
const deepClone = structuredClone(originalObj);
```

Supports nested objects
Handles arrays, dates, maps (modern browsers)

##### 3. JSON method (older but limited)

```javascript
const deepClone = JSON.parse(JSON.stringify(originalObj));
```

###### Limitations

- Loses functions
- Breaks Date, Map, Set
- Ignores undefined values

#### 6. Spread vs other approaches (interview point)

| Method            | Type    | Pros                | Cons                     |
| ----------------- | ------- | ------------------- | ------------------------ |
| Spread `{...obj}` | Shallow | Simple, readable    | No deep copy             |
| Object.assign     | Shallow | Legacy support      | Same limitation          |
| structuredClone   | Deep    | Reliable modern API | Not in very old browsers |
| JSON method       | Deep    | Easy fallback       | Loses data types         |

#### 7. Common pitfalls

##### ❌ Thinking spread does deep copy

Many candidates assume:

```javascript
const copy = { ...obj }; // NOT deep copy
```

##### ❌ Mutating nested objects

Leads to bugs in shared references.

#### 8. Best practice

- Use **spread operator for shallow cloning**
- Use **structuredClone for deep cloning**
- Avoid JSON cloning in modern code unless necessary

## Question 2. Explain destructuring of arrays and objects

## Question 3. Difference between nullish coalescing operator ?? and logical OR ||

## Question 4. Difference between optional chaining ?. and normal property access

## Question 5. Explain Map vs Object

## Question 6. Explain Set in JavaScript

## Question 7. How to remove duplicates from an array using Set

## Question 8. What are WeakMap and WeakSet?

## Question 9. Explain currying in JavaScript

## Question 10. What is memoization? Give example

## Question 11. Difference between var, let, const in closures

## Question 12. Explain function hoisting with examples

## Question 13. Explain how this behaves differently in arrow functions vs normal functions

## Question 14. How does JavaScript handle asynchronous code internally?

## Question 15. What is microtask and macrotask queue?

## Question 16. How to implement debouncing and throttling?

## Question 17. Difference between == vs === in depth

## Question 18. What is garbage collection in JavaScript?

## Question 19. How to prevent memory leaks in JS?

## Question 20. Explain JavaScript modules in ES6 vs CommonJS
