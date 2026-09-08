# Spread Operator & Destructuring in JavaScript

## Part 1: The Spread Operator (...)

### What is the Spread Operator?

The **spread operator** (`...`) allows an iterable (like an array or string) to be expanded in places where zero or more elements are expected.

**Simple definition:** The spread operator takes all elements from something and spreads them out individually.

---

## Spread Operator with Arrays

### 1. Copying an Array

**Before (without spread):**
```javascript
const original = [1, 2, 3];
const copy = original; // This is NOT a copy!

copy[0] = 999;
console.log(original[0]); // 999 (original was modified!)
```

**After (with spread):**
```javascript
const original = [1, 2, 3];
const copy = [...original]; // Real copy!

copy[0] = 999;
console.log(original[0]); // 1 (original is safe!)
```

---

### 2. Merging Arrays

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

const merged = [...arr1, ...arr2];
console.log(merged); // [1, 2, 3, 4, 5, 6]

// You can add elements too
const merged2 = [...arr1, 999, ...arr2];
console.log(merged2); // [1, 2, 3, 999, 4, 5, 6]
```

---

### 3. Adding Elements to Array

```javascript
const numbers = [1, 2, 3];

// Add at the beginning
const withStart = [0, ...numbers];
console.log(withStart); // [0, 1, 2, 3]

// Add at the end
const withEnd = [...numbers, 4];
console.log(withEnd); // [1, 2, 3, 4]

// Add in the middle
const withMiddle = [1, ...numbers, 5];
console.log(withMiddle); // [1, 1, 2, 3, 5]
```

---

## Spread Operator with Objects

### 1. Copying an Object

```javascript
const person = {
  name: "John",
  age: 30
};

const copy = { ...person }; // Shallow copy

copy.name = "Jane";
console.log(person.name); // "John" (original unchanged)
```

---

### 2. Merging Objects

```javascript
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };

const merged = { ...obj1, ...obj2 };
console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }
```

---

### 3. Overriding Properties

```javascript
const user = {
  name: "John",
  age: 30,
  city: "NYC"
};

// Update specific properties
const updatedUser = {
  ...user,
  age: 31,
  city: "LA"
};

console.log(updatedUser);
// { name: "John", age: 31, city: "LA" }
console.log(user); // Original unchanged
```

---

### 4. Adding New Properties

```javascript
const user = { name: "John", age: 30 };
const email = "john@email.com";

const userWithEmail = {
  ...user,
  email: email
};

console.log(userWithEmail);
// { name: "John", age: 30, email: "john@email.com" }
```

---

## Spread Operator with Function Arguments

### 1. Passing Array as Arguments

```javascript
const numbers = [1, 5, 3, 9, 2];

// Without spread
Math.max(numbers); // NaN (doesn't work!)

// With spread
Math.max(...numbers); // 9
console.log(...numbers); // 1 5 3 9 2
```

**What happens:**
```javascript
// This:
Math.max(...[1, 5, 3, 9, 2])

// Becomes:
Math.max(1, 5, 3, 9, 2)
```

---

### 2. Function with Multiple Arguments

```javascript
function sum(a, b, c) {
  return a + b + c;
}

const numbers = [10, 20, 30];
console.log(sum(...numbers)); // 60

// Same as:
console.log(sum(10, 20, 30)); // 60
```

---

## Spread Operator with Strings

```javascript
const str = "Hello";
const chars = [...str];
console.log(chars); // ["H", "e", "l", "l", "o"]

// Useful for reversing
const reversed = [...str].reverse().join("");
console.log(reversed); // "olleH"
```

---

## Real-World Example: Component Props (React)

```javascript
// React-like example
const defaultProps = {
  theme: "dark",
  size: "medium",
  disabled: false
};

const userProps = {
  theme: "light",
  customClass: "btn-primary"
};

// Merge props (userProps override defaultProps)
const finalProps = {
  ...defaultProps,
  ...userProps
};

console.log(finalProps);
// { theme: "light", size: "medium", disabled: false, customClass: "btn-primary" }
```

---

---

## Part 2: Destructuring

### What is Destructuring?

**Destructuring** is a convenient way to extract values from objects or arrays and assign them to variables.

**Simple definition:** Destructuring lets you "unpack" values from arrays or objects into separate variables in one line.

---

## Array Destructuring

### 1. Basic Array Destructuring

**Before (without destructuring):**
```javascript
const colors = ["red", "green", "blue"];
const first = colors[0];
const second = colors[1];
const third = colors[2];
```

**After (with destructuring):**
```javascript
const colors = ["red", "green", "blue"];
const [first, second, third] = colors;

console.log(first);  // "red"
console.log(second); // "green"
console.log(third);  // "blue"
```

---

### 2. Skipping Elements

```javascript
const [first, , third] = ["red", "green", "blue"];

console.log(first);  // "red"
console.log(third);  // "blue"
// "green" is skipped!
```

---

### 3. Rest Elements (Remaining Values)

```javascript
const [first, ...rest] = [1, 2, 3, 4, 5];

