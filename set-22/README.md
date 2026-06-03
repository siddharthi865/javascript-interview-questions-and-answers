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

# ✅ Direct Answer

Touch events and gestures in JavaScript are handled using **Touch Events (`touchstart`, `touchmove`, `touchend`)** or the modern **Pointer Events API**, and higher-level gesture logic is built by tracking movement, distance, direction, and timing between events.

For production apps, **Pointer Events are preferred**, while Touch Events are still useful for understanding low-level mobile behavior.

---

# 🧠 Interview-Level Explanation

Touch and gesture handling in JavaScript involves detecting:

- When a finger touches the screen
- How it moves
- When it lifts
- Whether the movement represents a gesture (swipe, pinch, zoom, rotate)

There are two main approaches:

---

# 1. 📱 Touch Events API (Legacy but important)

## Core events:

- `touchstart` → finger touches screen
- `touchmove` → finger moves
- `touchend` → finger lifted
- `touchcancel` → interrupted (e.g. system gesture)

---

## 📌 Basic example: detecting swipe direction

```js id="v1k8qx"
let startX = 0;
let startY = 0;

document.addEventListener("touchstart", (e) => {
  const touch = e.touches[0];
  startX = touch.clientX;
  startY = touch.clientY;
});

document.addEventListener("touchend", (e) => {
  const touch = e.changedTouches[0];

  const deltaX = touch.clientX - startX;
  const deltaY = touch.clientY - startY;

  if (Math.abs(deltaX) > Math.abs(deltaY)) {
    console.log(deltaX > 0 ? "Swipe Right" : "Swipe Left");
  } else {
    console.log(deltaY > 0 ? "Swipe Down" : "Swipe Up");
  }
});
```

---

## 🧠 Key concepts in Touch Events

### `touches`

All active touch points on screen

### `targetTouches`

Touches on the same element

### `changedTouches`

Touches involved in current event

---

## ⚠️ Pitfalls with Touch Events

### 1. Scroll interference

Browsers may scroll during `touchmove`.

Fix:

```js id="m0f3kc"
event.preventDefault();
```

But must use:

```css
touch-action: none;
```

for modern browsers.

---

### 2. Multi-touch complexity

Pinch/zoom requires tracking multiple touch points:

```js id="9p3xkq"
if (event.touches.length === 2) {
  // pinch gesture
}
```

---

### 3. Not unified with mouse

You must separately handle mouse + touch → duplication.

---

# 2. 🧭 Pointer Events API (Modern Recommended)

Pointer Events unify:

- Mouse
- Touch
- Pen/stylus

---

## 📌 Basic example

```js id="2xkq7v"
element.addEventListener("pointerdown", (e) => {
  console.log("Pointer down:", e.clientX, e.clientY);
});

element.addEventListener("pointermove", (e) => {
  console.log("Pointer move");
});

element.addEventListener("pointerup", (e) => {
  console.log("Pointer up");
});
```

---

## 🧠 Why Pointer Events are better

| Feature        | Touch Events | Pointer Events |
| -------------- | ------------ | -------------- |
| Mobile support | ✅           | ✅             |
| Mouse support  | ❌           | ✅             |
| Pen support    | ❌           | ✅             |
| Unified API    | ❌           | ✅             |
| Recommended    | ❌ legacy    | ✅ modern      |

---

## 📌 Prevent scrolling during gestures

```css id="5k9l0a"
element {
  touch-action: none;
}
```

This is critical for gesture-based apps.

---

# 3. 🧩 Building Gestures (Core Interview Concept)

JS does NOT provide built-in “swipe/zoom/rotate” gestures.

You implement them using math + event tracking.

---

## 📌 Swipe detection logic

- Capture start position
- Capture end position
- Calculate delta
- Determine direction

---

## 📌 Pinch-to-zoom (2-finger gesture)

```js id="h2k8pm"
let initialDistance = null;

function getDistance(t1, t2) {
  return Math.hypot(t2.clientX - t1.clientX, t2.clientY - t1.clientY);
}

element.addEventListener("touchstart", (e) => {
  if (e.touches.length === 2) {
    initialDistance = getDistance(e.touches[0], e.touches[1]);
  }
});

element.addEventListener("touchmove", (e) => {
  if (e.touches.length === 2) {
    const newDistance = getDistance(e.touches[0], e.touches[1]);
    const scale = newDistance / initialDistance;

    console.log("Zoom scale:", scale);
  }
});
```

---

# 4. ⚡ Gesture Libraries (Real-world approach)

In production apps, you usually don’t implement everything manually.

Popular libraries:

- Hammer.js (classic gesture handling)
- ZingTouch
- Interact.js

They handle:

- swipe
- pinch
- rotate
- pan
- inertia

---

# 5. 🧠 Event Loop & Performance Considerations

Touch/move events fire **very frequently (~60–120 FPS)**.

### ❌ Bad:

```js id="l9p3dv"
element.addEventListener("touchmove", heavyFunction);
```

### ✅ Good:

Use throttling or `requestAnimationFrame`:

```js id="7m1kqa"
let ticking = false;

element.addEventListener("touchmove", (e) => {
  if (!ticking) {
    window.requestAnimationFrame(() => {
      handleMove(e);
      ticking = false;
    });

    ticking = true;
  }
});
```

