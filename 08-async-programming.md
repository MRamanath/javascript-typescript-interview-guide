# 08. Async Programming in JavaScript

> Asynchronous programming allows JavaScript to perform non-blocking operations.

## 📋 Overview

JavaScript is single-threaded, but asynchronous programming enables it to handle time-consuming operations without blocking the main thread. Understanding async patterns is crucial for modern JavaScript development, especially when working with APIs, file operations, and timers.

**What You'll Learn:**
- ✅ Understanding the event loop
- ✅ Callbacks and callback hell
- ✅ Promises and promise chaining
- ✅ Async/await syntax
- ✅ Error handling in async code
- ✅ Promise.all, Promise.race, and other methods
- ✅ Microtasks vs macrotasks
- ✅ Common async patterns

---

## 🎯 Key Concepts

### 1. Synchronous vs Asynchronous

```javascript
// Synchronous code (blocking)
console.log("Start");

function processData() {
    console.log("Processing...");
    // Simulate heavy computation
    for (let i = 0; i < 1000000000; i++) {}
    console.log("Done processing");
}

processData();
console.log("End");
// Output: Start, Processing..., Done processing, End

// Asynchronous code (non-blocking)
console.log("Start");

setTimeout(() => {
    console.log("Async operation complete");
}, 2000);

console.log("End");
// Output: Start, End, Async operation complete (after 2 seconds)

// Real-world example: File reading
const fs = require('fs');

// Synchronous (blocks)
console.log("Reading file...");
const data = fs.readFileSync('file.txt', 'utf8');
console.log(data);
console.log("File read complete");

// Asynchronous (non-blocking)
console.log("Reading file...");
fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});
console.log("Continuing with other tasks...");
```

---

### 2. Callbacks

The traditional way to handle asynchronous operations.

```javascript
// Basic callback
function fetchData(callback) {
    setTimeout(() => {
        const data = { id: 1, name: "John" };
        callback(data);
    }, 1000);
}

fetchData((data) => {
    console.log("Data received:", data);
});

// Callback with error handling
function fetchUserData(userId, callback) {
    setTimeout(() => {
        if (userId <= 0) {
            callback(new Error("Invalid user ID"), null);
        } else {
            callback(null, { id: userId, name: "Alice" });
        }
    }, 1000);
}

fetchUserData(1, (error, data) => {
    if (error) {
        console.error("Error:", error.message);
        return;
    }
    console.log("User data:", data);
});

// Callback hell (pyramid of doom)
function step1(callback) {
    setTimeout(() => callback(null, "Step 1 complete"), 1000);
}

function step2(callback) {
    setTimeout(() => callback(null, "Step 2 complete"), 1000);
}

function step3(callback) {
    setTimeout(() => callback(null, "Step 3 complete"), 1000);
}

// Nested callbacks - hard to read and maintain
step1((err, result1) => {
    if (err) return console.error(err);
    console.log(result1);
    
    step2((err, result2) => {
        if (err) return console.error(err);
        console.log(result2);
        
        step3((err, result3) => {
            if (err) return console.error(err);
            console.log(result3);
            console.log("All steps complete!");
        });
    });
});

// Better: Named functions
function handleStep3(err, result) {
    if (err) return console.error(err);
    console.log(result);
    console.log("All steps complete!");
}

function handleStep2(err, result) {
    if (err) return console.error(err);
    console.log(result);
    step3(handleStep3);
}

function handleStep1(err, result) {
    if (err) return console.error(err);
    console.log(result);
    step2(handleStep2);
}

step1(handleStep1);
```

---

### 3. Promises

Promises represent the eventual completion or failure of an asynchronous operation.

