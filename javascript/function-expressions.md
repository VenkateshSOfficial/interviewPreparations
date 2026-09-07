# Function Expressions in JavaScript — Deep Dive (Interview Perspective)

## 1. What is a Function Expression?

A **function expression** is when a function is created as part of an **expression**, typically by assigning it to a variable, passing it as an argument, or using it inline — rather than declaring it as a standalone statement with the `function` keyword at the start of a line.

```js
const greet = function (name) {
  return `Hello, ${name}`;
};
```

Here, the right-hand side (`function (name) { ... }`) is the function expression; it's being *assigned* to the variable `greet`, just like you'd assign a string or a number.

Contrast this with a **function declaration**:
```js
function greet(name) {
  return `Hello, ${name}`;
}
```
Same-looking function, but declared as a statement, not assigned to anything as part of an expression. This distinction (declaration vs expression) is one of the most common early JS interview questions.

### The simplest way to tell them apart
> **If the `function` keyword appears where JS expects a value (right side of `=`, inside `()` as an argument, inside `[]`, etc.), it's a function expression. If it starts a statement on its own, it's a declaration.**

---

## 2. Types of Function Expressions

### a) Anonymous Function Expression
```js
const add = function (a, b) {
  return a + b;
};
```
No name given after `function` — the most common form when assigning to a variable.

### b) Named Function Expression
```js
const factorial = function fact(n) {
  return n <= 1 ? 1 : n * fact(n - 1);
};
```
The inner name (`fact`) is only visible **inside** the function body itself — useful for recursion or for showing a meaningful name in stack traces/debuggers, without polluting the outer scope with that name.

```js
console.log(typeof fact); // "undefined" — fact is not accessible outside
```

### c) Arrow Function Expression (ES6+)
```js
const add = (a, b) => a + b;
```
Technically also a function expression (assigned to a variable), but with different `this`/`arguments` semantics — covered separately in the `this`-binding practice file. Interviewers sometimes ask "is an arrow function a function expression?" — yes, structurally, but its *behavior* differs from a classic function expression.

### d) Immediately Invoked Function Expression (IIFE)
```js
(function () {
  console.log("Runs right away");
})();
```
A function expression that is defined and called in the same statement. Only possible with expressions — `function () {}()` alone (without the wrapping parentheses) is a syntax error, because the parser expects a declaration at the start of a statement, and declarations can't be immediately invoked this way.

---

## 3. When Do We Use Function Expressions? (Practical Use Cases)

### a) Conditional function definition
```js
let logger;
if (process.env.NODE_ENV === "development") {
  logger = function (msg) { console.log("[DEV]", msg); };
} else {
  logger = function (msg) { /* no-op or send to monitoring */ };
}
```
You can't conditionally create two functions with the **same name** using declarations in the same scope without errors/overwriting oddities — expressions let you assign a function value based on runtime logic cleanly.

### b) Passing functions as arguments (callbacks)
```js
[1, 2, 3].map(function (n) {
  return n * 2;
});

button.addEventListener("click", function () {
  console.log("Clicked");
});
```
Extremely common in test automation and general JS — passing an inline function expression as a callback to array methods, event listeners, `setTimeout`, promise `.then()`, etc.

### c) Returning functions from other functions (factories/closures)
```js
function createMultiplier(factor) {
  return function (n) {
    return n * factor;
  };
}

const double = createMultiplier(2);
console.log(double(5)); // 10
```
Function expressions are the natural way to return a customized function from a factory function — a core closures pattern (see closures practice file).

### d) IIFEs for private scope / one-time setup
```js
const config = (function () {
  const secretKey = "abc123"; // not accessible outside
  return { getKey: () => secretKey };
})();
```
Useful for module-like encapsulation, especially in codebases not using ES modules.

### e) Assigning methods to objects dynamically
```js
const handlers = {};
handlers.onSuccess = function (data) {
  console.log("Success:", data);
};
```

### f) Avoiding hoisting-related bugs intentionally
Since function expressions are **not hoisted** with their body (only the variable declaration is hoisted, and stays `undefined`/TDZ until the assignment line runs), some teams prefer expressions to force "define before use" discipline and avoid accidentally calling a function before its logic is finalized.

---

## 4. Function Expression vs Function Declaration — Key Differences (Interview Table)

| Aspect | Function Declaration | Function Expression |
| --- | --- | --- |
| Syntax | `function name() {}` as a statement | `const name = function() {}` |
| Hoisting | Fully hoisted (name + body) | Only the variable binding is hoisted, not the function body |
| Callable before definition in code | Yes | No — `TypeError` (var, calls `undefined`) or `ReferenceError` (let/const, TDZ) |
| Can be anonymous | No — must have a name | Yes |
| Requires a name | Yes | No |
| Common use | Reusable, general-purpose functions defined upfront | Callbacks, conditional logic, closures, IIFEs |

### Classic interview code snippet
```js
console.log(sum(2, 3));      // works — declaration is hoisted
console.log(multiply(2, 3)); // TypeError: multiply is not a function

function sum(a, b) {
  return a + b;
}

var multiply = function (a, b) {
  return a * b;
};
```

---

## 5. Common Interview Follow-Up Questions

**Q: Why use a named function expression instead of an anonymous one?**
A: Better stack traces during debugging, and it enables clean recursion without relying on the outer variable name (which could later be reassigned, breaking the recursive call).

```js
let fact = function factorial(n) {
  return n <= 1 ? 1 : n * factorial(n - 1); // safe even if `fact` variable is reassigned later
};
let temp = fact;
fact = null;
console.log(temp(5)); // still works — 120
```

**Q: Can a function expression be a constructor (used with `new`)?**
A: Yes, if it's a regular `function` expression (not an arrow function). Arrow function expressions cannot be used with `new`.

**Q: Does a function expression have its own `this`?**
A: A regular function expression — yes (dynamic, depends on call site). An arrow function expression — no (lexical `this`, inherited from enclosing scope).

**Q: What happens if you try to call a function expression before its line executes?**
A: Depends on the variable keyword:
```js
console.log(fn); // undefined (var) — no error yet
fn();             // TypeError: fn is not a function
var fn = function () {};
```
```js
console.log(fn2); // ReferenceError: Cannot access 'fn2' before initialization (TDZ)
let fn2 = function () {};
```

---

## Summary

- A function expression creates a function as part of a larger expression (usually a variable assignment), rather than as a standalone declaration.
- Use function expressions for **callbacks, closures, conditional definitions, IIFEs, and dynamic object methods** — anywhere you need functions as first-class values rather than fixed, always-available utilities.
- The most tested interview distinction: **hoisting behavior** differs completely from function declarations, which is a frequent source of "predict the output" and "find the bug" style questions.
