# Async/Await & Asynchronous Programming in JavaScript

## Introduction to Asynchronous Programming

### What is Asynchronous Programming?

**Asynchronous programming** allows your code to perform long-running operations without blocking the execution of other code. Instead of waiting for a task to complete, JavaScript continues executing the next line of code and handles the result when it's ready.

### Why Do We Need Asynchronous Programming?

JavaScript runs in a single-threaded environment. Without asynchronous programming, if a task takes a long time (like fetching data from a server), the entire application would freeze and become unresponsive.

```javascript
// ❌ Synchronous (Blocking) - Bad for slow operations
console.log("Start");
const data = fetchDataFromServer(); // Takes 5 seconds - BLOCKS HERE
console.log("End");                 // Won't run until fetchDataFromServer completes

// ✅ Asynchronous (Non-blocking) - Better
console.log("Start");
fetchDataFromServer().then(data => {
  console.log(data);                // Runs when data is ready
});
console.log("End");                 // Runs immediately
```

---

## The Evolution of Asynchronous JavaScript

There are three main approaches to handle asynchronous operations:

1. **Callbacks** (Older approach)
2. **Promises** (Better approach)
3. **Async/Await** (Modern best practice)

---

## Part 1: Callbacks

### What are Callbacks?

A **callback** is a function passed as an argument to another function, which is executed after a certain operation has been completed.

### Simple Callback Example

```javascript
// Basic callback
function greeting(name) {
  alert('Hello ' + name);
}

function processUserInput(callback) {
  const name = prompt('Please enter your name.');
  callback(name);
}

processUserInput(greeting);
```

### Asynchronous Callback Example

```javascript
// Simulating a server request (takes 2 seconds)
function fetchUser(userId, callback) {
  setTimeout(() => {
    const user = { id: userId, name: 'John Doe', email: 'john@example.com' };
    callback(user);
  }, 2000);
}

// Using the callback
fetchUser(1, function(user) {
  console.log('User fetched:', user);
});

console.log('Fetching user...');
// Output:
// Fetching user...
// User fetched: { id: 1, name: 'John Doe', email: 'john@example.com' }
```

### Callback with Error Handling

```javascript
function fetchUser(userId, successCallback, errorCallback) {
  setTimeout(() => {
    if (userId > 0) {
      const user = { id: userId, name: 'John Doe' };
      successCallback(user);
    } else {
      errorCallback('Invalid user ID');
    }
  }, 2000);
}

// Using callbacks
fetchUser(
  1,
  function(user) {
    console.log('Success:', user);
  },
  function(error) {
    console.log('Error:', error);
  }
);
```

### Callback Hell (Pyramid of Doom)

The biggest problem with callbacks is **callback hell** - when multiple asynchronous operations need to be done in sequence.

```javascript
// ❌ Callback Hell - Hard to read and maintain
getUser(userId, function(err, user) {
  if (err) {
    console.log(err);
  } else {
    getOrders(user.id, function(err, orders) {
      if (err) {
        console.log(err);
      } else {
        getOrderDetails(orders[0].id, function(err, details) {
          if (err) {
            console.log(err);
          } else {
            console.log(details);
          }
        });
      }
    });
  }
});
```

This is hard to read, maintain, and debug. That's why Promises and Async/Await were introduced.

---

## Part 2: Promises

### What is a Promise?

A **Promise** is an object that represents the eventual completion (or failure) of an asynchronous operation and its resulting value. It's a cleaner way to handle asynchronous code than callbacks.

### Promise States

A promise can be in one of three states:

1. **Pending** - Initial state, operation hasn't completed yet
2. **Fulfilled** - Operation completed successfully (resolved)
3. **Rejected** - Operation failed

Once a promise is settled (fulfilled or rejected), it cannot change states.

```javascript
// Pending promise
const promise = new Promise((resolve, reject) => {
  // Pending state here
  setTimeout(() => {
    resolve('Success!'); // Fulfilled state
  }, 2000);
});

// rejected promise
const rejectedPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('Something went wrong'); // Rejected state
  }, 2000);
});
```

### Creating a Promise

```javascript
// Basic promise structure
const promise = new Promise((resolve, reject) => {
  // "resolve" - call this when operation succeeds
  // "reject" - call this when operation fails

  if (operationSucceeds) {
    resolve(value);  // Fulfilled
  } else {
    reject(error);   // Rejected
  }
});
```