```javascript
// Creating a promise
const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        const success = true;
        
        if (success) {
            resolve("Operation successful!");
        } else {
            reject(new Error("Operation failed"));
        }
    }, 1000);
});

// Consuming a promise
promise
    .then(result => {
        console.log(result);  // "Operation successful!"
    })
    .catch(error => {
        console.error(error.message);
    })
    .finally(() => {
        console.log("Promise settled");
    });

// Promise states
// 1. Pending: initial state
// 2. Fulfilled: operation completed successfully
// 3. Rejected: operation failed

// Promise chaining
function fetchUser(userId) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (userId > 0) {
                resolve({ id: userId, name: "John" });
            } else {
                reject(new Error("Invalid user ID"));
            }
        }, 1000);
    });
}

function fetchPosts(userId) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve([
                { id: 1, title: "Post 1" },
                { id: 2, title: "Post 2" }
            ]);
        }, 1000);
    });
}

function fetchComments(postId) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve([
                { id: 1, text: "Comment 1" },
                { id: 2, text: "Comment 2" }
            ]);
        }, 1000);
    });
}

// Chaining promises
fetchUser(1)
    .then(user => {
        console.log("User:", user);
        return fetchPosts(user.id);
    })
    .then(posts => {
        console.log("Posts:", posts);
        return fetchComments(posts[0].id);
    })
    .then(comments => {
        console.log("Comments:", comments);
    })
    .catch(error => {
        console.error("Error:", error.message);
    });

// Returning values in then()
Promise.resolve(5)
    .then(num => num * 2)  // 10
    .then(num => num + 3)  // 13
    .then(num => {
        console.log(num);  // 13
    });

// Returning promises in then()
function delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

console.log("Start");
delay(1000)
    .then(() => {
        console.log("After 1 second");
        return delay(1000);
    })
    .then(() => {
        console.log("After 2 seconds");
        return delay(1000);
    })
    .then(() => {
        console.log("After 3 seconds");
    });

// Error handling in chains
fetchUser(-1)
    .then(user => fetchPosts(user.id))
    .then(posts => fetchComments(posts[0].id))
    .catch(error => {
        // Catches errors from any step
        console.error("Error:", error.message);
    })
    .finally(() => {
        console.log("Cleanup");
    });
```

---

### 4. Async/Await

Modern syntax for working with promises, making async code look synchronous.

```javascript
// Basic async/await
async function fetchUserData() {
    const user = await fetchUser(1);
    console.log("User:", user);
    
    const posts = await fetchPosts(user.id);
    console.log("Posts:", posts);
    
    const comments = await fetchComments(posts[0].id);
    console.log("Comments:", comments);
}

fetchUserData();

// Async function always returns a promise
async function getValue() {
    return 42;  // Wrapped in Promise.resolve(42)
}

getValue().then(value => console.log(value));  // 42

// Error handling with try/catch
async function fetchData() {
    try {
        const user = await fetchUser(1);
        const posts = await fetchPosts(user.id);
        const comments = await fetchComments(posts[0].id);
        
        return { user, posts, comments };
    } catch (error) {
        console.error("Error fetching data:", error.message);
        throw error;  // Re-throw if needed
    } finally {
        console.log("Cleanup operations");
    }
}

// Await only works in async functions
// await fetchUser(1);  // SyntaxError

// Top-level await (ES2022, in modules)
// const user = await fetchUser(1);

// Multiple independent async operations
async function fetchMultipleUsers() {
    // Sequential (slow - 3 seconds total)
    const user1 = await fetchUser(1);  // 1 second
    const user2 = await fetchUser(2);  // 1 second
    const user3 = await fetchUser(3);  // 1 second
    
    return [user1, user2, user3];
}

// Parallel execution (faster - 1 second total)
async function fetchMultipleUsersParallel() {
    const promise1 = fetchUser(1);
    const promise2 = fetchUser(2);
    const promise3 = fetchUser(3);
    
    const user1 = await promise1;
    const user2 = await promise2;
    const user3 = await promise3;
    
    return [user1, user2, user3];
}

// Better: Use Promise.all
async function fetchMultipleUsersBest() {
    const users = await Promise.all([
        fetchUser(1),
        fetchUser(2),
        fetchUser(3)
    ]);
    
    return users;
}

// Async arrow functions
const getData = async () => {
    const data = await fetchUser(1);
    return data;
};

// Async methods in objects
const api = {
    async getUser(id) {
        return await fetchUser(id);
    },
    
    async getPosts(userId) {
        return await fetchPosts(userId);
    }
};

// Async methods in classes
class UserService {
    async fetchUserProfile(userId) {
        try {
            const user = await fetchUser(userId);
            const posts = await fetchPosts(userId);
            
            return { user, posts };
        } catch (error) {
            console.error("Error:", error.message);
            return null;
        }
    }
}
```

---

### 5. Promise Methods

