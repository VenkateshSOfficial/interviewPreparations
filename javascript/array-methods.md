# Array Methods in JavaScript

## Introduction

Arrays are one of the most important data structures in JavaScript. Array methods make it easy to manipulate, iterate through, and transform arrays. This guide covers all the essential array methods with examples.

---

## Table of Contents

1. [Mutating Methods](#mutating-methods) - Methods that modify the original array
2. [Non-Mutating Methods](#non-mutating-methods) - Methods that return new arrays
3. [Iteration Methods](#iteration-methods) - Methods for looping through arrays
4. [Searching Methods](#searching-methods) - Methods to find elements
5. [Transformation Methods](#transformation-methods) - Methods to transform arrays
6. [Aggregation Methods](#aggregation-methods) - Methods to combine/reduce arrays
7. [Type Conversion](#type-conversion) - Methods to convert arrays to strings

---

## Mutating Methods

These methods **modify the original array** and return the modified array or affected length.

### 1. push() - Add to End

```javascript
const arr = [1, 2, 3];
const length = arr.push(4, 5);

console.log(arr);     // [1, 2, 3, 4, 5]
console.log(length);  // 5 (returns new length)
```

**Returns:** Number (new length of the array)
**Use case:** Adding new elements to the end of an array

### 2. pop() - Remove from End

```javascript
const arr = [1, 2, 3, 4, 5];
const removed = arr.pop();

console.log(arr);      // [1, 2, 3, 4]
console.log(removed);  // 5 (returns removed element)
```

**Returns:** Element removed, or undefined if array is empty
**Use case:** Removing the last element (like a stack)

### 3. unshift() - Add to Beginning

```javascript
const arr = [2, 3, 4];
const length = arr.unshift(0, 1);

console.log(arr);     // [0, 1, 2, 3, 4]
console.log(length);  // 5 (returns new length)
```

**Returns:** Number (new length of the array)
**Use case:** Adding elements to the start of an array

### 4. shift() - Remove from Beginning

```javascript
const arr = [1, 2, 3, 4, 5];
const removed = arr.shift();

console.log(arr);      // [2, 3, 4, 5]
console.log(removed);  // 1 (returns removed element)
```

**Returns:** Element removed, or undefined if array is empty
**Use case:** Removing the first element (like a queue)

### 5. splice() - Add/Remove at Any Position

```javascript
const arr = [1, 2, 3, 4, 5];

// Remove 2 elements starting at index 2
const removed = arr.splice(2, 2);
console.log(arr);     // [1, 2, 5]
console.log(removed); // [3, 4]

// Remove and add simultaneously
const arr2 = [1, 2, 3, 4, 5];
arr2.splice(2, 2, 'a', 'b', 'c');
console.log(arr2);    // [1, 2, 'a', 'b', 'c', 5]

// Add without removing
const arr3 = [1, 2, 3];
arr3.splice(1, 0, 'x', 'y');
console.log(arr3);    // [1, 'x', 'y', 2, 3]
```

**Returns:** Array of removed elements
**Use case:** Complex array manipulation (add/remove at specific index)

### 6. reverse() - Reverse Array

```javascript
const arr = [1, 2, 3, 4, 5];
arr.reverse();

console.log(arr); // [5, 4, 3, 2, 1]
```

**Returns:** The reversed array (same array reference)
**Use case:** Reversing the order of elements

### 7. sort() - Sort Array

```javascript
// Numbers (default - converts to strings!)
const arr1 = [3, 1, 4, 1, 5];
arr1.sort();
console.log(arr1); // [1, 1, 3, 4, 5]

// Strings
const arr2 = ['banana', 'apple', 'cherry'];
arr2.sort();
console.log(arr2); // ['apple', 'banana', 'cherry']

// Custom sort (compare function)
const arr3 = [3, 1, 4, 1, 5];
arr3.sort((a, b) => a - b); // Ascending
console.log(arr3); // [1, 1, 3, 4, 5]

const arr4 = [3, 1, 4, 1, 5];
arr4.sort((a, b) => b - a); // Descending
console.log(arr4); // [5, 4, 3, 1, 1]

// Sort objects by property
const users = [
  { name: 'John', age: 30 },
  { name: 'Jane', age: 25 },
  { name: 'Bob', age: 35 }
];

users.sort((a, b) => a.age - b.age);
console.log(users);
// [{ name: 'Jane', age: 25 }, { name: 'John', age: 30 }, { name: 'Bob', age: 35 }]
```

**Returns:** The sorted array (same array reference)
**Use case:** Sorting arrays in various orders

### 8. fill() - Fill Array with Value

```javascript
const arr = [1, 2, 3, 4, 5];
arr.fill(0, 2, 4); // Fill with 0, from index 2 to 4 (exclusive)

console.log(arr); // [1, 2, 0, 0, 5]

// Fill entire array
const arr2 = [1, 2, 3, 4, 5];
arr2.fill(9);
console.log(arr2); // [9, 9, 9, 9, 9]
```

**Returns:** The modified array (same array reference)
**Use case:** Initializing or resetting array values

### 9. copyWithin() - Copy Elements Within Array

```javascript
const arr = [1, 2, 3, 4, 5];
arr.copyWithin(0, 3, 5); // Copy elements from index 3-5 to start at index 0

console.log(arr); // [4, 5, 3, 4, 5]
```

**Returns:** The modified array (same array reference)
**Use case:** Copying array sections to different positions (rarely used)

---

## Non-Mutating Methods

These methods **do NOT modify** the original array and return a new array or value.

### 1. slice() - Extract Portion of Array

```javascript
const arr = [1, 2, 3, 4, 5];

const sliced1 = arr.slice(1, 4);      // Elements from index 1 to 4 (exclusive)
console.log(sliced1);                 // [2, 3, 4]

const sliced2 = arr.slice(2);         // Elements from index 2 to end
console.log(sliced2);                 // [3, 4, 5]

const sliced3 = arr.slice(-2);        // Last 2 elements
console.log(sliced3);                 // [4, 5]

const copy = arr.slice();             // Shallow copy of entire array
console.log(copy);                    // [1, 2, 3, 4, 5]

console.log(arr);                     // [1, 2, 3, 4, 5] (unchanged)
```

**Returns:** New array with selected elements
**Use case:** Getting a portion of an array without modifying original

### 2. concat() - Combine Arrays

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const combined = arr1.concat(arr2, arr3, 7, 8);
console.log(combined); // [1, 2, 3, 4, 5, 6, 7, 8]

console.log(arr1);     // [1, 2] (unchanged)
```

**Returns:** New array with combined elements
**Use case:** Combining multiple arrays without modifying originals

### 3. join() - Convert Array to String

```javascript
const arr = ['apple', 'banana', 'cherry'];

const str1 = arr.join();       // Default separator: ','
console.log(str1);             // 'apple,banana,cherry'

const str2 = arr.join(' - ');  // Custom separator
console.log(str2);             // 'apple - banana - cherry'

const str3 = arr.join('');     // No separator
console.log(str3);             // 'applebananacharry'

console.log(arr);              // ['apple', 'banana', 'cherry'] (unchanged)
```

**Returns:** String
**Use case:** Converting array to formatted string

### 4. flat() - Flatten Nested Arrays

```javascript
const arr1 = [1, 2, [3, 4, [5, 6]]];

const flat1 = arr1.flat();     // Flatten 1 level
console.log(flat1);            // [1, 2, 3, 4, [5, 6]]

const flat2 = arr1.flat(2);    // Flatten 2 levels
console.log(flat2);            // [1, 2, 3, 4, 5, 6]

const flat3 = arr1.flat(Infinity);  // Flatten all levels
console.log(flat3);            // [1, 2, 3, 4, 5, 6]

console.log(arr1);             // [1, 2, [3, 4, [5, 6]]] (unchanged)
```

**Returns:** New flattened array
**Use case:** Flattening nested arrays to single level

### 5. flatMap() - Map and Flatten

```javascript
const arr = [1, 2, 3, 4];

// Map then flatten in one step
const result = arr.flatMap(x => [x, x * 2]);
console.log(result); // [1, 2, 2, 4, 3, 6, 4, 8]

// Equivalent to:
const result2 = arr.map(x => [x, x * 2]).flat();
console.log(result2); // [1, 2, 2, 4, 3, 6, 4, 8]

// With strings
const sentences = ['Hello world', 'How are you'];
const words = sentences.flatMap(sentence => sentence.split(' '));
console.log(words); // ['Hello', 'world', 'How', 'are', 'you']
```

**Returns:** New flattened and mapped array
**Use case:** Combining map and flatten operations

---

## Iteration Methods

These methods execute a function for each array element.

### 1. forEach() - Execute for Each Element

```javascript
const arr = [1, 2, 3];

arr.forEach((element, index, array) => {
  console.log(`${index}: ${element}`);
});
// Output:
// 0: 1
// 1: 2
// 2: 3

// Practical example
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];

users.forEach(user => {
  console.log(`User ${user.id}: ${user.name}`);
});
```

**Returns:** undefined
**Use case:** Performing side effects (like logging, updating DOM)

### 2. map() - Transform Each Element

```javascript
const arr = [1, 2, 3, 4];

const doubled = arr.map(x => x * 2);
console.log(doubled); // [2, 4, 6, 8]

// With objects
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];

const usernames = users.map(user => user.name);
console.log(usernames); // ['John', 'Jane']

// String conversion
const numbers = [1, 2, 3, 4];
const strings = numbers.map(String);
console.log(strings); // ['1', '2', '3', '4']
```

**Returns:** New array with transformed elements
**Use case:** Transforming array elements

### 3. filter() - Keep Elements Matching Condition

```javascript
const arr = [1, 2, 3, 4, 5, 6];

const evens = arr.filter(x => x % 2 === 0);
console.log(evens); // [2, 4, 6]

// With objects
const users = [
  { id: 1, name: 'John', age: 30 },
  { id: 2, name: 'Jane', age: 25 },
  { id: 3, name: 'Bob', age: 35 }
];

const adults = users.filter(user => user.age >= 30);
console.log(adults);
// [
//   { id: 1, name: 'John', age: 30 },
//   { id: 3, name: 'Bob', age: 35 }
// ]

// Removing falsy values
const mixed = [0, 1, false, 2, '', 3, null, undefined];
const cleaned = mixed.filter(Boolean);
console.log(cleaned); // [1, 2, 3]
```

**Returns:** New array with filtered elements
**Use case:** Selecting elements that meet criteria

### 4. reduce() - Combine Elements into Single Value

```javascript
const arr = [1, 2, 3, 4];

// Sum
const sum = arr.reduce((acc, current) => acc + current, 0);
console.log(sum); // 10

// Product
const product = arr.reduce((acc, current) => acc * current, 1);
console.log(product); // 24

// Count occurrences
const words = ['apple', 'banana', 'apple', 'cherry', 'banana', 'apple'];
const count = words.reduce((acc, word) => {
  acc[word] = (acc[word] || 0) + 1;
  return acc;
}, {});
console.log(count); // { apple: 3, banana: 2, cherry: 1 }

// Flatten array
const nested = [[1, 2], [3, 4], [5, 6]];
const flat = nested.reduce((acc, arr) => acc.concat(arr), []);
console.log(flat); // [1, 2, 3, 4, 5, 6]

// Transform array of objects to object
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];
const userMap = users.reduce((acc, user) => {
  acc[user.id] = user.name;
  return acc;
}, {});
console.log(userMap); // { '1': 'John', '2': 'Jane' }
```

**Returns:** Single aggregated value (any type)
**Use case:** Aggregating array data into single value

### 5. reduceRight() - Reduce from Right to Left

```javascript
const arr = [1, 2, 3, 4];

const result = arr.reduceRight((acc, current) => {
  return acc + current;
}, 0);
console.log(result); // 10 (same result)

// Practical: Reverse without reverse()
const reversed = arr.reduceRight((acc, current) => {
  acc.push(current);
  return acc;
}, []);
console.log(reversed); // [4, 3, 2, 1]
```

**Returns:** Single aggregated value (any type)
**Use case:** Processing array from right to left

### 6. every() - Check if All Match Condition

```javascript
const arr = [2, 4, 6, 8];

const allEven = arr.every(x => x % 2 === 0);
console.log(allEven); // true

// With objects
const users = [
  { id: 1, age: 30 },
  { id: 2, age: 25 },
  { id: 3, age: 35 }
];

const allAdults = users.every(user => user.age >= 18);
console.log(allAdults); // true

const allOver30 = users.every(user => user.age > 30);
console.log(allOver30); // false
```

**Returns:** Boolean (true if all elements pass test)
**Use case:** Validating that all elements meet criteria

### 7. some() - Check if Any Match Condition

```javascript
const arr = [1, 3, 5, 7, 8];

const hasEven = arr.some(x => x % 2 === 0);
console.log(hasEven); // true (8 is even)

// With objects
const users = [
  { id: 1, isAdmin: false },
  { id: 2, isAdmin: false },
  { id: 3, isAdmin: true }
];

const hasAdmin = users.some(user => user.isAdmin);
console.log(hasAdmin); // true
```

**Returns:** Boolean (true if any element passes test)
**Use case:** Checking if any element meets criteria

---

## Searching Methods

These methods find specific elements in the array.

### 1. indexOf() - Find Index of First Match

```javascript
const arr = [10, 20, 30, 20, 40];

const index1 = arr.indexOf(20);      // 1 (first occurrence)
const index2 = arr.indexOf(40);      // 4
const index3 = arr.indexOf(99);      // -1 (not found)

// Search from specific index
const index4 = arr.indexOf(20, 2);   // 3 (search from index 2)

// With strings
const colors = ['red', 'green', 'blue', 'green'];
const greenIndex = colors.indexOf('green'); // 1

// Check if element exists
if (arr.includes(30)) {
  console.log('Found!');
}
```

**Returns:** Number (index of element, or -1 if not found)
**Use case:** Finding position of element

### 2. lastIndexOf() - Find Index of Last Match

```javascript
const arr = [10, 20, 30, 20, 40];

const lastIndex = arr.lastIndexOf(20);  // 3 (last occurrence)
console.log(lastIndex); // 3
```

**Returns:** Number (index of last occurrence, or -1 if not found)
**Use case:** Finding last occurrence of element

### 3. includes() - Check if Element Exists

```javascript
const arr = [1, 2, 3, 4, 5];

console.log(arr.includes(3));      // true
console.log(arr.includes(10));     // false

// With strings
const fruits = ['apple', 'banana', 'cherry'];
console.log(fruits.includes('apple')); // true

// With NaN (special case where indexOf fails)
const arr2 = [1, 2, NaN, 4];
console.log(arr2.indexOf(NaN));    // -1 (doesn't work)
console.log(arr2.includes(NaN));   // true (works!)

// Search from specific index
console.log(arr.includes(2, 2));   // false (search from index 2)
```

**Returns:** Boolean (true if element found)
**Use case:** Checking element existence

### 4. find() - Find First Element Matching Condition

```javascript
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 3, name: 'Bob' }
];

const user = users.find(u => u.id === 2);
console.log(user); // { id: 2, name: 'Jane' }

const notFound = users.find(u => u.id === 99);
console.log(notFound); // undefined

// Simple array
const arr = [5, 12, 8, 130, 44];
const found = arr.find(element => element > 10);
console.log(found); // 12
```

**Returns:** First element matching condition, or undefined if none found
**Use case:** Finding first element matching criteria

### 5. findIndex() - Find Index of Element Matching Condition

```javascript
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 3, name: 'Bob' }
];