### Promise Example: Fetching User

```javascript
// Creating a promise
function fetchUser(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (userId > 0) {
        const user = { id: userId, name: 'John Doe' };
        resolve(user);  // Fulfilled
      } else {
        reject('Invalid user ID'); // Rejected
      }
    }, 2000);
  });
}

// Using the promise
fetchUser(1)
  .then(user => console.log('User:', user))
  .catch(error => console.log('Error:', error));
```

### Promise Chaining (Chaining .then())

```javascript
// Multiple sequential async operations
fetchUser(1)
  .then(user => {
    console.log('Got user:', user);
    return fetchOrders(user.id);
  })
  .then(orders => {
    console.log('Got orders:', orders);
    return fetchOrderDetails(orders[0].id);
  })
  .then(details => {
    console.log('Got details:', details);
  })
  .catch(error => {
    console.log('Error:', error);
  });
```

### .then() Method

The `.then()` method takes up to two arguments: one for success and one for failure.

```javascript
promise
  .then(
    function(result) {
      // Executed if promise is fulfilled
      console.log('Success:', result);
    },
    function(error) {
      // Executed if promise is rejected
      console.log('Error:', error);
    }
  );

// More common - using .catch() for error handling
promise
  .then(result => console.log('Success:', result))
  .catch(error => console.log('Error:', error));
```

### .catch() Method

```javascript
fetchUser(1)
  .then(user => console.log('User:', user))
  .catch(error => console.log('Error:', error));
  // .catch() handles any error from .then() chain
```

### .finally() Method

Executes regardless of whether the promise is fulfilled or rejected.

```javascript
fetchUser(1)
  .then(user => console.log('User:', user))
  .catch(error => console.log('Error:', error))
  .finally(() => {
    console.log('Operation completed'); // Always runs
  });
```

### Promise.all() - Wait for All Promises

```javascript
// Fetch multiple users in parallel and wait for all
Promise.all([
  fetchUser(1),
  fetchUser(2),
  fetchUser(3)
])
  .then(users => {
    console.log('All users fetched:', users);
  })
  .catch(error => {
    console.log('One or more failed:', error);
  });

// If ANY promise rejects, the whole .all() rejects
```

### Promise.race() - Return First Completed Promise

```javascript
// Returns the first promise that settles (resolves or rejects)
Promise.race([
  fetchUser(1),
  fetchUser(2),
  fetchUser(3)
])
  .then(user => console.log('First user:', user))
  .catch(error => console.log('First error:', error));
```

### Promise.allSettled() - All Results (Success or Failure)

```javascript
// Returns all results, whether successful or failed
Promise.allSettled([
  fetchUser(1),
  fetchUser(2),
  fetchUser(3)
])
  .then(results => {
    // results = [
    //   { status: 'fulfilled', value: user1 },
    //   { status: 'rejected', reason: error },
    //   { status: 'fulfilled', value: user3 }
    // ]
    results.forEach(result => {
      if (result.status === 'fulfilled') {
        console.log('Success:', result.value);
      } else {
        console.log('Failed:', result.reason);
      }
    });
  });
```

### Promise.any() - First Fulfilled Promise

```javascript
// Returns the first FULFILLED promise (ignores rejections)
Promise.any([
  Promise.reject('Error 1'),
  Promise.reject('Error 2'),
  Promise.resolve('Success!')
])
  .then(value => console.log(value)) // 'Success!'
  .catch(error => console.log('All failed:', error));
```

---

## Part 3: Async/Await (Modern Approach)

### What is Async/Await?

**Async/await** is syntactic sugar built on top of Promises. It allows you to write asynchronous code that looks and behaves more like synchronous code, making it easier to read and understand.

### The `async` Keyword

An `async` function always returns a Promise.

```javascript
// Basic async function
async function fetchUser(userId) {
  // This function automatically returns a Promise
  return { id: userId, name: 'John Doe' };
}

// Calling the async function
fetchUser(1).then(user => console.log(user));

// Even if you don't explicitly return a Promise, it wraps it
async function greet() {
  return 'Hello!';  // Returns Promise.resolve('Hello!')
}

greet().then(msg => console.log(msg)); // 'Hello!'
```

### The `await` Keyword

The `await` keyword pauses execution until a Promise is settled. It can only be used inside an `async` function.