```javascript
// Promise.all - wait for all promises (fail-fast)
async function fetchAllData() {
    try {
        const [user, posts, comments] = await Promise.all([
            fetchUser(1),
            fetchPosts(1),
            fetchComments(1)
        ]);
        
        console.log({ user, posts, comments });
    } catch (error) {
        // If any promise rejects, catch handles it
        console.error("Error:", error.message);
    }
}

// Promise.allSettled - wait for all, get all results
async function fetchAllDataSettled() {
    const results = await Promise.allSettled([
        fetchUser(1),
        fetchUser(-1),  // This will fail
        fetchPosts(1)
    ]);
    
    results.forEach((result, index) => {
        if (result.status === "fulfilled") {
            console.log(`Promise ${index} succeeded:`, result.value);
        } else {
            console.log(`Promise ${index} failed:`, result.reason.message);
        }
    });
}

// Promise.race - first to settle wins
async function fetchWithTimeout(promise, timeout) {
    const timeoutPromise = new Promise((_, reject) => {
        setTimeout(() => reject(new Error("Timeout")), timeout);
    });
    
    return Promise.race([promise, timeoutPromise]);
}

// Usage
fetchWithTimeout(fetchUser(1), 2000)
    .then(user => console.log("User:", user))
    .catch(error => console.error("Error:", error.message));

// Promise.any - first fulfilled promise wins
async function fetchFromMultipleSources() {
    try {
        const data = await Promise.any([
            fetch("https://api1.example.com/data"),
            fetch("https://api2.example.com/data"),
            fetch("https://api3.example.com/data")
        ]);
        
        return data;
    } catch (error) {
        // All promises rejected
        console.error("All sources failed:", error);
    }
}

// Promise.resolve and Promise.reject
const resolvedPromise = Promise.resolve(42);
resolvedPromise.then(value => console.log(value));  // 42

const rejectedPromise = Promise.reject(new Error("Failed"));
rejectedPromise.catch(error => console.error(error.message));

// Converting callbacks to promises
function promisify(fn) {
    return function(...args) {
        return new Promise((resolve, reject) => {
            fn(...args, (error, result) => {
                if (error) reject(error);
                else resolve(result);
            });
        });
    };
}

// Usage
const fs = require('fs');
const readFilePromise = promisify(fs.readFile);

readFilePromise('file.txt', 'utf8')
    .then(data => console.log(data))
    .catch(error => console.error(error));
```

---

### 6. Error Handling Patterns

```javascript
// Pattern 1: Try/catch with async/await
async function fetchDataSafely() {
    try {
        const data = await fetchUser(1);
        return data;
    } catch (error) {
        console.error("Error:", error.message);
        return null;  // Provide fallback
    }
}

// Pattern 2: Promise catch
function fetchDataWithCatch() {
    return fetchUser(1)
        .then(data => data)
        .catch(error => {
            console.error("Error:", error.message);
            return null;
        });
}

// Pattern 3: Multiple catch blocks
fetchUser(1)
    .then(user => {
        if (!user) throw new Error("User not found");
        return fetchPosts(user.id);
    })
    .catch(error => {
        // Handle errors from fetchUser and validation
        console.error("User error:", error.message);
        throw error;  // Re-throw for next catch
    })
    .then(posts => {
        return fetchComments(posts[0].id);
    })
    .catch(error => {
        // Handle errors from fetchComments
        console.error("Comments error:", error.message);
    });

// Pattern 4: Custom error classes
class NetworkError extends Error {
    constructor(message) {
        super(message);
        this.name = "NetworkError";
    }
}

class ValidationError extends Error {
    constructor(message) {
        super(message);
        this.name = "ValidationError";
    }
}

async function fetchWithCustomErrors() {
    try {
        const response = await fetch("https://api.example.com/data");
        
        if (!response.ok) {
            throw new NetworkError(`HTTP ${response.status}`);
        }
        
        const data = await response.json();
        
        if (!data.id) {
            throw new ValidationError("Invalid data format");
        }
        
        return data;
    } catch (error) {
        if (error instanceof NetworkError) {
            console.error("Network issue:", error.message);
        } else if (error instanceof ValidationError) {
            console.error("Validation issue:", error.message);
        } else {
            console.error("Unexpected error:", error);
        }
        throw error;
    }
}

// Pattern 5: Error boundaries for multiple operations
async function processMultipleOperations() {
    const results = await Promise.allSettled([
        fetchUser(1),
        fetchUser(2),
        fetchUser(-1)  // Will fail
    ]);
    
    const successful = results
        .filter(r => r.status === "fulfilled")
        .map(r => r.value);
    
    const failed = results
        .filter(r => r.status === "rejected")
        .map(r => r.reason.message);
    
    console.log("Successful:", successful);
    console.log("Failed:", failed);
    
    return successful;
}
```

