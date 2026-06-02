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

### Short Answer

**WeakMap** and **WeakSet** are special versions of `Map` and `Set` in JavaScript where **keys (WeakMap) or values (WeakSet) must be objects and are held weakly**, meaning they do not prevent garbage collection.

---

# 1. Core Idea (Very Important)

| Feature            | Map / Set  | WeakMap / WeakSet           |
| ------------------ | ---------- | --------------------------- |
| Holds primitives   | Yes        | ❌ No                       |
| Holds objects      | Yes        | Yes (only objects)          |
| Garbage collection | No control | Objects can be auto-removed |
| Iterable           | Yes        | ❌ No                       |
| Size property      | Yes        | ❌ No                       |

---

# 2. WeakMap

A **WeakMap** is a collection of **key-value pairs where keys must be objects** and are held weakly.

## Syntax

```javascript id="wm1"
const wm = new WeakMap();
```

---

## Example

```javascript id="wm2"
let user = { name: "Aman" };

const wm = new WeakMap();
wm.set(user, "userData");

console.log(wm.get(user)); // "userData"
```

---

## Key concept: Weak reference

If the object key is no longer referenced anywhere else, it is **automatically garbage collected**.

```javascript id="wm3"
let user = { name: "Aman" };

const wm = new WeakMap();
wm.set(user, "data");

user = null; // object becomes eligible for garbage collection
```

👉 After this, entry may be removed automatically.

---

## Real-world use case

### 1. Private data storage

```javascript id="wm4"
const privateData = new WeakMap();

class User {
  constructor(name, age) {
    privateData.set(this, { age });
    this.name = name;
  }

  getAge() {
    return privateData.get(this).age;
  }
}

const u = new User("Aman", 25);

console.log(u.getAge()); // 25
```

✔ Used to simulate private variables

---

### 2. DOM element metadata

```javascript id="wm5"
const tooltipMap = new WeakMap();

const button = document.querySelector("button");

tooltipMap.set(button, { text: "Click me" });
```

If the DOM element is removed → memory is automatically cleaned.

---

# 3. WeakSet

A **WeakSet** is a collection of **objects only (no primitives)** and also uses weak references.

## Syntax

```javascript id="ws1"
const ws = new WeakSet();
```

---

## Example

```javascript id="ws2"
let obj = { id: 1 };

const ws = new WeakSet();

ws.add(obj);

console.log(ws.has(obj)); // true
```

---

## Garbage collection behavior

```javascript id="ws3"
let obj = { id: 1 };

const ws = new WeakSet();
ws.add(obj);

obj = null; // object becomes eligible for GC
```

👉 Object will be removed from WeakSet automatically.

---

# 4. Real-world use cases of WeakSet

## 1. Tracking processed objects

```javascript id="ws4"
const processed = new WeakSet();

function process(obj) {
  if (processed.has(obj)) return;

  console.log("Processing:", obj);
  processed.add(obj);
}

const data = { id: 1 };

process(data);
process(data); // ignored
```

---

## 2. DOM tracking

```javascript id="ws5"
const visitedNodes = new WeakSet();

const element = document.getElementById("app");

visitedNodes.add(element);
```

If element is removed → no memory leak.

---

# 5. Why “Weak”?

Because references are **weakly held**, meaning:

- They do NOT prevent garbage collection
- If object is unreachable elsewhere → GC removes it automatically

---

# 6. Key limitations (very important for interviews)

## ❌ Not iterable

```javascript id="lim1"
const wm = new WeakMap();

// ❌ Not allowed
for (let item of wm) {
}
```

---

## ❌ No size property

```javascript id="lim2"
console.log(wm.size); // undefined
```

---

## ❌ Only objects allowed

```javascript id="lim3"
const wm = new WeakMap();

wm.set("key", "value"); // ❌ TypeError
```

---

# 7. WeakMap vs WeakSet

| Feature   | WeakMap                | WeakSet          |
| --------- | ---------------------- | ---------------- |
| Structure | key → value            | only values      |
| Stores    | Object keys            | Object values    |
| Use case  | metadata, private data | tracking objects |
| Iterable  | ❌                     | ❌               |

---

# 8. Real-world analogy

## WeakMap

> A locker system where keys are people (objects). If the person leaves the system, their locker disappears automatically.

## WeakSet

