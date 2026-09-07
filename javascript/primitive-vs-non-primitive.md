# JavaScript Data Types: Primitive vs Non-Primitive

This is a common JavaScript interview topic because it connects directly to assignment, mutation, equality, function arguments, and a number of surprising bugs.

## 1. JavaScript Data Type Categories

JavaScript values are commonly grouped into two categories:

1. **Primitive values**: Immutable values that are not objects.
2. **Non-primitive values**: Objects, including collections and callable functions.

## 2. Primitive Data Types

JavaScript has **seven primitive types**:

| Type | Example | Notes |
| --- | --- | --- |
| `string` | `"hello"` | Textual data. |
| `number` | `42`, `3.14`, `NaN`, `Infinity` | Includes integers and floating-point values. |
| `bigint` | `123n` | Integers larger than `Number` can safely represent. |
| `boolean` | `true`, `false` | Logical values. |
| `undefined` | `let result;` | A value was not assigned, or is absent by default. |
| `symbol` | `Symbol("id")` | Creates unique identifiers, often for object keys. |
| `null` | `null` | Intentionally represents no value. |

### Key Characteristics of Primitives

- A primitive variable contains the value itself.
- Primitives are **immutable**. Once a primitive value exists, it cannot be changed; an operation produces a new value instead.
- Comparing two equal primitive values with `===` compares their actual values.

```js
let firstName = "Ada";
let copiedName = firstName;

copiedName = "Grace";

console.log(firstName);  // "Ada"
console.log(copiedName); // "Grace"
```

Reassigning `copiedName` does not affect `firstName`, because the string value was copied.

### Primitive Immutability

```js
let message = "hello";
message.toUpperCase();

console.log(message); // "hello"

message = message.toUpperCase();
console.log(message); // "HELLO"
```

`toUpperCase()` returns a new string. It does not modify the original string.

## 3. Non-Primitive Data Types

Non-primitive values are also called **reference types**. In JavaScript, all non-primitive values are objects.

Common examples include:

- Plain objects: `{ name: "Ada" }`
- Arrays: `[1, 2, 3]`
- Functions: `function greet() {}`
- Dates: `new Date()`
- Regular expressions: `/abc/`
- Maps, Sets, WeakMaps, and WeakSets

```js
const developer = {
  name: "Ada",
  skills: ["JavaScript", "TypeScript"]
};

console.log(typeof developer); // "object"
console.log(typeof developer.skills); // "object"
```

Functions are special objects:

```js
function greet() {
  return "Hello";
}

console.log(typeof greet); // "function"
```

## 4. Assignment: Value Copy vs Reference Copy

This is the most important distinction for interviews.

### Primitives Are Copied by Value

```js
let scoreA = 10;
let scoreB = scoreA;

scoreB = 20;

console.log(scoreA); // 10
console.log(scoreB); // 20
```

`scoreA` and `scoreB` hold independent primitive values.

### Objects Share a Reference

```js
const userA = { name: "Ada" };
const userB = userA;

userB.name = "Grace";

console.log(userA.name); // "Grace"
console.log(userB.name); // "Grace"
console.log(userA === userB); // true
```

The variables contain copies of the same object reference. Both variables can access the same object, so mutating the object through either variable is visible through the other.

> Interview precision: JavaScript is always **pass-by-value**. For objects, the value being copied is a reference to an object. Avoid saying JavaScript is simply "pass-by-reference."

## 5. Equality Comparison

### Primitive Equality

For primitives, strict equality compares value and type.

```js
console.log(5 === 5); // true
console.log("5" === 5); // false
console.log(true === true); // true
```

### Object Equality

For objects, strict equality compares identity, meaning whether both variables refer to the same object.

```js
const first = { id: 1 };
const second = { id: 1 };
const third = first;

console.log(first === second); // false
console.log(first === third);  // true
```

`first` and `second` have identical-looking contents but are separate objects.

Arrays follow the same rule:

```js
console.log([1, 2] === [1, 2]); // false
```