---

### 7. Advanced Async Patterns

```javascript
// Pattern 1: Retry logic
async function fetchWithRetry(fn, maxRetries = 3, delay = 1000) {
    for (let i = 0; i < maxRetries; i++) {
        try {
            return await fn();
        } catch (error) {
            if (i === maxRetries - 1) throw error;
            
            console.log(`Attempt ${i + 1} failed, retrying...`);
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
}

// Usage
fetchWithRetry(() => fetchUser(1), 3, 1000)
    .then(user => console.log("User:", user))
    .catch(error => console.error("All retries failed:", error));

// Pattern 2: Throttling/Rate limiting
class RateLimiter {
    constructor(maxCalls, interval) {
        this.maxCalls = maxCalls;
        this.interval = interval;
        this.queue = [];
        this.callCount = 0;
    }
    
    async execute(fn) {
        while (this.callCount >= this.maxCalls) {
            await new Promise(resolve => setTimeout(resolve, 100));
        }
        
        this.callCount++;
        
        setTimeout(() => {
            this.callCount--;
        }, this.interval);
        
        return fn();
    }
}

// Usage: Max 5 calls per second
const limiter = new RateLimiter(5, 1000);

async function makeApiCalls() {
    const promises = [];
    
    for (let i = 1; i <= 20; i++) {
        promises.push(
            limiter.execute(() => fetchUser(i))
        );
    }
    
    const results = await Promise.all(promises);
    console.log("All calls completed:", results.length);
}

// Pattern 3: Sequential execution
async function executeSequentially(tasks) {
    const results = [];
    
    for (const task of tasks) {
        const result = await task();
        results.push(result);
    }
    
    return results;
}

// Usage
const tasks = [
    () => fetchUser(1),
    () => fetchUser(2),
    () => fetchUser(3)
];

executeSequentially(tasks)
    .then(results => console.log("Results:", results));

// Pattern 4: Parallel execution with limit
async function executeWithLimit(tasks, limit) {
    const results = [];
    const executing = [];
    
    for (const task of tasks) {
        const promise = task().then(result => {
            executing.splice(executing.indexOf(promise), 1);
            return result;
        });
        
        results.push(promise);
        
        if (executing.length >= limit) {
            await Promise.race(executing);
        }
        
        executing.push(promise);
    }
    
    return Promise.all(results);
}

// Pattern 5: Caching
class AsyncCache {
    constructor() {
        this.cache = new Map();
    }
    
    async get(key, fetchFn, ttl = 60000) {
        if (this.cache.has(key)) {
            const { value, timestamp } = this.cache.get(key);
            
            if (Date.now() - timestamp < ttl) {
                return value;
            }
            
            this.cache.delete(key);
        }
        
        const value = await fetchFn();
        this.cache.set(key, { value, timestamp: Date.now() });
        
        return value;
    }
    
    clear() {
        this.cache.clear();
    }
}

// Usage
const cache = new AsyncCache();

async function getCachedUser(userId) {
    return cache.get(
        `user-${userId}`,
        () => fetchUser(userId),
        60000  // 1 minute TTL
    );
}
```

---

### 8. Event Loop and Microtasks

```javascript
// Understanding execution order
console.log("1: Sync");

setTimeout(() => {
    console.log("2: Timeout (macrotask)");
}, 0);

Promise.resolve().then(() => {
    console.log("3: Promise (microtask)");
});

console.log("4: Sync");

// Output: 1, 4, 3, 2
// Microtasks run before macrotasks!

// Microtasks vs Macrotasks
// Microtasks: Promises, queueMicrotask, MutationObserver
// Macrotasks: setTimeout, setInterval, setImmediate, I/O

// Example with multiple tasks
console.log("Start");

setTimeout(() => console.log("Timeout 1"), 0);
setTimeout(() => console.log("Timeout 2"), 0);

Promise.resolve()
    .then(() => console.log("Promise 1"))
    .then(() => console.log("Promise 2"));

queueMicrotask(() => console.log("Microtask"));

console.log("End");

// Output:
// Start
// End
// Promise 1
// Microtask
// Promise 2
// Timeout 1
// Timeout 2

// Nested promises
Promise.resolve()
    .then(() => {
        console.log("Outer promise");
        
        Promise.resolve()
            .then(() => console.log("Inner promise"));
        
        console.log("After inner promise");
    })
    .then(() => console.log("Outer then 2"));

// Output:
// Outer promise
// After inner promise
// Inner promise
// Outer then 2
```

