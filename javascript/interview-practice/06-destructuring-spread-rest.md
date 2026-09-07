# Practice: Destructuring, Spread & Rest

---

### Q1. Predict the output

```js
const user = { name: "Ada", age: 30, city: "Chennai" };
const { name, ...rest } = user;

console.log(name);
console.log(rest);
```

<details>
<summary>Answer</summary>

```
Ada
{ age: 30, city: "Chennai" }
```

`...rest` (rest pattern) collects all remaining properties not already destructured into a new object.
</details>

---

### Q2. Find the bug — default values in destructuring

```js
function createUser({ name, role = "guest" }) {
  console.log(`${name} - ${role}`);
}

createUser({ name: "Vijay" });
createUser({ name: "Dhoni", role: "admin" });
createUser(); // ?
```

<details>
<summary>Answer</summary>

First two print `Vijay - guest` and `Dhoni - admin`.

Third call throws `TypeError: Cannot destructure property 'name' of 'undefined' as it is undefined.` — destructuring requires *some* object to destructure from; a missing argument is `undefined`, not `{}`.

**Fix:** give the parameter itself a default: `function createUser({ name, role = "guest" } = {}) { ... }`
</details>

---

### Q3. Predict the output — spread copy vs reference

```js
const original = { a: 1, nested: { b: 2 } };
const copy = { ...original };

copy.a = 99;
copy.nested.b = 100;

console.log(original.a);        // ?
console.log(original.nested.b); // ?
```

<details>
<summary>Answer</summary>

```
1
100
```

Spread (`...`) performs a **shallow copy**. Top-level properties (`a`) are copied by value, but nested objects (`nested`) are still shared references. Mutating `copy.nested.b` also affects `original.nested.b`.

For a true deep copy: `structuredClone(original)` (modern JS/Node) or a deep-clone utility.
</details>

---

### Q4. Array destructuring with skipping and defaults — predict the output

```js
const scores = [90, undefined, 70];
const [first, second = 80, third, fourth = 50] = scores;

console.log(first, second, third, fourth);
```

<details>
<summary>Answer</summary>

`90 80 70 50`

Default values apply when the destructured element is `undefined` (not merely missing). `second` is `undefined` in the array → default `80` kicks in. `fourth` doesn't exist in the array at all → also `undefined` → default `50`.
</details>

---

### Q5. Find the bug — rest parameter must be last

```js
function logAll(...args, last) {
  console.log(args, last);
}
```

<details>
<summary>Answer</summary>

`SyntaxError: Rest parameter must be last formal parameter`

The rest parameter (`...args`) must always be the final parameter in a function signature — it collects "everything else", so nothing can come after it.
</details>

---

### Q6. Predict the output — spread in function calls vs merging objects

```js
const defaults = { retries: 3, timeout: 1000 };
const userConfig = { timeout: 5000 };

const finalConfig = { ...defaults, ...userConfig };
console.log(finalConfig);

function sum(a, b, c) {
  return a + b + c;
}
const nums = [1, 2, 3];
console.log(sum(...nums));
```

<details>
<summary>Answer</summary>

```
{ retries: 3, timeout: 5000 }
6
```

When merging objects with spread, **later spreads override earlier ones** for matching keys — `userConfig.timeout` (5000) wins over `defaults.timeout` (1000). Spread in a function call expands the array into individual positional arguments.
</details>

---

### Q7. What's the subtle difference here?

```js
const arr = [1, 2, 3];

const shallowCopy1 = [...arr];
const shallowCopy2 = arr.slice();
const sameRef = arr;

sameRef.push(4);

console.log(arr);          // ?
console.log(shallowCopy1);  // ?
console.log(shallowCopy2);  // ?
```

<details>
<summary>Answer</summary>

```
[1, 2, 3, 4]
[1, 2, 3]
[1, 2, 3]
```

`sameRef` is just another variable pointing to the **same array**, so mutating it via `push` affects `arr` too. `shallowCopy1` (spread) and `shallowCopy2` (`.slice()`) are independent new arrays created *before* the `push`, so they remain unaffected.
</details>