const index = users.findIndex(u => u.id === 2);
console.log(index); // 1

const notFound = users.findIndex(u => u.id === 99);
console.log(notFound); // -1

// Simple array
const arr = [5, 12, 8, 130, 44];
const index2 = arr.findIndex(element => element > 10);
console.log(index2); // 1 (12 is at index 1)
```

**Returns:** Number (index of element matching condition, or -1 if not found)
**Use case:** Finding index of element matching criteria

### 6. findLast() - Find Last Element Matching Condition (ES2023)

```javascript
const arr = [5, 12, 8, 130, 44, 15];

const found = arr.findLast(element => element > 10);
console.log(found); // 15 (last element > 10)
```

**Returns:** Last element matching condition, or undefined if none found
**Use case:** Finding last element matching criteria

### 7. findLastIndex() - Find Index of Last Element Matching (ES2023)

```javascript
const arr = [5, 12, 8, 130, 44, 15];

const index = arr.findLastIndex(element => element > 10);
console.log(index); // 5 (index of 15)
```

**Returns:** Number (index of last element matching condition, or -1 if not found)
**Use case:** Finding index of last matching element

---

## Transformation Methods

### 1. Array.from() - Convert to Array

```javascript
// From string
const str = 'hello';
const arr1 = Array.from(str);
console.log(arr1); // ['h', 'e', 'l', 'l', 'o']

