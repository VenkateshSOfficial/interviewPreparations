# Type Coercion in JavaScript

## What is Type Coercion?

**Type coercion** is the automatic conversion of one data type to another by JavaScript. It happens when an operation requires a specific type, but a value of a different type is provided. JavaScript will try to convert it to match the expected type.

### Simple Analogy
Imagine asking someone "How many apples do you have?" and they answer "5 apples". JavaScript doesn't understand "5 apples" as a number, so it tries to extract the number part (5) and use it.

---

## Types of Coercion

There are **two types** of coercion in JavaScript:

### 1. **Implicit Coercion** (Automatic)
JavaScript automatically converts types without you explicitly asking it to do so.

### 2. **Explicit Coercion** (Manual)
You manually convert types using functions or operators.

---

## Implicit Coercion (Automatic Conversion)

### When Does Implicit Coercion Happen?

Implicit coercion occurs in these common scenarios:

#### 1. **String Concatenation with + operator**

When one operand is a string, the other is converted to a string:

```javascript
// Number + String = String
5 + "5"                   // "55" (5 converted to string "5")
10 + " apples"            // "10 apples"
true + " is cool"         // "true is cool"
null + " value"           // "null value"
undefined + " here"       // "undefined here"

// Multiple operations
"The answer is " + 42     // "The answer is 42"
"5" + 3 + 2               // "532" (left to right: "5"+3="53", "53"+2="532")
3 + 2 + "5"               // "55" (left to right: 3+2=5, 5+"5"="55")
```

#### 2. **Arithmetic Operations (-, *, /, %)**

All operands are converted to numbers:

```javascript
// Subtraction
"5" - 2                   // 3 (string "5" → number 5, then 5-2=3)
"10" - "3"                // 7 (both converted to numbers)
true - 1                  // 0 (true → 1, then 1-1=0)
false - 5                 // -5 (false → 0, then 0-5=-5)

// Multiplication
"5" * 2                   // 10
"3" * "4"                 // 12
true * 5                  // 5 (true → 1, then 1*5=5)
false * 10                // 0 (false → 0, then 0*10=0)

// Division
"20" / "4"                // 5
"100" / 2                 // 50

// Modulo
"10" % 3                  // 1
5 % "2"                   // 1
```

#### 3. **Comparison Operators (<, >, <=, >=)**

Operands are converted to numbers for comparison:

```javascript
"10" > "2"                // false (string comparison: "10" < "2" alphabetically)
"10" > 2                  // true (both → numbers: 10 > 2)
true > false              // true (true → 1, false → 0, 1 > 0)
"5" >= 3                  // true (5 >= 3)
"abc" >= 1                // false ("abc" → NaN, NaN >= 1 is false)
```

**Important:** When comparing two strings, they are compared lexicographically (alphabetically), NOT as numbers:
```javascript
"10" > "2"                // false ("10" is less than "2" in alphabetical order)
10 > 2                    // true (numeric comparison)
"10" > "9"                // true ("10" is greater than "9" alphabetically)
```

#### 4. **Logical Operators (&&, ||, !)**

Values are converted to booleans:

```javascript
// AND operator
5 && "hello"              // "hello" (5 is truthy, returns next value)
0 && "hello"              // 0 (0 is falsy, stops here)
"" && "world"             // "" (empty string is falsy)
null && true              // null (null is falsy)

// OR operator
5 || "hello"              // 5 (5 is truthy, returns first truthy value)
0 || "hello"              // "hello" (0 is falsy, continues)
null || undefined || 10   // 10 (first two are falsy)
"" || "default"           // "default" (empty string is falsy)

// NOT operator
!5                        // false (5 is truthy, NOT true = false)
!0                        // true (0 is falsy, NOT false = true)
!"hello"                  // false (non-empty string is truthy)
!""                       // true (empty string is falsy)
!null                     // true (null is falsy)
```

#### 5. **Equality Operator (==)**

Both operands are converted to the same type before comparison:

