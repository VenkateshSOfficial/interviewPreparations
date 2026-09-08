# Object Copying in JavaScript

## Introduction

Object copying is a crucial concept in JavaScript. When you copy an object, you might get unexpected behavior if you don't understand the difference between shallow copy and deep copy. This guide covers all methods of copying objects with detailed examples and comparisons.

---

## Why Object Copying Matters

```javascript
// ❌ Pitfall 1: Assignment creates a reference, not a copy
const original = { name: 'John', age: 30 };
const copy = original;  // Not a copy, just a reference!

copy.name = 'Jane';
console.log(original.name); // 'Jane' (original changed!)
console.log(copy === original); // true (same object)

// ✅ Solution: Use proper copying methods
```

---

## Table of Contents

1. [Shallow Copy](#shallow-copy) - Copies only first level
2. [Deep Copy](#deep-copy) - Copies all nested levels
3. [Comparison Table](#comparison-table)

---

## Shallow Copy

A **shallow copy** creates a new object but only copies the first level of properties. If the object contains nested objects/arrays, only references to those nested objects are copied.

### Visual Representation

```
Original Object:        Shallow Copy:
┌─────────────────┐     ┌─────────────────┐
│ name: 'John'    │     │ name: 'John'    │ (copied)
│ age: 30         │────→│ age: 30         │ (copied)
│ address:        │     │ address:        │
│  ┌──────────────┤     │  ┌──────────────┤
│  │ city: 'NYC'  │     │  │ city: 'NYC'  │ (same reference!)
│  │ zip: '10001' │     │  │ zip: '10001' │
│  └──────────────┤     │  └──────────────┤
└─────────────────┘     └─────────────────┘

If you modify the nested object, both original and copy are affected!
```

### Method 1: Object.assign()

```javascript
const original = {
  name: 'John',
  age: 30,
  address: { city: 'NYC', zip: '10001' }
};

// Shallow copy using Object.assign()
const copy = Object.assign({}, original);

// Modify first level property
copy.name = 'Jane';
console.log(original.name); // 'John' (not affected ✅)

// Modify nested property
copy.address.city = 'LA';
console.log(original.address.city); // 'LA' (affected ❌)

console.log(copy.address === original.address); // true (same reference)
```

### Method 2: Spread Operator (...)

```javascript
const original = {
  name: 'John',
  age: 30,
  address: { city: 'NYC', zip: '10001' }
};

// Shallow copy using spread operator
const copy = { ...original };

// Modify first level property
copy.name = 'Jane';
console.log(original.name); // 'John' (not affected ✅)

// Modify nested property
copy.address.city = 'LA';
console.log(original.address.city); // 'LA' (affected ❌)

console.log(copy.address === original.address); // true (same reference)
```

### Method 3: Array.slice() (for arrays)

```javascript
const originalArray = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];

// Shallow copy of array
const copy = originalArray.slice();
// or
const copy2 = [...originalArray];

// Modify array element (object)
copy[0].name = 'Bob';
console.log(originalArray[0].name); // 'Bob' (affected ❌)

console.log(copy === originalArray);     // false (different arrays)
console.log(copy[0] === originalArray[0]); // true (same object reference)
```

### Method 4: Array.concat() (for arrays)

```javascript
const originalArray = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];

// Shallow copy using concat
const copy = originalArray.concat();

// Modify nested object
copy[0].name = 'Bob';
console.log(originalArray[0].name); // 'Bob' (affected ❌)
```

### Method 5: Object.create() with Object.assign()

```javascript
const original = {
  name: 'John',
  address: { city: 'NYC' }
};

// Shallow copy with Object.create
const copy = Object.assign(Object.create(Object.getPrototypeOf(original)), original);

// First level properties independent
copy.name = 'Jane';
console.log(original.name); // 'John' (not affected ✅)

// Nested objects still shared
copy.address.city = 'LA';
console.log(original.address.city); // 'LA' (affected ❌)
```

### Problem with Shallow Copy

```javascript
const user = {
  name: 'John',
  age: 30,
  hobbies: ['reading', 'gaming'],
  address: {
    street: '123 Main St',
    city: 'NYC'
  }
};

const userCopy = { ...user };

// Modifying arrays/objects affects both
userCopy.hobbies.push('coding');
console.log(user.hobbies); // ['reading', 'gaming', 'coding'] ❌

userCopy.address.city = 'LA';
console.log(user.address.city); // 'LA' ❌
```

---

## Deep Copy

A **deep copy** creates a completely independent copy of an object, including all nested objects and arrays. Modifying the copy does not affect the original.

### Visual Representation

```
Original Object:        Deep Copy:
┌─────────────────┐     ┌─────────────────┐
│ name: 'John'    │     │ name: 'John'    │ (independent)
│ age: 30         │     │ age: 30         │ (independent)
│ address:        │     │ address:        │
│  ┌──────────────┤     │  ┌──────────────┤
│  │ city: 'NYC'  │     │  │ city: 'NYC'  │ (independent copy)
│  │ zip: '10001' │     │  │ zip: '10001' │
│  └──────────────┤     │  └──────────────┤
└─────────────────┘     └─────────────────┘

If you modify any level, only the copy is affected!
```

### Method 1: JSON.parse(JSON.stringify())

```javascript
const original = {
  name: 'John',
  age: 30,
  address: { city: 'NYC', zip: '10001' },
  hobbies: ['reading', 'gaming']
};

// Deep copy using JSON methods
const deepCopy = JSON.parse(JSON.stringify(original));

// Modify first level
deepCopy.name = 'Jane';
console.log(original.name); // 'John' (not affected ✅)

// Modify nested object
deepCopy.address.city = 'LA';
console.log(original.address.city); // 'NYC' (not affected ✅)

// Modify array
deepCopy.hobbies.push('coding');
console.log(original.hobbies); // ['reading', 'gaming'] (not affected ✅)

console.log(deepCopy === original); // false
console.log(deepCopy.address === original.address); // false
console.log(deepCopy.hobbies === original.hobbies); // false
```

**⚠️ Limitations of JSON.parse/stringify:**

```javascript
const obj = {
  name: 'John',
  date: new Date(),                    // ❌ Becomes string
  func: function() { return 'hi'; },   // ❌ Lost
  regex: /test/g,                      // ❌ Becomes empty object
  symbol: Symbol('id'),                // ❌ Lost
  undefined: undefined,                // ❌ Lost
  circular: null                       // Will be set below
};

obj.circular = obj; // Circular reference

// This works
const copy = JSON.parse(JSON.stringify(obj));
console.log(copy.name); // 'John' ✅
console.log(copy.date); // "2023-01-01T..." (string, not Date ❌)
console.log(copy.func); // undefined ❌
console.log(copy.regex); // {} ❌
```

### Method 2: Recursive Function (Custom Deep Copy)

```javascript
// Deep copy using recursion
function deepCopy(obj) {
  // Handle null and non-objects
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }

  // Handle Date
  if (obj instanceof Date) {
    return new Date(obj);
  }

  // Handle Array
  if (Array.isArray(obj)) {
    return obj.map(item => deepCopy(item));
  }

  // Handle object
  const copy = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = deepCopy(obj[key]);
    }
  }
  return copy;
}

const original = {
  name: 'John',
  date: new Date('2023-01-01'),
  address: { city: 'NYC', zip: '10001' },
  hobbies: ['reading', 'gaming']
};

const copy = deepCopy(original);

// All modifications are independent
copy.name = 'Jane';
copy.date.setFullYear(2024);
copy.address.city = 'LA';
copy.hobbies.push('coding');

console.log(original.name);           // 'John' ✅
console.log(original.date.getFullYear()); // 2023 ✅
console.log(original.address.city);   // 'NYC' ✅
console.log(original.hobbies);        // ['reading', 'gaming'] ✅
```

### Method 3: structuredClone() (Modern)

```javascript
const original = {
  name: 'John',
  age: 30,
  address: { city: 'NYC' },
  date: new Date(),
  hobbies: ['reading', 'gaming']
};

// Deep clone using structuredClone (ES2022)
const deepCopy = structuredClone(original);

// Modify copy
deepCopy.name = 'Jane';
deepCopy.address.city = 'LA';
deepCopy.hobbies.push('coding');
deepCopy.date.setFullYear(2024);

// Original unchanged
console.log(original.name);           // 'John' ✅
console.log(original.address.city);   // 'NYC' ✅
console.log(original.hobbies);        // ['reading', 'gaming'] ✅
console.log(original.date.getFullYear()); // 2023 ✅
```

**Benefits of structuredClone():**
- ✅ Handles Date objects correctly
- ✅ Handles Map and Set
- ✅ Handles circular references
- ✅ No limitations like JSON methods

**Limitations:**
- ❌ Cannot copy functions
- ❌ Cannot copy DOM elements

### Method 4: Lodash Library (_.cloneDeep())

```javascript
// Using lodash library
const _ = require('lodash');

const original = {
  name: 'John',
  address: { city: 'NYC' },
  hobbies: ['reading', 'gaming'],
  greet: function() { return 'Hi'; }  // Functions are preserved!
};

const deepCopy = _.cloneDeep(original);

// Modify copy
deepCopy.name = 'Jane';
deepCopy.address.city = 'LA';
deepCopy.greet = function() { return 'Hello'; };

// Original unchanged
console.log(original.name);     // 'John' ✅
console.log(original.address.city); // 'NYC' ✅
console.log(original.greet());  // 'Hi' ✅
```

### Method 5: Manual Deep Clone with Getters/Setters

```javascript
function deepCloneWithDescriptors(obj) {
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }

  if (Array.isArray(obj)) {
    return obj.map(item => deepCloneWithDescriptors(item));
  }

  const clone = Object.create(Object.getPrototypeOf(obj));
  
  for (const key of Object.getOwnPropertyNames(obj)) {
    const descriptor = Object.getOwnPropertyDescriptor(obj, key);
    
    if (descriptor.value && typeof descriptor.value === 'object') {
      descriptor.value = deepCloneWithDescriptors(descriptor.value);
    }
    
    Object.defineProperty(clone, key, descriptor);
  }
  
  return clone;
}

const original = { name: 'John', age: 30 };
const copy = deepCloneWithDescriptors(original);
```

---

## Comparison of Copy Methods

### Copy Method 1: Assignment (Reference)

```javascript
const original = { name: 'John', address: { city: 'NYC' } };
const copy = original;

copy.name = 'Jane';
copy.address.city = 'LA';

console.log(original.name);           // 'Jane' ❌
console.log(original.address.city);   // 'LA' ❌
console.log(copy === original);        // true (same object)
```

### Copy Method 2: Shallow Copy Comparison

```javascript
const original = { name: 'John', address: { city: 'NYC' } };

// Method A: Object.assign()
const copy1 = Object.assign({}, original);

// Method B: Spread operator
const copy2 = { ...original };

// Both behave identically
copy1.name = 'Jane';
console.log(original.name); // 'John' ✅

copy1.address.city = 'LA';
console.log(original.address.city); // 'LA' ❌ (nested object affected)
```

### Copy Method 3: Deep Copy Comparison

```javascript
const original = {
  name: 'John',
  age: 30,
  date: new Date('2023-01-01'),
  address: { city: 'NYC' },
  hobbies: ['reading', 'gaming'],
  func: function() { return 'hi'; }
};

// Method A: JSON methods
const jsonCopy = JSON.parse(JSON.stringify(original));
console.log(jsonCopy.date);     // "2023-01-01T..." (string) ❌
console.log(jsonCopy.func);     // undefined ❌

// Method B: Custom recursive function
function deepCopy(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (obj instanceof Date) return new Date(obj);
  if (Array.isArray(obj)) return obj.map(item => deepCopy(item));
  const copy = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = deepCopy(obj[key]);
    }
  }
  return copy;
}
const customCopy = deepCopy(original);
console.log(customCopy.date instanceof Date); // true ✅
console.log(customCopy.func);     // function ✅

// Method C: structuredClone()
const structCopy = structuredClone(original);
console.log(structCopy.date instanceof Date); // true ✅
console.log(structCopy.func);     // undefined ❌ (functions not supported)

// Method D: Lodash
const _ = require('lodash');
const lodashCopy = _.cloneDeep(original);
console.log(lodashCopy.date instanceof Date); // true ✅
console.log(lodashCopy.func);     // function ✅
```

---

## Special Cases

### Copying with Circular References

```javascript
// Circular reference - Object references itself
const obj = { name: 'John' };
obj.self = obj;

// ❌ JSON methods fail
try {
  JSON.stringify(obj);
} catch (e) {
  console.log('Error:', e.message); // "Converting circular structure to JSON"
}

// ✅ Custom function (track visited objects)
function deepCopyWithCircular(obj, visited = new WeakMap()) {
  if (visited.has(obj)) {
    return visited.get(obj);
  }

  if (obj === null || typeof obj !== 'object') {
    return obj;
  }

  if (Array.isArray(obj)) {
    const arrCopy = [];
    visited.set(obj, arrCopy);
    obj.forEach((item, index) => {
      arrCopy[index] = deepCopyWithCircular(item, visited);
    });
    return arrCopy;
  }

  const objCopy = {};
  visited.set(obj, objCopy);
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      objCopy[key] = deepCopyWithCircular(obj[key], visited);
    }
  }
  return objCopy;
}

const cirCopy = deepCopyWithCircular(obj);
console.log(cirCopy.self === cirCopy); // true ✅
```

### Copying with Getters/Setters

```javascript
const original = {};
let _value = 'John';

Object.defineProperty(original, 'name', {
  get() { return _value; },
  set(val) { _value = val; },
  enumerable: true,
  configurable: true
});

// ❌ Spread operator loses getter/setter
const copy1 = { ...original };
console.log(Object.getOwnPropertyDescriptor(copy1, 'name').get); // undefined

// ✅ Custom function preserves descriptors
function deepCloneWithDescriptors(obj) {
  const clone = Object.create(Object.getPrototypeOf(obj));
  for (const key of Object.getOwnPropertyNames(obj)) {
    const descriptor = Object.getOwnPropertyDescriptor(obj, key);
    Object.defineProperty(clone, key, descriptor);
  }
  return clone;
}

const copy2 = deepCloneWithDescriptors(original);
console.log(Object.getOwnPropertyDescriptor(copy2, 'name').get); // [Function: get]
```

---

## Comparison Table

| Method | Copies First Level | Copies Nested | Handles Date | Handles Functions | Handles Circular | Speed | Use Case |
|--------|-------------------|----------------|--------------|-------------------|------------------|-------|----------|
| **Assignment (=)** | ❌ | ❌ | N/A | N/A | N/A | ⚡⚡⚡ | Reference only |
| **Object.assign()** | ✅ | ❌ | ❌ | ✅ | ❌ | ⚡⚡ | Shallow copy |
| **Spread (...)** | ✅ | ❌ | ❌ | ✅ | ❌ | ⚡⚡ | Shallow copy |
| **Array.slice()** | ✅ | ❌ | ❌ | ✅ | ❌ | ⚡⚡ | Array shallow copy |
| **Object.create()** | ✅ | ❌ | ❌ | ✅ | ❌ | ⚡ | Shallow with prototype |
| **JSON methods** | ✅ | ✅ | ❌ | ❌ | ❌ | ⚡ | Simple objects only |
| **Custom Recursive** | ✅ | ✅ | ✅ | ✅ | ❌ | 🐢 | Full control |
| **Custom (with WeakMap)** | ✅ | ✅ | ✅ | ✅ | ✅ | 🐢 | Complex objects |
| **structuredClone()** | ✅ | ✅ | ✅ | ❌ | ✅ | ⚡ | Modern standard |
| **Lodash _.cloneDeep()** | ✅ | ✅ | ✅ | ✅ | ✅ | ⚡ | Production use |

---

## Best Practices and Recommendations

### For Simple Objects (No Nesting)

```javascript
// Use shallow copy
const user = { name: 'John', age: 30 };
const copy = { ...user }; // or Object.assign({}, user)
```

### For Objects with Nested Properties

```javascript
// Option 1: Use structuredClone() (modern)
const user = { name: 'John', address: { city: 'NYC' } };
const copy = structuredClone(user);

// Option 2: Use Lodash (production)
const copy = _.cloneDeep(user);

// Option 3: Custom recursive function (full control)
```

### For Simple Objects with No Functions

```javascript
// Use JSON methods (simple and fast)
const data = { name: 'John', age: 30, hobbies: ['reading'] };
const copy = JSON.parse(JSON.stringify(data));
```

### For Objects with Circular References

```javascript
// Use custom function with WeakMap
function deepCopyWithCircular(obj, visited = new WeakMap()) {
  // ...implementation
}
```

### For Objects with Functions

```javascript
// Use Lodash or custom recursive function
const obj = { name: 'John', greet: () => 'Hi' };
const copy = _.cloneDeep(obj); // Functions preserved
```

---

## Summary

| Copy Type | What Gets Copied | When to Use | Trade-offs |
|-----------|------------------|------------|------------|
| **Reference** | Nothing (same object) | Never use for copying | All changes affect both |
| **Shallow Copy** | First level only | Simple objects, improving performance | Nested objects still linked |
| **Deep Copy (JSON)** | All levels | Simple objects without special types | Loses dates, functions, symbols |
| **Deep Copy (Custom)** | All levels + special handling | Complex objects | More code to maintain |
| **Deep Copy (structuredClone)** | All levels + circular refs | Modern browsers/Node.js 17+ | No function support |
| **Deep Copy (Lodash)** | All levels + functions | Production applications | External dependency |

---

## Decision Flowchart

```
Do you need to copy an object?
    ↓
Does object have nested properties?
    ├─ No → Use shallow copy (spread or Object.assign)
    └─ Yes → Does it need to be fully independent?
        ├─ No → Use shallow copy (faster)
        └─ Yes → What type of object?
            ├─ Simple (no functions/dates) → JSON.parse/stringify
            ├─ With dates → structuredClone() or custom function
            ├─ With functions → Lodash _.cloneDeep()
            ├─ With circular refs → Custom function with WeakMap
            └─ Full control needed → Custom recursive function
```

---

## Quick Reference

```javascript
// Shallow Copy
const shallow1 = { ...original };
const shallow2 = Object.assign({}, original);

// Deep Copy
const deep1 = JSON.parse(JSON.stringify(original));
const deep2 = structuredClone(original);
const deep3 = _.cloneDeep(original); // Lodash

// Custom Deep Copy
function deepCopy(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (Array.isArray(obj)) return obj.map(item => deepCopy(item));
  const copy = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = deepCopy(obj[key]);
    }
  }
  return copy;
}
```

