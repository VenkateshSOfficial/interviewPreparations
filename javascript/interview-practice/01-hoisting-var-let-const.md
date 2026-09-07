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
