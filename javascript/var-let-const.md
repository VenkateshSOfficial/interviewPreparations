# var vs let vs const

## 1) Scope
- `var` => function scope (accessible outside a block, but not outside its function)
- `let` & `const` => block scope (not accessible outside the block they are declared in)

```js
function varScope(){
    if(true){
        var msg = "hello";
    }
    console.log(msg); // works, "hello" (function scoped)
}

function letScope(){
    if(true){
        let greet = "hello world";
        console.log(greet); // works inside block
    }
    // console.log(greet); // Error: Cannot find name 'greet'.
}
```

## 2) Declaration / Assignment
- `var` can be declared first and initialized later
- `let` can be declared first and initialized later
- `const` must be initialized at the time of declaration (cannot be declared without a value)

```js
var x;
x = 10; // ok

let a;
a = 12; // ok

// const data; // Error: 'const' declarations must be initialized.
const data = 10; // ok
```

## 3) Re-declaration
- `var` allows re-declaration
- `let` & `const` do not allow re-declaration in the same scope

```js
var city = "Chennai";
var city = "Trichy"; // ok, re-declared

let place = "Madurai";
// let place = "Chennai"; // Error: Cannot redeclare block-scoped variable 'place'.

const country = "India";
// const country = "Russia"; // Error: Cannot redeclare block-scoped variable 'country'.
```

## 4) Re-initialization / Re-assignment
- `var` & `let` can be re-initialized (value can be changed)
- `const` cannot be re-initialized, it always holds a constant value

```js
var foo = 10;
foo = 20; // ok

let value = "Vijay";
value = "Dhoni"; // ok

// const cannot be reassigned after initialization
```

## 5) Hoisting
- `var` is hoisted and returns `undefined` when accessed before declaration
- `let` & `const` are also hoisted, but land in the "temporal dead zone" and throw a `ReferenceError` when accessed before initialization

```js
console.log(v); // undefined
var v = 10;

console.log(zz); // ReferenceError: Cannot access 'zz' before initialization
let zz = 20;
```

## Summary
| Feature              | var              | let          | const        |
|----------------------|------------------|--------------|--------------|
| Scope                | Function scope   | Block scope  | Block scope  |
| Declare w/o value    | Allowed          | Allowed      | Not allowed  |
| Re-declaration       | Allowed          | Not allowed  | Not allowed  |
| Re-initialization     | Allowed          | Allowed      | Not allowed  |
| Hoisting behavior    | `undefined`      | Temporal dead zone (ReferenceError) | Temporal dead zone (ReferenceError) |

**General guidance:** Avoid `var` in modern JS/TS. Use `let` when the value needs to change, use `const` when it shouldn't.
