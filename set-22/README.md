# Set 22

| S.No. | Question                                                                                                                                            |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How to implement debounced input handlers efficiently](#question-1-how-to-implement-debounced-input-handlers-efficiently)                          |
| 2.    | [How to use `requestIdleCallback` for low-priority tasks](#question-2-how-to-use-requestidlecallback-for-low-priority-tasks)                        |
| 3.    | [Difference between `clientX/clientY` and `pageX/pageY`](#question-3-difference-between-clientxclienty-and-pagexpagey)                              |
| 4.    | [How to handle touch events and gestures in JS](#question-4-how-to-handle-touch-events-and-gestures-in-js)                                          |
| 5.    | [How to implement drag-and-drop using `dragstart`, `dragover`, `drop`](#question-5-how-to-implement-drag-and-drop-using-dragstart-dragover-drop)    |
| 6.    | [Difference between capturing, bubbling, and target phases](#question-6-difference-between-capturing-bubbling-and-target-phases)                    |
| 7.    | [How to prevent memory leaks in long-lived event listeners](#question-7-how-to-prevent-memory-leaks-in-long-lived-event-listeners)                  |
| 8.    | [How to optimize scrolling performance for large tables/grids](#question-8-how-to-optimize-scrolling-performance-for-large-tablesgrids)             |
| 9.    | [How to implement a virtual DOM from scratch](#question-9-how-to-implement-a-virtual-dom-from-scratch)                                              |
| 10.   | [How to throttle expensive computations during resize/scroll](#question-10-how-to-throttle-expensive-computations-during-resizescroll)              |
| 11.   | [How to use `requestAnimationFrame` for smooth UI updates](#question-11-how-to-use-requestanimationframe-for-smooth-ui-updates)                     |
| 12.   | [How to detect browser support for features (feature detection)](#question-12-how-to-detect-browser-support-for-features-feature-detection)         |
| 13.   | [How to write polyfills for missing features](#question-13-how-to-write-polyfills-for-missing-features)                                             |
| 14.   | [How to implement a single-page app navigation without a framework](#question-14-how-to-implement-a-single-page-app-navigation-without-a-framework) |
| 15.   | [How to manage history state using `history.pushState`](#question-15-how-to-manage-history-state-using-historypushstate)                            |
| 16.   | [How to handle hash-based routing manually](#question-16-how-to-handle-hash-based-routing-manually)                                                 |
| 17.   | [How to debounce API calls triggered by user input](#question-17-how-to-debounce-api-calls-triggered-by-user-input)                                 |
| 18.   | [How to implement infinite pagination (infinite scroll) in frontend](#question-18-how-to-implement-infinite-pagination-infinite-scroll-in-frontend) |
| 19.   | [How to optimize large JSON parsing using streams or workers](#question-19-how-to-optimize-large-json-parsing-using-streams-or-workers)             |
| 20.   | [How to use `structuredClone` to deep-copy objects efficiently](#question-20-how-to-use-structuredclone-to-deep-copy-objects-efficiently)           |

## Question 1. How to implement debounced input handlers efficiently

> A debounced input handler delays execution of a function until the user stops typing (or triggering events) for a specified amount of time. This is commonly used for search boxes, API calls, autosave, resize events, and validation to avoid excessive executions.

A clean and efficient debounce implementation in JavaScript typically uses `setTimeout` and `clearTimeout`.

### Basic Debounce Implementation

```js
function debounce(fn, delay) {
  let timer;

  return function (...args) {
    clearTimeout(timer);

    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}
```

Usage:

```js
function handleSearch(event) {
  console.log("Searching:", event.target.value);
}

const debouncedSearch = debounce(handleSearch, 300);

document.getElementById("search").addEventListener("input", debouncedSearch);
```

### How It Works

Every time the input event fires:

1. Existing timer is cleared
2. A new timer starts
3. Function executes only after no new events occur within `delay`

This prevents running expensive operations on every keystroke.

### Why Debouncing Is Important

Without debouncing:

```txt
User types "javascript"
→ 10 API requests
```

With debouncing:

```txt
User types "javascript"
(wait 300ms)
→ 1 API request
```

This improves:

- Performance
- Network efficiency
- UI responsiveness
- Server load

### Real Example: Search API

```js
async function fetchResults(query) {
  const response = await fetch(`/search?q=${query}`);
  const data = await response.json();

  console.log(data);
}

const debouncedFetch = debounce(fetchResults, 500);

input.addEventListener("input", (e) => {
  debouncedFetch(e.target.value);
});
```

### Preserving `this` Context

Using `fn.apply(this, args)` is important because the returned function may be used as a method or event handler.

Bad version:

```js
fn(args);
```

Good version:

```js
fn.apply(this, args);
```

This preserves:

- method context
- DOM event context
- object instance references

### Advanced Debounce with Immediate Execution

Sometimes you want the function to execute immediately on the first call and then ignore repeated calls until delay passes.

Example:

```js
function debounce(fn, delay, immediate = false) {
  let timer;

  return function (...args) {
    const callNow = immediate && !timer;

    clearTimeout(timer);

    timer = setTimeout(() => {
      timer = null;

      if (!immediate) {
        fn.apply(this, args);
      }
    }, delay);

    if (callNow) {
      fn.apply(this, args);
    }
  };
}
```

Usage:

```js
const debounced = debounce(saveDraft, 1000, true);
```

### Debounce vs Throttle

Many interviewers ask this follow-up.

| Feature   | Debounce         | Throttle           |
| --------- | ---------------- | ------------------ |
| Execution | After inactivity | At fixed intervals |
| Best For  | Search input     | Scroll/resize      |
| Example   | API search       | Infinite scroll    |

Debounce:

```txt
----typing----[execute once]
```

Throttle:

```txt
--execute--execute--execute--
```

### Efficient Modern Pattern with AbortController

For search inputs, debouncing alone is not enough. You should also cancel old requests.

```js
function debounce(fn, delay) {
  let timer;

  return (...args) => {
    clearTimeout(timer);

    timer = setTimeout(() => {
      fn(...args);
    }, delay);
  };
}

let controller;

const search = debounce(async (query) => {
  if (controller) {
    controller.abort();
  }

  controller = new AbortController();

  try {
    const response = await fetch(`/search?q=${query}`, {
      signal: controller.signal,
    });

    const data = await response.json();

    console.log(data);
  } catch (err) {
    if (err.name !== "AbortError") {
      console.error(err);
    }
  }
}, 300);
```

This prevents race conditions where slower old responses overwrite newer results.

### React Example (Common Frontend Interview Question)

Using debounce with React hooks:

```js
import { useMemo } from "react";

function SearchBox() {
  const debouncedSearch = useMemo(() => {
    return debounce((value) => {
      console.log(value);
    }, 300);
  }, []);

  return <input onChange={(e) => debouncedSearch(e.target.value)} />;
}
```

Why `useMemo`?

Without it:

- debounce gets recreated every render
- timer state resets
- debouncing breaks

### Common Pitfalls

#### 1. Recreating Debounced Function

Bad:

```js
input.addEventListener("input", debounce(fn, 300));
```

inside repeated render cycles or loops.

Always reuse the same debounced instance.

#### 2. Losing Event Object

Some frameworks recycle synthetic events.

Bad:

```js
debouncedFn(event);
```

Better:

```js
debouncedFn(event.target.value);
```

#### 3. Memory Leaks

If components unmount, clear timers if needed.

Example:

```js
clearTimeout(timer);
```

especially in frameworks like React/Vue.

### Time Complexity

Each invocation:

- `clearTimeout`: O(1)
- `setTimeout`: O(1)

Overall debounce overhead is constant time.

### Best Practices

- Debounce expensive operations only
- Use 200–500ms for search inputs
- Combine with `AbortController` for APIs
- Preserve `this` using `apply`
- Avoid recreating debounced handlers
- Use throttle instead for continuous UI updates

### Summary

A debounced input handler delays execution until the user stops triggering events for a specified duration. It is implemented using `setTimeout` and `clearTimeout`, improving performance by reducing unnecessary executions such as API calls during typing. Efficient implementations preserve `this`, avoid recreating handlers, and often combine debouncing with `AbortController` to prevent stale asynchronous requests.

## Question 2. How to use `requestIdleCallback` for low-priority tasks

## Question 3. Difference between `clientX/clientY` and `pageX/pageY`

## Question 4. How to handle touch events and gestures in JS

## Question 5. How to implement drag-and-drop using `dragstart`, `dragover`, `drop`

## Question 6. Difference between capturing, bubbling, and target phases

## Question 7. How to prevent memory leaks in long-lived event listeners

## Question 8. How to optimize scrolling performance for large tables/grids

## Question 9. How to implement a virtual DOM from scratch

## Question 10. How to throttle expensive computations during resize/scroll

## Question 11. How to use `requestAnimationFrame` for smooth UI updates

## Question 12. How to detect browser support for features (feature detection)

## Question 13. How to write polyfills for missing features

## Question 14. How to implement a single-page app navigation without a framework

## Question 15. How to manage history state using `history.pushState`

## Question 16. How to handle hash-based routing manually

## Question 17. How to debounce API calls triggered by user input

## Question 18. How to implement infinite pagination (infinite scroll) in frontend

## Question 19. How to optimize large JSON parsing using streams or workers

## Question 20. How to use `structuredClone` to deep-copy objects efficiently