---

# 6. 🧠 Best Practices

### ✔ Prefer Pointer Events

Modern standard for all input types.

---

### ✔ Use `touch-action` CSS

Prevents unwanted scrolling behavior.

```css id="9k2qwe"
touch-action: none;
```

---

### ✔ Avoid heavy computation in move handlers

Use throttling or `requestAnimationFrame`.

---

### ✔ Normalize gesture logic

Always convert gestures into:

- deltaX / deltaY
- velocity
- duration

---

### ✔ Clean up listeners

Important for SPA memory safety:

```js id="2kq9mz"
element.removeEventListener("pointermove", handler);
```

---

# 🧾 Interview Summary

Touch and gesture handling in JavaScript is implemented using either Touch Events (`touchstart`, `touchmove`, `touchend`) or the modern Pointer Events API. Gestures like swipe, pinch, and rotate are not built-in and must be derived from coordinate changes, distances, and timing between events. Pointer Events are preferred because they unify mouse, touch, and pen input. Performance optimization is crucial because move events fire at a very high frequency.

## Question 5. How to implement drag-and-drop using `dragstart`, `dragover`, `drop`

# ✅ Direct Answer

Drag-and-drop in JavaScript is implemented using the **HTML Drag and Drop API**, primarily with three events:

- `dragstart` → when dragging begins
- `dragover` → when dragged item is over a valid drop target (must call `preventDefault`)
- `drop` → when item is dropped

You also typically use `dataTransfer` to pass data between drag source and drop target.

---

# 🧠 Interview-Level Explanation

The Drag and Drop API works by:

1. Marking an element as draggable (`draggable="true"`)
2. Capturing drag metadata in `dragstart`
3. Allowing drop zones via `dragover`
4. Handling the final placement in `drop`

The browser uses a shared object called:

> `event.dataTransfer`

to transfer data during drag operations.

---

# 📌 Basic Working Example

## HTML

```html id="k2p9ab"
<div id="item" draggable="true">Drag me</div>

<div id="dropzone">Drop here</div>
```

---

## JS Implementation

### 1. dragstart

```js id="a9m2xz"
const item = document.getElementById("item");

item.addEventListener("dragstart", (e) => {
  e.dataTransfer.setData("text/plain", e.target.id);
});
```

---

### 2. dragover (IMPORTANT)

```js id="p3k8nv"
const dropzone = document.getElementById("dropzone");

dropzone.addEventListener("dragover", (e) => {
  e.preventDefault(); // REQUIRED to allow dropping
});
```

---

### 3. drop

```js id="x7q1lm"
dropzone.addEventListener("drop", (e) => {
  e.preventDefault();

  const id = e.dataTransfer.getData("text/plain");
  const draggedElement = document.getElementById(id);

  dropzone.appendChild(draggedElement);

  console.log("Dropped!");
});
```

---

# 🧠 Key Concept: `dataTransfer`

This object is the **bridge between drag source and drop target**.

## Common methods:

```js id="c1v8dd"
e.dataTransfer.setData("text/plain", "value");
e.dataTransfer.getData("text/plain");
```

## You can also set:

- `text/plain`
- `text/html`
- custom MIME types

---

# ⚙️ Optional Enhancements

## 1. dragenter / dragleave (UI feedback)

```js id="m0k9zx"
dropzone.addEventListener("dragenter", () => {
  dropzone.classList.add("active");
});

dropzone.addEventListener("dragleave", () => {
  dropzone.classList.remove("active");
});
```

---

## 2. dragend (cleanup)

```js id="q8n3lp"
item.addEventListener("dragend", () => {
  console.log("Drag finished");
});
```

---

# 🎯 Real-World Example: Reordering List Items

```js id="t5p9kc"
let dragged;

document.querySelectorAll(".item").forEach((el) => {
  el.addEventListener("dragstart", (e) => {
    dragged = e.target;
  });

  el.addEventListener("dragover", (e) => {
    e.preventDefault();
  });

  el.addEventListener("drop", (e) => {
    e.preventDefault();

    if (dragged !== e.target) {
      const parent = e.target.parentNode;
      parent.insertBefore(dragged, e.target);
    }
  });
});
```

---

# ⚠️ Important Interview Pitfalls

## ❌ 1. Forgetting `preventDefault()` in dragover

Without it, drop will NOT work.

```js id="n1p3kd"
dropzone.addEventListener("dragover", (e) => {
  // missing preventDefault ❌
});
```

---

## ❌ 2. Using innerHTML for data transfer

Bad:

```js id="v9k2lm"
e.dataTransfer.setData("html", e.target.innerHTML);
```

Better:

- use IDs or structured data (JSON)

---

## ❌ 3. Not handling mobile devices

⚠️ Native Drag-and-Drop API is:

- poorly supported on mobile
- inconsistent on touch devices

👉 For mobile, use Pointer Events instead.

---

## ❌ 4. Losing reference to dragged element

Always store reference or use `dataTransfer`.

---

# 🧠 How It Works Internally (Important Senior Insight)

When dragging:

- Browser creates a **drag session**
- Tracks pointer movement
- Fires:
  - `dragstart`
  - many `dragover` events
  - `drop`
  - `dragend`