---

## 💡 Real-World Examples

### Example 1: Fetching User Profile with Related Data

```javascript
class UserAPI {
    async getUserProfile(userId) {
        try {
            // Fetch user details
            const user = await this.fetchUser(userId);
            
            // Fetch related data in parallel
            const [posts, followers, following] = await Promise.all([
                this.fetchUserPosts(userId),
                this.fetchFollowers(userId),
                this.fetchFollowing(userId)
            ]);
            
            return {
                user,
                stats: {
                    posts: posts.length,
                    followers: followers.length,
                    following: following.length
                },
                posts: posts.slice(0, 5),  // Latest 5 posts
                followers: followers.slice(0, 10),
                following: following.slice(0, 10)
            };
        } catch (error) {
            console.error("Error fetching user profile:", error);
            throw new Error("Failed to load user profile");
        }
    }
    
    async fetchUser(userId) {
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error("User not found");
        return response.json();
    }
    
    async fetchUserPosts(userId) {
        const response = await fetch(`/api/users/${userId}/posts`);
        if (!response.ok) return [];
        return response.json();
    }
    
    async fetchFollowers(userId) {
        const response = await fetch(`/api/users/${userId}/followers`);
        if (!response.ok) return [];
        return response.json();
    }
    
    async fetchFollowing(userId) {
        const response = await fetch(`/api/users/${userId}/following`);
        if (!response.ok) return [];
        return response.json();
    }
}

// Usage
const api = new UserAPI();

api.getUserProfile(123)
    .then(profile => {
        console.log("Profile loaded:", profile);
    })
    .catch(error => {
        console.error("Error:", error.message);
    });
```

### Example 2: File Upload with Progress

```javascript
class FileUploader {
    async uploadFile(file, onProgress) {
        const chunkSize = 1024 * 1024;  // 1MB chunks
        const totalChunks = Math.ceil(file.size / chunkSize);
        
        try {
            // Initialize upload
            const uploadId = await this.initializeUpload(file.name, file.size);
            
            // Upload chunks sequentially with progress
            for (let i = 0; i < totalChunks; i++) {
                const start = i * chunkSize;
                const end = Math.min(start + chunkSize, file.size);
                const chunk = file.slice(start, end);
                
                await this.uploadChunk(uploadId, i, chunk);
                
                const progress = ((i + 1) / totalChunks) * 100;
                if (onProgress) onProgress(progress);
            }
            
            // Finalize upload
            const result = await this.finalizeUpload(uploadId);
            
            return result;
        } catch (error) {
            console.error("Upload failed:", error);
            throw error;
        }
    }
    
    async initializeUpload(filename, size) {
        const response = await fetch("/api/upload/init", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ filename, size })
        });
        
        const data = await response.json();
        return data.uploadId;
    }
    
    async uploadChunk(uploadId, chunkNumber, chunk) {
        const formData = new FormData();
        formData.append("uploadId", uploadId);
        formData.append("chunkNumber", chunkNumber);
        formData.append("chunk", chunk);
        
        const response = await fetch("/api/upload/chunk", {
            method: "POST",
            body: formData
        });
        
        if (!response.ok) throw new Error("Chunk upload failed");
    }
    
    async finalizeUpload(uploadId) {
        const response = await fetch("/api/upload/finalize", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ uploadId })
        });
        
        return response.json();
    }
}

// Usage
const uploader = new FileUploader();
const fileInput = document.querySelector('input[type="file"]');

fileInput.addEventListener('change', async (e) => {
    const file = e.target.files[0];
    
    try {
        const result = await uploader.uploadFile(file, (progress) => {
            console.log(`Upload progress: ${progress.toFixed(2)}%`);
        });
        
        console.log("Upload complete:", result);
    } catch (error) {
        console.error("Upload error:", error);
    }
});
```

### Example 3: API Request Queue with Retry