// From Set
const set = new Set([1, 2, 3, 3, 2, 1]);
const arr2 = Array.from(set);
console.log(arr2); // [1, 2, 3]

// From Map
const map = new Map([['a', 1], ['b', 2]]);
const arr3 = Array.from(map);
console.log(arr3); // [['a', 1], ['b', 2]]

// With mapping function
const arr4 = Array.from([1, 2, 3], x => x * 2);
console.log(arr4); // [2, 4, 6]
```

**Returns:** New Array from iterable
**Use case:** Converting iterables to arrays

### 2. Array.isArray() - Check if Array

```javascript
console.log(Array.isArray([1, 2, 3]));     // true
console.log(Array.isArray('string'));      // false
console.log(Array.isArray({ length: 0 })); // false
console.log(Array.isArray(null));          // false
```

**Returns:** Boolean (true if value is an array)
**Use case:** Type checking for arrays

### 3. Array.of() - Create Array from Arguments

```javascript
const arr1 = Array.of(1, 2, 3);
console.log(arr1); // [1, 2, 3]

const arr2 = Array.of(5);
console.log(arr2); // [5] (not array with 5 empty slots)

// Difference from Array constructor
console.log(new Array(5));      // [empty × 5] (5 empty slots)
console.log(Array.of(5));       // [5] (array with single element)
```

**Returns:** New Array containing all arguments
**Use case:** Creating arrays reliably

---

## Aggregation Methods

### 1. at() - Access Element by Index (Including Negative)

```javascript
const arr = ['a', 'b', 'c', 'd'];