console.log(first); // 1
console.log(rest);  // [2, 3, 4, 5]

// Real-world example
const [head, ...tail] = [10, 20, 30, 40];
console.log(head); // 10
console.log(tail); // [20, 30, 40]
```

---

### 4. Default Values

```javascript
const [a, b, c] = [1, 2];

console.log(a); // 1
console.log(b); // 2
console.log(c); // undefined (no value!)

// Fix with default values
const [x, y, z = 999] = [1, 2];

console.log(x); // 1
console.log(y); // 2
console.log(z); // 999 (default value used)
```

---

### 5. Swapping Variables

```javascript
let a = 10;
let b = 20;

[a, b] = [b, a]; // Swap!

console.log(a); // 20
console.log(b); // 10
```

---

## Object Destructuring

### 1. Basic Object Destructuring

**Before:**
```javascript
const user = {
  name: "John",
  age: 30,
  email: "john@email.com"
};

const name = user.name;
const age = user.age;
```

**After:**
```javascript
const user = {
  name: "John",
  age: 30,
  email: "john@email.com"
};

const { name, age, email } = user;

console.log(name);  // "John"
console.log(age);   // 30
console.log(email); // "john@email.com"
```

---

### 2. Renaming Variables

```javascript
const user = { name: "John", age: 30 };

// Rename during destructuring
const { name: fullName, age: userAge } = user;

console.log(fullName); // "John"
console.log(userAge);  // 30
// Can't use 'name' or 'age' anymore!
```

---

### 3. Default Values for Objects

```javascript
const user = { name: "John" };

const { name, email = "no-email@example.com" } = user;

console.log(name);  // "John"
console.log(email); // "no-email@example.com"
```

---

### 4. Nested Destructuring

```javascript
const user = {
  name: "John",
  address: {
    city: "NYC",
    country: "USA"
  }
};

// Destructure nested object
const { name, address: { city, country } } = user;

console.log(name);    // "John"
console.log(city);    // "NYC"
console.log(country); // "USA"
```

---

### 5. Rest Properties (Remaining Object Properties)

```javascript
const user = {
  name: "John",
  age: 30,
  email: "john@email.com",
  phone: "555-1234"
};

const { name, age, ...otherInfo } = user;

console.log(name);      // "John"
console.log(age);       // 30
console.log(otherInfo); // { email: "john@email.com", phone: "555-1234" }
```

---

## Function Parameter Destructuring

### 1. Array Destructuring in Function Parameters

```javascript
// Without destructuring
function printCoords(point) {
  console.log(`X: ${point[0]}, Y: ${point[1]}`);
}

// With destructuring
function printCoords([x, y]) {
  console.log(`X: ${x}, Y: ${y}`);
}

printCoords([10, 20]); // X: 10, Y: 20
```

---

### 2. Object Destructuring in Function Parameters

```javascript
// Without destructuring
function printUser(user) {
  console.log(`${user.name} is ${user.age} years old`);
}

// With destructuring
function printUser({ name, age }) {
  console.log(`${name} is ${age} years old`);
}

const user = { name: "John", age: 30, email: "john@email.com" };
printUser(user); // John is 30 years old
```

---

### 3. Default Values in Function Parameters

```javascript
function greet({ name = "Guest", greeting = "Hello" } = {}) {
  console.log(`${greeting}, ${name}!`);
}

greet();                           // Hello, Guest!
greet({ name: "John" });           // Hello, John!
greet({ name: "John", greeting: "Hi" }); // Hi, John!
```

---

## Combining Spread & Destructuring

### 1. Rest with Spread

```javascript
const [a, ...rest] = [1, 2, 3, 4];
const newArray = [0, ...rest];

console.log(newArray); // [0, 2, 3, 4]
```

---

### 2. Copy and Destructure

```javascript
const original = [1, 2, 3];
const [first, ...copy] = [...original];

console.log(first); // 1
console.log(copy);  // [2, 3]
```

---

### 3. Function that Returns Spread Data

```javascript
function getData() {
  return [10, 20, 30, 40, 50];
}

const [first, second, ...others] = getData();

console.log(first);  // 10
console.log(second); // 20
console.log(others); // [30, 40, 50]
```

---

## Real-World Examples

### Example 1: React Component Props

```javascript
// Without destructuring
function UserCard(props) {
  return <div>{props.name} - {props.age}</div>;
}

