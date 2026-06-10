# Set 17

| S.No. | Question                                                                                                                                                     |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [Difference between focus and blur events](#question-1-difference-between-focus-and-blur-events)                                                             |
| 2.    | [How to throttle scroll events for performance](#question-2-how-to-throttle-scroll-events-for-performance)                                                   |
| 3.    | [How to prevent text selection in a webpage using JS](#question-3-how-to-prevent-text-selection-in-a-webpage-using-js)                                       |
| 4.    | [Difference between keydown, keypress, and keyup](#question-4-difference-between-keydown-keypress-and-keyup)                                                 |
| 5.    | [How to restrict input fields to accept only numbers](#question-5-how-to-restrict-input-fields-to-accept-only-numbers)                                       |
| 6.    | [How to create a countdown timer using JavaScript](#question-6-how-to-create-a-countdown-timer-using-javascript)                                             |
| 7.    | [Difference between document.body and document.documentElement](#question-7-difference-between-documentbody-and-documentdocumentelement)                     |
| 8.    | [How to get computed style of an element](#question-8-how-to-get-computed-style-of-an-element)                                                               |
| 9.    | [How to dynamically load a script in JavaScript](#question-9-how-to-dynamically-load-a-script-in-javascript)                                                 |
| 10.   | [How to implement tab navigation with keyboard accessibility](#question-10-how-to-implement-tab-navigation-with-keyboard-accessibility)                      |
| 11.   | [How to detect if a CSS class exists on an element](#question-11-how-to-detect-if-a-css-class-exists-on-an-element)                                          |
| 12.   | [How to implement a drag-and-drop functionality](#question-12-how-to-implement-a-drag-and-drop-functionality)                                                |
| 13.   | [How to detect viewport size and orientation in JS](#question-13-how-to-detect-viewport-size-and-orientation-in-js)                                          |
| 14.   | [Difference between element.offsetHeight, clientHeight, and scrollHeight](#question-14-difference-between-elementoffsetheight-clientheight-and-scrollheight) |
| 15.   | [How to prevent double submission of a form](#question-15-how-to-prevent-double-submission-of-a-form)                                                        |
| 16.   | [How to validate an email or phone number using regex](#question-16-how-to-validate-an-email-or-phone-number-using-regex)                                    |
| 17.   | [How to serialize a form to JSON](#question-17-how-to-serialize-a-form-to-json)                                                                              |
| 18.   | [How to implement infinite scroll](#question-18-how-to-implement-infinite-scroll)                                                                            |
| 19.   | [Difference between innerWidth/outerWidth and clientWidth](#question-19-difference-between-innerwidthouterwidth-and-clientwidth)                             |
| 20.   | [How to implement a copy-to-clipboard feature in JS](#question-20-how-to-implement-a-copy-to-clipboard-feature-in-js)                                        |

## Question 1. Difference between focus and blur events

`focus` and `blur` are DOM events used to detect when an element gains or loses user focus.

- **`focus` event** → Fired when an element becomes active or selected.
- **`blur` event** → Fired when an element loses focus.

These events are commonly used with form elements like `<input>`, `<textarea>`, `<select>`, and interactive UI components.

### 1. `focus` Event

The `focus` event occurs when a user clicks into an input field, tabs into it, or when `.focus()` is called programmatically.

#### Example

```html
<input type="text" id="username" placeholder="Enter username" />

<script>
  const input = document.getElementById("username");

  input.addEventListener("focus", () => {
    console.log("Input focused");
    input.style.backgroundColor = "lightyellow";
  });
</script>
```

#### What happens?

- User clicks the input
- Input gains focus
- `focus` event fires

### 2. `blur` Event

The `blur` event occurs when the focused element loses focus.

This can happen when:

- User clicks outside the element
- User tabs to another field
- Another element receives focus

#### Example

```html
<input type="text" id="email" placeholder="Enter email" />

<script>
  const input = document.getElementById("email");

  input.addEventListener("blur", () => {
    console.log("Input lost focus");
    input.style.backgroundColor = "";
  });
</script>
```

### 3. Real-World Usage

These events are heavily used in:

| Use Case                   | Event   |
| -------------------------- | ------- |
| Highlight active field     | `focus` |
| Form validation            | `blur`  |
| Show helper tooltip        | `focus` |
| Hide suggestions/dropdowns | `blur`  |
| Auto-select text           | `focus` |

### 4. Common Interview Example

#### Validate Input on Blur

```html
<input type="email" id="email" />

<script>
  const email = document.getElementById("email");

  email.addEventListener("blur", () => {
    if (!email.value.includes("@")) {
      console.log("Invalid email");
    }
  });
</script>
```

##### Why use `blur` for validation?

Because validation after the user finishes typing provides a better UX than validating on every keystroke.

### 5. Event Order

If a user moves from one input to another:

1. First input fires `blur`
2. Second input fires `focus`

Example flow:

```text
Input A blur
Input B focus
```

### 6. Important Difference: Bubbling

One of the most important interview points:

| Event   | Bubbles? |
| ------- | -------- |
| `focus` | ❌ No    |
| `blur`  | ❌ No    |

Because they do not bubble, event delegation using these events directly will not work as expected.

Instead, use:

| Alternative Bubbling Event |
| -------------------------- |
| `focusin`                  |
| `focusout`                 |

### 7. `focusin` and `focusout`

These are similar but bubble up the DOM tree.

```javascript
document.addEventListener("focusin", (e) => {
  console.log("Focused:", e.target);
});

document.addEventListener("focusout", (e) => {
  console.log("Blurred:", e.target);
});
```

Useful for:

- Large forms
- Event delegation
- Dynamic inputs

### 8. Programmatic Focus

JavaScript can trigger focus manually.

```javascript
input.focus();
```

Remove focus:

```javascript
input.blur();
```

Example:

```javascript
document.getElementById("username").focus();
```

### 9. Common Pitfalls

#### 1. `blur` fires before click sometimes

A frequent UI issue:

```text
Input blur → dropdown closes → click never happens
```

This occurs in autocomplete/dropdown components.

##### Solution

Use:

- `mousedown` instead of `click`
- Delay blur handling with `setTimeout`

#### 2. `focus` doesn’t bubble

Developers often try:

```javascript
parent.addEventListener("focus", handler);
```

This usually fails unless capture phase is used.

Correct alternatives:

```javascript
parent.addEventListener("focusin", handler);
```

or

```javascript
parent.addEventListener("focus", handler, true);
```

### 10. Accessibility Importance

Focus management is critical for accessibility and keyboard navigation.

Good applications:

- Show visible focus indicators
- Support tab navigation
- Avoid trapping focus
- Manage modal focus properly

Example:

```css
input:focus {
  outline: 2px solid blue;
}
```

### 11. Quick Comparison Table

| Feature               | `focus`                  | `blur`              |
| --------------------- | ------------------------ | ------------------- |
| Triggered when        | Element gains focus      | Element loses focus |
| Common usage          | Activate/highlight field | Validation/cleanup  |
| Bubbles               | ❌ No                    | ❌ No               |
| Opposite event        | `blur`                   | `focus`             |
| Bubbling alternatives | `focusin`                | `focusout`          |

## Question 2. How to throttle scroll events for performance

## Short answer

You throttle scroll events by ensuring the scroll handler runs at most once every fixed interval (e.g., 100–200ms), instead of firing on every single scroll event. This reduces CPU work and improves UI responsiveness.

---

# Why scroll needs throttling

The `scroll` event can fire **dozens of times per frame** while the user scrolls.

Without throttling:

- Heavy DOM work runs repeatedly
- Layout recalculations (reflow/repaint) become expensive
- Main thread gets blocked → janky scrolling

So we control execution frequency.

---

# 1. Basic Throttle Implementation (Interview Standard)

### Concept

Allow function execution once every `delay` milliseconds.

---

## Throttle function

```javascript
function throttle(fn, delay) {
  let lastCall = 0;

  return function (...args) {
    const now = Date.now();

    if (now - lastCall >= delay) {
      lastCall = now;
      fn.apply(this, args);
    }
  };
}
```

---

## Using it with scroll

```javascript
function onScroll() {
  console.log("Scroll event handled:", window.scrollY);
}

window.addEventListener("scroll", throttle(onScroll, 200));
```

---

# 2. More Accurate Version (Using setTimeout + flag)

This version ensures consistent timing behavior.

```javascript
function throttle(fn, delay) {
  let isThrottled = false;
  let savedArgs = null;
  let savedThis = null;

  return function (...args) {
    if (isThrottled) {
      savedArgs = args;
      savedThis = this;
      return;
    }

    fn.apply(this, args);
    isThrottled = true;

    setTimeout(() => {
      isThrottled = false;

      if (savedArgs) {
        fn.apply(savedThis, savedArgs);
        savedArgs = null;
        savedThis = null;
      }
    }, delay);
  };
}
```

### Why this is better

- Ensures last scroll event is not lost
- Prevents “laggy trailing updates”
- More predictable in UI updates

---

# 3. Throttling vs Debouncing (Important Interview Point)

| Concept  | Behavior                                        |
| -------- | ----------------------------------------------- |
| Throttle | Runs at fixed intervals during continuous event |
| Debounce | Runs only after event stops                     |

---

### Scroll use case:

| Use case                | Technique |
| ----------------------- | --------- |
| Show sticky header      | Throttle  |
| Infinite scroll trigger | Throttle  |
| Search input            | Debounce  |
| Resize handler          | Throttle  |

---

# 4. Real-world optimized scroll pattern

### Example: lazy loading / infinite scroll

```javascript
function handleScroll() {
  const scrollTop = window.scrollY;
  const windowHeight = window.innerHeight;
  const documentHeight = document.body.offsetHeight;

  if (scrollTop + windowHeight >= documentHeight - 200) {
    console.log("Load more content...");
  }
}

window.addEventListener("scroll", throttle(handleScroll, 150));
```

---

# 5. Even better optimization: `requestAnimationFrame`

For UI updates tied to rendering, `requestAnimationFrame` is often better than manual throttling.

```javascript
let ticking = false;

function onScroll() {
  if (!ticking) {
    window.requestAnimationFrame(() => {
      console.log("Scroll position:", window.scrollY);
      ticking = false;
    });

    ticking = true;
  }
}

window.addEventListener("scroll", onScroll);
```

---

## Why this is powerful

- Syncs with browser paint cycle (~60fps)
- Prevents unnecessary intermediate calculations
- Ideal for animations, parallax, sticky UI

---

# 6. Common pitfalls

### ❌ Mistake 1: No throttling

```javascript
window.addEventListener("scroll", heavyFunction);
```

Leads to performance degradation.

---

### ❌ Mistake 2: Using debounce for scroll tracking

Debounce waits until scrolling stops → bad for:

- Infinite scroll
- Progress indicators

---

### ❌ Mistake 3: Expensive DOM reads/writes inside scroll

Bad:

```javascript
window.addEventListener("scroll", () => {
  const el = document.getElementById("box");
  el.style.height = window.scrollY + "px";
});
```

Better:

- throttle + batch DOM updates
- avoid forced reflow

---

# 7. Best practices (senior-level answer)

- Prefer **throttle (100–200ms)** for scroll
- Use **requestAnimationFrame** for visual updates
- Minimize DOM access inside handler
- Cache selectors outside scroll handler
- Avoid layout thrashing (read + write separation)
- Use passive listeners when possible:

```javascript
window.addEventListener("scroll", handler, { passive: true });
```

---

# Final interview summary

To throttle scroll events:

- Use a throttle function to limit execution frequency
- Or use `requestAnimationFrame` for render-based updates
- Choose throttle over debounce for continuous scrolling behavior
- Combine with performance best practices (passive listeners, minimal DOM work)

## Question 3. How to prevent text selection in a webpage using JS

## Question 4. Difference between keydown, keypress, and keyup

## Question 5. How to restrict input fields to accept only numbers

## Question 6. How to create a countdown timer using JavaScript

## Question 7. Difference between document.body and document.documentElement

## Question 8. How to get computed style of an element

## Question 9. How to dynamically load a script in JavaScript

## Question 10. How to implement tab navigation with keyboard accessibility

## Question 11. How to detect if a CSS class exists on an element

## Question 12. How to implement a drag-and-drop functionality

## Question 13. How to detect viewport size and orientation in JS

## Question 14. Difference between element.offsetHeight, clientHeight, and scrollHeight

## Question 15. How to prevent double submission of a form

## Question 16. How to validate an email or phone number using regex

## Question 17. How to serialize a form to JSON

## Question 18. How to implement infinite scroll

## Question 19. Difference between innerWidth/outerWidth and clientWidth

## Question 20. How to implement a copy-to-clipboard feature in JS
