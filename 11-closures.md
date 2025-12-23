# 11. Closures in JavaScript

> Closures are functions that have access to variables from an outer function's scope.

## 📋 Overview

Closures are one of the most powerful and fundamental concepts in JavaScript. They enable data privacy, function factories, and many functional programming patterns. Understanding closures is essential for mastering JavaScript.

**What You'll Learn:**
- ✅ What closures are and how they work
- ✅ Lexical scope and scope chain
- ✅ Practical use cases for closures
- ✅ Data privacy with closures
- ✅ Function factories and currying
- ✅ Common closure patterns
- ✅ Memory considerations
- ✅ Closure gotchas and solutions

---

## 🎯 Key Concepts

### 1. Understanding Closures

```javascript
// Basic closure example
function outer() {
    const message = "Hello";
    
    function inner() {
        console.log(message);  // Can access outer's variable
    }
    
    return inner;
}

const myFunction = outer();
myFunction();  // "Hello" - closure remembers 'message'

// Closure definition:
// A function that has access to variables from its outer (enclosing) scope,
// even after the outer function has finished executing

// Another example
function createCounter() {
    let count = 0;
    
    return function() {
        count++;
        return count;
    };
}

const counter = createCounter();
console.log(counter());  // 1
console.log(counter());  // 2
console.log(counter());  // 3
// 'count' persists between calls!

// Multiple closures, separate scopes
const counter1 = createCounter();
const counter2 = createCounter();

console.log(counter1());  // 1
console.log(counter1());  // 2
console.log(counter2());  // 1 - separate scope!
console.log(counter2());  // 2

// Closures with parameters
function createMultiplier(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Why closures work: Lexical scoping
function outer() {
    const x = 10;
    
    function inner() {
        // 'inner' has access to 'x' because of lexical scoping
        // Closure = function + its lexical environment
        console.log(x);
    }
    
    return inner;
}

// Even though 'outer' has finished, 'inner' remembers 'x'
const fn = outer();
fn();  // 10
```

---

### 2. Lexical Scope

```javascript
// Lexical scope: Inner functions have access to outer scopes

const global = "global";

function level1() {
    const l1 = "level1";
    
    function level2() {
        const l2 = "level2";
        
        function level3() {
            const l3 = "level3";
            
            // Can access all outer scopes
            console.log(global);  // "global"
            console.log(l1);      // "level1"
            console.log(l2);      // "level2"
            console.log(l3);      // "level3"
        }
        
        level3();
    }
    
    level2();
}

level1();

// Scope chain
function outer() {
    const outerVar = "outer";
    
    function middle() {
        const middleVar = "middle";
        
        function inner() {
            const innerVar = "inner";
            
            // JavaScript looks up the scope chain:
            // 1. Check inner scope
            // 2. Check middle scope
            // 3. Check outer scope
            // 4. Check global scope
            console.log(innerVar);   // Found in inner
            console.log(middleVar);  // Found in middle
            console.log(outerVar);   // Found in outer
        }
        
        return inner;
    }
    
    return middle();
}

const fn = outer();
fn();

// Block scope with closures
for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 3, 3, 3 (var is function-scoped)
    }, 100);
}

// Fix with let (block-scoped)
for (let i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 0, 1, 2 (each iteration has own scope)
    }, 100);
}

// Fix with IIFE (Immediately Invoked Function Expression)
for (var i = 0; i < 3; i++) {
    (function(j) {
        setTimeout(function() {
            console.log(j);  // 0, 1, 2
        }, 100);
    })(i);
}

// Shadowing
const x = 10;

function demo() {
    const x = 20;  // Shadows outer x
    
    console.log(x);  // 20 (inner x)
}

demo();
console.log(x);  // 10 (outer x)
```

---

### 3. Data Privacy