> A guest list where only current guests (objects) are tracked. Once they leave, they are automatically removed.

---

# 9. When to use WeakMap / WeakSet?

## Use WeakMap when:

- You need **private data per object**
- You want **memory-safe metadata storage**
- Working with DOM elements or class instances

---

## Use WeakSet when:

- You want to track **whether an object has been processed**
- You want **memory-safe object tracking**

---

# 10. Interview one-liner

> WeakMap and WeakSet are specialized collections in JavaScript that store object references weakly, allowing automatic garbage collection of unused objects, making them ideal for memory-safe metadata storage and object tracking without preventing cleanup.

## Question 9. Explain currying in JavaScript

### Short Answer

**Currying** is a technique in JavaScript where a function with multiple arguments is transformed into a sequence of functions, each taking one argument at a time.

---

# 1. Core Idea

Instead of calling a function like this:

```javascript id="cur1"
add(2, 3, 4);
```

You call it like this:

```javascript id="cur2"
add(2)(3)(4);
```

---

# 2. Basic Example of Currying

### Normal function

```javascript id="cur3"
function add(a, b) {
  return a + b;
}

console.log(add(2, 3)); // 5
```

---

### Curried version

```javascript id="cur4"
function add(a) {
  return function (b) {
    return a + b;
  };
}

console.log(add(2)(3)); // 5
```

---

# 3. How currying works internally

Each function returns another function until all arguments are received.

```javascript id="cur5"
function multiply(a) {
  return function (b) {
    return function (c) {
      return a * b * c;
    };
  };
}

console.log(multiply(2)(3)(4)); // 24
```

---

# 4. Arrow function version (modern JS)

```javascript id="cur6"
const add = (a) => (b) => (c) => a + b + c;

console.log(add(1)(2)(3)); // 6
```

---

# 5. Why currying is useful (important for interviews)

## 1. Function reuse (partial application)

```javascript id="cur7"
const multiply = (a) => (b) => a * b;

const double = multiply(2);
const triple = multiply(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

---

## 2. Cleaner configuration functions

```javascript id="cur8"
const log = (level) => (message) => console.log(`[${level}] ${message}`);

const errorLog = log("ERROR");

errorLog("Something went wrong");
```

---

## 3. Event handling / functional composition

```javascript id="cur9"
const withUser = (user) => (action) => {
  console.log(`User: ${user}`);
  action();
};

const adminAction = withUser("Admin");

adminAction(() => console.log("Deleted record"));
```

---

# 6. Real-world analogy

Currying is like ordering coffee:

- First: choose size ☕
- Then: choose milk 🥛
- Then: choose sugar 🍬

Each step returns a new “customized order function”.

---

# 7. Currying vs Normal Function

| Feature     | Normal Function | Curried Function |
| ----------- | --------------- | ---------------- |
| Arguments   | All at once     | One at a time    |
| Flexibility | Low             | High             |
| Reusability | Limited         | High             |
| Readability | Simple          | Functional style |

---

# 8. Partial Application vs Currying (important interview concept)

## Currying:

Breaks function into single-argument functions.

```javascript id="cur10"
add(a)(b)(c);
```

## Partial Application:

Pre-fills some arguments.

```javascript id="cur11"
function add(a, b, c) {
  return a + b + c;
}

const add2 = add.bind(null, 2);

console.log(add2(3, 4)); // 9
```

👉 Partial application ≠ currying, but they are related.

---

# 9. Generic curry function (advanced interview level)

```javascript id="cur12"
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return (...next) => curried(...args, ...next);
  };
}
```

### Usage:

```javascript id="cur13"
function sum(a, b, c) {
  return a + b + c;
}

const curriedSum = curry(sum);

