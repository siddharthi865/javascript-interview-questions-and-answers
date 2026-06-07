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

## Short answer

You can prevent text selection in a webpage using JavaScript by disabling the browser’s default selection behavior via event handling (like `selectstart` or `mousedown`) and/or by applying CSS (`user-select: none`). The best practice is to use **CSS for performance and JS only when needed dynamically**.

---

# 1. Best approach (CSS solution — recommended)

Even though you asked “using JS”, the interview-grade answer always starts here because it’s the most efficient.

```css
.no-select {
  user-select: none;
}
```

```html
<div class="no-select">You cannot select this text</div>
```

### Why this is best:

- No JS overhead
- Works across interactions (mouse, keyboard, touch)
- Clean and performant

---

# 2. JavaScript approach (blocking selection events)

If you need to dynamically control it via JS:

## Using `selectstart` event (best JS method)

```javascript id="x9k2ab"
document.addEventListener("selectstart", function (e) {
  e.preventDefault();
});
```

### What happens:

- Browser tries to start text selection
- Event is canceled → selection never begins

---

## Apply only to a specific element

```javascript id="kq3m8d"
const box = document.getElementById("box");

box.addEventListener("selectstart", (e) => {
  e.preventDefault();
});
```

---

# 3. Older fallback: `mousedown`

Some older interview answers mention this:

```javascript id="f9s2ld"
document.addEventListener("mousedown", function (e) {
  e.preventDefault();
});
```

### ⚠️ Problem:

This is too aggressive:

- Prevents clicking buttons
- Breaks input focus
- Bad UX

So avoid it in real apps.

---

# 4. Hybrid approach (CSS + JS toggle)

Useful when selection should be disabled conditionally:

```javascript id="p0l7xq"
function disableSelection(element) {
  element.style.userSelect = "none";

  element.addEventListener("selectstart", (e) => {
    e.preventDefault();
  });
}
```

Usage:

```javascript id="t8v2mn"
const panel = document.getElementById("panel");
disableSelection(panel);
```

---

# 5. Re-enabling selection

If toggling dynamically:

```javascript id="r3w8kp"
function enableSelection(element) {
  element.style.userSelect = "auto";
}
```

Or remove listener:

```javascript id="c2v9ld"
element.removeEventListener("selectstart", handler);
```

(You must keep a reference to `handler`.)

---

# 6. Important edge cases

## 1. Keyboard selection still possible

Even if you block mouse selection:

- `Shift + Arrow keys` may still select text in some cases
- CSS `user-select: none` handles this better than JS

---

## 2. Copy prevention is NOT reliable

Even if selection is disabled:

- DevTools can still copy content
- `Ctrl + C` might still work depending on context

So this is **UX control, not security**

---

## 3. Touch devices behavior

On mobile:

- Long press triggers selection UI
- `user-select: none` is essential
- JS event prevention alone may be inconsistent

---

# 7. Common interview trap

### ❌ Wrong answer:

```javascript
document.body.onselectstart = () => false;
```

This works but is:

- Old style
- Not scalable
- Hard to manage

---

# 8. Best practice summary (what interviewers expect)

A strong senior-level answer:

> The correct way is to use CSS `user-select: none` for performance and accessibility consistency. JavaScript can be used with `selectstart` event prevention when dynamic control is required, but should be avoided for global blocking due to UX and maintainability concerns.

---

# 9. Final comparison

| Method                     | Recommended | Notes                  |
| -------------------------- | ----------- | ---------------------- |
| CSS `user-select: none`    | ✅ Best     | Fast, clean, reliable  |
| `selectstart` JS event     | ⚠️ Okay     | Use for dynamic cases  |
| `mousedown preventDefault` | ❌ Avoid    | Breaks UI interactions |

## Question 4. Difference between keydown, keypress, and keyup

## Short answer

These three keyboard events occur at different stages of a key interaction:

| Event      | When it Fires                                            |
| ---------- | -------------------------------------------------------- |
| `keydown`  | When a key is pressed down                               |
| `keypress` | When a character-producing key is pressed _(deprecated)_ |
| `keyup`    | When the key is released                                 |

**Modern JavaScript typically uses `keydown` and `keyup`. `keypress` is deprecated and should be avoided.**

---

# Event Sequence

When you press and release the **A** key:

```text
keydown
keypress   (deprecated)
keyup
```

Example:

```javascript
document.addEventListener("keydown", () => {
  console.log("keydown");
});

document.addEventListener("keypress", () => {
  console.log("keypress");
});

document.addEventListener("keyup", () => {
  console.log("keyup");
});
```

Output after pressing A:

```text
keydown
keypress
keyup
```

---

# 1. `keydown`

Fires immediately when a key is pressed.