console.log(arr.at(0));    // 'a' (first)
console.log(arr.at(-1));   // 'd' (last)
console.log(arr.at(-2));   // 'c' (second to last)

// Equivalent to:
console.log(arr[arr.length - 1]); // 'd'
```

**Returns:** Element at the specified index
**Use case:** Accessing elements with negative indices

### 2. length Property

```javascript
const arr = [1, 2, 3, 4, 5];
console.log(arr.length); // 5

// Setting length
arr.length = 3;
console.log(arr); // [1, 2, 3] (truncated)

arr.length = 0;
console.log(arr); // [] (cleared)
```

**Returns:** Number (length of array)
**Use case:** Getting/setting array size

---

## Type Conversion

### 1. toString() - Convert Array to String

```javascript
const arr = [1, 2, 3];
console.log(arr.toString()); // '1,2,3'

// With nested arrays
const nested = [1, [2, 3], 4];
console.log(nested.toString()); // '1,2,3,4'
```

**Returns:** String representation of array
**Use case:** String representation of array

### 2. toLocaleString() - Localized String

```javascript
const prices = [1000, 2000, 3000];
console.log(prices.toLocaleString('de-DE', { 
  style: 'currency', 
  currency: 'EUR' 
}));
// Output depends on locale
```

**Returns:** Localized string representation of array
**Use case:** Locale-specific string representation

---

## Common Patterns

### Pattern 1: Chaining Methods

```javascript
const data = [1, 2, 3, 4, 5, 6];