console.log(curriedSum(1)(2)(3)); // 6
console.log(curriedSum(1, 2)(3)); // 6
```

---

# 10. Common pitfalls

## ❌ Overusing currying

Too many nested functions can reduce readability:

```javascript id="cur14"
a => b => c => d => e => ...
```

---

## ❌ Misunderstanding execution

```javascript id="cur15"
add(2, 3)(4); // ❌ if not designed for currying
```

---

# 11. When to use currying?

Use currying when:

- You need **function reuse**
- You want **functional composition**
- You are building **configurable utilities**
- You work in **functional programming patterns**

Avoid when:

- Simple operations are enough
- Code readability becomes worse

---

# 12. Interview One-liner

> Currying is a functional programming technique in JavaScript where a function with multiple arguments is transformed into a series of nested functions, each taking a single argument, enabling better reusability, partial application, and function composition.

## Question 10. What is memoization? Give example

### Short Answer

**Memoization** is an optimization technique in JavaScript where you **cache the results of expensive function calls** and return the cached result when the same inputs occur again, instead of recomputing.

---

# 1. Core Idea

If a function is called multiple times with the same input:

- First time → compute result
- Next time → return stored result (cache)

This improves performance significantly for expensive operations.

---

# 2. Simple Example (Without Memoization)

```javascript id="mem1"
function slowSquare(n) {
  console.log("Computing...");
  return n * n;
}

console.log(slowSquare(5)); // Computing... 25
console.log(slowSquare(5)); // Computing... 25 (recomputed)
```

👉 Even though input is same, computation happens again.

---

# 3. With Memoization

```javascript id="mem2"
function memoizedSquare() {
  const cache = {};

  return function (n) {
    if (cache[n]) {
      return cache[n];
    }

    console.log("Computing...");
    const result = n * n;
    cache[n] = result;
    return result;
  };
}

const square = memoizedSquare();

console.log(square(5)); // Computing... 25
console.log(square(5)); // 25 (cached)
```

---

# 4. How it works internally

- `cache` stores input → output mapping
- Before computing:
  - check cache

- If exists → return cached result
- If not → compute and store

---

# 5. Real-world Example (Fibonacci)

## Without memoization (slow)

```javascript id="mem3"
function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
}

console.log(fib(40)); // very slow ❌
```

👉 Recomputes same values repeatedly

---

## With memoization (fast)

```javascript id="mem4"
function fibMemo() {
  const cache = {};

  function fib(n) {
    if (n in cache) return cache[n];

    if (n <= 1) return n;

    cache[n] = fib(n - 1) + fib(n - 2);
    return cache[n];
  }

  return fib;
}

const fib = fibMemo();

console.log(fib(40)); // fast ✅
```

---

# 6. Real-world use cases

## 1. Expensive calculations

- Fibonacci
- Factorials
- Mathematical computations

---

## 2. API response caching

```javascript id="mem5"
const cache = {};

async function fetchUser(id) {
  if (cache[id]) return cache[id];

  const res = await fetch(`/api/user/${id}`);
  const data = await res.json();

  cache[id] = data;
  return data;
}
```

---

## 3. React optimization (important)

React uses memoization concepts:

- `useMemo()` → memoize computed values
- `useCallback()` → memoize functions

```javascript id="mem6"
const result = useMemo(() => expensiveCalc(a, b), [a, b]);
```

---

# 7. Key benefits

✔ Improves performance
✔ Avoids repeated calculations
✔ Reduces CPU usage
✔ Useful in recursive functions

---

# 8. Limitations / pitfalls

## ❌ Memory usage increases

Because results are stored:

```javascript id="mem7"
// cache keeps growing
```

---

## ❌ Not useful for unique inputs

If inputs rarely repeat → memoization is wasted.

---

## ❌ Key limitation (important interview point)

Works best when:

- function is **pure** (same input → same output)

---

# 9. Memoization vs caching

| Concept     | Scope                                    |
| ----------- | ---------------------------------------- |
| Memoization | Function-level caching                   |
| Caching     | Broader (API, browser, DB, system level) |

---

# 10. Real-world analogy

> Memoization is like a calculator that remembers previous answers. If you already asked “5 × 5”, it won’t compute again—it will just recall “25”.

---

# 11. Interview one-liner

> Memoization is an optimization technique in JavaScript where function results are cached based on input arguments so that repeated calls with the same inputs return the cached result instead of recomputing, improving performance for expensive operations.

## Question 11. Difference between var, let, const in closures

### Short Answer

In closures, the difference between `var`, `let`, and `const` comes down to **scope behavior and how variables are captured**:

- **`var`** → function-scoped, can cause unexpected shared state in closures (classic bug due to hoisting)
- **`let`** → block-scoped, creates a new binding per iteration/block, works correctly with closures
- **`const`** → block-scoped like `let`, but cannot be reassigned (safe for closure values that shouldn’t change)

---

# 1. First: What is a closure? (quick recap)

A **closure** is when a function “remembers” variables from its outer scope even after that scope has finished execution.

```javascript id="cl1"
function outer() {
  let x = 10;

  function inner() {
    console.log(x);
  }

  return inner;
}