```javascript
class RequestQueue {
    constructor(concurrency = 3) {
        this.concurrency = concurrency;
        this.running = 0;
        this.queue = [];
    }
    
    async enqueue(request, options = {}) {
        return new Promise((resolve, reject) => {
            this.queue.push({
                request,
                resolve,
                reject,
                options,
                retries: 0
            });
            
            this.process();
        });
    }
    
    async process() {
        if (this.running >= this.concurrency || this.queue.length === 0) {
            return;
        }
        
        this.running++;
        const item = this.queue.shift();
        
        try {
            const result = await this.executeWithRetry(item);
            item.resolve(result);
        } catch (error) {
            item.reject(error);
        } finally {
            this.running--;
            this.process();
        }
    }
    
    async executeWithRetry(item) {
        const { request, options, retries } = item;
        const { maxRetries = 3, retryDelay = 1000 } = options;
        
        try {
            return await request();
        } catch (error) {
            if (retries < maxRetries) {
                console.log(`Retry ${retries + 1}/${maxRetries}`);
                
                await new Promise(resolve => 
                    setTimeout(resolve, retryDelay * (retries + 1))
                );
                
                item.retries++;
                return this.executeWithRetry(item);
            }
            
            throw error;
        }
    }
}

// Usage
const queue = new RequestQueue(3);  // Max 3 concurrent requests

const requests = Array.from({ length: 10 }, (_, i) => 
    () => fetchUser(i + 1)
);

async function processRequests() {
    const promises = requests.map(request => 
        queue.enqueue(request, { maxRetries: 3, retryDelay: 1000 })
    );
    
    const results = await Promise.allSettled(promises);
    
    const successful = results.filter(r => r.status === "fulfilled");
    const failed = results.filter(r => r.status === "rejected");
    
    console.log(`Successful: ${successful.length}`);
    console.log(`Failed: ${failed.length}`);
    
    return results;
}

processRequests();
```

---

## 🎤 Interview Questions

### Q1: What is the difference between callbacks, promises, and async/await?

**Answer:**

```javascript
// 1. Callbacks - oldest pattern
function fetchDataCallback(callback) {
    setTimeout(() => {
        callback(null, { data: "result" });
    }, 1000);
}

fetchDataCallback((error, data) => {
    if (error) console.error(error);
    else console.log(data);
});

// Problems: callback hell, hard to read, error handling scattered

// 2. Promises - better error handling and chaining
function fetchDataPromise() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({ data: "result" });
        }, 1000);
    });
}

fetchDataPromise()
    .then(data => console.log(data))
    .catch(error => console.error(error));

// Benefits: chainable, centralized error handling

// 3. Async/await - syntactic sugar over promises
async function fetchDataAsync() {
    try {
        const data = await fetchDataPromise();
        console.log(data);
    } catch (error) {
        console.error(error);
    }
}

// Benefits: looks synchronous, easier to read, try/catch works
```

**Key Differences:**
- Callbacks: Basic pattern, prone to callback hell
- Promises: Better composition, chaining, error handling
- Async/await: Most readable, synchronous-looking code, best for sequential operations

---

### Q2: Explain the event loop and how async code executes.

**Answer:**

```javascript
console.log("1");  // Synchronous

setTimeout(() => {
    console.log("2");  // Macrotask
}, 0);

Promise.resolve().then(() => {
    console.log("3");  // Microtask
});

console.log("4");  // Synchronous

// Output: 1, 4, 3, 2
```

**Explanation:**
1. Call stack executes synchronous code first (1, 4)
2. Microtasks (Promises) execute next (3)
3. Macrotasks (setTimeout) execute last (2)
4. Event loop checks: call stack → microtask queue → macrotask queue

---

### Q3: How do you handle errors in async/await?

**Answer:**

```javascript
// Method 1: try/catch
async function fetchData() {
    try {
        const data = await fetch("/api/data");
        return await data.json();
    } catch (error) {
        console.error("Error:", error.message);
        return null;  // Fallback
    }
}

// Method 2: .catch() on promise
async function fetchData2() {
    const data = await fetch("/api/data").catch(error => {
        console.error("Fetch error:", error);
        return null;
    });
    
    if (!data) return null;
    return data.json();
}

// Method 3: Wrapper function
async function to(promise) {
    return promise
        .then(data => [null, data])
        .catch(error => [error, null]);
}

async function fetchData3() {
    const [error, data] = await to(fetch("/api/data"));
    
    if (error) {
        console.error("Error:", error);
        return null;
    }
    
    return data.json();
}
```

---

