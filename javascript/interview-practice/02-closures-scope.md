# Practice: Closures & Scope

---

### Q1. Classic counter — what does this print?

```js
function makeCounter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}

const counter1 = makeCounter();
console.log(counter1()); // ?
console.log(counter1()); // ?

const counter2 = makeCounter();
console.log(counter2()); // ?
```

<details>
<summary>Answer</summary>

`1`, `2`, `1`

Each call to `makeCounter()` creates a **new closure** with its own `count` variable. `counter1` and `counter2` do not share state.
</details>

---

### Q2. Find the bug — meant to create 3 independent increment functions

```js
const funcs = [];
for (var i = 0; i < 3; i++) {
  funcs.push(function () {
    return i;
  });
}
console.log(funcs.map(f => f()));
```

<details>
<summary>Answer</summary>

`[3, 3, 3]` — all closures capture the same `var i`.

**Fix:** use `let` (block-scoped, fresh binding per loop iteration) → `[0, 1, 2]`.
</details>

---

### Q3. What gets logged, and why?

```js
function outer() {
  let secret = "abc123";
  function inner() {
    console.log(secret);
  }
  return inner;
}

const revealSecret = outer();
revealSecret();
```

<details>
<summary>Answer</summary>

`abc123`

Even though `outer()` has already returned, `inner` retains a reference to `outer`'s scope (closure). `secret` is not garbage collected because `inner` still references it.
</details>

---

### Q4. Private state via closures — predict the output

```js
function createBankAccount(balance) {
  return {
    deposit(amount) { balance += amount; return balance; },
    withdraw(amount) { balance -= amount; return balance; },
    getBalance() { return balance; }
  };
}

const account = createBankAccount(100);
account.deposit(50);
account.withdraw(30);
console.log(account.getBalance()); // ?
console.log(account.balance);      // ?
```

<details>
<summary>Answer</summary>

`120` then `undefined`.

`balance` is a private variable trapped in the closure — it's not a property on the returned object, so `account.balance` is `undefined`. This is the standard JS way to emulate private fields (pre `#private` syntax).
</details>

---

### Q5. What's wrong with this code (memory/scope trap)?

```js
function attachHandlers() {
  const largeData = new Array(1000000).fill("x");
  document.getElementById("btn").addEventListener("click", () => {
    console.log("clicked");
  });
}
```

<details>
<summary>Answer</summary>

Not a syntax bug, but a subtle **memory leak risk**: the arrow function closure keeps a reference to `attachHandlers`'s entire scope, including `largeData`, even though the handler never uses it. As long as the listener is attached, `largeData` cannot be garbage collected.

**Fix:** avoid capturing unused large variables in the closure, or null them out / scope them separately if not needed after use.
</details>

---

### Q6. Predict the output — shared vs individual closure

```js
function createLoggers() {
  const logs = [];
  for (let i = 0; i < 3; i++) {
    logs.push(() => console.log(`Logger ${i}`));
  }
  return logs;
}

createLoggers().forEach(fn => fn());
```

<details>
<summary>Answer</summary>

```
Logger 0
Logger 1
Logger 2
```

`let` creates a new binding of `i` per iteration, so each closure captures its own `i`.
</details>