```javascript
// Private variables with closures

function createBankAccount(initialBalance) {
    let balance = initialBalance;  // Private variable
    
    return {
        deposit(amount) {
            if (amount > 0) {
                balance += amount;
                return balance;
            }
            throw new Error("Invalid amount");
        },
        
        withdraw(amount) {
            if (amount > 0 && amount <= balance) {
                balance -= amount;
                return balance;
            }
            throw new Error("Invalid amount or insufficient funds");
        },
        
        getBalance() {
            return balance;
        }
    };
}

const account = createBankAccount(1000);

console.log(account.getBalance());  // 1000
account.deposit(500);
console.log(account.getBalance());  // 1500
account.withdraw(200);
console.log(account.getBalance());  // 1300

// Cannot access balance directly
console.log(account.balance);  // undefined

// Module pattern
const calculator = (function() {
    // Private variables and functions
    let result = 0;
    
    function validateNumber(num) {
        if (typeof num !== 'number') {
            throw new TypeError("Argument must be a number");
        }
    }
    
    // Public API
    return {
        add(num) {
            validateNumber(num);
            result += num;
            return this;
        },
        
        subtract(num) {
            validateNumber(num);
            result -= num;
            return this;
        },
        
        multiply(num) {
            validateNumber(num);
            result *= num;
            return this;
        },
        
        divide(num) {
            validateNumber(num);
            if (num === 0) throw new Error("Division by zero");
            result /= num;
            return this;
        },
        
        getResult() {
            return result;
        },
        
        reset() {
            result = 0;
            return this;
        }
    };
})();

calculator.add(10).multiply(2).subtract(5);
console.log(calculator.getResult());  // 15

// Private methods
function createUser(name, email) {
    let password;
    
    function hashPassword(pwd) {
        // Private helper function
        return pwd.split('').reverse().join('');  // Simplified
    }
    
    return {
        getName() {
            return name;
        },
        
        getEmail() {
            return email;
        },
        
        setPassword(pwd) {
            password = hashPassword(pwd);
        },
        
        validatePassword(pwd) {
            return hashPassword(pwd) === password;
        }
    };
}

const user = createUser("John", "john@example.com");
user.setPassword("secret123");
console.log(user.validatePassword("secret123"));  // true
console.log(user.validatePassword("wrong"));      // false

// Cannot access private methods
console.log(user.hashPassword);  // undefined
```

---

### 4. Function Factories

```javascript
// Creating specialized functions

function createGreeter(greeting) {
    return function(name) {
        return `${greeting}, ${name}!`;
    };
}

const sayHello = createGreeter("Hello");
const sayHi = createGreeter("Hi");
const sayGoodbye = createGreeter("Goodbye");

console.log(sayHello("Alice"));    // "Hello, Alice!"
console.log(sayHi("Bob"));         // "Hi, Bob!"
console.log(sayGoodbye("Charlie")); // "Goodbye, Charlie!"

// Power function factory
function createPower(exponent) {
    return function(base) {
        return Math.pow(base, exponent);
    };
}

const square = createPower(2);
const cube = createPower(3);

console.log(square(5));  // 25
console.log(cube(3));    // 27

// Tax calculator factory
function createTaxCalculator(taxRate) {
    return function(amount) {
        return amount * (1 + taxRate);
    };
}

const calculateWithVAT = createTaxCalculator(0.20);  // 20% VAT
const calculateWithSalesTax = createTaxCalculator(0.08);  // 8% sales tax

console.log(calculateWithVAT(100));        // 120
console.log(calculateWithSalesTax(100));   // 108

// Validator factory
function createValidator(regex, message) {
    return function(value) {
        if (!regex.test(value)) {
            throw new Error(message);
        }
        return true;
    };
}

const validateEmail = createValidator(
    /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
    "Invalid email format"
);

const validatePhone = createValidator(
    /^\d{3}-\d{3}-\d{4}$/,
    "Invalid phone format (xxx-xxx-xxxx)"
);

validateEmail("test@example.com");  // true
// validateEmail("invalid");  // Error: Invalid email format

// Logger factory with levels
function createLogger(prefix) {
    return {
        info(message) {
            console.log(`[${prefix}] INFO: ${message}`);
        },
        warn(message) {
            console.warn(`[${prefix}] WARN: ${message}`);
        },
        error(message) {
            console.error(`[${prefix}] ERROR: ${message}`);
        }
    };
}

const userLogger = createLogger("USER");
const authLogger = createLogger("AUTH");

userLogger.info("User logged in");
authLogger.error("Authentication failed");
```

---

### 5. Currying and Partial Application

