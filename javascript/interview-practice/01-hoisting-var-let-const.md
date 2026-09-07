# Practice: Hoisting & var / let / const Gotchas

Scenario-based questions. Try to predict the output (or find the bug) **before** scrolling to the answer.

---

### Q1. What gets printed?

```js
console.log(a);
var a = 5;
```

<details>
<summary>Answer</summary>

`undefined`

`var` declarations are hoisted to the top of their scope and initialized with `undefined`. Only the declaration is hoisted, not the assignment.
</details>

---

### Q2. What happens here?

```js
console.log(b);
let b = 5;
```

<details>
<summary>Answer</summary>

`ReferenceError: Cannot access 'b' before initialization`

`let`/`const` are hoisted too, but they stay in the **Temporal Dead Zone (TDZ)** from the start of the block until their declaration line is executed.
</details>

---

### Q3. Find the bug — this was meant to log 0,1,2 with a 1s gap

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
```

<details>
<summary>Answer</summary>

Prints `3, 3, 3` instead of `0, 1, 2`.

`var` is function-scoped, so all three callbacks share the **same** `i`. By the time `setTimeout` fires, the loop has finished and `i` is `3`.

**Fix:** use `let` (block-scoped, new binding per iteration):
```js
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// 0, 1, 2
```
</details>

---

### Q4. What's wrong with this code?

```js
function greet() {
  console.log(message);
  const message = "hi";
}
greet();
```

<details>
<summary>Answer</summary>

`ReferenceError: Cannot access 'message' before initialization` — same TDZ issue as Q2, just inside a function body.
</details>

---

### Q5. Predict the output

```js
if (true) {
  var x = 10;
}
console.log(x);

if (true) {
  let y = 20;
}
console.log(y);
```

<details>
<summary>Answer</summary>

`10` then `ReferenceError: y is not defined`.

`var` is function-scoped (leaks out of the `if` block); `let` is block-scoped (does not exist outside the `if` block).
</details>

---

### Q6. What is missing / wrong in this code?

```js
const config;
config = { retries: 3 };
console.log(config);
```

<details>
<summary>Answer</summary>

`SyntaxError: Missing initializer in const declaration.`

`const` must be assigned a value at the moment it's declared — it cannot be declared first and assigned later like `var`/`let`.
</details>

---

### Q7. Predict the output (re-declaration)

```js
var count = 1;
var count = 2;
console.log(count);

let total = 1;
let total = 2; // ?
```

<details>
<summary>Answer</summary>

First `console.log` prints `2` (var allows re-declaration).

The `let total` re-declaration throws `SyntaxError: Identifier 'total' has already been declared` — this is a compile-time error, so nothing in the block runs at all (not even the earlier `console.log`).
</details>

---

## Intermediate / Tougher Questions

---

### Q8. Predict the output — closures inside a loop, mixed with function scope

```js
function buildHandlers() {
  var handlers = [];
  for (var i = 0; i < 3; i++) {
    let j = i;
    handlers.push(function () {
      console.log(`i=${i}, j=${j}`);
    });
  }
  return handlers;
}

buildHandlers().forEach(fn => fn());
```

<details>
<summary>Answer</summary>

```
i=3, j=0
i=3, j=1
i=3, j=2
```

`i` is `var` — one shared variable across the whole function, so every closure sees the final value `3`. `j` is `let`, redeclared fresh inside the block on every iteration, so each closure captures its own `j` at the time it was created. This mixes both behaviors in one snippet — a favorite way interviewers test if you truly understand the difference (not just memorized "let = good, var = bad").
</details>

---

### Q9. What gets printed, and why does it not throw?

```js
console.log(typeof myVar);
console.log(typeof myLet);

var myVar = 10;
let myLet = 20;
```

<details>
<summary>Answer</summary>

```
undefined
```
then throws `ReferenceError: Cannot access 'myLet' before initialization`.

`typeof` on a hoisted-but-uninitialized `var` safely returns `"undefined"` — this is a special exception for `typeof` on undeclared/uninitialized `var`. But `typeof` on a `let`/`const` variable that's still in the **temporal dead zone** still throws, because the TDZ blocks *any* access to the binding, not just reading its value. This surprises many people who assume `typeof` is always "safe" to use on any variable.
</details>

---

### Q10. Find the bug — block scope vs function parameter shadowing

```js
function processOrder(status) {
  if (status === "pending") {
    var status = "processing";
    console.log("Inside if:", status);
  }
  console.log("After if:", status);
}

processOrder("pending");
```

<details>
<summary>Answer</summary>

```
Inside if: processing
After if: processing
```

Because `var status` inside the `if` block is **hoisted to the top of the function** and refers to the *same* `status` as the function parameter — there's no separate variable created. Redeclaring `var status` inside the block just reassigns the existing parameter. This can silently corrupt a function parameter's value, which is one of the strongest real-world arguments against using `var`.

If `let status` were used instead, it would throw `SyntaxError: Identifier 'status' has already been declared` if it shadowed at the same function-body level, or safely shadow the parameter only within the `if` block scope without affecting the outer `status` — behaving in a much more predictable way.
</details>

---

### Q11. Predict the output — IIFE and hoisting interaction

```js
var result = [];