```javascript
async function getData() {
  // await pauses here until promise resolves
  const user = await fetchUser(1);
  console.log('User:', user); // Only runs after await completes
  
  const orders = await fetchOrders(user.id);
  console.log('Orders:', orders);
  
  return { user, orders };
}
```

### Async/Await vs Promises

```javascript
// Using Promises
function getUser() {
  return fetchUser(1)
    .then(user => {
      return fetchOrders(user.id)
        .then(orders => {
          return { user, orders };
        });
    });
}

// Using Async/Await - Much cleaner!
async function getUser() {
  const user = await fetchUser(1);
  const orders = await fetchOrders(user.id);
  return { user, orders };
}
```

### Error Handling with Async/Await

#### Using Try/Catch

```javascript
async function getUser() {
  try {
    const user = await fetchUser(1);
    console.log('User:', user);
    
    const orders = await fetchOrders(user.id);
    console.log('Orders:', orders);
    
    return { user, orders };
  } catch (error) {
    console.log('Error:', error);
  } finally {
    console.log('Operation completed');
  }
}

getUser();
```

#### Multiple Try/Catch Blocks

```javascript
async function getUser() {
  let user;
  
  // Handle user fetch error separately
  try {
    user = await fetchUser(1);
  } catch (error) {
    console.log('Failed to fetch user:', error);
    return null;
  }
  
  // Handle orders fetch error separately
  try {
    const orders = await fetchOrders(user.id);
    return { user, orders };
  } catch (error) {
    console.log('Failed to fetch orders:', error);
    return { user, orders: [] };
  }
}
```

### Parallel Async Operations

```javascript
// ❌ Slow - Operations run sequentially (one after another)
async function slowFetch() {
  const user = await fetchUser(1);    // 2 seconds
  const orders = await fetchOrders(1); // 2 seconds
  return { user, orders };             // Total: 4 seconds
}

// ✅ Fast - Operations run in parallel
async function fastFetch() {
  const [user, orders] = await Promise.all([
    fetchUser(1),    // 2 seconds
    fetchOrders(1)   // 2 seconds (runs at same time)
  ]);               // Total: 2 seconds
  return { user, orders };
}
```

### Async/Await with Loops

```javascript
// Fetch multiple users sequentially
async function fetchMultipleUsers(userIds) {
  const users = [];
  
  for (const id of userIds) {
    const user = await fetchUser(id);  // Waits for each
    users.push(user);
  }
  
  return users;
}

// Fetch multiple users in parallel
async function fetchMultipleUsersParallel(userIds) {
  const promises = userIds.map(id => fetchUser(id));
  const users = await Promise.all(promises);
  return users;
}

// Using async with map
async function processItems(items) {
  return Promise.all(
    items.map(async item => {
      return await processItem(item);
    })
  );
}
```

---

## Real-World Examples

### Example 1: Fetching Data from an API

```javascript
// Simulating API call
function fetchFromAPI(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url) {
        resolve({ data: 'Sample data from ' + url });
      } else {
        reject('Invalid URL');
      }
    }, 1000);
  });
}

// Using async/await
async function loadData() {
  try {
    console.log('Loading...');
    const response = await fetchFromAPI('/api/users');
    console.log('Data loaded:', response.data);
  } catch (error) {
    console.log('Failed to load:', error);
  } finally {
    console.log('Loading complete');
  }
}

loadData();
```

### Example 2: Database Operations

```javascript
async function getUserProfile(userId) {
  try {
    // Fetch user from database
    const user = await db.users.findById(userId);
    
    if (!user) {
      throw new Error('User not found');
    }
    
    // Fetch user's posts
    const posts = await db.posts.findByUserId(userId);
    
    // Fetch user's friends
    const friends = await db.friends.findByUserId(userId);
    
    return {
      user,
      posts,
      friends
    };
  } catch (error) {
    console.log('Error fetching profile:', error);
    throw error; // Re-throw for caller to handle
  }
}

// Calling the async function
getUserProfile(1)
  .then(profile => console.log('Profile:', profile))
  .catch(error => console.log('Error:', error));
```

### Example 3: Sequential API Calls with Dependencies

```javascript
async function createUserWithSettings(userData) {
  try {
    // Step 1: Create user
    const user = await createUser(userData);
    console.log('User created:', user);
    
    // Step 2: Create settings for the user (depends on user.id)
    const settings = await createSettings(user.id, { theme: 'dark' });
    console.log('Settings created:', settings);
    
    // Step 3: Send welcome email (depends on user.email)
    await sendEmail(user.email, 'Welcome!');
    console.log('Email sent');
    
    return { user, settings };
  } catch (error) {
    console.log('Error:', error);
    // Could roll back changes here
  }
}
```

