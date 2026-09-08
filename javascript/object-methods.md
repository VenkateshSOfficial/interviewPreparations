# Object Methods in JavaScript

## Introduction

Objects are fundamental to JavaScript. Object methods are built-in functions that allow you to manipulate, inspect, and work with objects. This guide covers all essential Object methods with detailed examples.

---

## Table of Contents

1. [Object Inspection Methods](#object-inspection-methods) - Methods to inspect object structure
2. [Object Creation Methods](#object-creation-methods) - Methods to create objects
3. [Object Modification Methods](#object-modification-methods) - Methods to modify objects
4. [Object Protection Methods](#object-protection-methods) - Methods to protect objects
5. [Object Iteration Methods](#object-iteration-methods) - Methods to loop through objects
6. [Object Property Descriptor Methods](#object-property-descriptor-methods) - Methods to define properties
7. [Object Utility Methods](#object-utility-methods) - Other useful methods

---

## Object Inspection Methods

These methods help you examine the structure and contents of objects.

### 1. Object.keys() - Get All Keys

```javascript
const obj = { name: 'John', age: 30, city: 'NYC' };

const keys = Object.keys(obj);
console.log(keys); // ['name', 'age', 'city']

// With array
const arr = ['a', 'b', 'c'];
console.log(Object.keys(arr)); // ['0', '1', '2']

// Empty object
const empty = {};
console.log(Object.keys(empty)); // []
```

**Returns:** Array of property names (strings)
**Use case:** Getting all keys from an object

### 2. Object.values() - Get All Values

```javascript
const obj = { name: 'John', age: 30, city: 'NYC' };

const values = Object.values(obj);
console.log(values); // ['John', 30, 'NYC']

// With array
const arr = ['a', 'b', 'c'];
console.log(Object.values(arr)); // ['a', 'b', 'c']

// With mixed types
const mixed = { name: 'John', scores: [90, 85, 88] };
console.log(Object.values(mixed)); // ['John', [90, 85, 88]]
```

**Returns:** Array of property values
**Use case:** Extracting all values from an object

### 3. Object.entries() - Get Key-Value Pairs

```javascript
const obj = { name: 'John', age: 30, city: 'NYC' };

const entries = Object.entries(obj);
console.log(entries);
// [['name', 'John'], ['age', 30], ['city', 'NYC']]

// Convert to Map
const map = new Map(Object.entries(obj));
console.log(map.get('name')); // 'John'

// Loop through entries
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key}: ${value}`);
}
// name: John
// age: 30
// city: NYC
```

**Returns:** Array of [key, value] pairs
**Use case:** Getting both keys and values together, converting to Map

### 4. Object.getOwnPropertyNames() - Get All Properties

```javascript
const obj = { name: 'John', age: 30 };

const properties = Object.getOwnPropertyNames(obj);
console.log(properties); // ['name', 'age']

// Difference: includes non-enumerable properties
const obj2 = {};
Object.defineProperty(obj2, 'hidden', {
  value: 'secret',
  enumerable: false
});

console.log(Object.keys(obj2));           // [] (doesn't include hidden)
console.log(Object.getOwnPropertyNames(obj2)); // ['hidden'] (includes hidden)
```

**Returns:** Array of all property names (including non-enumerable)
**Use case:** Getting all properties, including non-enumerable ones

### 5. Object.getOwnPropertySymbols() - Get All Symbols

```javascript
const sym1 = Symbol('id');
const sym2 = Symbol('name');

const obj = {
  regular: 'property',
  [sym1]: 'symbol value 1',
  [sym2]: 'symbol value 2'
};

console.log(Object.keys(obj));                  // ['regular']
console.log(Object.getOwnPropertySymbols(obj)); // [Symbol(id), Symbol(name)]
```

**Returns:** Array of all symbol properties
**Use case:** Getting symbol properties from an object

### 6. Object.hasOwn() - Check if Property Exists (Modern)

```javascript
const obj = { name: 'John', age: 30 };

console.log(Object.hasOwn(obj, 'name'));        // true
console.log(Object.hasOwn(obj, 'email'));       // false
console.log(Object.hasOwn(obj, 'toString'));    // false (inherited property)

// Better than hasOwnProperty
const obj2 = Object.create(null);
obj2.name = 'Jane';
console.log(obj2.hasOwnProperty('name')); // ❌ Error (no hasOwnProperty method)
console.log(Object.hasOwn(obj2, 'name')); // ✅ true (works correctly)
```

**Returns:** Boolean (true if property exists)
**Use case:** Checking if object has a property (better than hasOwnProperty)

### 7. Object.getOwnPropertyDescriptor() - Get Property Details

```javascript
const obj = { name: 'John' };

const descriptor = Object.getOwnPropertyDescriptor(obj, 'name');
console.log(descriptor);
// {
//   value: 'John',
//   writable: true,
//   enumerable: true,
//   configurable: true
// }

// Non-existent property
console.log(Object.getOwnPropertyDescriptor(obj, 'age')); // undefined
```

**Returns:** Property descriptor object, or undefined if not found
**Use case:** Getting detailed information about a property

---

## Object Creation Methods

These methods help you create objects.

### 1. Object.create() - Create with Custom Prototype

```javascript
// Create object with null prototype
const obj1 = Object.create(null);
console.log(Object.getPrototypeOf(obj1)); // null
obj1.name = 'John';
console.log(obj1.name); // 'John'

// Create object with specific prototype
const parent = {
  greet() {
    return `Hello, ${this.name}`;
  }
};

const child = Object.create(parent);
child.name = 'Jane';
console.log(child.greet()); // 'Hello, Jane'

// Create with property descriptors
const obj2 = Object.create(Object.prototype, {
  name: {
    value: 'John',
    writable: true,
    enumerable: true,
    configurable: true
  },
  age: {
    value: 30,
    writable: false,
    enumerable: false,
    configurable: false
  }
});

console.log(obj2.name); // 'John'
console.log(obj2.age);  // 30
obj2.age = 40;         // ❌ Doesn't work (read-only)
```

**Returns:** New object with specified prototype
**Use case:** Creating objects with specific prototype chains, setting up inheritance

### 2. Object.assign() - Copy Properties

```javascript
// Copy properties from one object to another
const target = { a: 1 };
const source1 = { b: 2 };
const source2 = { c: 3 };

const result = Object.assign(target, source1, source2);
console.log(result); // { a: 1, b: 2, c: 3 }
console.log(target === result); // true (modifies target)

// Create a copy
const original = { name: 'John', age: 30 };
const copy = Object.assign({}, original);
console.log(copy); // { name: 'John', age: 30 }
console.log(copy === original); // false (different objects)

// Shallow copy with duplicate keys
const obj1 = { x: 1, y: 2 };
const obj2 = { y: 3, z: 4 };
Object.assign(obj1, obj2);
console.log(obj1); // { x: 1, y: 3, z: 4 } (y is overwritten)
```

**Returns:** Target object (modified)
**Use case:** Copying object properties, merging objects, creating shallow copies

---

## Object Modification Methods

These methods help you modify object properties.

### 1. Object.defineProperty() - Define Single Property

```javascript
const obj = {};

// Define a simple property
Object.defineProperty(obj, 'name', {
  value: 'John',
  writable: true,
  enumerable: true,
  configurable: true
});

console.log(obj.name); // 'John'

// Define read-only property
const obj2 = {};
Object.defineProperty(obj2, 'id', {
  value: 123,
  writable: false
});

obj2.id = 456; // ❌ Fails silently (read-only)
console.log(obj2.id); // 123

// Define with getter/setter
const obj3 = {};
let _age = 0;

Object.defineProperty(obj3, 'age', {
  get() {
    return _age;
  },
  set(value) {
    if (value < 0) {
      console.log('Age cannot be negative');
      return;
    }
    _age = value;
  },
  enumerable: true,
  configurable: true
});

obj3.age = 30;
console.log(obj3.age); // 30
obj3.age = -5; // 'Age cannot be negative'
```

**Returns:** The object with the new property
**Use case:** Defining properties with specific descriptors, creating getters/setters

### 2. Object.defineProperties() - Define Multiple Properties

```javascript
const obj = {};

Object.defineProperties(obj, {
  name: {
    value: 'John',
    writable: true,
    enumerable: true
  },
  age: {
    value: 30,
    writable: false,
    enumerable: false
  },
  email: {
    value: 'john@example.com',
    writable: true,
    enumerable: true
  }
});

console.log(obj.name);  // 'John'
console.log(obj.age);   // 30
console.log(obj.email); // 'john@example.com'

// Non-enumerable properties don't show in iteration
console.log(Object.keys(obj)); // ['name', 'email'] (age is not included)
```

**Returns:** The object with new properties
**Use case:** Defining multiple properties at once with specific descriptors

---

## Object Protection Methods

These methods help you protect objects from modification.

### 1. Object.freeze() - Prevent Modifications

```javascript
const obj = { name: 'John', age: 30 };

Object.freeze(obj);

obj.name = 'Jane';      // ❌ Fails silently
obj.email = 'new@ex.com'; // ❌ Fails silently
delete obj.age;         // ❌ Fails silently

console.log(obj); // { name: 'John', age: 30 } (unchanged)
console.log(Object.isFrozen(obj)); // true

// Nested objects are NOT frozen (shallow freeze)
const obj2 = {
  user: { name: 'John' }
};

Object.freeze(obj2);
obj2.user.name = 'Jane'; // ✅ This works! (nested object not frozen)
console.log(obj2); // { user: { name: 'Jane' } }
```

**Returns:** The frozen object
**Use case:** Making objects immutable, preventing accidental modifications

### 2. Object.seal() - Prevent Adding/Removing Properties

```javascript
const obj = { name: 'John', age: 30 };

Object.seal(obj);

obj.name = 'Jane';      // ✅ Works (modify existing)
obj.email = 'new@ex.com'; // ❌ Fails silently (add new property)
delete obj.age;         // ❌ Fails silently (delete property)

console.log(obj); // { name: 'Jane', age: 30 }
console.log(Object.isSealed(obj)); // true
```

**Returns:** The sealed object
**Use case:** Allowing modifications but preventing adding/removing properties

### 3. Object.preventExtensions() - Prevent Adding Properties

```javascript
const obj = { name: 'John', age: 30 };

Object.preventExtensions(obj);

obj.name = 'Jane';      // ✅ Works (modify existing)
obj.email = 'new@ex.com'; // ❌ Fails silently (add new property)
delete obj.age;         // ✅ Works (delete property)

console.log(obj); // { name: 'Jane' }
console.log(Object.isExtensible(obj)); // false
```

**Returns:** The object
**Use case:** Preventing new properties while allowing modifications and deletions

### Comparison Table: freeze vs seal vs preventExtensions

```javascript
const obj = { a: 1, b: 2 };

// freeze: Can't modify, add, or remove
Object.freeze(obj);
obj.a = 10;          // ❌
obj.c = 3;           // ❌
delete obj.b;        // ❌

// seal: Can modify, but can't add or remove
Object.seal(obj);
obj.a = 10;          // ✅
obj.c = 3;           // ❌
delete obj.b;        // ❌

// preventExtensions: Can modify or remove, but can't add
Object.preventExtensions(obj);
obj.a = 10;          // ✅
obj.c = 3;           // ❌
delete obj.b;        // ✅
```

---

## Object Iteration Methods

### 1. Object.keys() - Iterate Over Keys

```javascript
const user = { name: 'John', age: 30, email: 'john@ex.com' };

// Using keys
Object.keys(user).forEach(key => {
  console.log(`${key}: ${user[key]}`);
});
// name: John
// age: 30
// email: john@ex.com

// Filter keys
const adults = { john: 30, jane: 25, bob: 35 };
const over30 = Object.keys(adults)
  .filter(name => adults[name] > 30);
console.log(over30); // ['bob']
```

**Returns:** Array of keys
**Use case:** Iterating over object keys

### 2. Object.values() - Iterate Over Values

```javascript
const scores = { math: 90, english: 85, science: 88 };

// Using values
const average = Object.values(scores).reduce((sum, val) => sum + val, 0) / 3;
console.log(average); // 87.67

// Filter values
const passing = Object.values(scores).filter(score => score >= 85);
console.log(passing); // [90, 85, 88]
```

**Returns:** Array of values
**Use case:** Iterating over object values, aggregating data

### 3. Object.entries() - Iterate Over Pairs

```javascript
const user = { name: 'John', age: 30, email: 'john@ex.com' };

// Using entries
for (const [key, value] of Object.entries(user)) {
  console.log(`${key}: ${value}`);
}

// Transform to array of objects
const entries = Object.entries(user);
const formatted = entries.map(([key, value]) => ({ key, value }));
console.log(formatted);
// [
//   { key: 'name', value: 'John' },
//   { key: 'age', value: 30 },
//   { key: 'email', value: 'john@ex.com' }
// ]
```

**Returns:** Array of [key, value] pairs
**Use case:** Iterating over both keys and values

---

## Object Property Descriptor Methods

### 1. Object.getOwnPropertyDescriptors() - Get All Descriptors

```javascript
const obj = { name: 'John', age: 30 };

const descriptors = Object.getOwnPropertyDescriptors(obj);
console.log(descriptors);
// {
//   name: { value: 'John', writable: true, enumerable: true, configurable: true },
//   age: { value: 30, writable: true, enumerable: true, configurable: true }
// }
```

**Returns:** Object with all property descriptors
**Use case:** Getting detailed information about all properties

### 2. Object.getPrototypeOf() - Get Prototype

```javascript
const parent = { greet() { return 'Hello'; } };
const child = Object.create(parent);

console.log(Object.getPrototypeOf(child) === parent); // true

// With class
class Animal { }
class Dog extends Animal { }
const dog = new Dog();

console.log(Object.getPrototypeOf(dog) instanceof Dog); // true
```

**Returns:** The prototype of the object
**Use case:** Accessing the prototype chain

### 3. Object.setPrototypeOf() - Set Prototype

```javascript
const parent = {
  greet() { return 'Hello'; }
};

const child = {};
Object.setPrototypeOf(child, parent);

console.log(child.greet()); // 'Hello'

// ⚠️ Warning: Avoid in production (performance issues)
// Use Object.create() instead
```

**Returns:** The object
**Use case:** Changing an object's prototype (not recommended)

---

## Object Utility Methods

### 1. Object.is() - Strict Comparison

```javascript
// Same as === in most cases
console.log(Object.is(5, 5));         // true
console.log(Object.is('a', 'a'));    // true
console.log(Object.is(5, '5'));      // false

// Different from === for NaN and -0
console.log(5 === 5);                // true
console.log(Object.is(NaN, NaN));    // true (better than ===)
console.log(NaN === NaN);            // false

console.log(Object.is(-0, 0));       // false (detects -0)
console.log(-0 === 0);               // true
```

**Returns:** Boolean
**Use case:** Strict value comparison, NaN comparison

### 2. Object.fromEntries() - Convert Entries to Object

```javascript
// From array of entries
const entries = [['name', 'John'], ['age', 30]];
const obj = Object.fromEntries(entries);
console.log(obj); // { name: 'John', age: 30 }

// From Map
const map = new Map([['id', 1], ['name', 'Jane']]);
const user = Object.fromEntries(map);
console.log(user); // { id: 1, name: 'Jane' }

// Transform and convert
const query = 'name=John&age=30&city=NYC';
const params = new URLSearchParams(query);
const obj2 = Object.fromEntries(params);
console.log(obj2); // { name: 'John', age: '30', city: 'NYC' }
```

**Returns:** New object from entries
**Use case:** Converting entries, Map, or URLSearchParams to object

---

## Common Patterns and Best Practices

### Pattern 1: Object Copy

```javascript
const original = { name: 'John', age: 30 };

// Shallow copy
const copy1 = Object.assign({}, original);
const copy2 = { ...original };

// Deep copy (for simple objects)
const deepCopy = JSON.parse(JSON.stringify(original));
```

### Pattern 2: Object Merge

```javascript
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };

// Using Object.assign
const merged1 = Object.assign({}, obj1, obj2);

// Using spread operator
const merged2 = { ...obj1, ...obj2 };
```

### Pattern 3: Extracting Specific Keys

```javascript
const user = { name: 'John', age: 30, email: 'john@ex.com', password: 'secret' };

// Extract specific keys
const pick = (obj, keys) => 
  Object.fromEntries(keys.map(key => [key, obj[key]]));

const publicUser = pick(user, ['name', 'age', 'email']);
console.log(publicUser); // { name: 'John', age: 30, email: 'john@ex.com' }
```

### Pattern 4: Omitting Specific Keys

```javascript
const user = { name: 'John', age: 30, email: 'john@ex.com', password: 'secret' };

// Omit specific keys
const omit = (obj, keys) =>
  Object.fromEntries(
    Object.entries(obj).filter(([key]) => !keys.includes(key))
  );

const safeUser = omit(user, ['password']);
console.log(safeUser); // { name: 'John', age: 30, email: 'john@ex.com' }
```

### Pattern 5: Transforming Object Values

```javascript
const prices = { apple: 1.5, banana: 0.8, orange: 2 };

// Double all prices
const doubled = Object.fromEntries(
  Object.entries(prices).map(([key, value]) => [key, value * 2])
);
console.log(doubled); // { apple: 3, banana: 1.6, orange: 4 }
```

### Pattern 6: Grouping by Property

```javascript
const users = [
  { id: 1, department: 'IT', name: 'John' },
  { id: 2, department: 'HR', name: 'Jane' },
  { id: 3, department: 'IT', name: 'Bob' }
];

// Group by department
const grouped = users.reduce((acc, user) => {
  if (!acc[user.department]) {
    acc[user.department] = [];
  }
  acc[user.department].push(user);
  return acc;
}, {});

console.log(grouped);
// {
//   IT: [{ id: 1, ... }, { id: 3, ... }],
//   HR: [{ id: 2, ... }]
// }
```

---

## Quick Reference Table

| Method | Returns | Mutates | Use Case |
|--------|---------|---------|----------|
| keys() | Array | ❌ | Get property names |
| values() | Array | ❌ | Get property values |
| entries() | Array | ❌ | Get key-value pairs |
| assign() | Object | ✅ | Copy/merge properties |
| create() | Object | ❌ | Create with prototype |
| freeze() | Object | ✅ | Make immutable |
| seal() | Object | ✅ | Prevent add/remove |
| preventExtensions() | Object | ✅ | Prevent adding |
| defineProperty() | Object | ✅ | Define single property |
| defineProperties() | Object | ✅ | Define multiple properties |
| is() | Boolean | ❌ | Compare values |
| fromEntries() | Object | ❌ | Convert entries to object |
| getPrototypeOf() | Object | ❌ | Get prototype |
| hasOwn() | Boolean | ❌ | Check property exists |

---

## Key Takeaways

1. **Object.keys/values/entries()** - Essential for iteration
2. **Object.assign()** - Simple way to copy and merge objects
3. **Object.create()** - For setting up prototype chains
4. **Object.freeze/seal/preventExtensions()** - For protecting objects
5. **Object.defineProperty()** - For fine-grained control
6. **Object.fromEntries()** - Opposite of Object.entries()
7. **Use spread operator `{...obj}`** - Often simpler than Object.assign()
8. **Use Object.hasOwn()** - Better than hasOwnProperty()

---

## Summary

Object methods are powerful tools for:
- **Inspecting** objects (keys, values, entries)
- **Creating** objects (create, assign)
- **Modifying** objects (defineProperty, defineProperties)
- **Protecting** objects (freeze, seal, preventExtensions)
- **Iterating** over objects (keys, values, entries)
- **Transforming** objects (fromEntries, assign)

Master these methods and you'll write more robust and maintainable JavaScript code!

