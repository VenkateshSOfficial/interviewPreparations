# The Event Loop in JavaScript

## What is the Event Loop?

The **Event Loop** is JavaScript's mechanism for handling asynchronous operations like callbacks, promises, and timers. It's the heart of JavaScript's non-blocking, single-threaded runtime.

**Simple definition:** The Event Loop is a process that continuously checks if there's code to execute. It picks up tasks from different queues and executes them in a specific order.

---

## Why Do We Need the Event Loop?

JavaScript is **single-threaded**, meaning it can only execute one line of code at a time. But modern applications need to:
- Fetch data from servers
- Handle user interactions
- Run timers
- Do other things without freezing the app

The Event Loop allows JavaScript to do these things "asynchronously" even though it's single-threaded.

---

## Key Components of the Event Loop

### 1. **Call Stack**
The place where all your synchronous code executes. JavaScript goes through each line one at a time.

```javascript
function a() {
  console.log("A");
}

function b() {
  console.log("B");
  a();
  console.log("C");
}

b();
```

**Execution order:**
- `b()` is pushed to stack → prints "B"
- `a()` is pushed to stack → prints "A"
- `a()` pops from stack
- Prints "C"
- `b()` pops from stack

---

### 2. **Web APIs**
Browser features like `setTimeout`, `fetch`, event listeners, etc. These are NOT part of JavaScript—they're part of the browser!

```javascript
console.log("Start");

setTimeout(function() {
  console.log("Timeout");
}, 1000);

console.log("End");
```

**Output:**
```
Start
End
Timeout (after 1 second)
```

---

### 3. **Callback Queue (Macrotask Queue)**
Also called the "Task Queue". Holds callbacks that are ready to execute (like `setTimeout`, `setInterval`).

---

### 4. **Microtask Queue**
Holds higher-priority tasks like:
- Promise callbacks (`.then()`, `.catch()`, `.finally()`)
- `queueMicrotask()`
- `MutationObserver`

---

### 5. **Event Loop**
The manager that decides what code runs next. It follows these rules:
1. Execute all code in the **Call Stack**
2. If Call Stack is empty, check the **Microtask Queue**
3. Execute all microtasks
4. If Microtask Queue is empty, check the **Callback Queue**
5. Execute one callback (then check microtasks again)
6. Repeat from step 1

---

## Visual Diagram of the Event Loop

```
┌─────────────────────────────────────────────────────────────┐
│                     JAVASCRIPT ENGINE                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────┐          ┌────────────────────┐       │
│  │   CALL STACK     │          │   MICROTASK QUEUE  │       │
│  │                  │          │  (Promises, etc)   │       │
│  │  ┌────────────┐  │          │                    │       │
│  │  │ function() │  │          │  ┌──────────────┐  │       │
│  │  └────────────┘  │          │  │ .then()      │  │       │
│  │                  │          │  └──────────────┘  │       │
│  └──────────────────┘          └────────────────────┘       │
│                                                               │
│  ┌────────────────────────────────────────────────────┐     │
│  │         CALLBACK QUEUE (Macrotask Queue)           │     │
│  │     (setTimeout, setInterval, I/O events)         │     │
│  │                                                    │     │
│  │  ┌──────────────┐  ┌──────────────┐              │     │
│  │  │ setTimeout   │  │ setInterval  │              │     │
│  │  └──────────────┘  └──────────────┘              │     │
│  └────────────────────────────────────────────────────┘     │
│                                                               │
│  ┌────────────────────────────────────────────────────┐     │
│  │          🔄 EVENT LOOP (Manager)                  │     │
│  │  Decides what code executes next                 │     │
│  └────────────────────────────────────────────────────┘     │
│                                                               │
└─────────────────────────────────────────────────────────────┘

              ┌─────────────────────────┐
              │    WEB BROWSER APIs     │
              │ ┌─────────────────────┐ │
              │ │  setTimeout/Interval│ │
              │ │  fetch/XHR          │ │
              │ │  Event Listeners    │ │
              │ │  DOM Operations     │ │
              │ └─────────────────────┘ │
              └─────────────────────────┘
```

