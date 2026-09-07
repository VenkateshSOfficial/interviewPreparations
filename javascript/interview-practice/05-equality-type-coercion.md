# Practice: Equality & Type Coercion

---

### Q1. Predict the output for each

```js
console.log(1 == "1");     // ?
console.log(1 === "1");    // ?
console.log(null == undefined);  // ?
console.log(null === undefined); // ?
console.log(NaN == NaN);   // ?
console.log(0 == false);   // ?
console.log(0 === false);  // ?
console.log("" == false);  // ?
```

<details>
<summary>Answer</summary>

```
true   // "1" coerced to number 1
false  // different types, no coercion
true   // special case in spec
false  // different types
false  // NaN is never equal to anything, even itself
true   // false coerced to 0
false  // different types
true   // both coerced to 0/""→0
```

Rule of thumb: `==` performs type coercion before comparing; `===` never does. For interviews, always prefer `===`/`!==` unless there's a deliberate reason (e.g., `== null` to catch both `null` and `undefined`).
</details>

---

### Q2. Find the bug

```js
function isEmpty(value) {
  if (value == null) {
    return true;
  }
  return value.length === 0;
}

console.log(isEmpty(0)); // ?
```

<details>
<summary>Answer</summary>

Throws `TypeError: Cannot read properties of undefined (reading 'length')` — because `0.length` is `undefined`, and `undefined === 0` is `false`... wait, actually `(0).length` is `undefined`, not a throw. Let's be precise:

`(0).length` → `undefined` (no error, numbers just don't have `.length`), so `isEmpty(0)` returns `false` (since `undefined === 0` is `false`).

The real bug: this function assumes `value` is always a string/array. Calling it with a number (`0`) silently returns `false` instead of throwing or handling it explicitly — a common source of subtle logic bugs. Passing `isEmpty(undefined)` correctly returns `true` due to `== null` catching both `null` and `undefined`.
</details>

---

### Q3. What does this print, and why?

```js
console.log([] == false);
console.log([] == "");
console.log([] == 0);
console.log([1] == 1);
console.log([1, 2] == "1,2");
```

<details>
<summary>Answer</summary>

All print `true`.

Arrays get converted to primitives via `toString()` before comparison: `[].toString()` → `""`, `[1].toString()` → `"1"`, `[1,2].toString()` → `"1,2"`. Then the empty string / string coerces further to `0` where needed. This is a classic "why `==` is dangerous" interview trap.
</details>

---

### Q4. Predict the output — truthy/falsy edge cases

```js
if ("0") console.log("A");
if (0) console.log("B");
if ([]) console.log("C");
if ({}) console.log("D");
if ("") console.log("E");
if (" ") console.log("F");
```

<details>
<summary>Answer</summary>

Logs: `A`, `C`, `D`, `F`

Falsy values in JS: `false, 0, -0, 0n, "", null, undefined, NaN`. Everything else is truthy — including `[]` and `{}` (empty array/object are objects, and all objects are truthy), and `" "` (non-empty string).
</details>

---

### Q5. Find the bug — comparing objects

```js
const cartA = { items: 3 };
const cartB = { items: 3 };

if (cartA == cartB) {
  console.log("Carts match");
} else {
  console.log("Carts differ");
}
```

<details>
<summary>Answer</summary>

Prints `Carts differ`.

Objects are compared by reference, not by value/content, for both `==` and `===`. Even with identical properties, two separate object literals are never equal unless they refer to the exact same object in memory.

**Fix (deep compare):** use a utility like `assert.deepStrictEqual`, `lodash.isEqual`, or manual field comparison.
</details>

---

### Q6. What's printed, and what's the safer alternative?

```js
function add(a, b) {
  return a + b;
}

console.log(add(1, "2"));   // ?
console.log(add("1", 2));   // ?
console.log(add(1, 2));     // ?
console.log(add(null, 1));  // ?
console.log(add(undefined, 1)); // ?
```

<details>
<summary>Answer</summary>

```
"12"    // number + string → string concatenation
"12"    // same, order doesn't matter
3       // both numbers → arithmetic addition
1       // null coerced to 0
NaN     // undefined coerced to NaN
```

`+` is overloaded for both string concatenation and numeric addition, and JS silently coerces types instead of throwing — a frequent source of production bugs. In TypeScript, typing `a: number, b: number` prevents this class of bug entirely (a strong argument for TS over JS in test frameworks).
</details>