```javascript
// Currying: Transform f(a, b, c) into f(a)(b)(c)

function add(a) {
    return function(b) {
        return function(c) {
            return a + b + c;
        };
    };
}

console.log(add(1)(2)(3));  // 6

// Arrow function currying (cleaner)
const multiply = a => b => c => a * b * c;

console.log(multiply(2)(3)(4));  // 24

// Practical currying
function log(level) {
    return function(message) {
        return function(timestamp) {
            console.log(`[${timestamp}] [${level}]: ${message}`);
        };
    };
}

const infoLog = log("INFO");
const errorLog = log("ERROR");

infoLog("User logged in")(new Date().toISOString());
errorLog("Database error")(new Date().toISOString());

// Generic curry function
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        } else {
            return function(...moreArgs) {
                return curried.apply(this, args.concat(moreArgs));
            };
        }
    };
}

function sum(a, b, c) {
    return a + b + c;
}

const curriedSum = curry(sum);

console.log(curriedSum(1)(2)(3));     // 6
console.log(curriedSum(1, 2)(3));     // 6
console.log(curriedSum(1, 2, 3));     // 6

// Partial application
function partial(fn, ...fixedArgs) {
    return function(...remainingArgs) {
        return fn.apply(this, fixedArgs.concat(remainingArgs));
    };
}

function greet(greeting, name, punctuation) {
    return `${greeting}, ${name}${punctuation}`;
}

const sayHello = partial(greet, "Hello");
const sayHelloExcited = partial(greet, "Hello", "World");

console.log(sayHello("Alice", "!"));         // "Hello, Alice!"
console.log(sayHelloExcited("!!!"));         // "Hello, World!!!"

// Practical example: fetch with base URL
function createApiClient(baseUrl) {
    return function(endpoint) {
        return function(options = {}) {
            return fetch(`${baseUrl}${endpoint}`, options);
        };
    };
}

const api = createApiClient("https://api.example.com");
const getUsers = api("/users");
const getPosts = api("/posts");

// getUsers().then(res => res.json()).then(console.log);
// getPosts().then(res => res.json()).then(console.log);
```

---

### 6. Closure Patterns

```javascript
// Pattern 1: Counter
function createCounter(initial = 0) {
    let count = initial;
    
    return {
        increment() {
            return ++count;
        },
        decrement() {
            return --count;
        },
        get() {
            return count;
        },
        reset() {
            count = initial;
            return count;
        }
    };
}

// Pattern 2: Once function (execute only once)
function once(fn) {
    let called = false;
    let result;
    
    return function(...args) {
        if (!called) {
            called = true;
            result = fn.apply(this, args);
        }
        return result;
    };
}

const initializeApp = once(() => {
    console.log("App initialized");
    return { initialized: true };
});

initializeApp();  // "App initialized"
initializeApp();  // (nothing, already called)

// Pattern 3: Memoization
function memoize(fn) {
    const cache = new Map();
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (cache.has(key)) {
            console.log("Cache hit!");
            return cache.get(key);
        }
        
        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

function fibonacci(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

const memoizedFib = memoize(fibonacci);

console.log(memoizedFib(10));  // Calculated
console.log(memoizedFib(10));  // Cache hit!

// Pattern 4: Debounce
function debounce(fn, delay) {
    let timeoutId;
    
    return function(...args) {
        clearTimeout(timeoutId);
        
        timeoutId = setTimeout(() => {
            fn.apply(this, args);
        }, delay);
    };
}

const handleSearch = debounce((query) => {
    console.log("Searching for:", query);
}, 500);

// handleSearch("a");     // Cancelled
// handleSearch("ab");    // Cancelled
// handleSearch("abc");   // Executes after 500ms

// Pattern 5: Throttle
function throttle(fn, limit) {
    let inThrottle;
    
    return function(...args) {
        if (!inThrottle) {
            fn.apply(this, args);
            inThrottle = true;
            
            setTimeout(() => {
                inThrottle = false;
            }, limit);
        }
    };
}

const handleScroll = throttle(() => {
    console.log("Scroll event");
}, 1000);

// Pattern 6: Rate limiter
function createRateLimiter(maxCalls, timeWindow) {
    const calls = [];
    
    return function(fn) {
        const now = Date.now();
        
        // Remove old calls outside time window
        while (calls.length > 0 && calls[0] < now - timeWindow) {
            calls.shift();
        }
        
        if (calls.length < maxCalls) {
            calls.push(now);
            return fn();
        } else {
            throw new Error("Rate limit exceeded");
        }
    };
}

const limiter = createRateLimiter(3, 1000);  // 3 calls per second

// Pattern 7: Iterator with closure
function createIterator(array) {
    let index = 0;
    
    return {
        next() {
            if (index < array.length) {
                return { value: array[index++], done: false };
            }
            return { done: true };
        },
        
        hasNext() {
            return index < array.length;
        },
        
        reset() {
            index = 0;
        }
    };
}

const iterator = createIterator([1, 2, 3]);

console.log(iterator.next());  // { value: 1, done: false }
console.log(iterator.next());  // { value: 2, done: false }
console.log(iterator.next());  // { value: 3, done: false }
console.log(iterator.next());  // { done: true }
```