Event frequency can be very high → avoid heavy logic inside handlers.

---

# ⚡ Performance Best Practices

- Keep `dragover` handler lightweight
- Avoid DOM mutations during dragover
- Use CSS classes for visual feedback
- Debounce expensive operations if needed

---

# ⚖️ Drag-and-Drop vs Pointer Events

| Feature         | Drag & Drop API | Pointer Events |
| --------------- | --------------- | -------------- |
| Desktop support | ✅              | ✅             |
| Mobile support  | ❌ weak         | ✅ strong      |
| Custom gestures | ❌ limited      | ✅ flexible    |
| File dragging   | ✅ built-in     | ❌ manual      |

---

# 📁 Bonus: File Drag-and-Drop

```js id="r3m8pp"
dropzone.addEventListener("drop", (e) => {
  e.preventDefault();

  const files = e.dataTransfer.files;

  console.log(files);
});
```

Used in:

- uploaders
- image editors
- cloud storage apps

---

# 🧾 Interview Summary

Drag-and-drop in JavaScript is implemented using the Drag and Drop API with `dragstart`, `dragover`, and `drop` events. Data is passed via `dataTransfer`, and `preventDefault()` in `dragover` is required to enable dropping. While effective on desktop, it has limitations on mobile, so Pointer Events are often preferred for modern cross-device gesture handling.

## Question 6. Difference between capturing, bubbling, and target phases

# ✅ Direct Answer

In JavaScript event propagation, events flow through **three phases**:

1. **Capturing phase** → event travels from the root → target element
2. **Target phase** → event reaches the actual element
3. **Bubbling phase** → event travels from target → back up to the root

The key difference is the **direction of event flow**:

- Capturing = top → down
- Bubbling = bottom → up
- Target = event is at the element itself

---

# 🧠 Interview-Level Explanation

When an event (like `click`) occurs on an element, it doesn't just fire on that element. It travels through the DOM in a **structured propagation model** defined by the Event Capturing & Bubbling system.

This is part of the **DOM Event Propagation model**:

```
Document → HTML → Body → Parent → Target → Parent → Body → Document
```

---

# 📌 1. Capturing Phase (Trickling Down)

### 🔹 What happens?

The event starts from the **window/document** and moves **down the DOM tree toward the target element**.

### 🔹 Also called:

- Event trickling

### 🔹 How to listen in capturing phase:

```js id="c1p9kd"
element.addEventListener("click", handler, true);
```

The `true` enables capturing.

---

### 📌 Example

```html id="a9x2lm"
<div id="parent">
  <button id="child">Click</button>
</div>
```

```js id="q8m1nv"
document
  .getElementById("parent")
  .addEventListener("click", () => console.log("Parent capture"), true);

document
  .getElementById("child")
  .addEventListener("click", () => console.log("Child click"), true);
```

### Output:

```
Parent capture
Child click
```

---

# 🎯 2. Target Phase

### 🔹 What happens?

The event reaches the **actual element that was clicked**.

### 🔹 Important points:

- This phase is always executed
- Capturing and bubbling both converge here
- `event.target === actual clicked element`

---

### 📌 Example

```js id="t4k9qp"
button.addEventListener("click", () => {
  console.log("Target phase: button clicked");
});
```

---

### 🧠 Key properties:

```js id="z9x3lm"
event.target; // original element clicked
event.currentTarget; // element handling the event
```

---

# 🔄 3. Bubbling Phase (Default Behavior)

### 🔹 What happens?

After reaching the target, the event moves **back up the DOM tree**.

### 🔹 This is the default phase used in most apps.

```js id="b7m2xn"
element.addEventListener("click", handler, false); // default
```

---

### 📌 Example

```js id="p3k8lm"
document
  .getElementById("parent")
  .addEventListener("click", () => console.log("Parent bubble"));

document
  .getElementById("child")
  .addEventListener("click", () => console.log("Child bubble"));
```

### Output:

```
Child bubble
Parent bubble
```

---

# 📊 Full Flow Visualization

```
CAPTURING PHASE
Document → Parent → Child

TARGET PHASE
Child

BUBBLING PHASE
Child → Parent → Document
```

---

# ⚖️ Comparison Table

| Phase     | Direction   | Default? | Use case             |
| --------- | ----------- | -------- | -------------------- |
| Capturing | Top → Down  | ❌       | Rare (interception)  |
| Target    | At element  | ✔️       | Core event execution |
| Bubbling  | Bottom → Up | ✔️       | Event delegation     |

---

# 🧠 Why Bubbling Is Most Important

Because it enables **event delegation**:

### Instead of:

```js id="d1m8qp"
document.querySelectorAll("button").forEach((btn) => {
  btn.addEventListener("click", handler);
});
```

### You can do:

```js id="k2n9vx"
document.body.addEventListener("click", (e) => {
  if (e.target.tagName === "BUTTON") {
    console.log("Button clicked");
  }
});
```

✔ Efficient
✔ Fewer listeners
✔ Works for dynamic elements

---

# ⚠️ Important Pitfalls (Interview Favorites)

## ❌ 1. Confusing target vs currentTarget

```js id="x9p2lm"
parent.addEventListener("click", (e) => {
  console.log(e.target); // actual clicked element
  console.log(e.currentTarget); // parent element
});
```