```javascript
document.addEventListener("keydown", (event) => {
  console.log(event.key);
});
```

### Works for:

- Letters
- Numbers
- Arrow keys
- Shift
- Ctrl
- Alt
- Function keys

Example:

```text
ArrowUp
Shift
Enter
a
```

### Common Uses

- Keyboard shortcuts
- Game controls
- Form navigation
- Preventing default actions

```javascript
document.addEventListener("keydown", (e) => {
  if (e.ctrlKey && e.key === "s") {
    e.preventDefault();
    console.log("Save");
  }
});
```

---

# 2. `keypress` (Deprecated)

Historically used to detect character input.

```javascript
document.addEventListener("keypress", (e) => {
  console.log(e.key);
});
```

### Important

`keypress`:

- Fires only for printable characters
- Often ignores keys like:
  - Shift
  - Ctrl
  - Alt
  - Arrow keys
  - Function keys

Example:

| Key     | keypress Fires?   |
| ------- | ----------------- |
| A       | ✅                |
| 1       | ✅                |
| Enter   | Browser-dependent |
| Shift   | ❌                |
| ArrowUp | ❌                |

### Why deprecated?

Different browsers behaved inconsistently.

Modern standards recommend:

```javascript
keydown;
keyup;
```

instead.

---

# 3. `keyup`

Fires when the key is released.

```javascript
document.addEventListener("keyup", (e) => {
  console.log("Released:", e.key);
});
```

### Common Uses

- Stop movement in games
- Final validation
- Detect completed keyboard actions

Example:

```javascript
document.addEventListener("keyup", (e) => {
  if (e.key === "Enter") {
    submitForm();
  }
});
```

---

# Auto-Repeat Behavior

Holding a key down causes repeated `keydown` events.

```javascript
document.addEventListener("keydown", () => {
  console.log("down");
});
```

Holding **A**:

```text
down
down
down
down
...
```

But:

```text
keyup
```

fires only once when released.

---

# Practical Example

Track whether a key is currently pressed:

```javascript
const pressedKeys = {};

document.addEventListener("keydown", (e) => {
  pressedKeys[e.key] = true;
});

document.addEventListener("keyup", (e) => {
  pressedKeys[e.key] = false;
});
```

Used frequently in:

- Games
- Drawing apps
- Keyboard navigation systems

---

# Modern Properties

Instead of old properties:

```javascript
event.keyCode;
event.which;
event.charCode;
```

Use:

```javascript
event.key;
event.code;
```

Example:

```javascript
document.addEventListener("keydown", (e) => {
  console.log(e.key); // "a"
  console.log(e.code); // "KeyA"
});
```

### Difference

```text
event.key  → actual character
event.code → physical key position
```

For example on different keyboard layouts:

```text
key  = "z"
code = "KeyY"
```

can occur.

---

# Common Interview Pitfalls

## Pitfall 1: Using `keypress`

```javascript
document.addEventListener("keypress", handler);
```

This is deprecated.

Prefer:

```javascript
document.addEventListener("keydown", handler);
```

---

## Pitfall 2: Expecting Arrow Keys in `keypress`

```javascript
document.addEventListener("keypress", (e) => {
  console.log(e.key);
});
```

Pressing ArrowUp may produce nothing.

Use `keydown`.

---

## Pitfall 3: Reading Input Value During `keydown`

```javascript
input.addEventListener("keydown", () => {
  console.log(input.value);
});
```

The new character hasn't been inserted yet.

Example:

```text
Input = ""
Press A

keydown -> ""
keyup   -> "a"
```

For text input changes, consider:

```javascript
input.addEventListener("input", handler);
```

---

# Interview Comparison Table

| Feature                     | keydown | keypress          | keyup |
| --------------------------- | ------- | ----------------- | ----- |
| Fires when key pressed      | ✅      | ✅                | ❌    |
| Fires when key released     | ❌      | ❌                | ✅    |
| Supports non-character keys | ✅      | ❌ Mostly         | ✅    |
| Auto-repeat when held       | ✅      | Browser-dependent | ❌    |
| Deprecated                  | ❌      | ✅ Yes            | ❌    |
| Recommended today           | ✅      | ❌                | ✅    |

---

# Interview Tip

A strong interview answer is:

> `keydown` fires when a key is pressed, `keyup` fires when it is released, and `keypress` was historically used for character input but is now deprecated. Modern applications use `keydown` and `keyup` because they work consistently for all keys, including modifiers, navigation keys, and shortcuts.

## Question 5. How to restrict input fields to accept only numbers

## Short answer

The best way to restrict an input field to numbers is to:

1. Use an appropriate HTML input type (`type="number"` when suitable).
2. Validate and sanitize input using the `input` event.
3. Always perform server-side validation as well.