---

### 7. Practical Examples

```javascript
// Example 1: Private state management
function createStore(initialState) {
    let state = { ...initialState };
    const listeners = [];
    
    return {
        getState() {
            return { ...state };  // Return copy
        },
        
        setState(newState) {
            state = { ...state, ...newState };
            listeners.forEach(listener => listener(state));
        },
        
        subscribe(listener) {
            listeners.push(listener);
            
            // Return unsubscribe function
            return function unsubscribe() {
                const index = listeners.indexOf(listener);
                if (index > -1) {
                    listeners.splice(index, 1);
                }
            };
        }
    };
}

const store = createStore({ count: 0, user: null });

const unsubscribe = store.subscribe((state) => {
    console.log("State changed:", state);
});

store.setState({ count: 1 });  // "State changed: { count: 1, user: null }"
store.setState({ user: "John" });

unsubscribe();  // Stop listening

// Example 2: Event emitter
function createEventEmitter() {
    const events = new Map();
    
    return {
        on(event, handler) {
            if (!events.has(event)) {
                events.set(event, []);
            }
            events.get(event).push(handler);
            
            return () => this.off(event, handler);
        },
        
        off(event, handler) {
            if (!events.has(event)) return;
            
            const handlers = events.get(event);
            const index = handlers.indexOf(handler);
            
            if (index > -1) {
                handlers.splice(index, 1);
            }
        },
        
        emit(event, data) {
            if (!events.has(event)) return;
            
            events.get(event).forEach(handler => {
                handler(data);
            });
        },
        
        once(event, handler) {
            const wrapper = (data) => {
                handler(data);
                this.off(event, wrapper);
            };
            
            this.on(event, wrapper);
        }
    };
}

const emitter = createEventEmitter();

emitter.on("login", (user) => {
    console.log("User logged in:", user);
});

emitter.emit("login", { name: "John" });

// Example 3: Retry mechanism
function createRetry(maxAttempts, delay) {
    return async function(fn) {
        let lastError;
        
        for (let attempt = 1; attempt <= maxAttempts; attempt++) {
            try {
                return await fn();
            } catch (error) {
                lastError = error;
                
                if (attempt < maxAttempts) {
                    console.log(`Attempt ${attempt} failed, retrying...`);
                    await new Promise(resolve => setTimeout(resolve, delay));
                }
            }
        }
        
        throw new Error(`Failed after ${maxAttempts} attempts: ${lastError.message}`);
    };
}

const retry = createRetry(3, 1000);

// Usage
async function fetchData() {
    const response = await fetch("https://api.example.com/data");
    return response.json();
}

// retry(fetchData).then(data => console.log(data));

// Example 4: Circuit breaker
function createCircuitBreaker(failureThreshold, timeout) {
    let failureCount = 0;
    let lastFailureTime = null;
    let state = "CLOSED";  // CLOSED, OPEN, HALF_OPEN
    
    return function(fn) {
        return async function(...args) {
            if (state === "OPEN") {
                if (Date.now() - lastFailureTime >= timeout) {
                    state = "HALF_OPEN";
                } else {
                    throw new Error("Circuit breaker is OPEN");
                }
            }
            
            try {
                const result = await fn.apply(this, args);
                
                if (state === "HALF_OPEN") {
                    state = "CLOSED";
                }
                
                failureCount = 0;
                return result;
            } catch (error) {
                failureCount++;
                lastFailureTime = Date.now();
                
                if (failureCount >= failureThreshold) {
                    state = "OPEN";
                }
                
                throw error;
            }
        };
    };
}

const breaker = createCircuitBreaker(3, 5000);

const protectedFetch = breaker(fetch);

// Example 5: Cache with TTL
function createCache(ttl = 60000) {
    const cache = new Map();
    
    return {
        set(key, value) {
            cache.set(key, {
                value,
                expires: Date.now() + ttl
            });
        },
        
        get(key) {
            const item = cache.get(key);
            
            if (!item) return null;
            
            if (Date.now() > item.expires) {
                cache.delete(key);
                return null;
            }
            
            return item.value;
        },
        
        has(key) {
            return this.get(key) !== null;
        },
        
        delete(key) {
            cache.delete(key);
        },
        
        clear() {
            cache.clear();
        },
        
        size() {
            return cache.size;
        }
    };
}

const cache = createCache(5000);  // 5 second TTL

cache.set("user", { id: 1, name: "John" });
console.log(cache.get("user"));  // { id: 1, name: "John" }

setTimeout(() => {
    console.log(cache.get("user"));  // null (expired)
}, 6000);
```