const result = data
  .filter(x => x > 2)           // [3, 4, 5, 6]
  .map(x => x * 2)              // [6, 8, 10, 12]
  .reduce((sum, x) => sum + x, 0); // 36

console.log(result); // 36
```

### Pattern 2: Removing Duplicates

```javascript
// Using Set
const arr = [1, 2, 2, 3, 3, 3];
const unique1 = [...new Set(arr)];
console.log(unique1); // [1, 2, 3]

// Using filter
const unique2 = arr.filter((item, index) => arr.indexOf(item) === index);
console.log(unique2); // [1, 2, 3]
```

### Pattern 3: Grouping Elements

```javascript
const users = [
  { name: 'John', role: 'admin' },
  { name: 'Jane', role: 'user' },
  { name: 'Bob', role: 'admin' }
];

const grouped = users.reduce((acc, user) => {
  if (!acc[user.role]) {
    acc[user.role] = [];
  }
  acc[user.role].push(user);
  return acc;
}, {});

console.log(grouped);
// {
//   admin: [{name: 'John', role: 'admin'}, {name: 'Bob', role: 'admin'}],
//   user: [{name: 'Jane', role: 'user'}]
// }
```

### Pattern 4: Merging Arrays

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];

// Using concat
const merged1 = arr1.concat(arr2);

// Using spread operator
const merged2 = [...arr1, ...arr2];

// Using push with spread
arr1.push(...arr2);

console.log(merged1); // [1, 2, 3, 4]
console.log(merged2); // [1, 2, 3, 4]
console.log(arr1);    // [1, 2, 3, 4]
```