---

## Step-by-Step How the Event Loop Works

### The Main Algorithm:

```
WHILE EventLoop.isRunning() {
  
  // Step 1: Execute all synchronous code in Call Stack
  WHILE CallStack.isNotEmpty() {
    ExecuteNextFunction()
  }
  
  // Step 2: Execute all Microtasks (higher priority!)
  WHILE MicrotaskQueue.isNotEmpty() {
    ExecuteNextMicrotask()
  }
  
  // Step 3: Execute ONE Macrotask from Callback Queue
  IF CallbackQueue.isNotEmpty() {
    ExecuteNextCallback()
  }
  
}
```

---

## Detailed Examples

### Example 1: Basic Execution Order

```javascript
console.log("1: Start");

setTimeout(function() {
  console.log("2: Timeout");
}, 0);

Promise.resolve()
  .then(function() {
    console.log("3: Promise");
  });

console.log("4: End");
```

**Output:**
```
1: Start
4: End
3: Promise
2: Timeout
```

**Why this order?**
1. `console.log("1: Start")` → Call Stack → prints immediately
2. `setTimeout()` → sent to Web API → callback goes to Callback Queue
3. `Promise.then()` → callback goes to Microtask Queue
4. `console.log("4: End")` → Call Stack → prints immediately
5. Call Stack is empty → check Microtask Queue
6. Promise callback executes → prints "3: Promise"
7. Check Callback Queue
8. setTimeout callback executes → prints "2: Timeout"

---

### Example 2: Complex Ordering

```javascript
console.log("A");

setTimeout(function() {
  console.log("B");
  Promise.resolve().then(() => console.log("C"));
}, 0);

Promise.resolve()
  .then(function() {
    console.log("D");
    setTimeout(() => console.log("E"), 0);
  })
  .then(function() {
    console.log("F");
  });

console.log("G");
```

**Output:**
```
A
G
D
F
B
C
E
```

**Detailed breakdown:**

| Step | What Happens | Output |
|------|-------------|--------|
| 1 | `console.log("A")` in Call Stack | `A` |
| 2 | `setTimeout()` → Callback Queue | - |
| 3 | `Promise.then()` → Microtask Queue | - |
| 4 | `console.log("G")` in Call Stack | `G` |
| 5 | Call Stack empty → Check Microtask Queue | - |
| 6 | Execute first `.then()` → prints "D" | `D` |
| 7 | `setTimeout()` inside first `.then()` → Callback Queue | - |
| 8 | Continue with second `.then()` → prints "F" | `F` |
| 9 | Microtask Queue empty → Check Callback Queue | - |
| 10 | Execute first setTimeout → prints "B" | `B` |
| 11 | `Promise.then()` inside setTimeout → Microtask Queue | - |
| 12 | Back to Callback Queue → but first check Microtask Queue | - |
| 13 | Execute Promise → prints "C" | `C` |
| 14 | Execute second setTimeout → prints "E" | `E` |

---

### Example 3: Real-World Scenario - Fetching Data

```javascript
console.log("Fetching user data...");

fetch("https://api.example.com/user")
  .then(response => response.json())
  .then(data => {
    console.log("User data:", data);
  })
  .catch(error => {
    console.log("Error:", error);
  });

console.log("Request sent, waiting for response...");
```

**What happens:**
1. `console.log("Fetching...")` → prints immediately
2. `fetch()` → sent to Web API (browser handles the network request)
3. `console.log("Request sent...")` → prints immediately
4. Browser receives response
5. Promise callbacks → go to Microtask Queue
6. `.then()` callbacks → execute in order
7. Data is logged

---

## Priority Order (Most Important!)