const fn = outer();
fn(); // 10
```

---

# 2. Problem with `var` in closures

`var` is **function-scoped**, not block-scoped. This causes all closures to share the same variable.

## Classic interview problem

```javascript id="var1"
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  }, 100);
}
```

### Output:

```
3
3
3
```

### Why?

- Only **one `i` exists (function-scoped)**
- All closures reference the same variable
- By the time callback runs → loop is finished → `i = 3`

---

# 3. Fix using `let`

`let` is **block-scoped**, so each loop iteration creates a new binding.

```javascript id="let1"
for (let i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  }, 100);
}
```

### Output:

```
0
1
2
```

### Why?

Each iteration creates a **new lexical environment**, so closures capture different `i` values.

---

# 4. `const` in closures

`const` behaves like `let` in terms of scope but cannot be reassigned.

```javascript id="const1"
function outer() {
  const message = "Hello";

  return function inner() {
    console.log(message);
  };
}

const fn = outer();
fn(); // Hello
```

---

## Important distinction

```javascript id="const2"
const x = 10;
x = 20; // ❌ Error: Assignment to constant variable
```

But closure behavior is the same as `let`:

- same lexical capture
- same block scope
- difference is only **immutability of binding**

---

# 5. Key difference in closure behavior

| Feature           | var              | let                   | const                        |
| ----------------- | ---------------- | --------------------- | ---------------------------- |
| Scope             | Function         | Block                 | Block                        |
| Closure capture   | Shared reference | New binding per block | New binding per block        |
| Loop issue        | Yes              | No                    | No                           |
| Reassignment      | Yes              | Yes                   | No                           |
| Best for closures | ❌ risky         | ✅ safe               | ✅ safest (when value fixed) |

---

# 6. Real-world example (very important)

## Using `var` (buggy behavior)

```javascript id="real1"
function createButtons() {
  var buttons = [];

  for (var i = 0; i < 3; i++) {
    buttons[i] = function () {
      console.log("Button", i);
    };
  }

  return buttons;
}

createButtons()[0](); // Button 3 ❌
```

---

## Using `let` (correct behavior)

```javascript id="real2"
function createButtons() {
  let buttons = [];

  for (let i = 0; i < 3; i++) {
    buttons[i] = function () {
      console.log("Button", i);
    };
  }

  return buttons;
}

createButtons()[0](); // Button 0 ✅
```

---

# 7. Why this happens (interview insight)

### var behavior:

- single shared binding in function scope
- closures point to same memory reference

### let/const behavior:

- new lexical environment per block iteration
- closures capture different bindings

---

# 8. Memory perspective (advanced interview angle)

- `var` → one memory slot reused
- `let/const` in loops → multiple bindings created per iteration

This is why `let` is safer for async callbacks.

---

# 9. Common pitfalls

## ❌ Thinking closures “copy values”

Closures do NOT copy values—they **reference variables**.

---

## ❌ Assuming const makes objects immutable

```javascript id="pit1"
const obj = { a: 1 };
obj.a = 2; // allowed
```

Only binding is constant, not object content.

---

# 10. Best practices

✔ Prefer `let` over `var` always
✔ Use `const` by default unless reassignment is needed
✔ Avoid `var` in modern JavaScript
✔ Always use `let` in loops with async/closures

---

# 11. Interview one-liner

> In closures, `var` creates a single function-scoped variable shared across all closures, often causing unexpected behavior, while `let` and `const` create block-scoped bindings that ensure each closure captures its own distinct value, making them safer and more predictable.

## Question 12. Explain function hoisting with examples

## Question 13. Explain how this behaves differently in arrow functions vs normal functions

## Question 14. How does JavaScript handle asynchronous code internally?

## Question 15. What is microtask and macrotask queue?

## Question 16. How to implement debouncing and throttling?

## Question 17. Difference between == vs === in depth

## Question 18. What is garbage collection in JavaScript?

## Question 19. How to prevent memory leaks in JS?

## Question 20. Explain JavaScript modules in ES6 vs CommonJS
