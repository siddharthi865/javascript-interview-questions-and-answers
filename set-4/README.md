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

### Short Answer

**Destructuring** is an ES6 feature that allows you to extract values from arrays or properties from objects and assign them to variables in a concise, readable way.

```javascript
// Array destructuring
const [a, b] = [10, 20];

// Object destructuring
const { name, age } = { name: "John", age: 30 };
```

---

# What is Destructuring?

Before ES6, extracting values required multiple statements:

```javascript
const user = {
  name: "John",
  age: 30,
};

const name = user.name;
const age = user.age;
```

With destructuring:

```javascript
const { name, age } = user;
```

This reduces boilerplate and improves readability.

---

# Array Destructuring

Array destructuring extracts values based on their **position (index)**.

### Basic Example

```javascript
const numbers = [10, 20, 30];

const [first, second, third] = numbers;

console.log(first); // 10
console.log(second); // 20
console.log(third); // 30
```

---

## Skipping Elements

```javascript
const numbers = [10, 20, 30, 40];

const [first, , third] = numbers;

console.log(first); // 10
console.log(third); // 30
```

---

## Default Values

```javascript
const [a, b, c = 100] = [1, 2];

console.log(c); // 100
```

Default values are used when the corresponding element is `undefined`.

---

## Swapping Variables

A popular interview example:

```javascript
let x = 10;
let y = 20;

[x, y] = [y, x];

console.log(x); // 20
console.log(y); // 10
```

---

## Rest Operator with Arrays

```javascript
const [first, ...rest] = [1, 2, 3, 4, 5];

console.log(first); // 1
console.log(rest); // [2, 3, 4, 5]
```

---

# Object Destructuring

Object destructuring extracts values based on **property names**, not positions.

### Basic Example

```javascript
const user = {
  name: "John",
  age: 30,
};

const { name, age } = user;

console.log(name); // John
console.log(age); // 30
```

---

## Renaming Variables

Often used in interviews.

```javascript
const user = {
  name: "John",
};

const { name: userName } = user;

console.log(userName); // John
```

Here:

- Property name → `name`
- Variable name → `userName`

---

## Default Values

```javascript
const user = {
  name: "John",
};

const { name, age = 25 } = user;

console.log(age); // 25
```

---

## Rest Operator with Objects

```javascript
const user = {
  name: "John",
  age: 30,
  city: "Delhi",
};

const { name, ...others } = user;

console.log(name); // John
console.log(others); // { age: 30, city: "Delhi" }
```

---

# Nested Destructuring

### Objects Inside Objects

```javascript
const user = {
  name: "John",
  address: {
    city: "Delhi",
    country: "India",
  },
};

const {
  address: { city },
} = user;

console.log(city); // Delhi
```

---

### Arrays Inside Objects

```javascript
const user = {
  hobbies: ["Coding", "Reading"],
};

const {
  hobbies: [firstHobby],
} = user;

console.log(firstHobby); // Coding
```

---

# Function Parameter Destructuring

Very common in modern JavaScript and React.

### Without Destructuring

```javascript
function displayUser(user) {
  console.log(user.name);
  console.log(user.age);
}
```

### With Destructuring

```javascript
function displayUser({ name, age }) {
  console.log(name);
  console.log(age);
}
```

Usage:

```javascript
displayUser({
  name: "John",
  age: 30,
});
```

---

# Practical Real-World Example

### API Response

```javascript
const response = {
  data: {
    id: 1,
    title: "JavaScript Guide",
  },
};

const {
  data: { id, title },
} = response;

console.log(id);
console.log(title);
```

This is common when working with APIs.

---

# Common Pitfalls

## 1. Property Name Must Match

```javascript
const user = {
  name: "John",
};

const { username } = user;

console.log(username); // undefined
```

Because `username` property doesn't exist.

---

## 2. Destructuring Undefined Causes Error

```javascript
const user = undefined;

const { name } = user; // TypeError
```

Safer approach:

```javascript
const { name } = user || {};
```

---

## 3. Array Destructuring Depends on Order

```javascript
const [a, b] = [20, 10];

console.log(a); // 20
console.log(b); // 10
```

Arrays use positions, not names.

---

# Interview Comparison