```javascript
5 == "5"                  // true (string "5" → number 5)
1 == true                 // true (boolean true → number 1)
0 == false                // true (boolean false → number 0)
"" == false               // true (both → 0)
null == undefined         // true (special rule)
[] == false               // true ([].toString() → "", "" → 0)
```

#### 6. **if, while, and other conditionals**

The condition is converted to a boolean:

```javascript
if (5) {                  // true (5 is truthy)
  console.log("5 is truthy");
}

if ("") {                 // false (empty string is falsy)
  console.log("This won't run");
}

while (1) {               // Runs forever (1 is truthy)
  break;
}

const result = 10 ? "yes" : "no";  // "yes" (10 is truthy)
```

#### 7. **Template Literals**

Values are converted to strings:

```javascript
const age = 25;
const name = "John";
const isActive = true;

const message = `${name} is ${age} years old and active: ${isActive}`;
// "John is 25 years old and active: true"

const num = 42;
const result = `The number is ${num}`;
// "The number is 42"
```

---

## Explicit Coercion (Manual Conversion)

### Explicit Type Conversion Functions

#### 1. **String() - Convert to String**

```javascript
String(5)                 // "5"
String(true)              // "true"
String(false)             // "false"
String(null)              // "null"
String(undefined)         // "undefined"
String({})                // "[object Object]"
String([1, 2, 3])         // "1,2,3"

// Using toString()
(5).toString()            // "5"
true.toString()           // "true"
[1, 2].toString()         // "1,2"
```

#### 2. **Number() - Convert to Number**

```javascript
Number("5")               // 5
Number("5.5")             // 5.5
Number("0x10")            // 16 (hexadecimal)
Number(true)              // 1
Number(false)             // 0
Number(null)              // 0
Number(undefined)         // NaN
Number("")                // 0
Number("   ")             // 0 (whitespace)
Number("hello")           // NaN
Number([5])               // 5 (single element array)
Number([5, 10])           // NaN (multiple elements)

// Using unary + operator
+"5"                      // 5
+true                     // 1
+false                    // 0

// Using parseInt() - parses integer
parseInt("5")             // 5
parseInt("5.5")           // 5 (ignores decimal)
parseInt("10", 2)         // 2 (binary: 10 in binary = 2 in decimal)
parseInt("FF", 16)        // 255 (hexadecimal)

// Using parseFloat() - parses float
parseFloat("5.5")         // 5.5
parseFloat("3.14")        // 3.14
```

#### 3. **Boolean() - Convert to Boolean**

```javascript
Boolean(5)                // true (non-zero number is truthy)
Boolean(0)                // false
Boolean("")               // false (empty string)
Boolean("hello")          // true (non-empty string)
Boolean(null)             // false
Boolean(undefined)        // false
Boolean([])               // true (arrays are truthy)
Boolean({})               // true (objects are truthy)

// Using !! (double NOT)
!!5                       // true
!!0                       // false
!!"hello"                 // true
!!""                      // false
```

---

## Truthy and Falsy Values

Understanding truthy and falsy values is crucial for understanding implicit coercion in conditionals.

### Falsy Values (Only 6)
These values are always considered falsy in a boolean context:

```javascript
if (false)       // false
if (0)           // zero
if (-0)          // negative zero
if (0n)          // BigInt zero
if ("")          // empty string
if (null)        // null
if (undefined)   // undefined
if (NaN)         // NaN (Not a Number)

// Examples
if (0) { }                // False - won't execute
if ("") { }               // False - won't execute
if (null) { }             // False - won't execute
if (undefined) { }        // False - won't execute
```

### Truthy Values (Everything Else)
Any value NOT in the falsy list is truthy:

```javascript
if (1)                    // true
if (-1)                   // true (negative numbers)
if (0.1)                  // true (decimal numbers)
if ("0")                  // true (string "0" is truthy!)
if ("false")              // true (string "false" is truthy!)
if ([])                   // true (arrays are truthy)
if ({})                   // true (objects are truthy)
if (function() {})        // true (functions are truthy)

// Examples
if (1) { }                // True - will execute
if ("hello") { }          // True - will execute
if ([]) { }               // True - will execute (even empty array!)
if ("0") { }              // True - will execute (even string "0"!)
```