---

### 8. Common Gotchas

```javascript
// Gotcha 1: Loop closures with var
console.log("=== Gotcha 1: Loop closures ===");

for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 3, 3, 3 (not 0, 1, 2)
    }, 100);
}

// Why? All closures share the same 'i' variable
// After loop ends, i = 3

// Solution 1: Use let (block scope)
for (let i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 0, 1, 2
    }, 100);
}

// Solution 2: IIFE creates new scope
for (var i = 0; i < 3; i++) {
    (function(j) {
        setTimeout(function() {
            console.log(j);  // 0, 1, 2
        }, 100);
    })(i);
}

// Gotcha 2: Modifying closure variable
function createFunctions() {
    const functions = [];
    
    for (var i = 0; i < 3; i++) {
        functions.push(function() {
            return i;
        });
    }
    
    return functions;
}

const fns = createFunctions();
console.log(fns[0]());  // 3 (not 0)
console.log(fns[1]());  // 3 (not 1)
console.log(fns[2]());  // 3 (not 2)

// Fix with let or immediate capture
function createFunctionsFixed() {
    const functions = [];
    
    for (let i = 0; i < 3; i++) {
        functions.push(function() {
            return i;
        });
    }
    
    return functions;
}

// Gotcha 3: Memory leaks
function createHeavyObject() {
    const largeArray = new Array(1000000).fill("data");
    
    return function() {
        // Even if we don't use largeArray,
        // it's kept in memory because of closure
        console.log("Function called");
    };
}

// Solution: Don't capture unnecessary variables
function createHeavyObjectFixed() {
    const largeArray = new Array(1000000).fill("data");
    const needed = largeArray.length;  // Only capture what's needed
    
    return function() {
        console.log(`Array was ${needed} items`);
    };
}

// Gotcha 4: 'this' in closures
const obj = {
    value: 42,
    
    getValue: function() {
        return function() {
            return this.value;  // 'this' is not obj!
        };
    }
};

const fn = obj.getValue();
console.log(fn());  // undefined

// Solution 1: Arrow function (lexical this)
const obj2 = {
    value: 42,
    
    getValue: function() {
        return () => {
            return this.value;  // 'this' is obj2
        };
    }
};

// Solution 2: Save this reference
const obj3 = {
    value: 42,
    
    getValue: function() {
        const self = this;
        
        return function() {
            return self.value;
        };
    }
};

// Gotcha 5: Accidental global variable in closure
function createCounter() {
    // Missing 'let/const/var'
    count = 0;  // Creates global variable!
    
    return function() {
        return ++count;
    };
}

// Solution: Always declare variables
function createCounterFixed() {
    let count = 0;  // Proper declaration
    
    return function() {
        return ++count;
    };
}
```

---

## 💡 Interview Questions

### Q1: What is a closure and how does it work?

**Answer:**

A closure is a function that has access to variables from its outer (enclosing) function's scope, even after the outer function has finished executing.

```javascript
function outer() {
    const message = "Hello";
    
    function inner() {
        console.log(message);  // Closure: accesses outer's variable
    }
    
    return inner;
}

const fn = outer();
fn();  // "Hello" - still has access to 'message'

// How it works:
// 1. When outer() executes, it creates 'message'
// 2. inner() is created and references 'message'
// 3. outer() returns inner()
// 4. Even though outer() finished, inner() still remembers 'message'
// 5. This is because inner() "closes over" the outer scope
```

**Key points:**
- Closure = function + lexical environment
- Inner function remembers outer variables
- Works due to JavaScript's lexical scoping

---

### Q2: How do closures enable data privacy?

**Answer:**

```javascript
function createBankAccount(initialBalance) {
    let balance = initialBalance;  // Private variable
    
    return {
        deposit(amount) {
            balance += amount;
            return balance;
        },
        
        withdraw(amount) {
            if (amount <= balance) {
                balance -= amount;
                return balance;
            }
            throw new Error("Insufficient funds");
        },
        
        getBalance() {
            return balance;
        }
    };
}

const account = createBankAccount(1000);

// Can only access balance through methods
console.log(account.getBalance());  // 1000
account.deposit(500);
console.log(account.getBalance());  // 1500

// Cannot access balance directly
console.log(account.balance);  // undefined

// Cannot modify balance directly
account.balance = 999999;  // Doesn't affect private balance
console.log(account.getBalance());  // Still 1500
```