| Feature                | Array Destructuring      | Object Destructuring   |
| ---------------------- | ------------------------ | ---------------------- |
| Based on               | Position (index)         | Property name          |
| Order matters          | Yes                      | No                     |
| Supports defaults      | Yes                      | Yes                    |
| Supports rest operator | Yes                      | Yes                    |
| Common use             | Arrays, function returns | Objects, API responses |

---

# Best Practices

- Use destructuring to make code cleaner and more readable.
- Use default values when properties may be missing.
- Use parameter destructuring in functions for clarity.
- Avoid excessive nested destructuring when it hurts readability.

---

### Interview Summary

> Destructuring is an ES6 feature that allows extracting values from arrays and properties from objects into variables. Array destructuring is position-based, while object destructuring is property-name-based. It improves readability, reduces boilerplate code, and is widely used in modern JavaScript, React, and API handling.

## Question 3. Difference between nullish coalescing operator ?? and logical OR ||

### Short Answer

The **nullish coalescing operator (`??`)** returns the right-hand value only when the left-hand side is `null` or `undefined`, whereas the **logical OR (`||`)** returns the right-hand value when the left-hand side is any _falsy value_ (`false`, `0`, `""`, `NaN`, `null`, `undefined`).

---

# 1. Core Difference

| Operator                  | Fallback condition         | Treats as “empty”        |                 |                                                |
| ------------------------- | -------------------------- | ------------------------ | --------------- | ---------------------------------------------- |
| `                         |                            | ` (OR)                   | Any falsy value | `false`, `0`, `""`, `NaN`, `null`, `undefined` |
| `??` (Nullish coalescing) | Only `null` or `undefined` | Only `null`, `undefined` |                 |                                                |

---

# 2. Logical OR (`||`)

Returns the first **truthy** value.

```javascript id="or1"
const value = 0 || 100;

console.log(value); // 100
```

### Why?

- `0` is falsy → so fallback is used

---

### More examples

```javascript id="or2"
"" || "default"; // "default"
false || "yes"; // "yes"
NaN || 42; // 42
```

---

## ⚠️ Problem with `||`

It may override **valid values like `0` or empty string**

```javascript id="or3"
const quantity = 0;

console.log(quantity || 10); // 10 ❌ incorrect in many cases
```

Here, `0` is a valid value but gets replaced.

---

# 3. Nullish Coalescing (`??`)

Returns right-hand value only if left is `null` or `undefined`.

```javascript id="n1"
const value = 0 ?? 100;

console.log(value); // 0 ✅
```

---

### More examples

```javascript id="n2"
"" ?? "default"; // ""
false ?? true; // false
0 ?? 100; // 0
null ?? "fallback"; // "fallback"
undefined ?? 50; // 50
```

---

# 4. Real-world Example (Important for Interviews)

### User settings / configuration

```javascript id="rw1"
const userSettings = {
  theme: "",
  fontSize: 0,
};
```

---

### Using `||` (problematic)

```javascript id="rw2"
const theme = userSettings.theme || "dark";
console.log(theme); // "dark" ❌ overrides empty string
```

---

### Using `??` (correct)

```javascript id="rw3"
const theme = userSettings.theme ?? "dark";
console.log(theme); // "" ✅ preserved
```

---

# 5. API response handling example

```javascript id="api1"
const response = {
  data: 0,
};
```

### Using `||`

```javascript id="api2"
const value = response.data || 100;
console.log(value); // 100 ❌ wrong if 0 is valid
```

### Using `??`

```javascript id="api3"
const value = response.data ?? 100;
console.log(value); // 0 ✅ correct
```

---

# 6. Key Interview Insight

### Falsy values in JavaScript:

```javascript id="falsy"
false;
0;
("");
null;
undefined;
NaN;
```

- `||` treats ALL of them as fallback triggers
- `??` only treats `null` and `undefined`

---

# 7. Operator Precedence Gotcha

You **cannot mix `&&` or `||` directly with `??` without parentheses**

### ❌ Invalid

```javascript id="bad"
null || undefined ?? "default"
```

### ❌ Error:

> SyntaxError

---

### ✅ Correct

```javascript id="good"
(null || undefined) ?? "default";
```

---

# 8. When to use what?

## Use `||` when:

- You want fallback for _any falsy value_
- Example: UI toggles, boolean defaults

```javascript id="u1"
const isLoggedIn = user.status || false;
```

---

## Use `??` when:

- You only want fallback for missing values (`null` or `undefined`)
- Preserving valid falsy values like `0`, `""`, `false`