---

## ❌ 2. Forgetting event propagation order

Many assume:

> child → parent always

But actually:

> capturing → target → bubbling

---

## ❌ 3. Not controlling propagation

You can stop flow:

```js id="m8k2qp"
event.stopPropagation();
```

or stop all listeners:

```js id="v2n9lm"
event.stopImmediatePropagation();
```

---

# 🧠 Advanced Insight (Senior-Level)

- Events are implemented using a **tree traversal algorithm**
- DOM is traversed twice:
  - downward (capturing)
  - upward (bubbling)

- Target phase is a “logical stop point”

---

# 🧾 Interview Summary

JavaScript event propagation has three phases: capturing (event moves from root to target), target (event reaches the actual element), and bubbling (event moves back up from target to root). By default, events use bubbling. Capturing is used rarely, while bubbling enables powerful patterns like event delegation. Understanding `target` vs `currentTarget` and propagation control methods like `stopPropagation()` is essential for real-world DOM event handling.

## Question 7. How to prevent memory leaks in long-lived event listeners

# ✅ Direct Answer

To prevent memory leaks in long-lived event listeners, you must **properly remove event listeners when they are no longer needed**, avoid capturing unnecessary references in closures, and ensure DOM nodes and handlers are garbage-collectable.

The core idea is:

> Always clean up listeners + avoid retaining unintended references.

---

# 🧠 Interview-Level Explanation

In JavaScript, memory leaks commonly occur when:

- Event listeners stay attached to DOM nodes that are no longer used
- Closures retain references to large objects or DOM elements
- Components (SPA frameworks) mount/unmount but listeners are not cleaned up
- Global listeners accumulate over time

Because JavaScript uses **garbage collection (GC)**, memory is freed only when objects are **unreachable**. Event listeners often keep objects reachable unintentionally.

---

# 📌 1. Always Remove Event Listeners

### ❌ Problem

```js id="a1k9xq"
function handleClick() {
  console.log("clicked");
}

button.addEventListener("click", handleClick);

// later DOM removed but listener still exists ❌
```

If `button` is removed but listener remains referenced, memory is retained.

---

### ✅ Solution

```js id="c3m8vn"
button.removeEventListener("click", handleClick);
```

⚠️ Important rule:

> The function reference must be the same.

---

# 🧠 2. Avoid Anonymous Handlers (Big Interview Trap)

### ❌ Bad

```js id="x9p2lm"
button.addEventListener("click", () => {
  console.log("clicked");
});
```

You cannot remove this later because it has no reference.

---

### ✅ Good

```js id="k2n8qp"
const handleClick = () => {
  console.log("clicked");
};

button.addEventListener("click", handleClick);

// cleanup
button.removeEventListener("click", handleClick);
```

---

# 📌 3. Clean Up in SPA Framework Lifecycle

In React/Vue/Angular-like apps:

### React example

```js id="r4p9xn"
useEffect(() => {
  const handler = () => console.log("resize");

  window.addEventListener("resize", handler);

  return () => {
    window.removeEventListener("resize", handler);
  };
}, []);
```

### Why this matters:

- Components unmount
- If listener stays → memory leak

---

# 📌 4. Be Careful with Closures

Closures can accidentally retain large objects.

### ❌ Bad

```js id="z8m2qp"
function setup() {
  const bigData = new Array(1000000).fill("data");

  window.addEventListener("click", () => {
    console.log(bigData.length);
  });
}
```

Even after `setup()` ends:

- `bigData` is retained in memory ❌

---

### ✅ Fix

Detach or avoid capturing unnecessary data:

```js id="v2n9lm"
function setup() {
  const bigData = new Array(1000000).fill("data");

  const handler = () => {
    console.log("clicked");
  };

  window.addEventListener("click", handler);
}
```

Or explicitly nullify references when done.

---

# 📌 5. Use AbortController (Modern Best Practice)

A clean modern way to manage multiple listeners:

```js id="a9k3lm"
const controller = new AbortController();

window.addEventListener("scroll", () => console.log("scrolling"), {
  signal: controller.signal,
});

// later cleanup
controller.abort();
```

### Why this is powerful:

- removes all listeners at once
- avoids manual tracking
- reduces boilerplate

---

# 📌 6. Avoid Global Listeners When Possible

### ❌ Problem

```js id="g1k8pq"
window.addEventListener("mousemove", handler);
```

If not removed → runs forever.

---

### ✅ Better

- attach only when needed
- remove on exit conditions
- throttle heavy events

---

# 📌 7. Debounce/Throttle Long-lived Events

Frequent events like:

- scroll
- resize
- mousemove

can cause performance + memory pressure.

```js id="d9m2qp"
window.addEventListener("scroll", debounce(handler, 200));
```

---

# 📌 8. Nullify References When Done

Especially in modules or services:

```js id="n8p3lm"
let handler = null;

function init() {
  handler = () => console.log("event");
  window.addEventListener("click", handler);
}

function cleanup() {
  window.removeEventListener("click", handler);
  handler = null;
}
```

---

# 📊 Common Memory Leak Sources

