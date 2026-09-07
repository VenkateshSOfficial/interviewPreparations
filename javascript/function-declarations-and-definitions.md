# Different Ways of Function Declaration & Definition in JavaScript

A frequent interview topic, especially for candidates transitioning from Java, because JavaScript treats functions as **first-class citizens** — they can be assigned to variables, passed as arguments, returned from other functions, and defined in several syntactically different ways, each with its own hoisting, `this`-binding, and use-case behavior.

## 1. Function Declaration (Function Statement)

```js
function greet(name) {
  return `Hello, ${name}`;
}
```

**Characteristics:**
- Starts with the `function` keyword followed by a name.
- **Fully hoisted** — both the name and the function body are hoisted to the top of the enclosing scope, so it can be called before its definition in the source code.
- Has its own `this` (depends on how it's *called*, not where it's defined).
- Can be used as a constructor with `new`.

```js
console.log(greet("Ada")); // works even if called before definition, due to hoisting
```

**Interview angle:** This is the most "traditional" way, closest to how methods look in Java, but the hoisting behavior is uniquely JS.

---

## 2. Function Expression

```js
const greet = function (name) {
  return `Hello, ${name}`;
};
```

**Characteristics:**
- A function is created and assigned to a variable (or used inline, e.g., passed as a callback).
- **Not hoisted the same way** — only the variable declaration (`var`/`let`/`const`) is hoisted, not the function body. Calling it before the assignment throws `TypeError` (if `var`, it's `undefined` first) or `ReferenceError` (if `let`/`const`, TDZ).
- Can be **named** or **anonymous**.

```js
console.log(typeof greet); // undefined (if var) — cannot call yet
const greet = function (name) {
  return `Hi, ${name}`;
};
```

### Named Function Expression

```js
const greet = function greetInner(name) {
  return `Hello, ${name}`;
};
```

The name `greetInner` is only accessible **inside** the function itself (useful for recursion or stack traces), not from the outer scope.

**Interview angle:** Understanding why function expressions aren't hoisted the same way as declarations is a very common "predict the output" question.

---

## 3. Arrow Function Expression (ES6+)

```js
const greet = (name) => {
  return `Hello, ${name}`;
};

// Implicit return (single expression, no braces)
const greetShort = (name) => `Hello, ${name}`;

// Single parameter, parentheses optional
const square = x => x * x;

// No parameters
const sayHi = () => "Hi!";
```

**Characteristics:**
- Shorter syntax, especially useful for callbacks (array methods, promises, event handlers).
- **Does not have its own `this`** — it lexically inherits `this` from the enclosing scope at the time it's defined (huge interview topic, see `this`-binding practice file).
- Cannot be used as a constructor (`new ArrowFn()` throws `TypeError`).
- Does not have its own `arguments` object (uses the enclosing scope's `arguments`, or none at all).
- Not hoisted like declarations (same rules as function expressions, since it's typically assigned to a variable).

**Interview angle:** Arrow functions are heavily tested for `this` binding gotchas — e.g., using them as object methods when you actually need dynamic `this` (a mistake).

---

## 4. Function Constructor (rarely used, but sometimes asked)

```js
const greet = new Function("name", "return `Hello, ${name}`;");
console.log(greet("Ada"));
```

**Characteristics:**
- Creates a function from strings at runtime.
- **Avoid in production** — behaves like `eval()`: security risk (can execute arbitrary code, opens door to injection if inputs aren't trusted), poor performance (no JIT optimization the same way), and no access to the surrounding lexical scope (functions created this way only close over the global scope).

**Interview angle:** Good to *know it exists* and explain why it's avoided (OWASP-style code-injection risk), not something you'd use in real projects.

---

## 5. Immediately Invoked Function Expression (IIFE)

```js
(function () {
  console.log("Runs immediately");
})();

// Arrow function IIFE
(() => {
  console.log("Also runs immediately");
})();
```

**Characteristics:**
- Defines and executes a function in one step.
- Creates a private scope — commonly used (pre-ES6 modules/`let`) to avoid polluting the global scope and to fix closure-in-loop bugs (see hoisting practice file, Q11).
- Still relevant for module patterns, self-executing setup code, and avoiding naming collisions in scripts loaded via plain `<script>` tags.

---

## 6. Method Shorthand (ES6, inside objects/classes)

```js
const calculator = {
  add(a, b) {
    return a + b;
  },
  subtract(a, b) {
    return a - b;
  }
};

class Calculator {
  add(a, b) {
    return a + b;
  }
}
```

**Characteristics:**
- Shorthand syntax for defining functions as object/class properties — behaves like a regular function (has its own `this`, bound to whichever object calls it), not an arrow function.
- Common source of "extracting the method loses `this`" bugs (see `this`-binding practice file, Q3 & Q6).

---

## 7. Generator Functions

```js
function* idGenerator() {
  let id = 1;
  while (true) {
    yield id++;
  }
}

const gen = idGenerator();
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
```

**Characteristics:**
- Declared with `function*`.
- Can **pause** execution with `yield` and resume later — useful for lazy sequences, custom iterators.
- Less common in day-to-day QA automation code, but sometimes comes up in "advanced JS" interview rounds.

---

## 8. Async Functions

```js
async function fetchData() {
  const response = await fetch("/api/data");
  return response.json();
}

const fetchDataArrow = async () => {
  const response = await fetch("/api/data");
  return response.json();
};
```

**Characteristics:**
- Any of the above forms (declaration, expression, arrow, method shorthand) can be prefixed with `async`.
- Always returns a `Promise`, regardless of what the function body returns.
- Errors thrown inside become **rejected promises** rather than synchronous throws (catch with `.catch()` or `try/catch` + `await`).

**Interview angle:** Very relevant for Playwright/TS test automation, since most Playwright APIs (`page.click()`, `page.goto()`, etc.) are promise-based and used with `async/await`.

---

## Summary Table

| Form | Hoisted (fully usable before definition)? | Has own `this`? | Can use `new`? | Typical use |
| --- | --- | --- | --- | --- |
| Function Declaration | Yes | Yes | Yes | General-purpose reusable functions |
| Function Expression | No (TDZ/undefined) | Yes | Yes | Assigning to variables, conditional definition |
| Arrow Function | No | No (lexical) | No | Callbacks, preserving outer `this` |
| Function Constructor | No | Yes | Yes | Rarely used; avoid (security/perf) |
| IIFE | N/A (runs immediately) | Yes | N/A | Private scope, one-time setup |
| Method Shorthand | No | Yes (dynamic) | No (typically) | Object/class methods |
| Generator Function | Yes (as declaration) | Yes | Rarely | Lazy iteration, custom iterators |
| Async Function | Depends on base form | Depends on base form | No | Asynchronous operations, Promises |

## Key Interview Takeaways

1. **Hoisting differs**: declarations are fully hoisted; expressions (including arrow functions) are not.
2. **`this` binding differs**: regular functions get dynamic `this` (depends on call site); arrow functions get lexical `this` (inherited from where they're defined).
3. **Arrow functions cannot be constructors** and have no `arguments` object of their own.
4. **`async` is a modifier**, not a separate function type — it can combine with declarations, expressions, arrow functions, and methods.
5. Prefer **function declarations or arrow functions** in modern code; avoid the `Function` constructor entirely due to security and performance concerns.