```javascript id="u2"
const pageSize = config.pageSize ?? 10;
```

---

# 9. Interview One-liner

> `||` returns the first truthy value and treats all falsy values as fallback triggers, while `??` only falls back when the value is `null` or `undefined`, making it safer for preserving valid falsy values like 0 or empty strings.

## Question 4. Difference between optional chaining ?. and normal property access

### Short Answer

**Optional chaining (`?.`)** safely accesses nested object properties and returns `undefined` if any part of the chain is `null` or `undefined`, instead of throwing an error.
**Normal property access (`.`)** directly accesses properties and throws an error if you try to access a property on `null` or `undefined`.

---

# 1. Core Difference

| Feature                      | Normal Access (`.`)  | Optional Chaining (`?.`) |
| ---------------------------- | -------------------- | ------------------------ |
| Access behavior              | Direct access        | Safe access              |
| If value is `null/undefined` | ❌ Throws error      | ✅ Returns `undefined`   |
| Risk                         | High (runtime crash) | Low (safe fallback)      |

---

# 2. Normal Property Access

### Example

```javascript id="npa1"
const user = {
  name: "Aman",
};

console.log(user.name); // "Aman"
```

### Problem scenario

```javascript id="npa2"
const user = null;

console.log(user.name); // ❌ TypeError
```

### Error:

> Cannot read properties of null

---

## Why this happens

JavaScript tries to access `name` on `null`, which is invalid.

---

# 3. Optional Chaining (`?.`)

### Safe access

```javascript id="oc1"
const user = null;

console.log(user?.name); // undefined ✅
```

No error is thrown.

---

### With nested objects

```javascript id="oc2"
const user = {
  profile: {
    address: {
      city: "Delhi",
    },
  },
};

console.log(user?.profile?.address?.city); // "Delhi"
```

If any level is missing:

```javascript id="oc3"
console.log(user?.profile?.contact?.phone); // undefined
```

---

# 4. Real-world API Example (Very Important)

### API response might be incomplete

```javascript id="api1"
const response = {
  data: {
    user: {
      name: "John",
    },
  },
};
```

### Safe access using `?.`

```javascript id="api2"
const phone = response?.data?.user?.contact?.phone;

console.log(phone); // undefined (no crash)
```

---

# 5. Without optional chaining (risk)

```javascript id="api3"
const phone = response.data.user.contact.phone;
// ❌ crashes if contact doesn't exist
```

---

# 6. Optional chaining with functions

```javascript id="fn1"
const user = {
  greet: () => "Hello",
};

console.log(user.greet?.()); // "Hello"
```

If function doesn't exist:

```javascript id="fn2"
const user = {};

console.log(user.greet?.()); // undefined (no error)
```

---

# 7. Optional chaining with arrays

```javascript id="arr1"
const users = [{ name: "Aman" }];

console.log(users?.[0]?.name); // "Aman"
```

If array is undefined:

```javascript id="arr2"
const users = null;

console.log(users?.[0]?.name); // undefined
```

---

# 8. Real-world analogy

## Normal access:

> You open a chain of doors without checking if each door exists → you might hit a wall and crash.

## Optional chaining:

> You check each door before opening → if any door is missing, you safely stop and return "nothing".

---

# 9. Common Pitfalls

## ❌ Misconception 1: It fixes all errors

Optional chaining only prevents **null/undefined errors**, not logic errors.

```javascript id="pit1"
const user = { age: 0 };

console.log(user?.age.toString()); // works, but careful
```

---

## ❌ Misconception 2: It replaces all checks

Still need validation for business logic:

```javascript id="pit2"
if (user?.age > 18) {
  // may behave unexpectedly if age is undefined
}
```

Better:

```javascript id="pit3"
if (user?.age != null && user.age > 18) {
}
```

---

# 10. Performance Note (Interview insight)

- Optional chaining adds **minor overhead**
- But it improves **code safety and readability significantly**
- Trade-off: negligible performance cost vs major reduction in runtime crashes

---

# 11. When to use what?

## Use normal access when:

- You are 100% sure data exists
- You want strict failure (fail-fast behavior)

```javascript id="use1"
console.log(user.name);
```

---

## Use optional chaining when:

- Working with API data
- Nested objects may be missing
- UI rendering uncertain data

```javascript id="use2"
const city = user?.address?.city;
```

---

# 12. Interview One-liner