### Example 4: Retrying Failed Operations

```javascript
async function fetchWithRetry(url, maxRetries = 3) {
  let lastError;
  
  for (let i = 0; i < maxRetries; i++) {
    try {
      console.log(`Attempt ${i + 1}/${maxRetries}`);
      const response = await fetch(url);
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }
      
      return await response.json();
    } catch (error) {
      lastError = error;
      console.log(`Attempt ${i + 1} failed:`, error);
      
      // Wait before retrying (exponential backoff)
      await new Promise(resolve => 
        setTimeout(resolve, Math.pow(2, i) * 1000)
      );
    }
  }
  
  throw lastError;
}

// Usage
fetchWithRetry('https://api.example.com/data')
  .then(data => console.log('Success:', data))
  .catch(error => console.log('All retries failed:', error));
```

### Example 5: Parallel Requests with Error Handling

```javascript
async function loadDashboard(userId) {
  try {
    // Run all requests in parallel
    const results = await Promise.allSettled([
      fetchUser(userId),
      fetchUserPosts(userId),
      fetchUserStats(userId),
      fetchUserNotifications(userId)
    ]);
    
    // Process results
    const [userResult, postsResult, statsResult, notifResult] = results;
    
    const dashboard = {};
    
    if (userResult.status === 'fulfilled') {
      dashboard.user = userResult.value;
    } else {
      dashboard.userError = userResult.reason;
    }
    
    if (postsResult.status === 'fulfilled') {
      dashboard.posts = postsResult.value;
    } else {
      dashboard.postsError = postsResult.reason;
    }
    
    // ... handle other results similarly
    
    return dashboard;
  } catch (error) {
    console.log('Unexpected error:', error);
  }
}
```

---

## Common Patterns and Best Practices

### Pattern 1: Avoid Nested Promises in Async/Await

```javascript
// ❌ Bad - Mixing .then() with async/await
async function getUser() {
  return fetchUser(1).then(user => {
    return fetchOrders(user.id).then(orders => {
      return { user, orders };
    });
  });
}

// ✅ Good - Pure async/await
async function getUser() {
  const user = await fetchUser(1);
  const orders = await fetchOrders(user.id);
  return { user, orders };
}
```

### Pattern 2: Executing Async Functions Immediately

```javascript
// Pattern 1: IIFE (Immediately Invoked Function Expression)
(async () => {
  const user = await fetchUser(1);
  console.log(user);
})();

// Pattern 2: Top-level await (ES2022+)
const user = await fetchUser(1);
console.log(user);
```

### Pattern 3: Timeout for Promises

```javascript
function promiseWithTimeout(promise, timeoutMs) {
  return Promise.race([
    promise,
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error('Timeout')), timeoutMs)
    )
  ]);
}

async function fetchWithTimeout() {
  try {
    const data = await promiseWithTimeout(
      fetchUser(1),
      5000  // 5 second timeout
    );
    console.log(data);
  } catch (error) {
    console.log('Error:', error);
  }
}
```

### Pattern 4: Conditional Async Operations

```javascript
async function conditionalFetch(condition) {
  try {
    if (condition) {
      const data = await fetchData();
      return data;
    } else {
      return getCachedData();  // Can also be async
    }
  } catch (error) {
    console.log('Error:', error);
  }
}
```

---

## Common Pitfalls and Mistakes

### Pitfall 1: Forgetting to Await

```javascript
// ❌ Wrong - Returns a Promise, not the data
async function getUser() {
  const user = fetchUser(1);  // Missing await!
  console.log(user);          // Prints Promise { <pending> }
  return user;
}

// ✅ Correct
async function getUser() {
  const user = await fetchUser(1);
  console.log(user);          // Prints actual user data
  return user;
}
```

### Pitfall 2: Not Handling Errors

```javascript
// ❌ Bad - No error handling
async function getData() {
  const data = await fetchData();
  return data;
  // If fetchData() fails, error is unhandled
}

// ✅ Good - Proper error handling
async function getData() {
  try {
    const data = await fetchData();
    return data;
  } catch (error) {
    console.log('Error:', error);
    return null;  // or throw to let caller handle
  }
}
```

### Pitfall 3: Sequential Instead of Parallel