### Q4: What's the difference between Promise.all and Promise.allSettled?

**Answer:**

```javascript
// Promise.all - fails fast
async function example1() {
    try {
        const results = await Promise.all([
            Promise.resolve(1),
            Promise.reject("Error"),
            Promise.resolve(3)
        ]);
        console.log(results);  // Never reached
    } catch (error) {
        console.log(error);  // "Error" - stops at first rejection
    }
}

// Promise.allSettled - waits for all
async function example2() {
    const results = await Promise.allSettled([
        Promise.resolve(1),
        Promise.reject("Error"),
        Promise.resolve(3)
    ]);
    
    console.log(results);
    // [
    //   { status: "fulfilled", value: 1 },
    //   { status: "rejected", reason: "Error" },
    //   { status: "fulfilled", value: 3 }
    // ]
}
```

**Use cases:**
- `Promise.all`: When all operations must succeed
- `Promise.allSettled`: When you need results from all operations regardless of success/failure

---

### Q5: How do you implement a retry mechanism?

**Answer:**

```javascript
async function retry(fn, maxAttempts = 3, delay = 1000) {
    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
        try {
            return await fn();
        } catch (error) {
            if (attempt === maxAttempts) {
                throw error;
            }
            
            console.log(`Attempt ${attempt} failed, retrying...`);
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
}

// Usage
retry(() => fetch("/api/data"), 3, 1000)
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error("All retries failed:", error));

// With exponential backoff
async function retryWithBackoff(fn, maxAttempts = 3) {
    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
        try {
            return await fn();
        } catch (error) {
            if (attempt === maxAttempts) throw error;
            
            const delay = Math.pow(2, attempt) * 1000;  // 2s, 4s, 8s...
            console.log(`Retry in ${delay}ms...`);
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
}
```

---

### Q6: How do you cancel async operations?

**Answer:**

```javascript
// Using AbortController
async function fetchWithCancel() {
    const controller = new AbortController();
    const signal = controller.signal;
    
    // Cancel after 5 seconds
    setTimeout(() => controller.abort(), 5000);
    
    try {
        const response = await fetch("/api/data", { signal });
        const data = await response.json();
        console.log(data);
    } catch (error) {
        if (error.name === "AbortError") {
            console.log("Request was cancelled");
        } else {
            console.error("Error:", error);
        }
    }
}

// Custom cancellation token
class CancellationToken {
    constructor() {
        this.cancelled = false;
        this.listeners = [];
    }
    
    cancel() {
        this.cancelled = true;
        this.listeners.forEach(listener => listener());
    }
    
    onCancel(callback) {
        if (this.cancelled) {
            callback();
        } else {
            this.listeners.push(callback);
        }
    }
}

async function longOperation(token) {
    for (let i = 0; i < 10; i++) {
        if (token.cancelled) {
            console.log("Operation cancelled");
            return null;
        }
        
        await new Promise(resolve => setTimeout(resolve, 1000));
        console.log(`Step ${i + 1}`);
    }
    
    return "Complete";
}

// Usage
const token = new CancellationToken();
longOperation(token);

// Cancel after 3 seconds
setTimeout(() => token.cancel(), 3000);
```

---

### Q7: What are microtasks and macrotasks?

**Answer:**

```javascript
// Microtasks: Promises, queueMicrotask, MutationObserver
// Macrotasks: setTimeout, setInterval, I/O operations

console.log("1: Script start");

setTimeout(() => {
    console.log("2: setTimeout (macrotask)");
}, 0);

Promise.resolve()
    .then(() => {
        console.log("3: Promise then (microtask)");
        return Promise.resolve();
    })
    .then(() => {
        console.log("4: Chained then (microtask)");
    });

queueMicrotask(() => {
    console.log("5: queueMicrotask (microtask)");
});

console.log("6: Script end");

// Output: 1, 6, 3, 5, 4, 2

// Execution order:
// 1. Synchronous code (1, 6)
// 2. All microtasks (3, 5, 4)
// 3. Render (if in browser)
// 4. Next macrotask (2)
```

**Key points:**
- Microtasks run before rendering and before next macrotask
- All microtasks execute before moving to next macrotask
- Microtasks can queue more microtasks

---

### Q8: How do you handle multiple async operations in parallel with a limit?

**Answer:**