> Normal property access throws an error when encountering `null` or `undefined`, whereas optional chaining (`?.`) safely short-circuits and returns `undefined` instead of crashing, making it ideal for working with uncertain or deeply nested data.

## Question 5. Explain Map vs Object

### Short Answer

A **Map** is a collection of key–value pairs where keys can be of any type and the order of insertion is preserved. An **Object** is also a key–value structure, but keys are usually strings or symbols, and it is primarily designed for structured data representation.

---

# 1. Core Difference

| Feature     | Object                                 | Map                                    |
| ----------- | -------------------------------------- | -------------------------------------- |
| Key types   | String / Symbol only                   | Any type (object, function, primitive) |
| Order       | Not reliably guaranteed (historically) | Maintains insertion order              |
| Size        | Manual (`Object.keys().length`)        | Built-in `.size`                       |
| Iteration   | Needs `Object.keys/entries`            | Direct iteration supported             |
| Performance | Good for small/structured data         | Better for frequent additions/removals |
| Prototype   | Has prototype chain                    | No default keys (clean map)            |

---

# 2. Object Basics

Objects are the most common structure in JavaScript.

```javascript id="obj1"
const user = {
  name: "Aman",
  age: 30,
};

console.log(user.name); // Aman
```

### Keys are always strings (implicitly)

```javascript id="obj2"
const obj = {};
obj[1] = "one";

console.log(obj["1"]); // "one"
```

👉 `1` becomes `"1"`

---

## Object limitations

```javascript id="obj3"
const obj = {};

obj[true] = "yes";
obj[{ a: 1 }] = "object key";

console.log(obj);
// keys become "[object Object]" and "true"
```

This shows key coercion problem.

---

# 3. Map Basics

Map is designed for **better key flexibility and iteration control**.

```javascript id="map1"
const map = new Map();

map.set("name", "Aman");
map.set(1, "number key");
map.set(true, "boolean key");

console.log(map.get(1)); // "number key"
```

---

## Key advantage: any type as key

```javascript id="map2"
const objKey = { id: 1 };

const map = new Map();
map.set(objKey, "Object as key");

console.log(map.get(objKey)); // "Object as key"
```

👉 Objects cannot do this cleanly.

---

# 4. Size difference

### Object

```javascript id="objsize"
const obj = { a: 1, b: 2 };

console.log(Object.keys(obj).length); // 2
```

### Map

```javascript id="mapsize"
const map = new Map([
  ["a", 1],
  ["b", 2],
]);

console.log(map.size); // 2
```

---

# 5. Iteration difference

### Object (less direct)

```javascript id="objiter"
const obj = { a: 1, b: 2 };

for (let key of Object.keys(obj)) {
  console.log(key, obj[key]);
}
```

---

### Map (clean iteration)

```javascript id="mapiter"
const map = new Map([
  ["a", 1],
  ["b", 2],
]);

for (let [key, value] of map) {
  console.log(key, value);
}
```

---

# 6. Order behavior

### Object

Historically unordered (modern engines preserve insertion order for most cases, but not fully reliable in all scenarios especially numeric keys).

```javascript id="objorder"
const obj = {
  b: 2,
  a: 1,
};
```

---

### Map

Always preserves insertion order:

```javascript id="maporder"
const map = new Map();

map.set("b", 2);
map.set("a", 1);

console.log([...map.keys()]); // ["b", "a"]
```

---

# 7. Performance difference (important interview point)

| Operation | Object                    | Map                                |
| --------- | ------------------------- | ---------------------------------- |
| Insert    | Fast                      | Fast (better for frequent updates) |
| Delete    | Slower (`delete` keyword) | Faster (`map.delete()`)            |
| Lookup    | Very fast                 | Very fast                          |
| Iteration | Slower for large sets     | Optimized for iteration            |

👉 Map performs better when frequently adding/removing entries.

---

# 8. Real-world usage examples

## Object use case (structured data)

```javascript id="objuse"
const user = {
  id: 1,
  name: "Aman",
  isActive: true,
};
```

✔ Best for:

- Models
- JSON data
- API responses

---

## Map use case (dynamic collections)

```javascript id="mapuse"
const cache = new Map();

cache.set("/api/user", { id: 1 });
cache.set("/api/posts", [1, 2, 3]);
```

✔ Best for:

- Caching
- Lookup tables
- Dynamic key-value storage
- Associative mapping

---

# 9. Garbage collection advantage

