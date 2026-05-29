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

## Question 3. How to prevent layout thrashing in the browser?

## Question 4. How to minimize repaint and reflow for DOM updates?

## Question 5. How to implement virtual scrolling for large lists?

## Question 6. How to use IntersectionObserver API effectively?

## Question 7. Difference between MutationObserver and IntersectionObserver

## Question 8. How to lazy-load images/videos using IntersectionObserver

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