| Cause                | Example                            |
| -------------------- | ---------------------------------- |
| Unremoved listeners  | `addEventListener` without cleanup |
| Closures             | retaining DOM/data unintentionally |
| Global subscriptions | window/document events             |
| Timers               | `setInterval` not cleared          |
| Detached DOM refs    | nodes still referenced in JS       |

---

# ⚠️ Advanced Pitfalls (Senior Interview Level)

## 1. DOM removed ≠ GC freed

If JS still references element → not collected.

```js id="x2p9lm"
let el = document.getElementById("box");

document.body.removeChild(el);

// still referenced → memory leak risk
```

---

## 2. Event listener retains entire object graph

A listener keeps closure scope alive.

---

## 3. Multiple bindings accidentally stacking

```js id="p4m8qp"
window.addEventListener("resize", handler);
window.addEventListener("resize", handler); // duplicate ❌
```

---

# 🧠 Best Practices Summary

✔ Always remove event listeners when no longer needed
✔ Avoid anonymous functions for listeners
✔ Use lifecycle cleanup in frameworks
✔ Be careful with closures capturing large objects
✔ Prefer `AbortController` for modern cleanup
✔ Throttle/debounce frequent events
✔ Avoid unnecessary global listeners

---

# 🧾 Interview Summary

Memory leaks in event listeners occur when handlers or their closures keep references alive after they are no longer needed. To prevent this, always remove event listeners, avoid anonymous handlers, clean up in component lifecycles, and be mindful of closures capturing large objects. Modern approaches like `AbortController` simplify cleanup by allowing bulk cancellation of listeners. Proper event management ensures efficient memory usage and prevents performance degradation in long-running applications.

## Question 8. How to optimize scrolling performance for large tables/grids

# ✅ Direct Answer

To optimize scrolling performance for large tables or grids, you should **avoid rendering all rows at once** and instead use techniques like **virtualization (windowing)**, **row recycling, lazy rendering, and efficient DOM updates** to keep the DOM small and reduce layout/reflow costs.

The key idea is:

> Render only what is visible in the viewport (+ small buffer), not the entire dataset.

---

# 🧠 Interview-Level Explanation

Large tables are expensive because:

- Thousands of DOM nodes = heavy memory usage
- Each scroll triggers layout + paint
- Recalculation of styles and layout becomes slow
- JavaScript event handlers may block the main thread

So optimization focuses on:

### 🎯 Goal:

Minimize:

- DOM nodes
- layout recalculations
- paint/repaint cycles

---

# 🚀 1. Virtualization (MOST IMPORTANT CONCEPT)

Instead of rendering 100,000 rows:

👉 Render only visible rows (~20–50)

---

## 📌 How it works

```text
Total rows: 100,000
Visible rows: 20

Only 20 rows exist in DOM at any time
```

---

## 📌 Basic Virtual Scroll Implementation

```js id="v1k9qp"
const container = document.getElementById("container");
const rowHeight = 40;
const totalRows = 100000;

container.style.height = "400px";
container.style.overflow = "auto";

const spacer = document.createElement("div");
spacer.style.height = `${totalRows * rowHeight}px`;
container.appendChild(spacer);

const visibleBox = document.createElement("div");
container.appendChild(visibleBox);

container.addEventListener("scroll", () => {
  const scrollTop = container.scrollTop;

  const start = Math.floor(scrollTop / rowHeight);
  const end = start + 10;

  visibleBox.style.position = "absolute";
  visibleBox.style.top = `${start * rowHeight}px`;

  visibleBox.innerHTML = "";

  for (let i = start; i < end; i++) {
    const row = document.createElement("div");
    row.style.height = `${rowHeight}px`;
    row.textContent = `Row ${i}`;
    visibleBox.appendChild(row);
  }
});
```

---

# 🧠 Why This Works

Instead of:

❌ 100,000 DOM nodes

You get:

✔ ~10–30 DOM nodes only

---

# 📌 2. Windowing Libraries (Production Standard)

Instead of building manually:

- `react-window`
- `react-virtualized`
- `vue-virtual-scroller`

---

## Example (React Window)

```js id="k2p8lm"
import { FixedSizeList } from "react-window";

const Row = ({ index, style }) => <div style={style}>Row {index}</div>;

export default function Table() {
  return (
    <FixedSizeList height={400} itemCount={100000} itemSize={35} width={300}>
      {Row}
    </FixedSizeList>
  );
}
```

---

# 📌 3. Use `requestAnimationFrame` for Scroll Handling

Avoid running heavy logic on every scroll event.

---

## ❌ Bad

```js id="m8p2qp"
container.addEventListener("scroll", handler);
```

(scroll fires many times per frame)

---

## ✅ Good

```js id="d9k3lm"
let ticking = false;

container.addEventListener("scroll", () => {
  if (!ticking) {
    requestAnimationFrame(() => {
      updateVisibleRows();
      ticking = false;
    });

    ticking = true;
  }
});
```

---

# 📌 4. Reduce Layout Thrashing

Avoid mixing reads & writes:

---

## ❌ Bad

```js id="x2m8qp"
el.style.height = "100px";
console.log(el.offsetHeight); // forces reflow
```

---

## ✅ Good

```js id="p9k3lm"
const height = el.offsetHeight;

el.style.height = "100px";
```