Map allows better memory control in some cases:

```javascript id="gcmap"
let obj = { key: "data" };

const map = new Map();
map.set(obj, "value");

obj = null; // object can be garbage collected if no other references
```

Object keys don’t behave as cleanly for this pattern.

---

# 10. Common pitfalls

## ❌ Object key coercion surprises

```javascript id="pitobj"
const obj = {};

obj[1] = "one";
obj["1"] = "string one";

console.log(obj); // same key overwrites
```

---

## ❌ Map misuse for simple data

Using Map when Object is enough adds unnecessary complexity.

---

# 11. When to use what?

## Use Object when:

- Data is static / structured
- You need JSON compatibility
- You are modeling entities

```javascript id="useobj"
const product = {
  id: 1,
  name: "Phone",
};
```

---

## Use Map when:

- Keys are dynamic or unknown
- Keys are not strings
- Frequent insert/delete operations
- Need guaranteed iteration order

```javascript id="usemap"
const sessions = new Map();
```

---

# 12. Interview One-liner

> An Object is best for structured, static key-value data with string keys, while a Map is a more powerful and flexible collection that supports any type of key, preserves insertion order, and is optimized for dynamic operations like frequent insertions and deletions.

## Question 6. Explain Set in JavaScript

### Short Answer

A **Set** in JavaScript is a built-in collection that stores **only unique values** (no duplicates) and maintains insertion order. It can hold values of any type (primitives or objects).

---

# 1. What is a Set?

A Set is used when you want to store a collection of **unique items**.

```javascript id="set1"
const set = new Set();

set.add(1);
set.add(2);
set.add(2);

console.log(set); // Set {1, 2}
```

👉 Duplicate `2` is ignored automatically.

---

# 2. Key Features of Set

| Feature         | Set                            |
| --------------- | ------------------------------ |
| Duplicates      | ❌ Not allowed                 |
| Order           | Preserves insertion order      |
| Value types     | Any type (primitive or object) |
| Access by index | ❌ Not supported               |
| Size            | `.size` property               |

---

# 3. Creating a Set

### From array

```javascript id="set2"
const numbers = new Set([1, 2, 3, 3, 4]);

console.log(numbers); // Set {1, 2, 3, 4}
```

---

# 4. Common Set Methods

## add()

```javascript id="set3"
const set = new Set();

set.add(10);
set.add(20);
```

---

## has()

Checks if value exists:

```javascript id="set4"
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(5)); // false
```

---

## delete()

```javascript id="set5"
const set = new Set([1, 2, 3]);

set.delete(2);

console.log(set); // Set {1, 3}
```

---

## clear()

```javascript id="set6"
const set = new Set([1, 2, 3]);

set.clear();

console.log(set); // Set {}
```

---

## size

```javascript id="set7"
const set = new Set([1, 2, 3]);

console.log(set.size); // 3
```

---

# 5. Iterating over a Set

```javascript id="set8"
const set = new Set(["a", "b", "c"]);

for (let value of set) {
  console.log(value);
}
```

Output:

```
a
b
c
```

---

# 6. Important Behavior: Object References

Set checks uniqueness using **reference equality** for objects.

```javascript id="set9"
const set = new Set();

set.add({ a: 1 });
set.add({ a: 1 });

console.log(set.size); // 2 ❗ different references
```

👉 Even though values look same, they are different objects in memory.

---

# 7. Real-world Use Cases

## 1. Removing duplicates from array

```javascript id="set10"
const arr = [1, 2, 2, 3, 4, 4];

const unique = [...new Set(arr)];

console.log(unique); // [1, 2, 3, 4]
```

✔ Very common interview question

---

## 2. Tracking unique users

```javascript id="set11"
const activeUsers = new Set();

activeUsers.add("user1");
activeUsers.add("user2");
activeUsers.add("user1");

console.log(activeUsers.size); // 2
```

---

## 3. Checking membership efficiently

```javascript id="set12"
const allowedIds = new Set([101, 102, 103]);

if (allowedIds.has(101)) {
  console.log("Access granted");
}
```

---

# 8. Set vs Array

| Feature      | Array        | Set                  |
| ------------ | ------------ | -------------------- |
| Duplicates   | Allowed      | Not allowed          |
| Lookup       | O(n)         | O(1) average (`has`) |
| Index access | Yes          | No                   |
| Use case     | Ordered list | Unique collection    |