for (var i = 0; i < 3; i++) {
  (function (captured) {
    result.push(function () {
      return captured;
    });
  })(i);
}

console.log(result.map(fn => fn()));
```

<details>
<summary>Answer</summary>

`[0, 1, 2]`

This is the **pre-ES6 fix** for the classic `var`-in-loop closure problem — before `let` existed, developers used an **Immediately Invoked Function Expression (IIFE)** to force a new function scope per iteration, passing `i`'s current value in as `captured`. Each IIFE call creates its own scope with its own `captured` parameter, so the inner closures each remember a different value. Knowing this pattern shows you understand *why* `let` was even introduced, not just that it "magically" fixes loops.
</details>

---

### Q12. What's the output, and what TDZ trap is hiding here?

```js
let value = 10;

function demo() {
  console.log(value);
  let value = 20;
}

demo();
```

<details>
<summary>Answer</summary>

`ReferenceError: Cannot access 'value' before initialization`

Even though an outer `value` exists (`10`) and could seemingly be logged, JavaScript resolves variable references by **scope, not by which value "makes sense."** Since `demo()` declares its own local `value` with `let`, that name is scoped to the entire function body from the start — shadowing the outer `value` completely. The `console.log(value)` line refers to the *local* `value`, which is still in its temporal dead zone at that point, so it throws instead of silently falling back to the outer variable.
</details>

---

### Q13. Find the bug — const with destructuring and re-declaration in a loop

```js
const items = [{ id: 1 }, { id: 2 }, { id: 3 }];

for (const { id } of items) {
  console.log(id);
}
```

<details>
<summary>Answer</summary>

Prints `1`, `2`, `3` — no error.

This looks like it should fail ("can't redeclare const"), but it doesn't: in a `for...of` loop, **each iteration creates a completely new block scope**, just like with `let`. So `const { id }` is a fresh binding every time, not a re-declaration of the same variable — perfectly legal. This is the same per-iteration-scope mechanism that makes `let i` work correctly in a regular `for` loop.
</details>

---

### Q14. Predict the output — function declaration hoisting vs var

```js
console.log(typeof greet);
console.log(typeof sayBye);

function greet() {
  return "hi";
}

var sayBye = function () {
  return "bye";
};
```

<details>
<summary>Answer</summary>

```
function
undefined
```

Function **declarations** (`function greet() {}`) are fully hoisted — both the name and the entire function body move to the top, so calling/checking `typeof greet` before the line works fine. Function **expressions** assigned to `var` (`var sayBye = function () {}`) only hoist the `var` declaration (initialized to `undefined`); the assignment happens later at runtime. This distinction trips up people who assume "hoisting" always means the same thing for functions and variables.
</details>

---

### Q15. Find the bug — nested block re-declaration confusion

```js
let level = "outer";
{
  console.log(level);
  let level = "inner";
}
```

<details>
<summary>Answer</summary>

`ReferenceError: Cannot access 'level' before initialization`

Even though this looks like just a bare `{ }` block (not a function/if/for), it still creates its own scope for `let`. The inner `let level` shadows the outer one for the *entire* block, including the line before its declaration — so `console.log(level)` hits the TDZ of the inner `level`, not the outer one. Bare blocks are an underused but valid scoping tool in JS.
</details>

---

### Q16. Predict the output — switch statement scoping trap

```js
const action = "create";

switch (action) {
  case "create":
    let message = "Creating item";
    console.log(message);
    break;
  case "delete":
    let message = "Deleting item"; // ?
    console.log(message);
    break;
}
```

<details>
<summary>Answer</summary>

`SyntaxError: Identifier 'message' has already been declared`

All `case` blocks in a single `switch` share **one** block scope (the switch's own `{ }`), not a separate scope per `case`. Declaring `let message` in two different `case` branches is a re-declaration in the same scope, which throws at parse time — even though only one branch would ever actually execute.

**Fix:** wrap each `case` body in its own `{ }` block to give it a private scope:
```js
case "create": {
  let message = "Creating item";
  console.log(message);
  break;
}
case "delete": {
  let message = "Deleting item";
  console.log(message);
  break;
}
```
</details>

---

### Q17. What gets printed, and why is this considered risky?

```js
function outer() {
  return inner();

  function inner() {
    return "hoisted function works";
  }
}

console.log(outer());
```

<details>
<summary>Answer</summary>

`hoisted function works`

Function declarations are hoisted to the top of their enclosing scope, so `inner` is fully available even though it's called *before* its declaration appears in the source. This works, but relying on it (calling functions before their declaration) is considered poor practice — it makes code harder to read top-to-bottom and can break unexpectedly if `inner` is ever changed to a `const`/arrow function expression instead.
</details>

---

### Q18. Find the bug — const object hoisting misconception

```js
function setup() {
  console.log(config);
  const config = { debug: true };
}