For maximum control and cross-browser consistency, many applications use a text input with JavaScript filtering.

---

# 1. Using `type="number"`

```html
<input type="number" min="0" max="100" />
```

### Advantages

- Built-in browser validation
- Mobile devices show numeric keyboards
- Supports min/max/step

### Limitations

Users can still enter characters such as:

```text
e
E
+
-
.
```

because browsers support scientific notation:

```text
1e5 = 100000
```

Therefore, `type="number"` alone is often insufficient.

---

# 2. Restricting Input Using the `input` Event (Recommended)

The most reliable approach is to sanitize the value whenever it changes.

```html
<input type="text" id="numberInput" />
```

```javascript
const input = document.getElementById("numberInput");

input.addEventListener("input", (e) => {
  e.target.value = e.target.value.replace(/\D/g, "");
});
```

### How it works

`\D` means:

```text
Any non-digit character
```

Examples:

| Input   | Result |
| ------- | ------ |
| `123`   | `123`  |
| `12a3`  | `123`  |
| `1@2#3` | `123`  |
| `abc`   | ``     |

---

# 3. Restricting at `keydown` Time

Another approach is to prevent invalid keys before they appear.

```javascript
input.addEventListener("keydown", (e) => {
  if (!/[0-9]/.test(e.key)) {
    e.preventDefault();
  }
});
```

### Problem

This blocks useful keys:

```text
Backspace
Delete
ArrowLeft
ArrowRight
Tab
```

So you must explicitly allow them.

```javascript
const allowedKeys = ["Backspace", "Delete", "ArrowLeft", "ArrowRight", "Tab"];

input.addEventListener("keydown", (e) => {
  if (!/[0-9]/.test(e.key) && !allowedKeys.includes(e.key)) {
    e.preventDefault();
  }
});
```

---

# 4. Handling Paste Operations

Users can bypass `keydown` restrictions by pasting.

```text
Ctrl + V
Right-click → Paste
```

This is why `input` event validation is superior.

```javascript
input.addEventListener("input", (e) => {
  e.target.value = e.target.value.replace(/\D/g, "");
});
```

It handles:

- Typing
- Paste
- Drag-and-drop
- Autofill

---

# 5. Allowing Decimals

For decimal numbers:

```javascript
input.addEventListener("input", (e) => {
  e.target.value = e.target.value.replace(/[^\d.]/g, "");
});
```

Input:

```text
12.34
```

Output:

```text
12.34
```

---

# 6. Allowing Negative Numbers

```javascript
input.addEventListener("input", (e) => {
  e.target.value = e.target.value.replace(/[^\d-]/g, "");
});
```

More robust validation would ensure `-` appears only at the beginning.

---

# 7. Mobile-Friendly Numeric Input

A common production pattern:

```html
<input type="text" inputmode="numeric" pattern="[0-9]*" />
```

### Benefits

- Shows numeric keyboard on mobile
- Avoids quirks of `type="number"`
- Gives full control to JavaScript

This is often preferred in OTP, PIN, and phone number fields.

---

# 8. React Example

```jsx
function NumberInput() {
  const handleChange = (e) => {
    e.target.value = e.target.value.replace(/\D/g, "");
  };

  return <input type="text" onInput={handleChange} />;
}
```

---

# Common Interview Pitfalls

### ❌ Only using `keydown`

```javascript
input.addEventListener("keydown", validate);
```

Fails for:

- Paste
- Drag/drop
- Autofill

---

### ❌ Trusting `type="number"`

```html
<input type="number" />
```

Many browsers still allow:

```text
1e5
+
-
.
```

---

### ❌ Relying solely on frontend validation

Never trust client-side validation alone.

Always validate again on the server.

---

# Best Practice (Senior-Level Answer)

For numeric-only input:

```html
<input type="text" inputmode="numeric" />
```

```javascript
input.addEventListener("input", (e) => {
  e.target.value = e.target.value.replace(/\D/g, "");
});
```

This approach:

- Works for typing and paste
- Is mobile-friendly
- Avoids `type="number"` quirks
- Provides consistent behavior across browsers

---

# Interview Summary

| Technique             | Recommended        | Notes                      |
| --------------------- | ------------------ | -------------------------- |
| `type="number"`       | ⚠️ Partial         | Allows `e`, `+`, `-`, etc. |
| `keydown` filtering   | ⚠️ Okay            | Misses paste/autofill      |
| `input` sanitization  | ✅ Best            | Handles all input methods  |
| `inputmode="numeric"` | ✅ Best for mobile | Shows numeric keyboard     |

A senior frontend answer is: **Use the `input` event to sanitize input, optionally combine it with `inputmode="numeric"` for mobile keyboards, and never rely solely on `type="number"` or frontend validation.**

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