---

# 9. Performance Insight

- `Set.has()` is generally faster than `Array.includes()` for large datasets
- Good for membership checks and deduplication

---

# 10. Common Pitfalls

## ❌ Expecting index access

```javascript id="pit1"
const set = new Set([1, 2, 3]);

console.log(set[0]); // undefined ❌
```

---

## ❌ Thinking Set sorts values

```javascript id="pit2"
const set = new Set([3, 1, 2]);

console.log([...set]); // [3, 1, 2] (not sorted)
```

---

## ❌ Object uniqueness confusion

```javascript id="pit3"
set.add({});
set.add({});

console.log(set.size); // 2 ❗ different references
```

---

# 11. When to use Set?

Use Set when:

- You need **unique values**
- You want **fast lookup**
- You want to remove duplicates
- You are tracking **distinct items**

```javascript id="use1"
const visitedPages = new Set();
```

---

# 12. Interview One-liner

> A Set is a built-in JavaScript collection that stores only unique values, preserves insertion order, and provides efficient membership checking using the `.has()` method.

## Question 7. How to remove duplicates from an array using Set

### Short Answer

You can remove duplicates from an array in JavaScript using a **Set** by converting the array into a Set (which automatically removes duplicates) and then converting it back to an array.

```javascript id="setdedup1"
const uniqueArray = [...new Set(array)];
```

---

# 1. How it works (conceptually)

A **Set only stores unique values**, so when you pass an array into it:

- duplicates are automatically removed
- insertion order is preserved

Then you spread it back into an array.

---

# 2. Basic Example

```javascript id="setdedup2"
const arr = [1, 2, 2, 3, 4, 4, 5];

const unique = [...new Set(arr)];

console.log(unique); // [1, 2, 3, 4, 5]
```

---

# 3. Step-by-step breakdown

```javascript id="setdedup3"
const arr = [1, 1, 2, 3];

const set = new Set(arr);
// Set {1, 2, 3}

const unique = [...set];
// [1, 2, 3]
```

---

# 4. Alternative ways (important for interviews)

## 1. Using Array.from()

```javascript id="setdedup4"
const arr = [1, 2, 2, 3];

const unique = Array.from(new Set(arr));

console.log(unique); // [1, 2, 3]
```

---

## 2. Using filter() (without Set)

```javascript id="setdedup5"
const arr = [1, 2, 2, 3];

const unique = arr.filter((item, index) => {
  return arr.indexOf(item) === index;
});

console.log(unique); // [1, 2, 3]
```

⚠️ Less efficient than Set approach.

---

# 5. Real-world example

## Removing duplicate user IDs

```javascript id="setdedup6"
const userIds = [101, 102, 103, 101, 104, 102];

const uniqueUserIds = [...new Set(userIds)];

console.log(uniqueUserIds);
// [101, 102, 103, 104]
```

---

# 6. Handling strings

Works for strings too:

```javascript id="setdedup7"
const letters = ["a", "b", "a", "c", "b"];

const unique = [...new Set(letters)];

console.log(unique); // ["a", "b", "c"]
```

---

# 7. Important interview insight

### Why Set is better than filter + indexOf

| Approach         | Time Complexity | Performance           |
| ---------------- | --------------- | --------------------- |
| Set              | O(n)            | Fast                  |
| filter + indexOf | O(n²)           | Slow for large arrays |

👉 Set is preferred in real-world production code.

---

# 8. Object array limitation (important edge case)

```javascript id="setdedup8"
const arr = [{ a: 1 }, { a: 1 }];

const unique = [...new Set(arr)];

console.log(unique); // both objects remain ❗
```

### Why?

Because objects are compared by **reference**, not value.

---

## Correct deep dedup approach for objects

```javascript id="setdedup9"
const arr = [{ id: 1 }, { id: 1 }, { id: 2 }];

const unique = Array.from(new Map(arr.map((item) => [item.id, item])).values());

console.log(unique);
// [{ id: 1 }, { id: 2 }]
```

---

# 9. When to use Set for deduplication

✔ Best for:

- numbers
- strings
- booleans
- primitive arrays

❌ Not ideal for:

- complex objects (unless using keys)

---

# 10. Interview one-liner

> The simplest and most efficient way to remove duplicates from an array in JavaScript is by converting it into a Set, which automatically enforces uniqueness, and then converting it back into an array using the spread operator or Array.from().

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