To compare object contents, use an explicit comparison strategy. For simple JSON-compatible data, `JSON.stringify()` can sometimes help, but it is not a general deep-equality solution because property order and unsupported values can matter. In production, use a suitable deep-equality utility or write a comparison based on the required fields.

## 6. Function Arguments

JavaScript passes arguments by value.

### Primitive Argument

```js
function increase(value) {
  value += 1;
}

let count = 1;
increase(count);

console.log(count); // 1
```

The function receives a copy of the number. Reassigning its local parameter does not alter `count`.

### Object Argument

```js
function markAsActive(account) {
  account.active = true;
}

const account = { active: false };
markAsActive(account);

console.log(account.active); // true
```

The function receives a copied reference that points to the same object, so property mutation is observable outside the function.

However, reassigning the parameter itself does not replace the caller's variable:

```js
function replaceAccount(account) {
  account = { active: true };
}

const originalAccount = { active: false };
replaceAccount(originalAccount);

console.log(originalAccount.active); // false
```

## 7. `const` Does Not Make Objects Immutable

`const` prevents reassignment of the variable binding. It does not freeze an object.

```js
const settings = { theme: "light" };

settings.theme = "dark"; // Allowed: object mutation
// settings = {};         // TypeError: reassignment is not allowed
```

Use `Object.freeze()` when shallow immutability is needed:

```js
const configuration = Object.freeze({
  api: { timeout: 5000 }
});

// configuration.api = {};             // Prevented
configuration.api.timeout = 1000;      // Still allowed: nested object
```

`Object.freeze()` is shallow. Nested objects must also be frozen, or application code should use immutable update patterns.

## 8. The `typeof null` Quirk

```js
console.log(typeof null); // "object"
```

Despite this result, `null` is a primitive. `typeof null === "object"` is a long-standing historical bug in JavaScript that remains for backward compatibility.

To check specifically for `null`:

```js
value === null;
```

## 9. Primitive Wrapper Objects and Autoboxing

Primitives can use methods because JavaScript temporarily wraps them in an object when needed.

```js
const language = "javascript";
console.log(language.length);       // 10
console.log(language.toUpperCase()); // "JAVASCRIPT"
```

Do not normally construct wrapper objects manually:

```js
const primitiveText = "hello";
const objectText = new String("hello");

console.log(primitiveText === objectText); // false
console.log(typeof primitiveText); // "string"
console.log(typeof objectText);    // "object"
```

Prefer primitive literals such as `"hello"`, `42`, and `true` over `new String()`, `new Number()`, and `new Boolean()`.

## 10. Type Coercion and Falsy Values

JavaScript may automatically convert a value from one type to another. This is called **implicit type coercion** and is a frequent interview topic.

```js
console.log("5" + 3);         // "53"  (string concatenation)
console.log("5" - 3);         // 2     (string converted to number)
console.log(true + 1);         // 2     (true converts to 1)
console.log(null + 1);         // 1     (null converts to 0)
console.log(undefined + 1);    // NaN
```

Prefer explicit conversion when intent matters:

```js
console.log(Number("42"));  // 42
console.log(String(99));      // "99"
console.log(Boolean(0));      // false
console.log(Boolean("hello")); // true
```

The falsy primitive values are `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, and `NaN`. Every object is truthy, including empty arrays and empty objects.

```js
console.log(Boolean([])); // true
console.log(Boolean({})); // true
```

> Interview tip: Prefer `===` to `==`. Loose equality performs coercion, which can produce surprising results such as `"5" == 5` being `true`.

## 11. Copying Objects: Shallow vs Deep Copy

Assignment does not copy an object; it shares its reference. Create a clone when an independent object is required.

```js
const original = { name: "Ada", role: "Engineer" };
const copy = { ...original };

copy.name = "Grace";

console.log(original.name); // "Ada"
console.log(copy.name);     // "Grace"
```

Object spread and `Object.assign()` create **shallow copies**. Top-level properties are copied, but nested objects are still shared.

```js
const original = { profile: { city: "London" } };
const shallowCopy = { ...original };