---

## Type Coercion Rules (The Algorithm)

### String Coercion
When a value is converted to a string:

```javascript
String(true)              // "true"
String(false)             // "false"
String(123)               // "123"
String(null)              // "null"
String(undefined)         // "undefined"
String([1, 2, 3])         // "1,2,3" (array.toString())
String({})                // "[object Object]"
```

### Number Coercion
When a value is converted to a number:

```javascript
// Strings
Number("123")             // 123
Number("123.45")          // 123.45
Number("")                // 0
Number("  ")              // 0 (whitespace ignored)
Number("0x10")            // 16 (hexadecimal)
Number("hello")           // NaN

// Booleans
Number(true)              // 1
Number(false)             // 0

// null and undefined
Number(null)              // 0 (converts to 0)
Number(undefined)         // NaN (no defined conversion)

// Arrays
Number([])                // 0
Number([5])               // 5
Number([1, 2])            // NaN (multiple values)

// Objects
Number({})                // NaN
```

### Boolean Coercion
When a value is used in a boolean context:

```javascript
// Falsy → false
Boolean(0)                // false
Boolean("")               // false
Boolean(null)             // false
Boolean(undefined)        // false
Boolean(NaN)              // false

// Truthy → true
Boolean(1)                // true
Boolean("hello")          // true
Boolean([])               // true
Boolean({})               // true
```

---

## Common Coercion Pitfalls

### Pitfall 1: String Concatenation with +

```javascript
// ❌ Unexpected result
console.log(5 + 10 + "20");  // "1520" (not "1520")
// Explanation: 5+10=15, then 15+"20"="1520"

// ✅ Make it clear
console.log((5 + 10) + "20"); // "1520" (explicit grouping)
```

### Pitfall 2: "0" is Truthy!

```javascript
// ❌ Common mistake
if ("0") {
  console.log("This runs!"); // This WILL run
}

// Explanation: "0" is a non-empty string, so it's truthy
// Use explicit check instead
if ("0" !== "") {
  console.log("More explicit");
}
```

### Pitfall 3: Empty Arrays are Truthy!

```javascript
// ❌ Misleading
if ([]) {
  console.log("This runs!"); // This WILL run
}

// ✅ Better check
if (Array.isArray([]) && [].length > 0) {
  console.log("Array has items");
}
```

### Pitfall 4: Loose Equality (==) Confusion

```javascript
// ❌ Type coercion surprises
console.log(5 == "5");        // true
console.log(0 == false);      // true
console.log("" == false);     // true
console.log(null == undefined); // true

// ✅ Use strict equality
console.log(5 === "5");       // false
console.log(0 === false);     // false
console.log("" === false);    // false
console.log(null === undefined); // false
```

### Pitfall 5: NaN Comparisons

```javascript
// ❌ NaN never equals anything
console.log(NaN == NaN);      // false
console.log(NaN === NaN);     // false

// ✅ Use isNaN() or Number.isNaN()
console.log(isNaN(NaN));          // true
console.log(Number.isNaN(NaN));   // true (better - no coercion)
```

### Pitfall 6: Array to Number Coercion

```javascript
// ❌ Surprising behavior
console.log([5] == 5);        // true (array → "5" → 5)
console.log([] == 0);         // true (array → "" → 0)
console.log(["5"] == "5");    // true

// ✅ Explicit conversion
console.log([5][0] === 5);    // true (access element)
```

---

## Real-World Examples

### Example 1: User Input Validation

```javascript
// ❌ Problem: User input is always a string
const userAge = "25"; // From form input
if (userAge >= 18) {  // Implicit coercion happens
  console.log("User is adult"); // This works but not clear
}

// ✅ Better: Explicit conversion
const age = Number(userAge);
if (age >= 18) {
  console.log("User is adult"); // Clear intent
}

// ✅ Alternative: parseInt
const ageFromInput = parseInt(userAge, 10);
if (ageFromInput >= 18) {
  console.log("User is adult");
}
```