---

# 📌 5. Use CSS Containment

Helps browser isolate rendering:

```css id="c1k8qp"
.table-row {
  contain: layout paint;
}
```

---

### Why it helps:

- prevents repaint bubbling
- isolates layout recalculation

---

# 📌 6. Use `position: absolute` for rows

Instead of reflowing whole table:

```css id="v3m8qp"
.row {
  position: absolute;
}
```

This avoids shifting DOM structure during scroll.

---

# 📌 7. Avoid Expensive DOM Operations

### ❌ Bad

```js id="g8k3lm"
container.innerHTML = newRows;
```

(heavy re-render)

---

### ✅ Better

- reuse DOM nodes
- update only textContent
- use document fragments

```js id="t2p9lm"
const fragment = document.createDocumentFragment();
```

---

# 📌 8. Debounce or Throttle Scroll Events

```js id="b8k2qp"
function throttle(fn, delay) {
  let last = 0;

  return (...args) => {
    const now = Date.now();
    if (now - last > delay) {
      last = now;
      fn(...args);
    }
  };
}
```

---

# 📌 9. Avoid Heavy Styles

Expensive CSS triggers:

- box-shadow
- filters
- large gradients
- complex selectors

---

# 📌 10. Reduce Repaints with `transform`

Instead of:

```css id="r1k9qp"
top: 100px;
```

Use:

```css id="z3m8lm"
transform: translateY(100px);
```

✔ GPU accelerated
✔ faster rendering

---

# 📊 Performance Strategy Summary

| Technique                   | Impact     |
| --------------------------- | ---------- |
| Virtualization              | ⭐⭐⭐⭐⭐ |
| requestAnimationFrame       | ⭐⭐⭐⭐   |
| CSS containment             | ⭐⭐⭐     |
| Throttling scroll           | ⭐⭐⭐     |
| DOM reuse                   | ⭐⭐⭐⭐   |
| transform instead of layout | ⭐⭐⭐⭐   |

---

# ⚠️ Common Pitfalls

## ❌ Rendering full dataset

Kills performance immediately.

---

## ❌ Re-rendering entire table on scroll

Causes layout thrashing.

---

## ❌ Heavy logic in scroll handler

Leads to frame drops.

---

## ❌ Ignoring GC pressure

Too many DOM nodes = memory spikes.

---

# 🧠 Senior-Level Insight

Modern virtual tables are:

- **windowed rendering engine**
- with **binary search for scroll position**
- and **fixed row height assumptions**
- or **dynamic measurement caching**

Advanced systems (like AG Grid) also use:

- row pooling
- incremental rendering
- offscreen DOM buffers

---

# 🧾 Interview Summary

To optimize scrolling in large tables, the primary technique is virtualization, where only visible rows are rendered in the DOM. Additional optimizations include throttling scroll events using `requestAnimationFrame`, reducing layout recalculations, using CSS containment, and avoiding full DOM re-renders. Together, these strategies ensure smooth 60fps scrolling even with very large datasets.

## Question 9. How to implement a virtual DOM from scratch

# ✅ Direct Answer

A Virtual DOM can be implemented by creating a **JavaScript representation of the real DOM**, then performing a **diffing algorithm** to detect changes and applying only minimal updates to the real DOM via a **patch function**.

At a high level:

> Virtual DOM = JS tree → diff old vs new tree → patch real DOM efficiently

---

# 🧠 Interview-Level Explanation

The Virtual DOM solves a core problem:

### ❌ Problem with direct DOM manipulation

- DOM operations are slow
- Frequent updates cause reflow/repaint
- Updating entire UI is inefficient

### ✅ Virtual DOM approach

- Keep a lightweight JS copy of DOM
- Recompute UI in memory
- Compare old vs new tree (diffing)
- Apply only minimal DOM updates

---

# 🧩 Architecture Overview

```text id="vdom1"
JSX / render()
      ↓
Virtual DOM Tree (JS objects)
      ↓
Diff algorithm (reconciliation)
      ↓
Minimal DOM updates (patch)
      ↓
Real DOM
```

---

# 📌 Step 1: Represent Virtual DOM

We model DOM nodes as plain objects:

```js id="vdom2"
function createElement(type, props = {}, ...children) {
  return {
    type,
    props: props || {},
    children: children.flat(),
  };
}
```

---

## Example Virtual Node

```js id="vdom3"
const vdom = createElement(
  "div",
  { id: "app" },
  createElement("h1", null, "Hello"),
  createElement("p", null, "Virtual DOM"),
);
```

---

# 📌 Step 2: Render Virtual DOM → Real DOM

```js id="vdom4"
function render(vnode) {
  // text node
  if (typeof vnode === "string") {
    return document.createTextNode(vnode);
  }

  const el = document.createElement(vnode.type);

  // set props
  for (const key in vnode.props) {
    el.setAttribute(key, vnode.props[key]);
  }

  // render children
  vnode.children.forEach((child) => {
    el.appendChild(render(child));
  });

  return el;
}
```

---

## Usage

```js id="vdom5"
document.getElementById("root").appendChild(render(vdom));
```

---

# 📌 Step 3: Diffing Algorithm (Core Concept)

We compare:

- oldVirtualDOM
- newVirtualDOM

