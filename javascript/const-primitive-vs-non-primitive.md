# Usage of `const` — Primitive vs Non-Primitive

A common misconception is that `const` makes a value immutable. In reality, `const` only prevents **reassignment of the variable binding**. Whether the underlying value can change depends on whether it is primitive or non-primitive.

## 1. `const` with Primitives

Primitive values (`string`, `number`, `boolean`, `bigint`, `symbol`, `undefined`, `null`) are immutable by nature and stored directly in the variable. Since `const` disallows reassignment, a `const` primitive can **never** change.

```js
const age = 30;
// age = 31; // Error: Assignment to constant variable.

const name = "Vijay";
// name = "Dhoni"; // Error: Assignment to constant variable.
```

There is no way to "mutate" a primitive in place, so `const` fully locks the value.

## 2. `const` with Non-Primitives (Objects, Arrays, Functions)

Non-primitive values are stored as a **reference**. `const` only locks the reference (i.e., the variable cannot be pointed to a different object/array), but the contents of that object/array can still be mutated freely.

```js
const user = { name: "Ada" };
user.name = "Grace"; // allowed - mutating a property
console.log(user); // { name: "Grace" }

// user = { name: "Turing" }; // Error: Assignment to constant variable.
```

```js
const numbers = [1, 2, 3];
numbers.push(4); // allowed - mutating array contents
console.log(numbers); // [1, 2, 3, 4]

// numbers = [5, 6]; // Error: Assignment to constant variable.
```

## 3. Why This Happens

- `const` creates a binding, not a frozen value.
- For primitives: the binding holds the value itself, so no reassignment = no change possible.
- For non-primitives: the binding holds a reference to an object in memory. `const` blocks re-pointing that reference, but the object it points to is still mutable.

## 4. Making Objects Truly Immutable

To prevent mutation of a `const` object's contents, use `Object.freeze()` (shallow freeze):

```js
const settings = Object.freeze({ theme: "dark" });
settings.theme = "light"; // silently fails (throws in strict mode)
console.log(settings.theme); // "dark"
```

Note: `Object.freeze()` is shallow — nested objects inside the frozen object are still mutable unless frozen individually.

## Summary

| Aspect | Primitive + `const` | Non-Primitive + `const` |
| --- | --- | --- |
| Reassignment of variable | Not allowed | Not allowed |
| Mutation of value/contents | Not possible (immutable by nature) | Allowed (properties/elements can change) |
| True immutability | Guaranteed | Requires `Object.freeze()` (shallow) |