### Example 2: Working with API Data

```javascript
const apiResponse = {
  id: "123",           // String from API
  active: "true",      // String boolean from API
  count: "42"          // String number from API
};

// ❌ Risky: Relying on implicit coercion
if (apiResponse.active) {  // "true" is truthy (will always pass)
  console.log("Active");   // Wrong assumption
}

// ✅ Better: Explicit conversion
const isActive = apiResponse.active === "true";
const id = Number(apiResponse.id);
const count = parseInt(apiResponse.count, 10);

if (isActive) {
  console.log("Explicitly true");
}
```

### Example 3: Fallback Values

```javascript
// Implicit coercion in fallback patterns
const user = {
  name: "John",
  age: 0  // Falsy value
};

// ❌ Problem: 0 is falsy
const age = user.age || 18;  // Returns 18 (wrong!)

// ✅ Better: Explicit null check
const age2 = user.age !== undefined ? user.age : 18;

// ✅ Modern: Nullish coalescing operator
const age3 = user.age ?? 18;  // Returns 0 (correct!)
```

---

## Best Practices

### 1. **Always use === instead of ==**

```javascript
// ❌ Avoid
if (value == 5) { }

// ✅ Use
if (value === 5) { }
```

### 2. **Be Explicit with Type Conversions**

```javascript
// ❌ Implicit
const total = price + quantity + discount;

// ✅ Explicit
const total = Number(price) + Number(quantity) + Number(discount);
```

### 3. **Avoid Relying on Implicit Coercion in Conditionals**

```javascript
// ❌ Unclear
if (data) { }

// ✅ Explicit
if (data !== null && data !== undefined) { }
if (typeof data === 'string' && data.length > 0) { }
```

### 4. **Use Type Checking Functions**

```javascript
// ✅ Good
if (typeof value === 'string') { }
if (Array.isArray(value)) { }
if (Number.isNaN(value)) { }
if (value instanceof Object) { }
```

### 5. **Use Nullish Coalescing and Optional Chaining**

```javascript
// ✅ Modern JavaScript
const value = user?.age ?? 18;  // Safe navigation + default
```

---

## Summary Table

| Value | String | Number | Boolean |
|-------|--------|--------|---------|
| undefined | "undefined" | NaN | false |
| null | "null" | 0 | false |
| true | "true" | 1 | true |
| false | "false" | 0 | false |
| "" (empty) | "" | 0 | false |
| "0" | "0" | 0 | true |
| "hello" | "hello" | NaN | true |
| 0 | "0" | 0 | false |
| 1 | "1" | 1 | true |
| [] | "" | 0 | true |
| [5] | "5" | 5 | true |
| {} | "[object Object]" | NaN | true |

---

## Quick Reference

### Implicit Coercion Happens With:
- **String concatenation**: `+` when one operand is a string
- **Arithmetic operations**: `-`, `*`, `/`, `%` convert to numbers
- **Comparisons**: `<`, `>`, `<=`, `>=` convert to numbers
- **Loose equality**: `==` and `!=` convert types
- **Conditionals**: `if`, `while`, ternary convert to boolean
- **Logical operators**: `&&`, `||`, `!` convert to boolean

### Type Conversion Functions:
- **String()**: Explicit string conversion
- **Number()**: Explicit number conversion
- **Boolean()**: Explicit boolean conversion
- **parseInt()**: Parse string to integer
- **parseFloat()**: Parse string to float

---

## Key Takeaways

1. **Type coercion is automatic** - JavaScript converts types behind the scenes
2. **Implicit coercion can be dangerous** - It can lead to unexpected results
3. **Always use `===`** - Avoid loose equality and its type coercion
4. **Be explicit** - Use `Number()`, `String()`, `Boolean()` for clarity
5. **Understand truthy/falsy** - Only 6 falsy values: false, 0, "", null, undefined, NaN
6. **Test edge cases** - Type coercion has many surprising behaviors
7. **Use modern operators** - Nullish coalescing (`??`) and optional chaining (`?.`) are safer
