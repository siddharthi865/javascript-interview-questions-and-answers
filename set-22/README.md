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

# ✅ Direct Answer

`requestIdleCallback()` is used to schedule **low-priority work** (like analytics, logging, prefetching, or non-critical UI updates) during the browser’s **idle periods**, so it does not block rendering or user interactions.

In short:

> Run heavy or non-urgent tasks only when the browser is idle.

---

# 🧠 Interview-Level Explanation

Browsers prioritize work like:

1. User input
2. Rendering (layout, paint)
3. Animations
4. JavaScript execution

If the main thread is busy, low-priority work should wait.

`requestIdleCallback()` allows you to:

- execute code when the browser is idle
- avoid jank in UI rendering
- improve responsiveness

---

# 📌 1. Basic Syntax

```js id="ric1"
requestIdleCallback((deadline) => {
  // low-priority work here
});
```

---

## `deadline` object:

```js id="ric2"
deadline.timeRemaining(); // how many ms left in idle time
deadline.didTimeout; // whether callback was forced
```

---

# 🚀 2. Basic Example

```js id="ric3"
requestIdleCallback((deadline) => {
  console.log("Idle time available:", deadline.timeRemaining());

  while (deadline.timeRemaining() > 0) {
    console.log("Doing low-priority work...");
    break;
  }
});
```

---

# 📌 3. Real Use Case: Analytics Logging

```js id="ric4"
function logEvent(event) {
  requestIdleCallback(() => {
    sendToServer(event); // non-critical task
  });
}
```

---

# 📌 4. Chunking Heavy Work (Important Pattern)

Instead of blocking the main thread:

---

## ❌ Bad (blocking)

```js id="ric5"
for (let i = 0; i < 1000000; i++) {
  processItem(i);
}
```

---

## ✅ Good (idle chunking)

```js id="ric6"
const tasks = Array.from({ length: 1000000 }, (_, i) => i);

function processChunk(deadline) {
  while (deadline.timeRemaining() > 0 && tasks.length > 0) {
    const item = tasks.shift();
    processItem(item);
  }

  if (tasks.length > 0) {
    requestIdleCallback(processChunk);
  }
}

requestIdleCallback(processChunk);
```

---

# 📌 5. Prefetching Example (Very Common Interview Use Case)

```js id="ric7"
requestIdleCallback(() => {
  import("./heavy-module.js").then((mod) => {
    mod.init();
  });
});
```

---

# 📌 6. DOM Pre-rendering Optimization

```js id="ric8"
requestIdleCallback(() => {
  const images = document.querySelectorAll("img.lazy");

  images.forEach((img) => {
    img.src = img.dataset.src;
  });
});
```

---

# ⚠️ 7. Important Limitation

`requestIdleCallback` is:

- not supported in all browsers (Safari partial support historically)
- not guaranteed to run immediately
- may be delayed indefinitely if main thread is busy

---

# 📌 8. Fallback Implementation (Important Interview Question)

```js id="ric9"
function safeRequestIdleCallback(cb) {
  if ("requestIdleCallback" in window) {
    return requestIdleCallback(cb);
  }

  return setTimeout(() => {
    const start = Date.now();

    cb({
      timeRemaining: () => Math.max(0, 50 - (Date.now() - start)),
      didTimeout: true,
    });
  }, 1);
}
```

---

# 📊 requestIdleCallback vs requestAnimationFrame

| Feature  | requestIdleCallback | requestAnimationFrame |
| -------- | ------------------- | --------------------- |
| Purpose  | background tasks    | UI rendering          |
| Timing   | idle time           | before repaint        |
| Priority | low                 | high                  |
| Use case | analytics, prefetch | animations            |

---

# 🧠 When to Use It

## Best use cases:

✔ analytics batching
✔ logging
✔ lazy loading modules
✔ precomputing data
✔ cache warming
✔ non-critical DOM updates

---

## Avoid using for:

❌ animations
❌ input handling
❌ layout-critical updates

---

# 🧠 Senior-Level Insight

### 1. Idle time is unpredictable

Browser may:

- skip idle periods
- deprioritize idle callbacks under load

---

### 2. Scheduling is cooperative

Unlike Web Workers, this is:

> cooperative scheduling, not parallel execution

---

### 3. Often combined with other APIs

Modern apps combine:

- `requestIdleCallback`
- `requestAnimationFrame`
- Web Workers

for full performance layering.

---

# 📌 Best Practices

✔ Always check `timeRemaining()`
✔ Break work into chunks
✔ Provide fallback (`setTimeout`)
✔ Don’t rely on strict execution timing
✔ Avoid long-running loops inside callback

---

# 🧾 Interview Summary

