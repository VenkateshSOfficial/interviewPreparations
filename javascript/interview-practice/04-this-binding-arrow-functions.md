# Practice: `this` Binding & Arrow Functions

---

### Q1. Find the bug — `this` is undefined inside the callback

```js
const user = {
  name: "Vijay",
  greet: function () {
    setTimeout(function () {
      console.log(`Hello, ${this.name}`);
    }, 100);
  }
};

user.greet();
```

<details>
<summary>Answer</summary>

Prints `Hello, undefined`.

The regular `function` passed to `setTimeout` has its own `this`, which defaults to `undefined` (strict mode) or the global object (non-strict), not `user`.

**Fix — use an arrow function** (inherits `this` from enclosing scope):
```js
greet: function () {
  setTimeout(() => {
    console.log(`Hello, ${this.name}`);
  }, 100);
}
```
</details>

---

### Q2. What gets printed?

```js
const obj = {
  value: 42,
  getValue: () => {
    return this.value;
  }
};

console.log(obj.getValue());
```

<details>
<summary>Answer</summary>

`undefined`

Arrow functions don't have their own `this` — they capture `this` from the surrounding lexical scope at definition time (here, the module/global scope, not `obj`). Arrow functions should not be used as object methods when you need `this` to refer to the object.
</details>

---

### Q3. Predict the output — losing `this` via destructuring/passing method as callback

```js
class Counter {
  count = 0;
  increment() {
    this.count++;
    console.log(this.count);
  }
}

const counter = new Counter();
const incrementFn = counter.increment;
incrementFn(); // ?
```

<details>
<summary>Answer</summary>

`TypeError: Cannot read properties of undefined (reading 'count')`

When `increment` is extracted as a plain reference and called standalone, it loses its binding to `counter`. Inside the function, `this` is `undefined` (strict mode/class body).

**Fixes:**
```js
const incrementFn = counter.increment.bind(counter);
// or call as counter.increment()
// or define as an arrow class field: increment = () => { this.count++; ... }
```
</details>

---

### Q4. What's wrong with this array callback?

```js
class Cart {
  items = [10, 20, 30];
  total = 0;

  calculateTotal() {
    this.items.forEach(function (item) {
      this.total += item; // ?
    });
    return this.total;
  }
}

console.log(new Cart().calculateTotal());
```

<details>
<summary>Answer</summary>

`TypeError: Cannot read properties of undefined (reading 'total')`

The regular function passed to `forEach` has its own `this` (undefined in strict mode), unrelated to the `Cart` instance.

**Fix — use an arrow function**, which inherits `this` from `calculateTotal`:
```js
this.items.forEach((item) => {
  this.total += item;
});
```
</details>

---

### Q5. Predict the output — explicit binding methods

```js
function sayName() {
  console.log(this.name);
}

const person1 = { name: "Ada" };
const person2 = { name: "Grace" };

const boundToPerson1 = sayName.bind(person1);
boundToPerson1(); // ?
boundToPerson1.call(person2); // ?
```

<details>
<summary>Answer</summary>

`Ada` then `Ada`

`bind()` permanently locks `this` to the object passed in (`person1`). Once bound, subsequent `.call()`/`.apply()` attempts to override `this` are ignored.
</details>

---

### Q6. Event handler `this` trap (common in DOM/Playwright page-object style code)

```js
class Page {
  constructor() {
    this.title = "Home Page";
  }
  logTitle() {
    console.log(this.title);
  }
}

const page = new Page();
const button = { onClick: page.logTitle };
button.onClick(); // ?
```

<details>
<summary>Answer</summary>

`undefined`

`page.logTitle` is passed as a plain function reference and invoked as `button.onClick()`, so `this` inside `logTitle` refers to `button`, not `page`. `button.title` doesn't exist → `undefined`.

**Fix:** `button.onClick = page.logTitle.bind(page);` or use an arrow function wrapper: `() => page.logTitle()`.
</details>