### Pattern 5: Shallow Copy vs Deep Copy

```javascript
const original = [1, [2, 3], 4];

// Shallow copy (only copies first level)
const shallow = original.slice();
shallow[1][0] = 999;
console.log(original); // [1, [999, 3], 4] (affected!)

// Deep copy (using JSON - works for simple objects)
const deepCopy = JSON.parse(JSON.stringify(original));
deepCopy[1][0] = 999;
console.log(original); // [1, [2, 3], 4] (not affected)

// Using spread operator (also shallow)
const spread = [...original];
```

---

## Quick Reference Table

| Method | Mutates | Returns | Use Case |
|--------|---------|---------|----------|
| push() | ✅ | length | Add to end |
| pop() | ✅ | element | Remove from end |
| unshift() | ✅ | length | Add to start |
| shift() | ✅ | element | Remove from start |
| splice() | ✅ | removed[] | Add/remove at index |
| reverse() | ✅ | array | Reverse array |
| sort() | ✅ | array | Sort array |
| slice() | ❌ | array | Copy portion |
| concat() | ❌ | array | Combine arrays |
| join() | ❌ | string | Convert to string |
| map() | ❌ | array | Transform elements |
| filter() | ❌ | array | Select elements |
| reduce() | ❌ | value | Aggregate data |
| forEach() | ❌ | undefined | Loop through |
| find() | ❌ | element | Find first match |
| includes() | ❌ | boolean | Check existence |
| indexOf() | ❌ | number | Find index |
| flat() | ❌ | array | Flatten nested |
| flatMap() | ❌ | array | Map & flatten |
| some() | ❌ | boolean | Check any match |
| every() | ❌ | boolean | Check all match |

---

## Best Practices

1. **Use non-mutating methods by default** - Prefer `slice()` over `splice()` to avoid side effects
2. **Chain methods for clarity** - Combine operations logically
3. **Use `filter()` + `map()` instead of loops** - More readable
4. **Use `reduce()` for aggregation** - Powerful for complex transformations
5. **Use `===` when searching** - `indexOf()` uses `===` comparison
6. **Use `some()` to break early** - More efficient than `forEach()` when checking
7. **Be aware of `includes(NaN)`** - Special case where it works differently
8. **Sort with compare function** - Don't rely on default string sorting for numbers

---

## Summary

Array methods are the backbone of JavaScript programming. Master them and you'll write cleaner, more efficient code. The main categories are:

- **Mutating**: Change the original array
- **Non-mutating**: Return new array/value
- **Iteration**: Execute function for each element
- **Searching**: Find elements
- **Transformation**: Convert/restructure array
- **Aggregation**: Combine into single value