setup();
```

<details>
<summary>Answer</summary>

`ReferenceError: Cannot access 'config' before initialization`

Some assume `const` objects behave differently from `const` primitives regarding hoisting — they don't. `const` (and `let`) are hoisted to the top of their scope but remain in the TDZ regardless of what type of value they'll eventually hold. The bug has nothing to do with objects vs primitives — it's purely about accessing the binding before its declaration line executes.
</details>

---

### Q19. Predict the output — hoisting with multiple `var` in different branches

```js
function checkAccess(role) {
  if (role === "admin") {
    var permission = "full";
  } else {
    var permission = "limited";
  }
  return permission;
}

console.log(checkAccess("admin"));
console.log(checkAccess("guest"));
console.log(typeof permission);
```

<details>
<summary>Answer</summary>

```
full
limited
undefined
```

Both `var permission` declarations (in the `if` and `else` branches) refer to the **same single hoisted variable** at the top of `checkAccess`'s function scope — so whichever branch runs simply assigns to that one variable, and it works correctly inside the function. The last line, `typeof permission` at the **global** scope, is `undefined` because `permission` never leaked outside the function — `var` is function-scoped, not global, when declared inside a function.
</details>

---

### Q20. Find the bug — temporal dead zone with default parameters

```js
function process(a, b = a + 1, a) {
  console.log(a, b);
}
```

<details>
<summary>Answer</summary>

`SyntaxError: Identifier 'a' has already been declared`

You can't declare two parameters with the same name (`a`) when the function uses any destructuring/default-value parameters — this is disallowed in strict mode and in any function with non-simple parameters (defaults, rest, destructuring). Note separately: even without the duplicate, default parameter expressions (`b = a + 1`) can reference earlier parameters because each parameter effectively behaves like sequential `let` declarations in their own mini temporal-dead-zone chain — referencing a *later* parameter in an earlier default would throw a TDZ error.
</details>

---

### Q21. Predict the output — class hoisting vs function hoisting

```js
console.log(typeof Vehicle);

const car = new Vehicle(); // ?

class Vehicle {
  constructor() {
    this.wheels = 4;
  }
}
```

<details>
<summary>Answer</summary>

`typeof Vehicle` logs `"undefined"`... actually more precisely: this throws before even reaching that log in real engines? Let's be exact:

Classes, like `let`/`const`, are hoisted but stay in the **temporal dead zone** until their definition executes — unlike function declarations, they are NOT usable before their declaration line. `console.log(typeof Vehicle)` — for `let`/`const`/`class` bindings, `typeof` also throws inside the TDZ (this is a well-known exception to `typeof`'s usual "always safe" behavior, unlike Q9's `var` case). So the actual output is:

`ReferenceError: Cannot access 'Vehicle' before initialization`

Key contrast: function **declarations** are fully hoisted and callable early; **classes** are hoisted but unusable early. Many candidates incorrectly assume classes behave like functions since both use similar syntax patterns.
</details>

---

### Q22. Find the bug — global `var` leaking onto the global object

```js
var apiUrl = "https://api.example.com";

function resetConfig() {
  apiUrl = "https://staging.example.com"; // missing var/let/const
}

resetConfig();
console.log(apiUrl);
console.log(window.apiUrl === apiUrl); // in a browser
```

<details>
<summary>Answer</summary>

```
https://staging.example.com
true
```

Since `resetConfig` reassigns `apiUrl` without `var`/`let`/`const`, it doesn't create a new local variable — it just walks up the scope chain and modifies the existing global `apiUrl`. Additionally, `var` declared at the top level in browsers creates a property directly on the `window` object, so `window.apiUrl` and the loose global `apiUrl` are literally the same binding. This is a classic reason to use `"use strict"` (or ES modules, which are strict by default) — under strict mode, an undeclared assignment like this throws `ReferenceError: apiUrl is not defined` instead of silently polluting global scope.
</details>

---

### Q23. Predict the output — closures capturing `const` in async loop

```js
async function processItems() {
  const results = [];
  for (const id of [1, 2, 3]) {
    results.push(
      new Promise(resolve => setTimeout(() => resolve(id * 10), 100))
    );
  }
  console.log(await Promise.all(results));
}

processItems();
```

<details>
<summary>Answer</summary>

`[10, 20, 30]`

Even though this uses `setTimeout` inside a loop (echoing the classic `var` bug from earlier questions), it works correctly here for two reasons: (1) `for...of` with `const` creates a fresh binding of `id` per iteration — same mechanism as `let` — so each `setTimeout` callback closes over its own `id`; and (2) `Promise.all` waits for all three timers to resolve and preserves the **original array order** in its result, regardless of which promise settles first. Combining closures, `const`/`let` per-iteration scoping, and `Promise.all` ordering is a common "integration" question that tests multiple concepts at once.
</details>