shallowCopy.profile.city = "Paris";

console.log(original.profile.city); // "Paris"
```

For many modern JavaScript environments, use `structuredClone()` for a deep copy of supported data:

```js
const original = { profile: { city: "London" } };
const deepCopy = structuredClone(original);

deepCopy.profile.city = "Paris";

console.log(original.profile.city); // "London"
```

`JSON.parse(JSON.stringify(value))` is sometimes used for JSON-only data, but it loses or changes values such as `undefined`, `Symbol`, functions, `Date`, `Map`, `Set`, `Infinity`, and circular references. It is not a universal deep-cloning solution.

## 12. `null` vs `undefined`

Both represent absence of a useful value, but their intent differs.

- `undefined` usually means a value has not been assigned, a property is missing, or a function did not return a value.
- `null` is an explicit assignment indicating intentional absence.

```js
let notAssigned;
const selectedUser = null;

console.log(notAssigned);  // undefined
console.log(selectedUser); // null
```

## 13. Memory Model: A Careful Interview Explanation

A simplified model is useful:

- Primitive variables hold their values directly.
- Object variables hold a value that identifies or references an object.
- Assigning an object variable copies that reference value, allowing multiple variables to reach the same object.

Avoid claiming exact memory layout details, such as "primitives are always stored on the stack and objects are always stored on the heap." JavaScript engines can optimize storage differently. The reliable language-level behavior is value copying for primitives and reference-value copying for objects.

## 14. Common Interview Questions and Answers

### Q1. What are the primitive data types in JavaScript?

**Answer:** `string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, and `null`.

### Q2. Are arrays primitive or non-primitive?

**Answer:** Arrays are non-primitive objects. `typeof []` returns `"object"`; use `Array.isArray(value)` to detect arrays reliably.

### Q3. Is a function an object in JavaScript?

**Answer:** Yes. Functions are callable objects. That is why properties can be assigned to them, even though `typeof someFunction` returns `"function"`.

### Q4. Why do two objects with the same properties fail `===`?

**Answer:** `===` checks object identity, not deep content. Two object literals create two different objects, even if their properties match.

### Q5. Does `const` make an object immutable?

**Answer:** No. It prevents reassignment of the variable, but its object properties can still be changed unless the object is frozen or managed immutably.

### Q6. Is JavaScript pass-by-reference?

**Answer:** No. JavaScript is pass-by-value. When an object is passed, the copied value is a reference to that object, which allows mutation through the parameter.

### Q7. Why is `typeof null` equal to `"object"`?

**Answer:** It is a historical JavaScript bug preserved for compatibility. `null` is still a primitive value.

### Q8. What is the difference between a shallow copy and a deep copy?

**Answer:** A shallow copy duplicates only the top-level object. Nested objects retain their shared references. A deep copy duplicates nested data too, so changes to the nested values do not affect the original object.

### Q9. Are empty arrays and objects falsy?

**Answer:** No. `[]` and `{}` are objects, and all objects are truthy in JavaScript.

## 15. Quick Comparison Table

| Feature | Primitive | Non-Primitive |
| --- | --- | --- |
| Examples | `42`, `"hi"`, `true`, `null` | `{}`, `[]`, `function () {}` |
| Main category | Value, not object | Object/reference type |
| Assignment behavior | Copies the value | Copies a reference value |
| Mutable? | No | Usually yes |
| `===` behavior | Compares value and type | Compares object identity |
| Common `typeof` result | `"string"`, `"number"`, etc. | Usually `"object"`; functions are `"function"` |

## 16. Strong Interview Summary

> Primitive values are immutable and are copied directly during assignment or argument passing. Non-primitive values are objects. JavaScript still passes them by value, but the copied value is an object reference, so multiple variables or function parameters can mutate the same object. Strict equality compares primitive values by value and objects by identity.

That explanation is accurate, concise, and avoids the common mistake of calling JavaScript purely pass-by-reference.