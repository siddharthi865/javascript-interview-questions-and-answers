# Set 21

| S.No. | Question                                                                                                                                                                                      |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between synchronous and asynchronous script loading in browsers?](#question-1-what-is-the-difference-between-synchronous-and-asynchronous-script-loading-in-browsers) |
| 2.    | [How to defer or async load external scripts?](#question-2-how-to-defer-or-async-load-external-scripts)                                                                                       |
| 3.    | [How to prevent layout thrashing in the browser?](#question-3-how-to-prevent-layout-thrashing-in-the-browser)                                                                                 |
| 4.    | [How to minimize repaint and reflow for DOM updates?](#question-4-how-to-minimize-repaint-and-reflow-for-dom-updates)                                                                         |
| 5.    | [How to implement virtual scrolling for large lists?](#question-5-how-to-implement-virtual-scrolling-for-large-lists)                                                                         |
| 6.    | [How to use IntersectionObserver API effectively?](#question-6-how-to-use-intersectionobserver-api-effectively)                                                                               |
| 7.    | [Difference between MutationObserver and IntersectionObserver](#question-7-difference-between-mutationobserver-and-intersectionobserver)                                                      |
| 8.    | [How to lazy-load images/videos using IntersectionObserver](#question-8-how-to-lazy-load-imagesvideos-using-intersectionobserver)                                                             |
| 9.    | [How to implement infinite scroll efficiently](#question-9-how-to-implement-infinite-scroll-efficiently)                                                                                      |
| 10.   | [Difference between `requestAnimationFrame` and `setInterval` for animations](#question-10-difference-between-requestanimationframe-and-setinterval-for-animations)                           |
| 11.   | [How to reduce JavaScript blocking time for page load](#question-11-how-to-reduce-javascript-blocking-time-for-page-load)                                                                     |
| 12.   | [How to implement code splitting in JavaScript](#question-12-how-to-implement-code-splitting-in-javascript)                                                                                   |
| 13.   | [How to dynamically import modules for performance](#question-13-how-to-dynamically-import-modules-for-performance)                                                                           |
| 14.   | [Difference between CSSOM and DOM, and how JS interacts with both](#question-14-difference-between-cssom-and-dom-and-how-js-interacts-with-both)                                              |
| 15.   | [How to batch DOM updates to improve performance](#question-15-how-to-batch-dom-updates-to-improve-performance)                                                                               |
| 16.   | [Difference between microtasks and macrotasks in the browser](#question-16-difference-between-microtasks-and-macrotasks-in-the-browser)                                                       |
| 17.   | [How to avoid long tasks blocking the main thread](#question-17-how-to-avoid-long-tasks-blocking-the-main-thread)                                                                             |
| 18.   | [How to measure performance using Performance API](#question-18-how-to-measure-performance-using-performance-api)                                                                             |
| 19.   | [How to implement smooth scrolling programmatically](#question-19-how-to-implement-smooth-scrolling-programmatically)                                                                         |
| 20.   | [How to optimize event listeners for dynamic elements](#question-20-how-to-optimize-event-listeners-for-dynamic-elements)                                                                     |

## Question 1. What is the difference between synchronous and asynchronous script loading in browsers?

The difference between **synchronous** and **asynchronous** script loading is how the browser handles downloading and executing JavaScript while parsing HTML.

- **Synchronous loading** blocks HTML parsing until the script is downloaded and executed.
- **Asynchronous loading** allows HTML parsing to continue while the script downloads, improving page performance and responsiveness.

This behavior mainly depends on how `<script>` tags are used (`normal`, `async`, or `defer`).

### 1. Synchronous Script Loading

By default, JavaScript scripts are loaded **synchronously**.

```html
<script src="app.js"></script>
```

#### How It Works

When the browser encounters this script tag:

1. HTML parsing pauses.
2. Browser downloads the script.
3. Script executes immediately.
4. HTML parsing resumes.

#### Execution Flow

```text
HTML Parsing ---> STOP
                 Download JS
                 Execute JS
HTML Parsing ---> Continue
```

#### Example

```html
<body>
  <h1>Hello</h1>

  <script src="app.js"></script>

  <p>World</p>
</body>
```

The browser will not continue rendering `<p>World</p>` until `app.js` finishes executing.

### Problems with Synchronous Loading

#### 1. Blocks Rendering

Large scripts delay page rendering.

#### 2. Poor Performance

Users may see blank screens longer.

#### 3. Network Delays Affect UI

Slow internet = delayed page interactivity.

### 2. Asynchronous Script Loading

Modern browsers support asynchronous loading using:

```html
<script async src="app.js"></script>
```

or

```html
<script defer src="app.js"></script>
```

Both avoid blocking HTML parsing during download.

### 3. `async` Attribute

```html
<script async src="analytics.js"></script>
```

#### Behavior

- Script downloads in parallel with HTML parsing.
- Executes immediately once downloaded.
- Parsing pauses briefly during execution.

#### Execution Timeline

```text
HTML Parsing ---------------------->
        Script Download (parallel)

If download completes:
    Pause Parsing
    Execute Script
    Resume Parsing
```

### Important Characteristics

#### Advantages

- Faster page load.
- Good for independent scripts.

#### Disadvantages

- Execution order is NOT guaranteed.
- Can execute before DOM is fully parsed.

#### Best Use Cases

- Analytics
- Ads
- Tracking scripts
- Independent third-party libraries

Example:

```html
<script async src="https://example.com/analytics.js"></script>
```

### 4. `defer` Attribute

```html
<script defer src="app.js"></script>
```

#### Behavior

- Downloads in parallel.
- Execution waits until HTML parsing completes.
- Scripts execute in document order.

#### Execution Timeline

```text
HTML Parsing ---------------------->
        Script Download (parallel)

HTML Finished
Execute Deferred Scripts
DOMContentLoaded Fires
```

### Advantages

#### 1. Non-blocking Download

Improves performance.

#### 2. Preserves Order

Multiple deferred scripts execute sequentially.

#### 3. DOM Is Ready

Safe for DOM manipulation.

### Best Use Cases

Main application scripts:

```html
<script defer src="vendor.js"></script>
<script defer src="main.js"></script>
```

`vendor.js` executes before `main.js`.

### 5. Key Differences (`async` vs `defer` vs synchronous)

| Feature                             | Normal Script    | async                 | defer          |
| ----------------------------------- | ---------------- | --------------------- | -------------- |
| Blocks HTML Parsing                 | Yes              | During execution only | No             |
| Downloads in Parallel               | No               | Yes                   | Yes            |
| Executes Immediately After Download | Yes              | Yes                   | No             |
| Waits for DOM Parsing               | No               | No                    | Yes            |
| Maintains Execution Order           | Yes              | No                    | Yes            |
| Best For                            | Critical scripts | Analytics/ads         | Main app logic |

### 6. Deep Understanding

#### Why JavaScript Blocks Parsing

JavaScript can modify the DOM:

```js
document.write("<h1>Injected</h1>");
```

Because scripts may alter HTML structure, the browser pauses parsing to maintain consistency.

This is why synchronous scripts block rendering.

### 7. Browser Internals Perspective

Browsers use:

- HTML parser
- Networking layer
- JavaScript engine
- Event loop

With synchronous scripts:

- Parser waits for JS execution.

With async/defer:

- Browser optimizes by downloading scripts concurrently.

This improves:

- First Contentful Paint (FCP)
- Largest Contentful Paint (LCP)
- Overall UX

### 8. Common Interview Question

#### Q: Which is better: `async` or `defer`?

#### Answer

It depends on the use case.

Use:

- `async` for independent scripts.
- `defer` for scripts relying on DOM or execution order.

In modern applications, `defer` is usually preferred for app bundles.

### 9. Common Pitfalls

#### Problem with `async`

```html
<script async src="jquery.js"></script>
<script async src="app.js"></script>
```

`app.js` may execute before `jquery.js`.

This can cause:

```text
$ is not defined
```

#### Safer with `defer`

```html
<script defer src="jquery.js"></script>
<script defer src="app.js"></script>
```

Execution order is guaranteed.

### 10. Inline Scripts Behavior

`defer` only works with external scripts.

```html
<script defer>
  console.log("ignored");
</script>
```

The `defer` attribute is ignored here.

### 11. Modern Best Practice

Most production apps today use:

```html
<script type="module" src="main.js"></script>
```

ES modules behave similarly to `defer` by default.

Advantages:

- Deferred execution
- Dependency support
- Scoped modules
- Better optimization

### 12. Real-World Recommendation

#### Use `defer` for

- Application bundles
- DOM-dependent code
- Framework apps (React/Vue/Angular)

#### Use `async` for

- Ads
- Analytics
- Heatmaps
- Third-party widgets

#### Avoid plain synchronous scripts unless necessary.

### Final Summary

Synchronous scripts block HTML parsing until downloaded and executed, which can hurt performance. Asynchronous loading (`async` and `defer`) allows scripts to download in parallel with HTML parsing. `async` executes immediately after download and does not guarantee order, while `defer` waits until parsing completes and preserves execution order. In modern web development, `defer` is generally preferred for application scripts because it improves performance without breaking DOM-dependent logic.

## Question 2. How to defer or async load external scripts?

## Short Answer

You can load external scripts asynchronously or defer their execution using the HTML `<script>` tag attributes:

- `async` → loads script in parallel and executes immediately when ready
- `defer` → loads script in parallel but executes after HTML parsing is complete

```html
<script async src="app.js"></script>
<script defer src="app.js"></script>
```

You can also dynamically load scripts using JavaScript for more control.

---

# 1. Using `async` Attribute

## Syntax

```html
<script async src="https://example.com/script.js"></script>
```

## Behavior

- Script downloads in parallel with HTML parsing
- Executes immediately after download
- Does NOT guarantee order

## When to use

Best for **independent scripts** like:

- Analytics
- Ads
- Tracking tools

## Example

```html
<head>
  <script async src="https://www.google-analytics.com/analytics.js"></script>
</head>
```

---

## Key Interview Insight

`async` is non-blocking but **unpredictable in execution order**, so it should never be used when script dependencies exist.

---

# 2. Using `defer` Attribute

## Syntax

```html
<script defer src="app.js"></script>
```

## Behavior

- Script downloads in parallel
- Execution is delayed until HTML parsing is complete
- Maintains script execution order

---

## Example with multiple scripts

```html
<script defer src="vendor.js"></script>
<script defer src="main.js"></script>
```

Execution order is guaranteed:

```text
vendor.js → main.js
```

---

## When to use

Best for **main application scripts**:

- DOM manipulation code
- SPA frameworks (React, Angular, Vue bundles)
- Dependent scripts

---

## Key Interview Insight

`defer` behaves like:

> “Download now, execute later after DOM is ready”

It effectively aligns with `DOMContentLoaded`.

---

# 3. Dynamic Script Loading (JavaScript Approach)

Sometimes you need to load scripts conditionally or at runtime.

## Basic dynamic loading

```javascript
function loadScript(src, callback) {
  const script = document.createElement("script");
  script.src = src;

  script.onload = () => callback?.();
  script.onerror = () => console.error("Failed to load script:", src);

  document.head.appendChild(script);
}

loadScript("https://example.com/app.js", () => {
  console.log("Script loaded!");
});
```

---

## Async dynamic loading (default behavior)

Dynamic scripts behave like `async` by default:

```javascript
const script = document.createElement("script");
script.src = "app.js";
document.head.appendChild(script);
```

👉 Executes as soon as it loads

---

## Forcing `defer`-like behavior

There is no direct `defer` property in dynamic scripts, but you can control execution manually:

```javascript
const script = document.createElement("script");
script.src = "app.js";

script.onload = () => {
  console.log("Executed after load");
};

document.head.appendChild(script);
```

To ensure sequencing:

```javascript
loadScript("vendor.js", () => {
  loadScript("app.js");
});
```

---

# 4. Advanced Pattern: Promise-based Loader

Modern approach using Promises:

```javascript
function loadScript(src) {
  return new Promise((resolve, reject) => {
    const script = document.createElement("script");
    script.src = src;

    script.onload = () => resolve(src);
    script.onerror = () => reject(new Error("Failed: " + src));

    document.head.appendChild(script);
  });
}

loadScript("vendor.js")
  .then(() => loadScript("app.js"))
  .then(() => console.log("All scripts loaded"));
```

---

# 5. Comparison Table

| Method                           | Blocking | Execution Timing      | Order Guarantee | Use Case                           |
| -------------------------------- | -------- | --------------------- | --------------- | ---------------------------------- |
| Normal `<script>`                | Yes      | Immediate             | Yes             | Legacy / critical blocking scripts |
| `async`                          | No       | As soon as downloaded | No              | Independent scripts                |
| `defer`                          | No       | After HTML parsing    | Yes             | App scripts                        |
| Dynamic script (`createElement`) | No       | On load event         | Manual control  | Conditional loading                |

---

# 6. Common Pitfalls (Important for Interviews)

## ❌ Using `async` for dependent scripts

```html
<script async src="jquery.js"></script>
<script async src="plugin.js"></script>
```

Risk:

```text
plugin.js runs before jquery.js → errors
```

---

## ❌ Assuming `defer` works on inline scripts

```html
<script defer>
  console.log("Won't defer");
</script>
```

`defer` only works on external scripts.

---

## ❌ Dynamic scripts without error handling

Always include:

```javascript
script.onerror = () => handleFailure();
```

---

# 7. Modern Best Practice (Real-world)

### Recommended pattern:

- Use `defer` for application code
- Use `async` for third-party scripts
- Use dynamic loading for conditional or lazy-loaded features

Example:

```html
<script defer src="/bundle.js"></script>
<script async src="https://ads.network.com/ad.js"></script>
```

---

# 8. Interview Summary (Strong Answer)

To defer or asynchronously load external scripts in the browser, we use the `defer` and `async` attributes on the `<script>` tag. `async` loads the script in parallel and executes it immediately after download, without guaranteeing order, making it suitable for independent scripts. `defer` also loads scripts in parallel but delays execution until the HTML document has been fully parsed, preserving execution order and making it ideal for application logic. Alternatively, scripts can be dynamically loaded using JavaScript by creating a `<script>` element and appending it to the DOM, which provides fine-grained control over execution timing, dependencies, and error handling.

## Question 3. How to prevent layout thrashing in the browser?

## Short Answer

You prevent **layout thrashing** by avoiding repeated interleaving of DOM reads and writes that force the browser to repeatedly recalculate layout (reflow). Instead, you should **batch reads and writes separately**, minimize forced synchronous layouts, and use techniques like `requestAnimationFrame`, caching layout values, and using CSS transforms instead of layout-triggering properties.

---

# 1. What is Layout Thrashing?

**Layout thrashing** happens when your code repeatedly:

1. Reads layout values (like `offsetHeight`, `getBoundingClientRect`)
2. Writes to the DOM (style changes)
3. Reads again immediately after writing

This forces the browser to recalculate layout multiple times in a single frame, severely hurting performance.

---

## Example of Layout Thrashing ❌

```javascript
const boxes = document.querySelectorAll(".box");

boxes.forEach((box) => {
  const height = box.offsetHeight; // READ (forces layout)
  box.style.height = height + 10 + "px"; // WRITE
});
```

If repeated in loops, this causes multiple reflows.

---

## Why it's bad

Each forced layout triggers:

- Recalculate styles
- Layout (reflow)
- Paint
- Composite

This is expensive and blocks the main thread.

---

# 2. Core Principle: Read → Write Separation

## Fix by batching reads first, then writes

```javascript
const boxes = document.querySelectorAll(".box");

// STEP 1: READ
const heights = Array.from(boxes).map((box) => box.offsetHeight);

// STEP 2: WRITE
boxes.forEach((box, i) => {
  box.style.height = heights[i] + 10 + "px";
});
```

### Why this works

- Only one layout calculation pass
- No forced reflow in between operations

---

# 3. Use `requestAnimationFrame` for DOM writes

```javascript
function updateBoxes(boxes) {
  const heights = Array.from(boxes).map((b) => b.offsetHeight);

  requestAnimationFrame(() => {
    boxes.forEach((box, i) => {
      box.style.height = heights[i] + "px";
    });
  });
}
```

### Benefit

Ensures DOM writes happen in sync with browser paint cycle.

---

# 4. Avoid Forced Synchronous Layout

## Bad pattern ❌

```javascript
element.style.width = "500px";
const height = element.offsetHeight; // forces reflow immediately
```

## Better ✔

```javascript
const height = element.offsetHeight;
element.style.width = "500px";
```

---

# 5. Cache Layout Reads

If you need the same value multiple times, don’t recompute:

```javascript
const rect = element.getBoundingClientRect();

console.log(rect.width);
console.log(rect.height);
console.log(rect.top);
```

Instead of calling `getBoundingClientRect()` multiple times.

---

# 6. Use CSS transforms instead of layout properties

## Layout-triggering properties ❌

- `top`, `left`
- `width`, `height`
- `margin`, `padding`

## Non-layout (compositor-only) ✔

- `transform`
- `opacity`

Example:

```javascript
box.style.transform = `translateX(100px)`;
```

This avoids reflow and uses GPU compositing.

---

# 7. Use Document Fragments for bulk DOM updates

```javascript
const fragment = document.createDocumentFragment();

for (let i = 0; i < 1000; i++) {
  const div = document.createElement("div");
  div.textContent = i;
  fragment.appendChild(div);
}

document.body.appendChild(fragment);
```

### Benefit

- Single DOM insertion
- Single reflow instead of 1000

---

# 8. Debounce / Throttle UI updates

Useful for scroll, resize, input events.

## Debounce example

```javascript
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

window.addEventListener(
  "resize",
  debounce(() => {
    console.log("Resize handled once");
  }, 200),
);
```

---

# 9. Use `ResizeObserver` instead of polling layout

```javascript
const observer = new ResizeObserver((entries) => {
  for (let entry of entries) {
    console.log(entry.contentRect.width);
  }
});

observer.observe(document.querySelector(".box"));
```

### Benefit

Efficient, avoids repeated forced layout checks.

---

# 10. Avoid reading layout inside loops

## Bad ❌

```javascript
for (let i = 0; i < boxes.length; i++) {
  const h = boxes[i].offsetHeight;
  boxes[i].style.height = h + 10 + "px";
}
```

## Good ✔

```javascript
const heights = boxes.map((b) => b.offsetHeight);

boxes.forEach((box, i) => {
  box.style.height = heights[i] + 10 + "px";
});
```

---

# 11. Framework Perspective (React/Vue)

Modern frameworks reduce layout thrashing by:

- Virtual DOM batching
- Async state updates
- Commit phase separation

But you can still cause thrashing if you manually read layout during render cycles.

---

# 12. Advanced Optimization: `will-change`

```css
.box {
  will-change: transform;
}
```

### Use carefully

- Hints browser to optimize element
- Overuse increases memory usage

---

# 13. Key Interview Mental Model

Think of browser rendering pipeline:

```text
JS → Style → Layout → Paint → Composite
```

Layout thrashing occurs when JS repeatedly forces:

👉 Layout → JS → Layout → JS → Layout …

Your goal:

👉 JS (read all) → JS (write all) → Layout once

---

# 14. Common Pitfalls

## Mixing reads and writes

```javascript
el.style.width = "200px";
console.log(el.offsetWidth); // forced layout
```

---

## Querying layout in scroll handlers without batching

```javascript
window.addEventListener("scroll", () => {
  el.style.top = window.scrollY + "px";
  console.log(el.offsetHeight); // expensive
});
```

---

# 15. Best Practices Summary

To prevent layout thrashing:

- Separate DOM reads and writes
- Batch updates
- Avoid forced synchronous layout queries
- Use `requestAnimationFrame` for visual updates
- Prefer `transform` over layout properties
- Cache layout values
- Use `DocumentFragment` for bulk DOM insertion
- Debounce scroll/resize handlers

---

# Final Interview Summary

Layout thrashing occurs when repeated DOM reads and writes force the browser to recalculate layout multiple times per frame, leading to performance degradation. It can be prevented by batching DOM reads and writes separately, avoiding interleaved access patterns, and minimizing layout-triggering operations such as reading `offsetHeight` or `getBoundingClientRect` inside loops. Instead, developers should cache layout values, use `requestAnimationFrame` for synchronized updates, prefer GPU-accelerated CSS properties like `transform`, and use techniques like debouncing, document fragments, and `ResizeObserver` for efficient UI updates.

## Question 4. How to minimize repaint and reflow for DOM updates?

## Short Answer

To minimize **repaint and reflow**, you should reduce layout-triggering DOM operations, batch DOM reads and writes, avoid frequent style changes in loops, and prefer CSS transforms/opacity over properties that affect layout. The key idea is: **trigger layout once per frame (or less), not repeatedly.**

---

# 1. Understand Reflow vs Repaint (Interview Foundation)

## 🔄 Reflow (Layout)

Happens when the browser recalculates element positions and geometry.

Triggered by changes like:

- `width`, `height`
- `margin`, `padding`
- `display`, `position`
- DOM structure changes

👉 Expensive operation

---

## 🎨 Repaint

Happens when visual styling changes but layout does NOT change.

Triggered by:

- `color`
- `background`
- `box-shadow`
- `visibility`

👉 Less expensive than reflow, but still costly at scale

---

# 2. Core Rule to Minimize Both

> Avoid forcing the browser to repeatedly recalculate layout or repaint in tight loops.

The golden strategy:

### ✔ Batch DOM reads separately from writes

---

# 3. Avoid Interleaving Reads and Writes ❌

## Bad Example (causes repeated reflow)

```javascript
const boxes = document.querySelectorAll(".box");

boxes.forEach((box) => {
  const height = box.offsetHeight; // READ → forces layout
  box.style.height = height + 20 + "px"; // WRITE → invalidates layout
});
```

### Problem

Each iteration may trigger layout recalculation.

---

# 4. Fix: Batch Reads → Batch Writes ✔

```javascript
const boxes = document.querySelectorAll(".box");

// STEP 1: READ
const heights = Array.from(boxes).map((box) => box.offsetHeight);

// STEP 2: WRITE
boxes.forEach((box, i) => {
  box.style.height = heights[i] + 20 + "px";
});
```

### Why this works

- Only **one layout calculation pass**
- Writes don’t interfere with reads

---

# 5. Use `requestAnimationFrame` for UI updates

```javascript
function updateUI(boxes) {
  const heights = Array.from(boxes).map((b) => b.offsetHeight);

  requestAnimationFrame(() => {
    boxes.forEach((box, i) => {
      box.style.height = heights[i] + "px";
    });
  });
}
```

### Benefit

Aligns updates with browser paint cycle → smoother rendering

---

# 6. Prefer Transform & Opacity (No Layout / Repaint Heavy Work)

## Bad ❌ (causes reflow)

```javascript
box.style.left = "100px";
box.style.top = "50px";
```

## Good ✔ (compositor-only)

```javascript
box.style.transform = "translate(100px, 50px)";
```

### Why it matters

- `transform` avoids layout & repaint
- Uses GPU compositing

---

# 7. Avoid Forced Synchronous Layout

## Bad ❌

```javascript
element.style.width = "500px";
const height = element.offsetHeight; // forces reflow immediately
```

## Good ✔

```javascript
const height = element.offsetHeight;
element.style.width = "500px";
```

---

# 8. Reduce DOM Manipulations in Loops

## Bad ❌

```javascript
for (let i = 0; i < 1000; i++) {
  const div = document.createElement("div");
  document.body.appendChild(div); // triggers multiple reflows
}
```

## Good ✔ (use DocumentFragment)

```javascript
const fragment = document.createDocumentFragment();

for (let i = 0; i < 1000; i++) {
  const div = document.createElement("div");
  fragment.appendChild(div);
}

document.body.appendChild(fragment); // single reflow
```

---

# 9. Debounce or Throttle High-Frequency Events

## Example: scroll optimization

```javascript
function throttle(fn, limit) {
  let lastCall = 0;

  return (...args) => {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      fn(...args);
    }
  };
}

window.addEventListener(
  "scroll",
  throttle(() => {
    console.log("Scroll update");
  }, 100),
);
```

### Why it helps

Prevents repeated layout recalculations on scroll/resize/input events.

---

# 10. Cache Layout Reads

## Bad ❌

```javascript
console.log(el.offsetHeight);
console.log(el.offsetHeight);
```

## Good ✔

```javascript
const height = el.offsetHeight;
console.log(height);
console.log(height);
```

---

# 11. Use CSS Instead of JavaScript When Possible

## Prefer CSS animations

```css
.box {
  transition: transform 0.3s ease;
}

.box.move {
  transform: translateX(100px);
}
```

### Why CSS is better:

- Handled by compositor thread
- No JS-induced layout recalculation
- GPU accelerated

---

# 12. Avoid Layout Queries Inside Loops

## Bad ❌

```javascript
for (let i = 0; i < boxes.length; i++) {
  boxes[i].style.height = boxes[i].offsetHeight + "px";
}
```

## Good ✔

```javascript
const heights = boxes.map((b) => b.offsetHeight);

boxes.forEach((box, i) => {
  box.style.height = heights[i] + "px";
});
```

---

# 13. Use `ResizeObserver` Instead of Polling

```javascript
const observer = new ResizeObserver((entries) => {
  for (let entry of entries) {
    console.log(entry.contentRect.width);
  }
});

observer.observe(document.querySelector(".box"));
```

### Benefit

Efficient, avoids manual layout checks

---

# 14. Browser Rendering Pipeline Insight (Interview Gold)

```
JS → Style → Layout → Paint → Composite
```

### Your goal:

- Avoid repeatedly forcing **Layout**
- Avoid unnecessary **Paint**
- Keep updates in **Composite-only path (transform/opacity)** when possible

---

# 15. Common Pitfalls

## ❌ Mixing reads and writes

```javascript
el.style.width = "300px";
console.log(el.offsetWidth); // forces reflow
```

---

## ❌ Repeated style changes in loops

```javascript
elements.forEach((el) => {
  el.style.width = "200px";
  el.style.height = "200px";
});
```

Better: batch via CSS class

```javascript
container.classList.add("updated");
```

---

# 16. Best Practices Summary

To minimize repaint and reflow:

### DOM strategy

- Batch reads and writes separately
- Avoid layout access in loops
- Cache layout values

### Rendering strategy

- Use `requestAnimationFrame`
- Use `DocumentFragment` for bulk updates

### CSS strategy

- Prefer `transform` & `opacity`
- Avoid layout-affecting properties in animations

### Event strategy

- Debounce/throttle scroll, resize, input

### Architecture strategy

- Use class-based updates instead of inline style churn
- Delegate rendering to CSS where possible

---

# Final Interview Summary

To minimize repaint and reflow in the browser, developers must reduce layout thrashing by avoiding interleaved DOM reads and writes, batching updates, and minimizing forced synchronous layout calculations. Performance can be further improved by using `requestAnimationFrame` for coordinated rendering, caching layout values, and favoring compositor-friendly CSS properties like `transform` and `opacity` instead of layout-triggering properties. Additionally, techniques like `DocumentFragment`, debouncing high-frequency events, and leveraging `ResizeObserver` help reduce unnecessary rendering work, resulting in smoother and more efficient UI performance.

## Question 5. How to implement virtual scrolling for large lists?

## Short Answer

**Virtual scrolling (windowing)** is implemented by rendering only the visible items in a large list plus a small buffer, instead of rendering the entire dataset. As the user scrolls, you dynamically update which items are mounted based on scroll position, while preserving the illusion of a full list using a spacer (total height container).

---

# 1. Why Virtual Scrolling is Needed

Rendering thousands of DOM nodes causes:

- High memory usage
- Slow initial render
- Layout thrashing during updates
- Poor scroll performance

Example problem:

```javascript
// BAD: rendering 10,000+ DOM nodes
items.map((item) => <Row item={item} />);
```

---

# 2. Core Idea of Virtual Scrolling

Instead of rendering everything:

👉 Render only visible items + buffer

You simulate full height using:

- **Top spacer (offset padding)**
- **Visible items**
- **Bottom spacer**

---

## Visual Model

```
[ Spacer Top ]
[ Visible Item 10 ]
[ Visible Item 11 ]
[ Visible Item 12 ]
[ Spacer Bottom ]
```

---

# 3. Basic Implementation (Fixed Height Items)

This is the simplest and most common approach.

---

## Step 1: Setup

Assume:

```javascript
const itemHeight = 50;
const containerHeight = 300;
```

---

## Step 2: Compute visible range

```javascript
function getVisibleRange(scrollTop) {
  const startIndex = Math.floor(scrollTop / itemHeight);
  const visibleCount = Math.ceil(containerHeight / itemHeight);

  return {
    startIndex,
    endIndex: startIndex + visibleCount + 2, // buffer
  };
}
```

---

## Step 3: Render only visible items

```javascript
function render(items, scrollTop) {
  const { startIndex, endIndex } = getVisibleRange(scrollTop);

  const visibleItems = items.slice(startIndex, endIndex);

  const offsetY = startIndex * itemHeight;
}
```

---

# 4. Full Vanilla JS Implementation

```html
<div id="container" style="height:300px; overflow:auto;">
  <div id="spacerTop"></div>
  <div id="list"></div>
  <div id="spacerBottom"></div>
</div>
```

---

```javascript
const container = document.getElementById("container");
const list = document.getElementById("list");
const spacerTop = document.getElementById("spacerTop");
const spacerBottom = document.getElementById("spacerBottom");

const itemHeight = 50;
const visibleCount = 6;

const items = Array.from({ length: 10000 }, (_, i) => `Item ${i}`);

container.addEventListener("scroll", () => {
  const scrollTop = container.scrollTop;

  const startIndex = Math.floor(scrollTop / itemHeight);
  const endIndex = startIndex + visibleCount + 2;

  const visibleItems = items.slice(startIndex, endIndex);

  // Update spacers
  spacerTop.style.height = startIndex * itemHeight + "px";
  spacerBottom.style.height = (items.length - endIndex) * itemHeight + "px";

  // Render visible DOM
  list.innerHTML = visibleItems
    .map((item) => `<div style="height:50px">${item}</div>`)
    .join("");
});
```

---

# 5. Key Concept: Spacer Trick

Instead of rendering all items:

- Top spacer pushes content down
- Bottom spacer fills remaining space

This preserves correct scroll height:

```text
totalHeight = items.length * itemHeight
```

---

# 6. Handling Variable Height Items (Advanced)

Fixed height is easy. Variable height requires measurement caching.

---

## Strategy

- Measure item heights dynamically
- Maintain prefix sum (cumulative heights)
- Use binary search to find visible range

---

## Data structure

```javascript
const heights = [40, 60, 55, 70, ...];
const prefixSum = [];
```

---

## Compute offsets

```javascript
prefixSum[i] = heights[0] + heights[1] + ... + heights[i]
```

---

## Find start index (binary search)

```javascript
function findStartIndex(scrollTop, prefixSum) {
  let left = 0,
    right = prefixSum.length - 1;

  while (left < right) {
    const mid = Math.floor((left + right) / 2);

    if (prefixSum[mid] < scrollTop) {
      left = mid + 1;
    } else {
      right = mid;
    }
  }

  return left;
}
```

---

# 7. Performance Optimizations (Important for Interviews)

## 1. Use `requestAnimationFrame`

```javascript
let ticking = false;

container.addEventListener("scroll", () => {
  if (!ticking) {
    requestAnimationFrame(() => {
      render();
      ticking = false;
    });
    ticking = true;
  }
});
```

---

## 2. Reuse DOM nodes (avoid re-rendering)

Instead of `innerHTML`:

- Use DOM recycling
- Update text only

---

## 3. Keep buffer zone

Pre-render extra items:

```javascript
buffer = 5;
```

Prevents flickering during fast scroll.

---

## 4. Avoid layout thrashing

Do NOT do:

```javascript
element.offsetHeight inside scroll loop ❌
```

Cache values instead.

---

# 8. React-style Virtualization (Conceptual)

Libraries like:

- React Window
- React Virtualized

Use:

```javascript
visibleItems = items.slice(startIndex, endIndex);
```

Rendered inside a scroll container.

---

# 9. Advanced Optimization: Absolute Positioning

Instead of spacers, each item is positioned:

```javascript
item.style.position = "absolute";
item.style.top = index * itemHeight + "px";
```

Container:

```css
position: relative;
height: totalHeight;
```

---

# 10. Trade-offs

## Advantages

- Handles 100k+ items smoothly
- Low DOM memory usage
- Fast initial render

## Disadvantages

- More complex logic
- Scroll syncing edge cases
- Harder with variable heights

---

# 11. Common Pitfalls (Interview Traps)

## ❌ Rendering full list then hiding items

```javascript
display: none on most items
```

Still creates DOM → no performance gain

---

## ❌ Recomputing layout on every scroll

```javascript
getBoundingClientRect() in scroll handler ❌
```

---

## ❌ No buffer zone

Causes flicker during fast scroll

---

# 12. Real-world Best Practice

In production:

- Use virtual scrolling library OR
- Build abstraction like:

```javascript
<VirtualList items={data} itemHeight={50} renderItem={fn} />
```

---

# 13. Mental Model (Interview Gold)

Think:

> “I render a moving window over a static dataset, while faking total scroll height using spacers or absolute positioning.”

---

# Final Interview Summary

Virtual scrolling is a performance optimization technique used to efficiently render large lists by only rendering the visible portion of items in the viewport. It works by calculating the visible index range based on scroll position and item height, rendering only those items, and maintaining correct scroll height using spacer elements or absolute positioning. This reduces DOM size significantly, preventing layout thrashing and improving rendering performance. Advanced implementations handle variable heights using prefix sums and binary search, while optimizations like `requestAnimationFrame`, DOM reuse, and buffering further improve smoothness during fast scrolling.

## Question 6. How to use IntersectionObserver API effectively?

## Short Answer

The **IntersectionObserver API** is used to efficiently detect when an element enters or leaves the viewport (or a parent container) without relying on scroll event listeners. You use it by creating an observer, passing a callback, and observing target elements. It’s commonly used for **lazy loading images, infinite scrolling, and visibility-based animations**.

---

# 1. What Problem Does IntersectionObserver Solve?

Before it existed, developers used:

- `scroll` event listeners ❌
- `getBoundingClientRect()` polling ❌

These approaches cause:

- Layout thrashing
- High CPU usage
- Poor scroll performance

---

## IntersectionObserver Advantage

- Async, non-blocking
- Browser optimized
- No manual scroll handling
- Efficient batching internally

---

# 2. Basic Usage

## Step 1: Create observer

```javascript id="m8k2op"
const observer = new IntersectionObserver((entries, observer) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      console.log("Visible:", entry.target);
    }
  });
});
```

---

## Step 2: Observe elements

```javascript id="v4h9qd"
const items = document.querySelectorAll(".item");

items.forEach((item) => observer.observe(item));
```

---

# 3. Core Concept

Each `entry` contains:

```javascript id="k8s9lp"
entry.isIntersecting; // true if visible
entry.intersectionRatio; // percentage visible
entry.target; // DOM element
```

---

# 4. Practical Use Case: Lazy Loading Images

## HTML

```html id="t9q7kd"
<img data-src="image1.jpg" class="lazy" />
<img data-src="image2.jpg" class="lazy" />
```

---

## JS Implementation

```javascript id="g7r3nf"
const images = document.querySelectorAll(".lazy");

const imageObserver = new IntersectionObserver((entries, observer) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;

      observer.unobserve(img); // stop observing once loaded
    }
  });
});

images.forEach((img) => imageObserver.observe(img));
```

---

## Why this is powerful

- Images load only when needed
- Saves bandwidth
- Speeds up initial page load

---

# 5. Infinite Scrolling Pattern

## Sentinel element approach

```html id="n2kq3x"
<div id="list"></div>
<div id="sentinel"></div>
```

---

## Observer logic

```javascript id="u8kq1m"
const sentinel = document.getElementById("sentinel");

const observer = new IntersectionObserver((entries) => {
  if (entries[0].isIntersecting) {
    loadMoreItems();
  }
});

observer.observe(sentinel);
```

---

## Why sentinel works

Instead of tracking scroll position manually:

👉 You observe a "trigger element"

---

# 6. Configuring Observer Options

```javascript id="c9p4lm"
const observer = new IntersectionObserver(callback, {
  root: null, // viewport
  rootMargin: "100px",
  threshold: 0.5,
});
```

---

## Option Breakdown

### 1. `root`

- `null` → viewport
- or a scroll container

```javascript id="r8p3jq"
root: document.querySelector(".scroll-container");
```

---

### 2. `rootMargin`

Expands or shrinks the viewport boundary

```javascript id="f3m9sn"
rootMargin: "200px";
```

👉 Useful for **preloading before element appears**

---

### 3. `threshold`

Controls visibility percentage

```javascript id="x7k2lm"
threshold: 0.25;
```

Meaning callback triggers when 25% visible.

---

# 7. Advanced Optimization Patterns

---

## 1. Unobserve after trigger

```javascript id="p4n8sd"
observer.unobserve(element);
```

✔ Prevents unnecessary future callbacks

---

## 2. Disconnect when done

```javascript id="k3m8qp"
observer.disconnect();
```

✔ Frees memory and stops all observations

---

## 3. Preload using rootMargin

```javascript id="z9q1nf"
rootMargin: "300px";
```

👉 Loads content before user reaches it

---

## 4. Use with virtual scrolling sentinel

```javascript id="v7k3lm"
if (entry.isIntersecting) {
  renderNextPage();
}
```

---

# 8. Performance Benefits

IntersectionObserver:

- Runs off main scroll thread
- Batches observations internally
- Avoids continuous polling
- Prevents layout thrashing caused by scroll handlers

---

# 9. Common Mistakes (Interview Traps)

---

## ❌ Using scroll event instead

```javascript id="scroll_bad"
window.addEventListener("scroll", checkVisibility);
```

👉 Causes performance issues

---

## ❌ Not unobserving elements

```javascript id="no_unobserve"
observer.observe(img); // never cleaned up
```

👉 Memory leaks in large lists

---

## ❌ Using too small rootMargin incorrectly

```javascript id="bad_margin"
rootMargin: "0px";
```

👉 Causes late loading (bad UX)

---

## ❌ Assuming immediate callback

Observer is async → not instant

---

# 10. IntersectionObserver vs Scroll Events

| Feature               | IntersectionObserver | Scroll Event        |
| --------------------- | -------------------- | ------------------- |
| Performance           | High                 | Low                 |
| Layout thrashing risk | None                 | High                |
| Ease of use           | Simple               | Complex             |
| Battery usage         | Efficient            | Inefficient         |
| Use case              | Visibility tracking  | Custom scroll logic |

---

# 11. Real-World Use Cases

### 1. Lazy loading images

### 2. Infinite scroll

### 3. Ad impression tracking

### 4. Animations on scroll

### 5. Video autoplay on visibility

### 6. Analytics tracking (viewability)

---

# 12. Example: Scroll Animation Trigger

```javascript id="anim1"
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      entry.target.classList.add("animate");
    }
  });
});

document.querySelectorAll(".box").forEach((el) => {
  observer.observe(el);
});
```

---

```css id="css1"
.box {
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.5s ease;
}

.box.animate {
  opacity: 1;
  transform: translateY(0);
}
```

---

# 13. Mental Model (Interview Gold)

Think of IntersectionObserver as:

> “A browser-optimized subscription system that notifies you when an element enters or leaves a visibility threshold in a scroll container.”

---

# 14. Best Practices Summary

To use IntersectionObserver effectively:

- Use it instead of scroll listeners
- Unobserve elements after use
- Use `rootMargin` for preloading UX
- Use `threshold` for fine-grained visibility
- Combine with lazy loading and virtual scrolling
- Disconnect observer when no longer needed
- Use sentinel pattern for infinite scroll

---

# Final Interview Summary

IntersectionObserver is a browser API that efficiently detects visibility changes of DOM elements relative to a viewport or container. It replaces expensive scroll-based calculations with a performant, asynchronous observation mechanism. It is commonly used for lazy loading, infinite scrolling, and visibility-based animations. By configuring options like `root`, `rootMargin`, and `threshold`, and by properly managing observation lifecycle with `observe`, `unobserve`, and `disconnect`, developers can build highly efficient and scalable UI interactions without causing layout thrashing or performance bottlenecks.

## Question 7. Difference between MutationObserver and IntersectionObserver

## Short Answer

**MutationObserver** watches for changes in the DOM structure (nodes added, removed, or attributes changed), while **IntersectionObserver** watches for visibility changes of elements relative to a viewport or container.

In simple terms:

- **MutationObserver → “What changed in the DOM?”**
- **IntersectionObserver → “Is the element visible on screen?”**

---

# 1. Core Concept Difference

| Feature           | MutationObserver                 | IntersectionObserver                 |
| ----------------- | -------------------------------- | ------------------------------------ |
| What it observes  | DOM mutations                    | Visibility changes                   |
| Trigger condition | Node/attribute changes           | Element enters/leaves viewport       |
| Use case          | DOM tracking                     | Scroll-based visibility              |
| Performance       | Can be expensive if overused     | Highly optimized                     |
| Replaces          | DOM mutation events (deprecated) | Scroll + getBoundingClientRect hacks |

---

# 2. MutationObserver (DOM Change Detection)

## What it does

Tracks changes like:

- Nodes added/removed
- Attribute changes
- Text content changes

---

## Example usage

```javascript id="m1a2b3"
const observer = new MutationObserver((mutationsList) => {
  for (const mutation of mutationsList) {
    console.log("Mutation type:", mutation.type);
  }
});

observer.observe(document.body, {
  childList: true,
  attributes: true,
  subtree: true,
});
```

---

## What triggers it?

### 1. Node added/removed

```javascript id="n4o5p6"
document.body.appendChild(document.createElement("div"));
```

### 2. Attribute changes

```javascript id="q7r8s9"
element.setAttribute("class", "active");
```

---

## Common use cases

- Detecting dynamic DOM updates
- Framework internals (React-like rendering detection)
- Watching third-party DOM changes
- Content scripts in extensions

---

## Pitfalls

### ❌ Can be expensive

If you observe `subtree: true`, every small DOM change triggers callback.

### ❌ Easy to create infinite loops

```javascript id="loop1"
observer = new MutationObserver(() => {
  document.body.appendChild(document.createElement("div"));
});
```

👉 This triggers itself repeatedly

---

# 3. IntersectionObserver (Visibility Detection)

## What it does

Tracks whether an element is:

- Visible in viewport
- Intersecting a parent container
- How much of it is visible

---

## Example usage

```javascript id="io1"
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      console.log("Visible:", entry.target);
    }
  });
});

observer.observe(document.querySelector(".box"));
```

---

## What triggers it?

- Element enters viewport
- Element exits viewport
- Visibility threshold changes

---

## Common use cases

- Lazy loading images
- Infinite scrolling
- Scroll animations
- Ad impression tracking

---

## Example: Lazy loading

```javascript id="io2"
const imgObserver = new IntersectionObserver((entries, obs) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      entry.target.src = entry.target.dataset.src;
      obs.unobserve(entry.target);
    }
  });
});

document.querySelectorAll("img").forEach((img) => {
  imgObserver.observe(img);
});
```

---

# 4. Key Mental Model Difference (Interview Insight)

## MutationObserver

👉 Watches **DOM structure changes**

Think:

> “Did something change in the HTML tree?”

---

## IntersectionObserver

👉 Watches **visual presence**

Think:

> “Is this element visible to the user right now?”

---

# 5. Performance Characteristics

## MutationObserver

- Can become expensive if:
  - Observing large subtree
  - High-frequency DOM updates

- Runs synchronously after mutations batch

---

## IntersectionObserver

- Highly optimized by browser
- Uses internal rendering pipeline
- Does NOT require layout polling

👉 Much better for scroll-related logic

---

# 6. Comparison in Real Scenarios

---

## Scenario 1: Lazy loading images

- MutationObserver ❌ wrong tool
- IntersectionObserver ✔ correct tool

---

## Scenario 2: Detect new DOM elements added dynamically

- MutationObserver ✔ correct tool

Example:

```javascript id="mo1"
observer.observe(container, {
  childList: true,
  subtree: true,
});
```

---

## Scenario 3: Trigger animation on scroll visibility

- MutationObserver ❌
- IntersectionObserver ✔

---

## Scenario 4: Detect framework rendering updates

- MutationObserver ✔ (sometimes used internally)

---

# 7. Lifecycle Differences

## MutationObserver

- Observes DOM mutations continuously
- Must be disconnected manually

```javascript id="mo2"
observer.disconnect();
```

---

## IntersectionObserver

- Observes visibility state changes
- Can unobserve per element

```javascript id="io3"
observer.unobserve(element);
```

---

# 8. Common Interview Pitfalls

## ❌ Using MutationObserver for scroll detection

Wrong tool:

```javascript id="bad1"
MutationObserver(() => {
  console.log("scrolled");
});
```

👉 Doesn’t track scroll at all

---

## ❌ Using IntersectionObserver to detect DOM edits

Wrong tool:

```javascript id="bad2"
IntersectionObserver(() => {
  console.log("DOM changed");
});
```

👉 Only tracks visibility, not structure

---

# 9. Advanced Insight (Browser Internals)

## MutationObserver

- Hooks into DOM mutation queue
- Processes changes after microtask checkpoint

## IntersectionObserver

- Integrated with rendering pipeline
- Uses compositor-level optimizations
- Avoids layout thrashing completely

---

# 10. When to Use What (Interview Rule of Thumb)

### Use MutationObserver when:

- You need to detect DOM structure changes
- You are building dev tools or frameworks
- You need to track dynamic DOM injection

---

### Use IntersectionObserver when:

- You care about visibility
- You are handling scroll-based UI behavior
- You want performance-friendly scroll logic

---

# Final Interview Summary

MutationObserver and IntersectionObserver solve completely different problems in the browser. MutationObserver detects changes in the DOM structure such as added or removed nodes and attribute modifications, making it useful for tracking dynamic DOM updates. In contrast, IntersectionObserver detects visibility changes of elements relative to the viewport or a container, making it ideal for scroll-based features like lazy loading, infinite scrolling, and animations. MutationObserver operates on DOM mutation events, while IntersectionObserver is optimized within the browser’s rendering pipeline and avoids expensive scroll or layout calculations.

## Question 8. How to lazy-load images/videos using IntersectionObserver

## Short Answer

You lazy-load images or videos using **IntersectionObserver** by observing media elements and only setting their `src` (or triggering playback) when they enter the viewport. This avoids loading heavy resources until they’re actually needed, improving performance and initial page load time.

---

# 1. Core Idea

Instead of loading everything upfront:

❌ Load all images/videos immediately
✔ Load only when visible on screen

IntersectionObserver tells you:

> “This element is now entering the viewport — load it.”

---

# 2. Lazy Loading Images (Basic Pattern)

## HTML (use `data-src` instead of `src`)

```html id="img1"
<img class="lazy" data-src="image1.jpg" alt="image" />
<img class="lazy" data-src="image2.jpg" alt="image" />
```

---

## JavaScript (IntersectionObserver)

```javascript id="io-img1"
const images = document.querySelectorAll("img.lazy");

const imageObserver = new IntersectionObserver(
  (entries, observer) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        const img = entry.target;

        // load actual image
        img.src = img.dataset.src;

        // optional: remove lazy class
        img.classList.remove("lazy");

        // stop observing once loaded
        observer.unobserve(img);
      }
    });
  },
  {
    root: null,
    rootMargin: "100px", // preload slightly before visible
    threshold: 0.1,
  },
);

images.forEach((img) => imageObserver.observe(img));
```

---

## Why this works

- Browser doesn’t download image until `src` is set
- `IntersectionObserver` triggers only when needed
- `rootMargin` improves perceived performance (preloading)

---

# 3. Lazy Loading Videos

Videos are heavier, so lazy-loading is even more important.

---

## HTML

```html id="vid1"
<video class="lazy-video" controls data-src="video.mp4"></video>
```

---

## JavaScript

```javascript id="io-video1"
const videos = document.querySelectorAll("video.lazy-video");

const videoObserver = new IntersectionObserver(
  (entries, observer) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        const video = entry.target;

        // set video source
        const source = document.createElement("source");
        source.src = video.dataset.src;
        source.type = "video/mp4";

        video.appendChild(source);

        // load and optionally autoplay
        video.load();

        // optional autoplay when visible
        video.play().catch(() => {
          // autoplay may be blocked by browser
        });

        observer.unobserve(video);
      }
    });
  },
  {
    root: null,
    rootMargin: "200px",
    threshold: 0.25,
  },
);

videos.forEach((video) => videoObserver.observe(video));
```

---

# 4. Advanced Pattern: Placeholder + Fade-in

## HTML

```html id="img2"
<img class="lazy" src="placeholder.jpg" data-src="real-image.jpg" />
```

---

## CSS

```css id="css1"
img {
  transition: opacity 0.3s ease;
}

img.loaded {
  opacity: 1;
}
```

---

## JS

```javascript id="io-img2"
const observer = new IntersectionObserver((entries, obs) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      const img = entry.target;

      img.src = img.dataset.src;

      img.onload = () => {
        img.classList.add("loaded");
      };

      obs.unobserve(img);
    }
  });
});

document.querySelectorAll("img.lazy").forEach((img) => {
  observer.observe(img);
});
```

---

# 5. Key Optimization Techniques

## 1. Use `rootMargin` for preloading

```javascript id="opt1"
rootMargin: "150px";
```

👉 Starts loading before user sees it → smoother UX

---

## 2. Unobserve after loading

```javascript id="opt2"
observer.unobserve(img);
```

👉 Prevents unnecessary callbacks

---

## 3. Use low-quality placeholder images (LQIP)

```html id="lqip1"
<img src="tiny-blur.jpg" data-src="high-res.jpg" />
```

---

## 4. Avoid layout shifts (important)

Set width/height:

```html id="layout1"
<img width="600" height="400" />
```

👉 Prevents CLS (Cumulative Layout Shift)

---

# 6. Common Mistakes (Interview Traps)

---

## ❌ Setting `src` directly in HTML

```html id="bad1"
<img src="heavy.jpg" />
```

👉 defeats lazy loading

---

## ❌ Forgetting unobserve

```javascript id="bad2"
observer.observe(img); // never unobserved
```

👉 memory + performance overhead

---

## ❌ Using scroll event instead of IntersectionObserver

```javascript id="bad3"
window.addEventListener("scroll", checkImages);
```

👉 causes layout thrashing

---

## ❌ Not using placeholder dimensions

👉 causes layout jumps

---

# 7. Video Autoplay Strategy (Real-world nuance)

Browsers block autoplay unless:

- muted
- user interaction occurred

Better pattern:

```javascript id="video2"
video.muted = true;
video.play();
```

---

# 8. Infinite Scroll + Lazy Load Combo

You often combine:

- IntersectionObserver (sentinel)
- Lazy loading (images/videos)

Example:

```text id="combo1"
[ items load ]
[ images lazy load ]
[ sentinel triggers next page ]
```

---

# 9. Performance Benefits

Using IntersectionObserver for lazy loading:

- Reduces initial page weight
- Speeds up First Contentful Paint (FCP)
- Saves bandwidth
- Reduces main-thread work
- Eliminates scroll-based polling

---

# 10. Mental Model (Interview Gold)

Think:

> “IntersectionObserver acts as a trigger system that tells me exactly when to hydrate a media element just before it becomes visible.”

---

# 11. Best Practices Summary

To properly lazy-load images/videos:

- Use `data-src` instead of `src`
- Use IntersectionObserver to detect visibility
- Load media only when `isIntersecting === true`
- Use `rootMargin` for preloading UX
- Always `unobserve` after loading
- Set fixed dimensions to avoid layout shifts
- Use placeholders for smooth UX
- Prefer video `.load()` + controlled autoplay

---

# Final Interview Summary

Lazy-loading images and videos using IntersectionObserver involves deferring the loading of media resources until they enter the viewport. This is achieved by observing elements with a placeholder `data-src` attribute and assigning the actual `src` only when the element becomes visible (`isIntersecting` is true). For videos, sources are dynamically appended and loaded using the `.load()` method. Optimizations such as `rootMargin` for preloading, unobserving elements after load, and using placeholders to prevent layout shifts significantly improve performance and user experience while reducing bandwidth usage and initial page load time.

## Question 9. How to implement infinite scroll efficiently

## Question 10. Difference between `requestAnimationFrame` and `setInterval` for animations

## Question 11. How to reduce JavaScript blocking time for page load

## Question 12. How to implement code splitting in JavaScript

## Question 13. How to dynamically import modules for performance

## Question 14. Difference between CSSOM and DOM, and how JS interacts with both

## Question 15. How to batch DOM updates to improve performance

## Question 16. Difference between microtasks and macrotasks in the browser

## Question 17. How to avoid long tasks blocking the main thread

## Question 18. How to measure performance using Performance API

## Question 19. How to implement smooth scrolling programmatically

## Question 20. How to optimize event listeners for dynamic elements