Goal:
👉 Find minimal changes

---

## Simple Diff Strategy (Interview Version)

We compare:

- node type
- text content
- props
- children length

---

```js id="vdom6"
function diff(oldVNode, newVNode) {
  if (!oldVNode) {
    return { type: "CREATE", newVNode };
  }

  if (!newVNode) {
    return { type: "REMOVE" };
  }

  if (typeof oldVNode !== typeof newVNode) {
    return { type: "REPLACE", newVNode };
  }

  if (typeof oldVNode === "string") {
    if (oldVNode !== newVNode) {
      return { type: "TEXT", text: newVNode };
    }
    return null;
  }

  if (oldVNode.type !== newVNode.type) {
    return { type: "REPLACE", newVNode };
  }

  return {
    type: "UPDATE",
    props: diffProps(oldVNode.props, newVNode.props),
    children: diffChildren(oldVNode.children, newVNode.children),
  };
}
```

---

## Diff Props

```js id="vdom7"
function diffProps(oldProps, newProps) {
  const patches = {};

  // changed or added
  for (const key in newProps) {
    if (oldProps[key] !== newProps[key]) {
      patches[key] = newProps[key];
    }
  }

  // removed props
  for (const key in oldProps) {
    if (!(key in newProps)) {
      patches[key] = null;
    }
  }

  return patches;
}
```

---

## Diff Children

```js id="vdom8"
function diffChildren(oldChildren, newChildren) {
  const patches = [];
  const maxLength = Math.max(oldChildren.length, newChildren.length);

  for (let i = 0; i < maxLength; i++) {
    patches.push(diff(oldChildren[i], newChildren[i]));
  }

  return patches;
}
```

---

# 📌 Step 4: Patch (Apply Changes to Real DOM)

```js id="vdom9"
function patch(parent, patchObj, index = 0) {
  if (!patchObj) return;

  const el = parent.childNodes[index];

  switch (patchObj.type) {
    case "CREATE":
      parent.appendChild(render(patchObj.newVNode));
      break;

    case "REMOVE":
      parent.removeChild(el);
      break;

    case "REPLACE":
      parent.replaceChild(render(patchObj.newVNode), el);
      break;

    case "TEXT":
      el.textContent = patchObj.text;
      break;

    case "UPDATE":
      // update props
      for (const key in patchObj.props) {
        if (patchObj.props[key] === null) {
          el.removeAttribute(key);
        } else {
          el.setAttribute(key, patchObj.props[key]);
        }
      }

      // patch children
      patchObj.children.forEach((childPatch, i) => {
        patch(el, childPatch, i);
      });
      break;
  }
}
```

---

# 📌 Step 5: Example Update Cycle

```js id="vdom10"
let oldTree = createElement("div", null, "Hello");

let newTree = createElement("div", null, "Hello World");

const patchObj = diff(oldTree, newTree);

const root = document.getElementById("root");

patch(root, patchObj);
```

---

# 🧠 Key Concepts Behind Virtual DOM

## 1. Reconciliation

Comparing old vs new tree

---

## 2. Diffing

Finding minimal change set

---

## 3. Patch

Applying only required updates

---

## 4. Component re-rendering

Whole tree re-generated in memory

---

# ⚖️ Why Virtual DOM is Faster

| Approach           | Cost                 |
| ------------------ | -------------------- |
| Direct DOM updates | Expensive            |
| Virtual DOM diff   | Cheap JS computation |
| Minimal patching   | Fast DOM updates     |

---

# 🚨 Limitations (Important Interview Insight)

### ❌ Not always faster

- For small updates, direct DOM is faster
- Diffing has overhead

---

### ❌ O(n³) worst-case diffing (theoretical)

React optimizes this using heuristics.

---

### ❌ Key-based reconciliation needed

Without keys → inefficient re-renders

---

# 📌 Real React Optimization Tricks

React improves VDOM with:

- Fiber architecture
- keyed diffing
- batching updates
- prioritization (concurrent rendering)

---

# 🧠 Senior-Level Insight

Modern VDOM is not just diffing:

- It is a **scheduler + renderer + reconciler**
- Uses **incremental rendering**
- Splits work into chunks (Fiber nodes)
- Supports interruptible rendering

---

# 🧾 Interview Summary

A Virtual DOM is a JavaScript representation of the real DOM used to optimize updates by minimizing direct DOM manipulations. It works by creating a virtual tree, diffing old and new versions, and applying only necessary changes through a patching process. While conceptually simple, real-world implementations use advanced optimizations like keyed reconciliation, batching, and incremental rendering to achieve high performance.

## Question 10. How to throttle expensive computations during resize/scroll

# ✅ Direct Answer

To throttle expensive computations during `resize` or `scroll`, you limit how often your handler runs using **throttling techniques**—typically via `requestAnimationFrame`, timestamp-based throttling, or utility functions. The goal is to ensure the function runs at most once per frame (or per fixed interval), instead of on every event fire.

---

# 🧠 Interview-Level Explanation

Events like:

- `scroll`
- `resize`
- `mousemove`

can fire **dozens of times per second** (often 50–100+).

If you run expensive logic on every event:

❌ layout thrashing
❌ janky UI (dropped frames)
❌ CPU spikes