`requestIdleCallback()` is a browser API that schedules low-priority tasks to run during idle periods, helping improve UI responsiveness by avoiding interference with critical rendering and input handling. It provides a `deadline` object to check remaining idle time and is best used for tasks like analytics, prefetching, and background processing. Since execution is not guaranteed immediately, it should be used with chunking strategies and fallback mechanisms.

---

If you want, I can next explain **how browsers schedule idle time internally and how this relates to the event loop, task queue, and rendering pipeline (very senior-level performance topic)**.

## Question 3. Difference between `clientX/clientY` and `pageX/pageY`

# ✅ Direct Answer

- **`clientX / clientY`**: coordinates relative to the **visible browser viewport**
- **`pageX / pageY`**: coordinates relative to the **entire document (including scroll offset)**

So the key difference is:

> `pageX/pageY = clientX/clientY + scroll offset`

---

# 🧠 Interview-Level Explanation

In JavaScript mouse events (`MouseEvent`), both `clientX/clientY` and `pageX/pageY` describe the position of the mouse, but they use different reference frames.

---

## 📍 1. `clientX` / `clientY`

These represent the position **relative to the viewport (visible area of the browser window)**.

- (0, 0) is the **top-left corner of the visible screen**
- Does NOT change with page scrolling

```js
document.addEventListener("click", (e) => {
  console.log("clientX:", e.clientX);
  console.log("clientY:", e.clientY);
});
```

### 🧠 Think of it as:

> “Where is the mouse inside what I currently see?”

---

## 📄 2. `pageX` / `pageY`

These represent the position **relative to the full document**, including the portion scrolled out of view.

- (0, 0) is the **top-left of the entire page**
- Includes scroll offset automatically

```js
document.addEventListener("click", (e) => {
  console.log("pageX:", e.pageX);
  console.log("pageY:", e.pageY);
});
```

### 🧠 Think of it as:

> “Where is the mouse on the full webpage, even the hidden part?”

---

# 🔄 Relationship Between Them

```js
pageX = clientX + window.scrollX;
pageY = clientY + window.scrollY;
```

---

## 🧪 Example Scenario

Imagine:

- You scroll down 500px
- Click at the same visible point

| Property | Value |
| -------- | ----- |
| clientY  | 200   |
| pageY    | 700   |

Because:

```
pageY = 200 + 500 (scroll offset)
```

---

# 📊 Visual Mental Model

```
TOP OF DOCUMENT (pageX/pageY origin)
┌─────────────────────────────┐
│ hidden content (scrolled)   │
│                             │
│   ↓ scroll 500px            │
│ ┌─────────────────────────┐ │
│ │ visible viewport        │ │ ← clientX/clientY origin
│ │ click here (200px)      │ │
│ └─────────────────────────┘ │
└─────────────────────────────┘
```

---

# ⚖️ Key Differences Table

| Feature          | clientX/clientY            | pageX/pageY               |
| ---------------- | -------------------------- | ------------------------- |
| Reference        | Viewport                   | Full document             |
| Includes scroll? | ❌ No                      | ✅ Yes                    |
| Origin (0,0)     | Top-left of visible screen | Top-left of page          |
| Use case         | UI positioning in viewport | Absolute page positioning |

---

# 🧠 When to Use What

## ✅ Use `clientX/clientY` when:

- positioning tooltips
- drawing overlays in viewport
- drag-and-drop inside screen
- UI interactions relative to visible area

```js
tooltip.style.left = e.clientX + "px";
tooltip.style.top = e.clientY + "px";
```

---

## ✅ Use `pageX/pageY` when:

- placing elements in full document coordinates
- working with scroll-aware layouts
- saving click positions for later restoration

```js
savedClick = {
  x: e.pageX,
  y: e.pageY,
};
```

---

# ⚠️ Common Pitfalls

## ❌ 1. Using clientX for absolute positioning

If page is scrolled, tooltip will appear offset incorrectly.

---

## ❌ 2. Ignoring scroll position

Some developers manually compute page position incorrectly:

Bad:

```js
const y = e.clientY; // wrong for scroll-aware layouts
```

Good:

```js
const y = e.pageY;
```

---

## ❌ 3. Mixing coordinate systems

Using `clientX` with document-based layout calculations leads to misalignment bugs.

---

# 🧠 Related Interview Follow-up

### Q: How does this relate to `getBoundingClientRect()`?

- `getBoundingClientRect()` → viewport-based (like `clientX`)
- `offsetTop/offsetLeft` → document-based (like `pageX` but older/less reliable)

---

# 🧾 Interview Summary

`clientX/clientY` give mouse coordinates relative to the visible browser viewport, while `pageX/pageY` give coordinates relative to the entire document, including scroll offset. The relationship is `pageX = clientX + scrollX`. `clientX` is preferred for UI positioning, while `pageX` is useful for absolute document-level calculations.

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