// With destructuring (cleaner!)
function UserCard({ name, age, email = "N/A" }) {
  return (
    <div>
      <p>{name}</p>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}

const props = { name: "John", age: 30 };
<UserCard {...props} />;
```

---

### Example 2: API Response Handling

```javascript
const apiResponse = {
  data: {
    user: {
      id: 1,
      name: "John",
      email: "john@email.com"
    },
    posts: [
      { id: 1, title: "First Post" },
      { id: 2, title: "Second Post" }
    ]
  },
  status: 200
};

// Extract nested data
const { data: { user: { name, email }, posts } } = apiResponse;

console.log(name);   // "John"
console.log(email);  // "john@email.com"
console.log(posts);  // Array of posts
```

---

### Example 3: Default Configuration

```javascript
function createServer(options = {}) {
  const {
    port = 3000,
    host = "localhost",
    ssl = false,
    timeout = 5000
  } = options;

  console.log(`Server running on ${host}:${port}`);
  console.log(`SSL: ${ssl}, Timeout: ${timeout}ms`);
}

createServer();
// Server running on localhost:3000
// SSL: false, Timeout: 5000ms

createServer({ port: 8080, ssl: true });
// Server running on localhost:8080
// SSL: true, Timeout: 5000ms
```

---

### Example 4: Filtering and Reorganizing Data

```javascript
const users = [
  { id: 1, name: "John", role: "admin" },
  { id: 2, name: "Jane", role: "user" },
  { id: 3, name: "Bob", role: "user" }
];

// Extract names and filter
const names = users.map(({ name }) => name);
console.log(names); // ["John", "Jane", "Bob"]

// Create new objects with selected properties
const userIds = users.map(({ id, name, ...rest }) => ({
  id,
  name,
  // ...rest contains role
}));
```

---

## Common Gotchas

### Gotcha 1: Destructuring with No Match

```javascript
const [a, b, c] = [1, 2];
console.log(c); // undefined (no error!)
```

**Fix with default value:**
```javascript
const [a, b, c = 0] = [1, 2];
console.log(c); // 0
```

---

### Gotcha 2: Shallow Copy with Spread

```javascript
const user = {
  name: "John",
  address: { city: "NYC" }
};

const copy = { ...user };
copy.address.city = "LA";

console.log(user.address.city); // "LA" (nested objects are still referenced!)
```

**Deep copy would need additional work:**
```javascript
const deepCopy = {
  ...user,
  address: { ...user.address }
};
```

---

### Gotcha 3: Order Matters in Object Destructuring

```javascript
const { a = 1, b = 2, c = 3 } = { b: 20 };
console.log(a); // 1 (default used)
console.log(b); // 20 (from object)
console.log(c); // 3 (default used)
```

---

## Interview Questions

**Q1: What will this output?**
```javascript
const arr = [1, 2, 3, 4, 5];
const [first, ...middle, last] = arr;
```

**A:** SyntaxError! Only the last element can use rest operator. Correct:
```javascript
const [first, ...middle] = arr;
// first = 1, middle = [2, 3, 4, 5]
```

---

**Q2: What's the output?**
```javascript
const { a, b = 10 } = { a: 5 };
console.log(a, b);
```

**A:** `5, 10` (b uses default value since it's not in the object)

---

**Q3: Explain the difference:**
```javascript
const obj = { x: 1, y: 2 };
const copy1 = { ...obj };
const copy2 = obj;

copy1.x = 999;
copy2.y = 999;

console.log(obj);
```

**A:** `{ x: 1, y: 999 }` (spread creates new object, but copy2 is a reference to original)

---

**Q4: What will this log?**
```javascript
function print([a, b] = []) {
  console.log(a, b);
}

print();
print([1, 2]);
print([1]);
```

**A:**
```
undefined undefined
1 2
1 undefined
```

---

## Key Takeaways

### Spread Operator (`...`)
✅ Expands elements from arrays/objects  
✅ Used for copying, merging, passing arguments  
✅ Creates shallow copies only  
✅ Works left-to-right (later values override)  

### Destructuring
✅ Extracts values from arrays/objects into variables  
✅ Works with arrays `[a, b, c]` and objects `{x, y}`  
✅ Supports default values and renaming  
✅ Rest operator `...` captures remaining elements/properties  
✅ Makes code more readable and concise  

### When to Use
- **Spread**: Copying, merging, passing multiple arguments  
- **Destructuring**: Extracting values, function parameters, cleaner code  

Both features make modern JavaScript more elegant and functional!

---

## Quick Reference

```javascript
// SPREAD
[...array]                    // Copy array
[...arr1, ...arr2]           // Merge arrays
{ ...object }                // Copy object
{ ...obj1, ...obj2 }         // Merge objects
Math.max(...numbers)         // Pass array as arguments
function(...args)            // Rest parameters

// DESTRUCTURING
const [a, b] = array         // Extract array elements
const { x, y } = object      // Extract object properties
const [a, ...rest] = array   // Rest in array
const { x, ...rest } = obj   // Rest in object
const { x: newX } = obj      // Rename while destructuring
function({ name, age }) {}   // Destructure in parameters
```

---

## Summary

The **spread operator** and **destructuring** are powerful tools that make JavaScript code cleaner, more readable, and easier to work with. They're essential for modern JavaScript development and will definitely come up in interviews!

Master these and you'll write much more elegant code! 💪