```javascript
// ❌ Slow - Takes 6 seconds (2+2+2)
async function slowProcess() {
  const user = await fetchUser(1);      // 2 seconds
  const orders = await fetchOrders(1);  // 2 seconds
  const posts = await fetchPosts(1);    // 2 seconds
  return { user, orders, posts };
}

// ✅ Fast - Takes 2 seconds (all parallel)
async function fastProcess() {
  const [user, orders, posts] = await Promise.all([
    fetchUser(1),
    fetchOrders(1),
    fetchPosts(1)
  ]);
  return { user, orders, posts };
}
```

### Pitfall 4: Not Returning from Async Function

```javascript
// ❌ Problem - Caller can't wait for completion
async function setupUser(userId) {
  const user = await fetchUser(userId);
  const settings = await createSettings(user.id);
  // Function completes, but no return value
}

// ✅ Better - Clear return value
async function setupUser(userId) {
  const user = await fetchUser(userId);
  const settings = await createSettings(user.id);
  return { user, settings };  // Return for caller
}
```

### Pitfall 5: Async Function in Array.map Without Handling

```javascript
// ❌ Problem - Promises not awaited
const users = [1, 2, 3];
users.map(async (id) => {
  await fetchUser(id);  // Promise not awaited by map
});

// ✅ Correct - Use Promise.all
const users = [1, 2, 3];
await Promise.all(
  users.map(async (id) => {
    return await fetchUser(id);
  })
);
```

---

## Comparison: Callbacks vs Promises vs Async/Await

```javascript
// Same operation with different approaches

// Callbacks (Old)
function getUser(id, callback) {
  setTimeout(() => {
    callback({ id, name: 'John' });
  }, 1000);
}

getUser(1, function(user) {
  console.log('User:', user);
});

// Promises (Better)
function getUser(id) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve({ id, name: 'John' });
    }, 1000);
  });
}

getUser(1)
  .then(user => console.log('User:', user));

// Async/Await (Best)
async function getUser() {
  const user = await new Promise(resolve => {
    setTimeout(() => {
      resolve({ id: 1, name: 'John' });
    }, 1000);
  });
  console.log('User:', user);
}

getUser();
```

---

## Event Loop and How Async Works (Bonus)

### Understanding the Event Loop

JavaScript has an event loop that manages execution:

1. **Call Stack** - Where functions are executed
2. **Task Queue** - Where asynchronous callbacks wait
3. **Microtask Queue** - Higher priority than task queue (Promises use this)

```javascript
console.log('1. Start');

setTimeout(() => {
  console.log('2. Timeout (Task Queue)');
}, 0);

Promise.resolve()
  .then(() => {
    console.log('3. Promise (Microtask Queue)');
  });

console.log('4. End');

// Output:
// 1. Start
// 4. End
// 3. Promise (Microtask Queue)
// 2. Timeout (Task Queue)
```

### Why Promises are Faster

Microtasks (Promises) are executed before macrotasks (setTimeout, setInterval):

```javascript
console.log('Start');

// Macrotask
setTimeout(() => console.log('setTimeout'), 0);

// Microtasks (higher priority)
Promise.resolve().then(() => console.log('Promise 1'));
Promise.resolve().then(() => console.log('Promise 2'));

console.log('End');

// Output:
// Start
// End
// Promise 1
// Promise 2
// setTimeout
```

---

## Best Practices Summary

1. **Always use `async/await`** for new code - It's cleaner and easier to understand
2. **Use `try/catch`** for error handling - Not `.catch()`
3. **Use `Promise.all()`** for parallel operations - Not sequential awaits
4. **Always `await`** promises - Don't forget the await keyword
5. **Handle errors properly** - Don't let promises reject silently
6. **Avoid mixing** callbacks, promises, and async/await
7. **Return from async functions** - Make it clear what the result is
8. **Use `Promise.allSettled()`** when you need all results, even failures
9. **Keep try/catch blocks focused** - Don't catch too much
10. **Test edge cases** - Timeouts, rejections, empty responses

---

## Summary

| Approach | Pros | Cons |
|----------|------|------|
| **Callbacks** | Simple for basic cases | Callback hell, hard to read |
| **Promises** | Better than callbacks, chainable | Still verbose for complex flows |
| **Async/Await** | Clean, readable, synchronous-like | Requires try/catch for errors |

**Modern JavaScript uses Async/Await!**