```javascript
async function parallelLimit(tasks, limit) {
    const results = [];
    const executing = [];
    
    for (const [index, task] of tasks.entries()) {
        // Create promise for task
        const promise = Promise.resolve().then(() => task());
        results.push(promise);
        
        // Remove from executing when done
        const e = promise.then(() => {
            executing.splice(executing.indexOf(e), 1);
        });
        
        executing.push(e);
        
        // Wait if limit reached
        if (executing.length >= limit) {
            await Promise.race(executing);
        }
    }
    
    return Promise.all(results);
}

// Usage
const tasks = Array.from({ length: 10 }, (_, i) => 
    () => fetch(`/api/item/${i}`)
);

// Execute max 3 tasks at a time
parallelLimit(tasks, 3)
    .then(results => console.log("All done:", results.length));

// Alternative: p-limit library
// const pLimit = require('p-limit');
// const limit = pLimit(3);
// 
// const promises = tasks.map(task => limit(() => task()));
// await Promise.all(promises);
```

---

### Q9: Explain Promise.race vs Promise.any?

**Answer:**

```javascript
// Promise.race - returns first settled (fulfilled OR rejected)
async function raceExample() {
    const result = await Promise.race([
        new Promise(resolve => setTimeout(() => resolve("Fast"), 100)),
        new Promise((_, reject) => setTimeout(() => reject("Error"), 50))
    ]);
    // Rejects with "Error" (first to settle)
}

// Promise.any - returns first FULFILLED (ignores rejections)
async function anyExample() {
    const result = await Promise.any([
        new Promise((_, reject) => setTimeout(() => reject("Error 1"), 50)),
        new Promise((_, reject) => setTimeout(() => reject("Error 2"), 75)),
        new Promise(resolve => setTimeout(() => resolve("Success"), 100))
    ]);
    // Resolves with "Success" (first fulfilled)
}

// Use case for race: timeout implementation
async function fetchWithTimeout(url, timeout) {
    return Promise.race([
        fetch(url),
        new Promise((_, reject) => 
            setTimeout(() => reject(new Error("Timeout")), timeout)
        )
    ]);
}

// Use case for any: fallback sources
async function fetchFromMultipleSources(urls) {
    return Promise.any(
        urls.map(url => fetch(url))
    );
}
```

---

### Q10: How do you convert callback-based code to promises?

**Answer:**

```javascript
// Manual conversion
function readFilePromise(path) {
    return new Promise((resolve, reject) => {
        fs.readFile(path, 'utf8', (error, data) => {
            if (error) reject(error);
            else resolve(data);
        });
    });
}

// Generic promisify function
function promisify(fn) {
    return function(...args) {
        return new Promise((resolve, reject) => {
            fn(...args, (error, result) => {
                if (error) reject(error);
                else resolve(result);
            });
        });
    };
}

// Usage
const fs = require('fs');
const readFile = promisify(fs.readFile);

readFile('file.txt', 'utf8')
    .then(data => console.log(data))
    .catch(error => console.error(error));

// Node.js util.promisify
const { promisify: nodePromisify } = require('util');
const readFileAsync = nodePromisify(fs.readFile);

// With async/await
async function readFiles() {
    try {
        const data1 = await readFileAsync('file1.txt', 'utf8');
        const data2 = await readFileAsync('file2.txt', 'utf8');
        return [data1, data2];
    } catch (error) {
        console.error("Error reading files:", error);
    }
}

// Promisify entire module
const fsPromises = Object.fromEntries(
    Object.entries(fs)
        .filter(([key, value]) => typeof value === 'function')
        .map(([key, fn]) => [key, promisify(fn)])
);
```

---

## 📚 Summary

**Key Takeaways:**
- Async programming enables non-blocking operations in JavaScript
- Callbacks → Promises → Async/await (evolution of async patterns)
- Always handle errors in async code (try/catch or .catch())
- Use Promise.all for parallel operations
- Understand microtasks vs macrotasks for execution order
- Implement patterns like retry, rate limiting, and caching for robust code

**Best Practices:**
- ✅ Prefer async/await over promises for readability
- ✅ Use Promise.all for independent parallel operations
- ✅ Always handle errors (try/catch or .catch())
- ✅ Avoid mixing callbacks and promises
- ✅ Use AbortController for cancellable requests
- ✅ Implement timeouts for network operations
- ✅ Consider rate limiting for API calls
- ✅ Cache async results when appropriate

---

**Next Topic:** [09. Modules](./09-modules.md)