**Benefits:**
- True data privacy (not just convention like `_property`)
- Controlled access through methods
- Validation and business logic enforcement

---

### Q3: Explain the classic loop closure problem and its solutions.

**Answer:**

```javascript
// Problem: var is function-scoped
for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 3, 3, 3 (not 0, 1, 2)
    }, 100);
}

// Why? All closures share same 'i' reference
// When setTimeout callbacks run, loop is done and i = 3

// Solution 1: Use let (block-scoped)
for (let i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 0, 1, 2
    }, 100);
}

// Solution 2: IIFE (Immediately Invoked Function Expression)
for (var i = 0; i < 3; i++) {
    (function(j) {
        setTimeout(function() {
            console.log(j);  // 0, 1, 2
        }, 100);
    })(i);
}

// Solution 3: Pass parameter to setTimeout
for (var i = 0; i < 3; i++) {
    setTimeout(function(j) {
        console.log(j);  // 0, 1, 2
    }, 100, i);
}

// Solution 4: forEach (creates new scope)
[0, 1, 2].forEach(function(i) {
    setTimeout(function() {
        console.log(i);  // 0, 1, 2
    }, 100);
});
```

---

### Q4: What is the difference between closure and scope?

**Answer:**

```javascript
// Scope: Where variables are accessible
function outer() {
    const x = 10;
    
    function inner() {
        console.log(x);  // x is in scope here
    }
    
    inner();
    // x is in scope throughout outer()
}

// Closure: Function remembering outer scope after execution
function createCounter() {
    let count = 0;  // In scope during execution
    
    return function() {
        count++;  // Closure: still accessible after createCounter() finished
        return count;
    };
}

const counter = createCounter();
// createCounter() has finished executing
// but the returned function still has access to 'count'

console.log(counter());  // 1
console.log(counter());  // 2

// Key difference:
// Scope: Where you CAN access variables (during execution)
// Closure: How you STILL access variables (after execution)
```

---

### Q5: Can closures cause memory leaks? How?

**Answer:**

```javascript
// Yes, if you're not careful

// Problem: Unnecessary large data in closure
function createHandler() {
    const largeData = new Array(1000000).fill("x");
    
    return function() {
        console.log("Handler called");
        // largeData is in closure even if not used!
    };
}

const handlers = [];
for (let i = 0; i < 100; i++) {
    handlers.push(createHandler());
}
// Memory: 100 * 1MB = 100MB kept in memory!

// Solution 1: Only capture what you need
function createHandlerFixed() {
    const largeData = new Array(1000000).fill("x");
    const needed = largeData.length;
    
    // largeData can be garbage collected
    return function() {
        console.log(`Data size was ${needed}`);
    };
}

// Solution 2: Manually clear references
function createHandlerWithCleanup() {
    let largeData = new Array(1000000).fill("x");
    
    const handler = function() {
        if (largeData) {
            console.log("Using data");
            largeData = null;  // Clear after first use
        }
    };
    
    return handler;
}

// Problem: Circular references
function createCircular() {
    const obj = {};
    
    obj.handler = function() {
        console.log(obj);  // obj references handler, handler references obj
    };
    
    return obj;
}

// Solution: Break circular reference
function createCircularFixed() {
    const obj = {};
    
    obj.handler = function() {
        console.log(obj);
    };
    
    obj.cleanup = function() {
        obj.handler = null;  // Break reference
    };
    
    return obj;
}

// Best practices:
// 1. Only capture variables you actually use
// 2. Clear references when done
// 3. Be aware of long-lived closures
// 4. Use WeakMap for object-keyed data
```

---

## 📚 Summary

**Key Takeaways:**
- Closures = function + lexical environment
- Enable data privacy and encapsulation
- Power function factories and currying
- Essential for patterns like memoization, debounce, throttle
- Understand lexical scope and scope chain
- Be aware of memory implications

**Best Practices:**
- ✅ Use closures for data privacy
- ✅ Leverage closures in function factories
- ✅ Be mindful of memory with large data in closures
- ✅ Use `let`/`const` to avoid loop closure issues
- ✅ Only capture variables you actually need
- ✅ Understand `this` binding in closures
- ✅ Use arrow functions for lexical `this`

---

**Next Topic:** [12. Prototypes & Inheritance](./12-prototypes.md)