So we control execution frequency using **throttling**.

---

# 🚀 1. Best Practice: `requestAnimationFrame` Throttle (Preferred)

This ensures the callback runs **once per frame (~16ms at 60fps)**.

---

## 📌 Implementation

```js id="th1"
function throttleRAF(fn) {
  let scheduled = false;

  return function (...args) {
    if (!scheduled) {
      scheduled = true;

      requestAnimationFrame(() => {
        fn.apply(this, args);
        scheduled = false;
      });
    }
  };
}
```

---

## 📌 Usage (scroll)

```js id="th2"
function onScroll() {
  console.log("Expensive scroll calculation");
}

window.addEventListener("scroll", throttleRAF(onScroll));
```

---

## 🧠 Why this is best

- syncs with browser repaint cycle
- avoids unnecessary intermediate calls
- keeps UI smooth at 60fps

---

# ⚡ 2. Time-Based Throttle (Classic Approach)

Runs function at most once every `delay ms`.

---

## 📌 Implementation

```js id="th3"
function throttle(fn, delay) {
  let last = 0;

  return function (...args) {
    const now = Date.now();

    if (now - last >= delay) {
      last = now;
      fn.apply(this, args);
    }
  };
}
```

---

## 📌 Usage

```js id="th4"
window.addEventListener(
  "resize",
  throttle(() => {
    console.log("Resize logic");
  }, 200),
);
```

---

## 🧠 Behavior

| Time  | Event fired | Execution |
| ----- | ----------- | --------- |
| 0ms   | scroll      | ✅ run    |
| 10ms  | scroll      | ❌ skip   |
| 30ms  | scroll      | ❌ skip   |
| 200ms | scroll      | ✅ run    |

---

# 📌 3. Combined RAF + Timestamp (Advanced Pattern)

Balances precision + frame safety.

---

```js id="th5"
function throttleHybrid(fn, delay) {
  let last = 0;
  let scheduled = false;
  let lastArgs;

  return function (...args) {
    lastArgs = args;
    const now = Date.now();

    if (!scheduled) {
      scheduled = true;

      requestAnimationFrame(() => {
        if (now - last >= delay) {
          fn.apply(this, lastArgs);
          last = now;
        }

        scheduled = false;
      });
    }
  };
}
```

---

# 📌 4. Example: Expensive Scroll Calculation

Imagine calculating element visibility:

---

## ❌ Bad (no throttle)

```js id="th6"
window.addEventListener("scroll", () => {
  heavyLayoutCalculation(); // runs 100+ times/sec ❌
});
```

---

## ✅ Good (RAF throttle)

```js id="th7"
window.addEventListener("scroll", throttleRAF(heavyLayoutCalculation));
```

---

# 📌 5. Resize Optimization Pattern

Resize is especially expensive because it triggers:

- reflow
- repaint
- layout recalculation

---

## Best practice:

```js id="th8"
window.addEventListener(
  "resize",
  throttleRAF(() => {
    recalculateLayout();
  }),
);
```

---

# 📊 Throttling vs Debouncing (Interview Favorite)

| Feature      | Throttle     | Debounce     |
| ------------ | ------------ | ------------ |
| Execution    | Periodic     | After pause  |
| Scroll       | ✅ Best      | ❌ Not ideal |
| Resize       | ✅ Good      | ⚠️ sometimes |
| Input search | ❌ not ideal | ✅ best      |

---

# 🧠 Why `requestAnimationFrame` is superior

Compared to `setTimeout` throttle:

| Feature           | setTimeout | requestAnimationFrame |
| ----------------- | ---------- | --------------------- |
| Sync with repaint | ❌         | ✅                    |
| Jank prevention   | medium     | high                  |
| Efficiency        | lower      | higher                |

---

# ⚠️ Common Pitfalls

## ❌ 1. Heavy logic inside scroll handler

Even throttled code should avoid:

- layout reads + writes mixed
- forced reflows

---

## ❌ 2. Multiple listeners without sharing throttle

Bad:

```js id="th9"
scroll handler A (throttle)
scroll handler B (throttle)
```

Better:

- single shared throttled pipeline

---

## ❌ 3. Ignoring cleanup

In SPAs:

```js id="th10"
window.removeEventListener("scroll", handler);
```

---

## ❌ 4. Using debounce for scroll

This delays updates → feels laggy.

---

# 🧠 Senior-Level Insight

Modern browsers already try to optimize scroll:

- passive listeners
- compositing layers
- scroll event coalescing

So best strategy is:

> Keep scroll handlers lightweight + frame-synced

---

# 📌 Bonus: Passive Event Listener (Important Optimization)

```js id="th11"
window.addEventListener("scroll", handler, {
  passive: true,
});
```

### Why it helps:

- tells browser you won’t call `preventDefault()`
- enables smoother scrolling
- improves rendering pipeline

---

# 🧾 Interview Summary

To throttle expensive computations during scroll or resize, you use techniques like `requestAnimationFrame` throttling or time-based throttling. `requestAnimationFrame` is preferred because it aligns execution with the browser’s rendering cycle, ensuring smooth 60fps updates. This prevents excessive function calls, reduces layout thrashing, and improves UI performance during high-frequency events like scrolling and resizing.

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
