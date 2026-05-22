# Set 5

| S.No. | Question                                                                                                                                                                                                   |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What are service workers?](#question-1-what-are-service-workers)                                                                                                                                          |
| 2.    | [Explain how JS handles promises under the hood](#question-2-explain-how-js-handles-promises-under-the-hood)                                                                                               |
| 3.    | [How to chain promises with error handling](#question-3-how-to-chain-promises-with-error-handling)                                                                                                         |
| 4.    | [Explain generator functions in JavaScript](#question-4-explain-generator-functions-in-javascript)                                                                                                         |
| 5.    | [Difference between generators and async functions](#question-5-difference-between-generators-and-async-functions)                                                                                         |
| 6.    | [How to use async iterators](#question-6-how-to-use-async-iterators)                                                                                                                                       |
| 7.    | [What is Proxy in JavaScript? Give use case](#question-7-what-is-proxy-in-javascript-give-use-case)                                                                                                        |
| 8.    | [What is Reflect API?](#question-8-what-is-reflect-api)                                                                                                                                                    |
| 9.    | [How to create private fields in JS classes](#question-9-how-to-create-private-fields-in-js-classes)                                                                                                       |
| 10.   | [What are decorators?](#question-10-what-are-decorators)                                                                                                                                                   |
| 11.   | [Explain the concept of Symbol.iterator](#question-11-explain-the-concept-of-symboliterator)                                                                                                               |
| 12.   | [Difference between deep copy using structuredClone vs JSON methods](#question-12-difference-between-deep-copy-using-structuredclone-vs-json-methods)                                                      |
| 13.   | [What are Tagged Template Literals?](#question-13-what-are-tagged-template-literals)                                                                                                                       |
| 14.   | [How to implement a simple event emitter in JavaScript](#question-14-how-to-implement-a-simple-event-emitter-in-javascript)                                                                                |
| 15.   | [Explain JS memory model: stack vs heap](#question-15-explain-js-memory-model-stack-vs-heap)                                                                                                               |
| 16.   | [Difference between shallow equality and deep equality](#question-16-difference-between-shallow-equality-and-deep-equality)                                                                                |
| 17.   | [How to optimize performance of large array operations](#question-17-how-to-optimize-performance-of-large-array-operations)                                                                                |
| 18.   | [Explain the difference between classical async patterns (callbacks) vs promises vs async/await](#question-18-explain-the-difference-between-classical-async-patterns-callbacks-vs-promises-vs-asyncawait) |
| 19.   | [What are Web Workers? How do they work?](#question-19-what-are-web-workers-how-do-they-work)                                                                                                              |
| 20.   | [How to prevent race conditions in asynchronous JS code](#question-20-how-to-prevent-race-conditions-in-asynchronous-js-code)                                                                              |

## Question 1. What are service workers?

> Service Workers are **background scripts in the browser that act as a network proxy**, enabling features like **offline support, caching, push notifications, and background sync** for web applications.

## Detailed Explanation

A **Service Worker** is a special type of JavaScript file that runs separately from the main browser thread. It sits between your web app and the network, intercepting network requests and deciding how to handle them (fetch from cache, network, or both).

It is a key technology behind **Progressive Web Apps (PWAs)**.

### Key Characteristics

#### 1. Runs in the background

- Not tied to a web page
- Runs independently of the main JavaScript thread
- Cannot directly access DOM

#### 2. Acts as a network proxy

It can intercept requests made by the page:

```javascript
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      return response || fetch(event.request);
    }),
  );
});
```

Here:

- It first checks cache
- If not found, fetches from network

#### 3. Event-driven lifecycle

Service workers follow a strict lifecycle:

- **Register**
- **Install**
- **Activate**

Example:

```javascript
// Register service worker
if ("serviceWorker" in navigator) {
  navigator.serviceWorker
    .register("/sw.js")
    .then((registration) => {
      console.log("Service Worker registered:", registration);
    })
    .catch((err) => {
      console.log("Registration failed:", err);
    });
}
```

Inside `sw.js`:

```javascript
self.addEventListener("install", (event) => {
  console.log("Service Worker installing...");
});

self.addEventListener("activate", (event) => {
  console.log("Service Worker activated");
});
```

### Key Features Enabled by Service Workers

#### 1. Offline Support

Using caching strategies (Cache API), apps can work without internet.

Example:

- Gmail offline mode
- Google Docs offline editing

#### 2. Caching Strategies

Common strategies:

- Cache First
- Network First
- Stale-While-Revalidate

Example (Cache First):

```javascript
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((cached) => {
      return cached || fetch(event.request);
    }),
  );
});
```

#### 3. Push Notifications

Service workers can receive push messages even when the browser is closed.

```javascript
self.addEventListener("push", (event) => {
  const data = event.data.json();

  self.registration.showNotification(data.title, {
    body: data.body,
    icon: "/icon.png",
  });
});
```

#### 4. Background Sync

Allows retrying failed network requests when connectivity returns.

```javascript
self.addEventListener("sync", (event) => {
  if (event.tag === "sync-data") {
    event.waitUntil(syncDataToServer());
  }
});
```

### Important Limitations

- No direct DOM access
- Runs only over HTTPS (except localhost)
- Event-based (terminates when idle)
- Requires explicit registration
- Browser support varies (but modern browsers support it well)

### Common Pitfalls

#### 1. Caching mistakes

Improper caching can serve stale or broken data.

#### 2. Complex debugging

Since it runs separately, debugging requires DevTools → Application tab.

#### 3. Versioning issues

Old service workers may remain active unless properly updated.

### Best Practices

- Always version your cache names:

  ```javascript
  const CACHE_NAME = "app-v1";
  ```

- Use proper update strategy:
  - Skip waiting when needed
  - Clean old caches in `activate`

- Keep service worker logic lightweight

### Real-World Use Cases

- Twitter Lite (fast loading, offline support)
- Pinterest (improved performance)
- Flipkart PWA (offline browsing + push notifications)

### Summary

A **Service Worker** is a background JavaScript file that:

- Intercepts network requests
- Enables offline-first web apps
- Handles caching, push notifications, and background sync
- Runs independently of the main browser thread

It is a core building block for **Progressive Web Apps (PWAs)** and modern web performance optimization.

## Question 2. Explain how JS handles promises under the hood

## Question 3. How to chain promises with error handling

## Question 4. Explain generator functions in JavaScript

## Question 5. Difference between generators and async functions

## Question 6. How to use async iterators

## Question 7. What is Proxy in JavaScript? Give use case

## Question 8. What is Reflect API?

## Question 9. How to create private fields in JS classes

## Question 10. What are decorators?

## Question 11. Explain the concept of Symbol.iterator

## Question 12. Difference between deep copy using structuredClone vs JSON methods

## Question 13. What are Tagged Template Literals?

## Question 14. How to implement a simple event emitter in JavaScript

## Question 15. Explain JS memory model: stack vs heap

## Question 16. Difference between shallow equality and deep equality

## Question 17. How to optimize performance of large array operations

## Question 18. Explain the difference between classical async patterns (callbacks) vs promises vs async/await

## Question 19. What are Web Workers? How do they work?

## Question 20. How to prevent race conditions in asynchronous JS code
