# Practice: Async / Await, Promises, Event Loop Order

---

### Q1. Classic ordering — predict the output

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");
```

<details>
<summary>Answer</summary>

```
1
4
3
2
```

Synchronous code runs first (`1`, `4`). Then the **microtask queue** (Promises) drains before the **macrotask queue** (setTimeout), even with a `0ms` delay.
</details>

---

### Q2. What does this print, and in what order?

```js
async function foo() {
  console.log("A");
  await null;
  console.log("B");
}

console.log("start");
foo();
console.log("end");
```

<details>
<summary>Answer</summary>

```
start
A
end
B
```

`foo()` runs synchronously until the first `await`. After `await`, the rest of the function (`console.log("B")`) is scheduled as a microtask, so it runs after the current synchronous code (`console.log("end")`) finishes.
</details>

---

### Q3. Find the bug — this was meant to log results in order 1, 2, 3

```js
function delay(val, ms) {
  return new Promise(resolve => setTimeout(() => resolve(val), ms));
}

[3, 1, 2].forEach(async (num) => {
  const result = await delay(num, num * 100);
  console.log(result);
});
```

<details>
<summary>Answer</summary>

Prints `1, 2, 3` (not the array order `3, 1, 2`) — because each callback runs independently and resolves based on its own delay, not the array order.

Bigger issue: `forEach` does **not** wait for async callbacks — it fires all three immediately without awaiting them, so if the intent was sequential processing, this is a bug. Use a `for...of` loop with `await` for true sequential execution:
```js
for (const num of [3, 1, 2]) {
  const result = await delay(num, num * 100);
  console.log(result);
}
// 3, 1, 2 in that exact order, waiting for each
```
</details>

---

### Q4. What's missing in this code (common real-world bug)?

```js
async function getUser(id) {
  const response = fetch(`/api/users/${id}`);
  const data = response.json();
  return data;
}
```

<details>
<summary>Answer</summary>

Missing `await` on both `fetch` and `.json()`.

```js
async function getUser(id) {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();
  return data;
}
```

Without `await`, `response` is a `Promise`, and calling `.json()` on a `Promise` object throws `TypeError: response.json is not a function`.
</details>

---

### Q5. Predict the output — Promise.all vs sequential awaits

```js
function delay(val, ms) {
  return new Promise(resolve => setTimeout(() => resolve(val), ms));
}

async function sequential() {
  console.time("seq");
  await delay(1, 1000);
  await delay(2, 1000);
  console.timeEnd("seq"); // ~?
}

async function parallel() {
  console.time("par");
  await Promise.all([delay(1, 1000), delay(2, 1000)]);
  console.timeEnd("par"); // ~?
}
```

<details>
<summary>Answer</summary>

`sequential()` takes ~2000ms (each `await` blocks until the previous resolves).
`parallel()` takes ~1000ms (`Promise.all` runs both promises concurrently).

Common interview trap: candidates write sequential awaits when independent async calls could run in parallel via `Promise.all`.
</details>

---

### Q6. What happens if a promise rejects and there's no `catch`?

```js
async function risky() {
  throw new Error("boom");
}

risky();
console.log("after call");
```

<details>
<summary>Answer</summary>

`after call` still prints, then an **unhandled promise rejection** is logged (`UnhandledPromiseRejection: Error: boom`).

`risky()` returns a rejected promise; since nothing calls `.catch()` or awaits it inside a `try/catch`, the rejection goes unhandled. In Node.js this can even crash the process (depending on version/config) or in the browser log a console warning.

**Fix:**
```js
risky().catch(err => console.error(err.message));
```
</details>

---

### Q7. Predict the output — microtask vs macrotask ordering with async/await mixed

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

async function asyncFn() {
  console.log("3");
  await Promise.resolve();
  console.log("4");
}
asyncFn();

Promise.resolve().then(() => console.log("5"));

console.log("6");
```

<details>
<summary>Answer</summary>

```
1
3
6
4
5
2
```

Walkthrough:
1. `console.log("1")` → sync.
2. `setTimeout` scheduled (macrotask).
3. `asyncFn()` called → logs `"3"` synchronously, then hits `await` → rest of function queued as microtask.
4. `Promise.resolve().then(...)` for `"5"` queued as microtask (after the one from step 3, since it was scheduled later).
5. `console.log("6")` → sync.
6. Microtask queue drains in order: `"4"` then `"5"`.
7. Macrotask (`setTimeout`) runs last: `"2"`.
</details>