```
🟢 HIGH PRIORITY (Microtask Queue)
├─ Promise callbacks (.then, .catch, .finally)
├─ queueMicrotask()
├─ MutationObserver
└─ process.nextTick() [Node.js only]

🟡 MEDIUM PRIORITY (Rendering)
├─ Update the DOM (if needed)
└─ Repaint the screen

🔴 LOW PRIORITY (Callback Queue)
├─ setTimeout
├─ setInterval
├─ setImmediate [Node.js]
├─ I/O operations
└─ UI events (click, scroll, etc)
```

---

## Flowchart of Event Loop

```
START
  ↓
┌─────────────────────┐
│ Call Stack Empty?   │
└─────────────────────┘
  ↓ No              ↓ Yes
Execute          ┌──────────────┐
Next       →   Microtask
Function        Queue Empty?
  ↓             └──────────────┘
Return to        ↓ No        ↓ Yes
top         Execute      ┌────────────┐
            Next         Callback
            Microtask    Queue Empty?
              ↓          └────────────┘
            Return         ↓ No    ↓ Yes
            to top    Execute   Render
                      Next      (if needed)
                      Callback    ↓
                        ↓       Return to
                      Return     top
                      to top
```

---

## Common Gotchas

### Gotcha 1: setTimeout is NOT guaranteed to run at exact time

```javascript
setTimeout(function() {
  console.log("This might not run at exactly 1000ms");
}, 1000);

// If Call Stack and Microtask Queue are busy, setTimeout waits!
```

### Gotcha 2: Microtasks run before the next macrotask

```javascript
setTimeout(() => console.log("A"), 0);
setTimeout(() => console.log("B"), 0);

Promise.resolve().then(() => console.log("C"));
Promise.resolve().then(() => console.log("D"));

// Output: C D A B
// NOT: A B C D
```

### Gotcha 3: Mixing async/await (which uses Microtask Queue)

```javascript
async function example() {
  console.log("1");
  
  await Promise.resolve();
  
  console.log("2");
}

setTimeout(() => console.log("3"), 0);

example();
console.log("4");

// Output: 1 4 2 3
```

---

## Key Takeaways

✅ JavaScript is **single-threaded** but uses the Event Loop for asynchronous operations  
✅ **Call Stack** executes synchronous code  
✅ **Web APIs** handle async operations (setTimeout, fetch, etc)  
✅ **Microtask Queue** has higher priority (Promises)  
✅ **Callback Queue** has lower priority (setTimeout, events)  
✅ Event Loop picks tasks in order: Call Stack → Microtask Queue → Callback Queue  
✅ Microtasks run **before** the next macrotask  

---

## Interview Questions

**Q1: What will be the output?**
```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timeout");
}, 0);

Promise.resolve().then(() => console.log("Promise"));

console.log("End");
```

**A:** Start, End, Promise, Timeout

---

**Q2: Why does setTimeout have a minimum delay of ~4ms in browsers?**

**A:** For performance reasons. Browsers can't process callbacks too frequently or it would waste CPU resources. Even if you set `setTimeout(..., 0)`, it will wait at least 4-5ms before executing.

---

**Q3: What's the difference between Microtask and Macrotask?**

**A:** 
- Microtasks (Promises) run immediately after current code and before rendering
- Macrotasks (setTimeout) run after rendering and other tasks

---

**Q4: Can you explain why this logs in the order it does?**
```javascript
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve()
  .then(() => {
    console.log("3");
    setTimeout(() => console.log("4"), 0);
  })
  .then(() => console.log("5"));

console.log("6");
```

**A:** 
- 1, 6 → Synchronous code
- 3, 5 → Microtasks (Promises)
- 2 → First macrotask (setTimeout from line 1)
- 4 → Second macrotask (setTimeout from inside Promise)

Output: 1, 6, 3, 5, 2, 4

---

## Summary

The Event Loop is what makes JavaScript feel "asynchronous" even though it's single-threaded. Understanding it is crucial for:
- Writing efficient code
- Debugging timing issues
- Working with Promises and async/await
- Optimizing performance
- Acing JavaScript interviews!

Remember: **Call Stack → Microtask Queue → Callback Queue → Repeat!**
