# Closures in JavaScript

## What is a Closure?

A **closure** is a function that remembers and can access the variables from the scope in which it was created, even after that scope has finished executing.

In simpler words: A closure is when a function has access to variables from another function's scope. This is possible because functions in JavaScript form closures around the data they need to work with.

---

## The Core Concept

When you create a function inside another function, the inner function gets access to:
1. Its own variables
2. The outer function's variables
3. Global variables

This combination of a function and the variables it has access to is called a **closure**.

---

## Simple Real-World Analogy

Think of a closure like a student who moves out of their parent's house but keeps a key to the house. Even though they don't live there anymore, they can still go back and use things from the house whenever they need them. The student is like the inner function, and the parent's house is like the outer function's scope.

---

## Simple Examples

### Example 1: Basic Closure

```javascript
function outer() {
  let message = "Hello"; // Variable in outer scope
  
  function inner() {
    console.log(message); // inner() can access message
  }
  
  return inner;
}

const greet = outer();
greet(); // Output: "Hello"
```

**What's happening here:**
- `outer()` creates a variable `message`
- `inner()` is defined inside `outer()` and can access `message`
- We return `inner()` from `outer()`
- Even though `outer()` has finished executing, `inner()` still remembers `message`
- When we call `greet()`, it can still access `message` - **this is a closure!**

---

### Example 2: Counter Function (Most Common Example)

```javascript
function createCounter() {
  let count = 0; // This variable is "trapped" inside the closure
  
  return function() {
    count++; // Can access and modify count
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // Output: 1
console.log(counter()); // Output: 2
console.log(counter()); // Output: 3
```

**What's happening:**
- `createCounter()` creates a variable `count = 0`
- The returned function is a closure that can access `count`
- Each time we call `counter()`, it increments `count`
- `count` is not reset because the closure "remembers" it
- The variable `count` is protected and can only be modified through the returned function

---

### Example 3: Bank Account (Private Data)

```javascript
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private variable
  
  return {
    deposit: function(amount) {
      balance += amount;
      return `Deposited $${amount}. New balance: $${balance}`;
    },
    withdraw: function(amount) {
      balance -= amount;
      return `Withdrew $${amount}. New balance: $${balance}`;
    },
    getBalance: function() {
      return `Current balance: $${balance}`;
    }
  };
}

const myAccount = createBankAccount(1000);
console.log(myAccount.deposit(500));    // Output: Deposited $500. New balance: $1500
console.log(myAccount.withdraw(200));   // Output: Withdrew $200. New balance: $1300
console.log(myAccount.getBalance());    // Output: Current balance: $1300
```

**Key points:**
- `balance` is private - you can't access it directly
- You can only modify it through the methods (deposit, withdraw)
- All three methods form closures over the `balance` variable

---

### Example 4: Loop with Closure (Common Interview Question)

**Problem WITHOUT closure:**
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // Prints 3, 3, 3
  }, 1000);
}
```

**Why it prints 3, 3, 3:**
- `var` has function scope, not block scope
- By the time the timeout executes, the loop has finished and `i = 3`

**Solution WITH closure:**
```javascript
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j); // Prints 0, 1, 2
    }, 1000);
  })(i);
}
```

**Or simpler, using `let` (block scope):**
```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // Prints 0, 1, 2
  }, 1000);
}
```

---

## Why Are Closures Important?

### 1. **Data Privacy**
```javascript
function createSecret(secret) {
  return function() {
    return secret; // External code can't access secret directly
  };
}

const mySecret = createSecret("I love JavaScript");
console.log(mySecret()); // Output: "I love JavaScript"
// mySecret.secret would be undefined - it's protected!
```

### 2. **Function Factories**
```javascript
function makeMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = makeMultiplier(2);
const triple = makeMultiplier(3);

console.log(double(5)); // Output: 10
console.log(triple(5)); // Output: 15
```

### 3. **Callbacks and Event Handlers**
```javascript
function setupButtons() {
  for (let i = 1; i <= 3; i++) {
    const button = document.createElement('button');
    button.textContent = `Button ${i}`;
    
    button.addEventListener('click', function() {
      console.log(`Button ${i} was clicked`); // Closure over i
    });
  }
}
```

---

## How Closures Work (Under the Hood)

When JavaScript creates a function, it creates a **scope chain**:

```javascript
function outer() {
  let x = 5;
  
  function inner() {
    console.log(x); // Looks for x here
  }
  
  return inner;
}
```

**Scope chain for `inner()` function:**
1. Local scope: `inner`'s own variables
2. Outer scope: `outer`'s variables (including `x`)
3. Global scope: Global variables

When `inner()` is returned and executed later, this scope chain is **preserved** in memory - that's the closure!

---

## Key Takeaways

✅ **What is a closure:** A function that has access to variables from its outer scope  
✅ **When it's created:** Every time a function is created  
✅ **Why it matters:** Data privacy, function factories, callbacks  
✅ **How it works:** The function "remembers" its scope even after the outer function finishes  

---

## Interview Question

**Q: How many times will "Hi" be logged?**
```javascript
function test() {
  let count = 0;
  
  function increment() {
    count++;
    console.log("Hi");
  }
  
  increment();
  increment();
  increment();
  
  return increment;
}

const fn = test(); // Logs "Hi" 3 times
fn(); // Logs "Hi" 1 more time (4th time)
```

**A:** "Hi" is logged 4 times total. The first 3 times from calling `increment()` inside `test()`, and once more when we call `fn()`. The `count` variable persists in the closure.

---

## Summary

Closures are one of the most powerful features of JavaScript. They allow you to:
- Create private variables
- Build function factories
- Handle asynchronous code elegantly
- Maintain state in functions

Understanding closures is essential for writing advanced JavaScript and is almost guaranteed to come up in interviews!
