# 10. Error Handling in JavaScript

> Proper error handling makes code robust, maintainable, and user-friendly.

## 📋 Overview

Error handling is crucial for building reliable applications. JavaScript provides several mechanisms to catch, handle, and respond to errors gracefully. Understanding different error types and handling strategies helps create robust applications.

**What You'll Learn:**
- ✅ Try/catch/finally blocks
- ✅ Error types and custom errors
- ✅ Throwing errors
- ✅ Error handling in async code
- ✅ Error boundaries
- ✅ Best practices for error handling
- ✅ Debugging techniques

---

## 🎯 Key Concepts

### 1. Try/Catch/Finally

```javascript
// Basic try/catch
try {
    const result = riskyOperation();
    console.log(result);
} catch (error) {
    console.error('An error occurred:', error.message);
}

// With finally block (always executes)
try {
    const file = openFile('data.txt');
    processFile(file);
} catch (error) {
    console.error('Error processing file:', error);
} finally {
    // Always runs, even if error occurs
    console.log('Cleanup operations');
    closeFile();
}

// Finally runs even with return
function example() {
    try {
        return 'try block';
    } finally {
        console.log('Finally runs before return');
    }
}

console.log(example());
// Output:
// Finally runs before return
// try block

// Nested try/catch
try {
    try {
        throw new Error('Inner error');
    } catch (innerError) {
        console.log('Caught inner error');
        throw new Error('Outer error');
    }
} catch (outerError) {
    console.log('Caught outer error:', outerError.message);
}

// Without catch (try/finally)
try {
    riskyOperation();
} finally {
    cleanup();  // Runs even if error occurs
}
// Error propagates up if no catch

// Multiple catch conditions
try {
    riskyOperation();
} catch (error) {
    if (error instanceof TypeError) {
        console.log('Type error occurred');
    } else if (error instanceof ReferenceError) {
        console.log('Reference error occurred');
    } else {
        console.log('Unknown error:', error);
    }
}
```

---

### 2. Error Types

```javascript
// Built-in Error types

// 1. Error - Generic error
try {
    throw new Error('Something went wrong');
} catch (error) {
    console.log(error.name);     // "Error"
    console.log(error.message);  // "Something went wrong"
    console.log(error.stack);    // Stack trace
}

// 2. SyntaxError - Invalid JavaScript code
try {
    eval('const x = ');  // Invalid syntax
} catch (error) {
    console.log(error instanceof SyntaxError);  // true
}

// 3. ReferenceError - Invalid reference
try {
    console.log(undefinedVariable);
} catch (error) {
    console.log(error instanceof ReferenceError);  // true
    console.log(error.message);  // "undefinedVariable is not defined"
}

// 4. TypeError - Wrong type
try {
    null.toString();  // Cannot call method on null
} catch (error) {
    console.log(error instanceof TypeError);  // true
    console.log(error.message);  // "Cannot read property 'toString' of null"
}

// 5. RangeError - Value out of range
try {
    const arr = new Array(-1);  // Invalid array length
} catch (error) {
    console.log(error instanceof RangeError);  // true
}

// 6. URIError - Invalid URI functions
try {
    decodeURIComponent('%');  // Invalid escape sequence
} catch (error) {
    console.log(error instanceof URIError);  // true
}

// 7. EvalError - Error in eval() (rarely used)
// Mostly for historical reasons

// Error properties
const error = new Error('Test error');
console.log(error.name);      // "Error"
console.log(error.message);   // "Test error"
console.log(error.stack);     // Stack trace
```

---

### 3. Custom Errors

```javascript
// Basic custom error
class CustomError extends Error {
    constructor(message) {
        super(message);
        this.name = 'CustomError';
    }
}

try {
    throw new CustomError('This is custom');
} catch (error) {
    console.log(error instanceof CustomError);  // true
    console.log(error.name);     // "CustomError"
    console.log(error.message);  // "This is custom"
}

// Validation error
class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
    }
}

function validateUser(user) {
    if (!user.email) {
        throw new ValidationError('Email is required', 'email');
    }
    
    if (!user.name) {
        throw new ValidationError('Name is required', 'name');
    }
}

try {
    validateUser({ name: 'John' });
} catch (error) {
    if (error instanceof ValidationError) {
        console.log(`Validation failed for ${error.field}: ${error.message}`);
    }
}

// Network error with status code
class NetworkError extends Error {
    constructor(message, statusCode, response) {
        super(message);
        this.name = 'NetworkError';
        this.statusCode = statusCode;
        this.response = response;
    }
}

async function fetchData(url) {
    const response = await fetch(url);
    
    if (!response.ok) {
        const body = await response.text();
        throw new NetworkError(
            `HTTP ${response.status}: ${response.statusText}`,
            response.status,
            body
        );
    }
    
    return response.json();
}

// Database error
class DatabaseError extends Error {
    constructor(message, query, originalError) {
        super(message);
        this.name = 'DatabaseError';
        this.query = query;
        this.originalError = originalError;
    }
}

// Authentication error
class AuthenticationError extends Error {
    constructor(message) {
        super(message);
        this.name = 'AuthenticationError';
        this.statusCode = 401;
    }
}

// Authorization error
class AuthorizationError extends Error {
    constructor(message, requiredRole) {
        super(message);
        this.name = 'AuthorizationError';
        this.statusCode = 403;
        this.requiredRole = requiredRole;
    }
}

// Not found error
class NotFoundError extends Error {
    constructor(resource, id) {
        super(`${resource} with id ${id} not found`);
        this.name = 'NotFoundError';
        this.statusCode = 404;
        this.resource = resource;
        this.id = id;
    }
}

// Usage in application
function getUser(id) {
    const user = database.find(id);
    
    if (!user) {
        throw new NotFoundError('User', id);
    }
    
    return user;
}

try {
    const user = getUser(999);
} catch (error) {
    if (error instanceof NotFoundError) {
        console.log(`${error.resource} not found`);
    } else {
        console.log('Unexpected error:', error);
    }
}
```

---

### 4. Throwing Errors

```javascript
// Throwing errors

// 1. Throw Error object (recommended)
throw new Error('Something went wrong');

// 2. Throw custom error
throw new ValidationError('Invalid input');

// 3. Throw string (not recommended)
throw 'Error message';

// 4. Throw number (not recommended)
throw 404;

// 5. Throw object (not recommended)
throw { message: 'Error', code: 500 };

// When to throw errors

// Invalid input
function divide(a, b) {
    if (b === 0) {
        throw new Error('Division by zero');
    }
    return a / b;
}

// Invalid state
class BankAccount {
    #balance = 0;
    
    withdraw(amount) {
        if (amount > this.#balance) {
            throw new Error('Insufficient funds');
        }
        this.#balance -= amount;
    }
}

// Unimplemented methods
class Shape {
    area() {
        throw new Error('Method area() must be implemented');
    }
}

// Early validation
function processPayment(amount, currency) {
    if (typeof amount !== 'number') {
        throw new TypeError('Amount must be a number');
    }
    
    if (amount <= 0) {
        throw new RangeError('Amount must be positive');
    }
    
    if (!['USD', 'EUR', 'GBP'].includes(currency)) {
        throw new Error('Unsupported currency');
    }
    
    // Process payment
}

// Re-throwing errors
try {
    riskyOperation();
} catch (error) {
    console.error('Error occurred:', error);
    throw error;  // Re-throw for caller to handle
}

// Wrapping errors
try {
    JSON.parse(invalidJson);
} catch (error) {
    throw new Error(`Failed to parse JSON: ${error.message}`);
}
```

---

### 5. Error Handling in Async Code

```javascript
// Promises - use .catch()
fetch('/api/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => {
        console.error('Error fetching data:', error);
    })
    .finally(() => {
        console.log('Request completed');
    });

// Async/await - use try/catch
async function fetchData() {
    try {
        const response = await fetch('/api/data');
        
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}`);
        }
        
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Error fetching data:', error);
        throw error;  // Re-throw or return default
    } finally {
        console.log('Cleanup');
    }
}

// Multiple async operations
async function fetchMultiple() {
    try {
        const [users, posts] = await Promise.all([
            fetch('/api/users').then(r => r.json()),
            fetch('/api/posts').then(r => r.json())
        ]);
        
        return { users, posts };
    } catch (error) {
        console.error('Error fetching data:', error);
        return null;
    }
}

// Error handling with Promise.allSettled
async function fetchAllData() {
    const results = await Promise.allSettled([
        fetch('/api/users'),
        fetch('/api/posts'),
        fetch('/api/comments')
    ]);
    
    const errors = results
        .filter(r => r.status === 'rejected')
        .map(r => r.reason);
    
    if (errors.length > 0) {
        console.error('Some requests failed:', errors);
    }
    
    const data = results
        .filter(r => r.status === 'fulfilled')
        .map(r => r.value);
    
    return data;
}

// Async error with timeout
async function fetchWithTimeout(url, timeout = 5000) {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), timeout);
    
    try {
        const response = await fetch(url, {
            signal: controller.signal
        });
        
        clearTimeout(timeoutId);
        return await response.json();
    } catch (error) {
        if (error.name === 'AbortError') {
            throw new Error('Request timeout');
        }
        throw error;
    }
}

// Retry logic
async function fetchWithRetry(url, maxRetries = 3) {
    for (let i = 0; i < maxRetries; i++) {
        try {
            const response = await fetch(url);
            
            if (response.ok) {
                return await response.json();
            }
            
            throw new Error(`HTTP ${response.status}`);
        } catch (error) {
            if (i === maxRetries - 1) {
                throw error;  // Last attempt failed
            }
            
            console.log(`Retry ${i + 1}/${maxRetries}`);
            await new Promise(resolve => setTimeout(resolve, 1000));
        }
    }
}
```

---

### 6. Error Handling Patterns

```javascript
// Pattern 1: Error-first callbacks (Node.js style)
function readFile(path, callback) {
    fs.readFile(path, 'utf8', (error, data) => {
        if (error) {
            callback(error, null);
            return;
        }
        callback(null, data);
    });
}

readFile('file.txt', (error, data) => {
    if (error) {
        console.error('Error reading file:', error);
        return;
    }
    console.log('File content:', data);
});

// Pattern 2: Result object
function divide(a, b) {
    if (b === 0) {
        return { success: false, error: 'Division by zero' };
    }
    return { success: true, value: a / b };
}

const result = divide(10, 2);
if (result.success) {
    console.log('Result:', result.value);
} else {
    console.error('Error:', result.error);
}

// Pattern 3: Maybe/Optional pattern
class Maybe {
    constructor(value) {
        this._value = value;
    }
    
    static of(value) {
        return new Maybe(value);
    }
    
    isNothing() {
        return this._value === null || this._value === undefined;
    }
    
    map(fn) {
        return this.isNothing() ? this : Maybe.of(fn(this._value));
    }
    
    getOrElse(defaultValue) {
        return this.isNothing() ? defaultValue : this._value;
    }
}

const value = Maybe.of(null).map(x => x * 2).getOrElse(0);
console.log(value);  // 0

// Pattern 4: Either pattern (Success/Failure)
class Either {
    constructor(value, isLeft = false) {
        this._value = value;
        this._isLeft = isLeft;
    }
    
    static left(value) {
        return new Either(value, true);
    }
    
    static right(value) {
        return new Either(value, false);
    }
    
    map(fn) {
        return this._isLeft ? this : Either.right(fn(this._value));
    }
    
    fold(leftFn, rightFn) {
        return this._isLeft ? leftFn(this._value) : rightFn(this._value);
    }
}

function divide2(a, b) {
    return b === 0
        ? Either.left('Division by zero')
        : Either.right(a / b);
}

divide2(10, 2)
    .map(x => x * 2)
    .fold(
        error => console.error('Error:', error),
        value => console.log('Result:', value)
    );

// Pattern 5: Global error handler
window.addEventListener('error', (event) => {
    console.error('Global error:', event.error);
    // Send to logging service
    logError(event.error);
});

window.addEventListener('unhandledrejection', (event) => {
    console.error('Unhandled promise rejection:', event.reason);
    // Send to logging service
    logError(event.reason);
});

// Pattern 6: Error boundary (React-like)
class ErrorBoundary {
    constructor(component) {
        this.component = component;
        this.hasError = false;
        this.error = null;
    }
    
    render() {
        if (this.hasError) {
            return this.renderError();
        }
        
        try {
            return this.component.render();
        } catch (error) {
            this.hasError = true;
            this.error = error;
            return this.renderError();
        }
    }
    
    renderError() {
        return `<div>Error: ${this.error.message}</div>`;
    }
}

// Pattern 7: Circuit breaker
class CircuitBreaker {
    constructor(threshold = 5, timeout = 60000) {
        this.failureCount = 0;
        this.threshold = threshold;
        this.timeout = timeout;
        this.state = 'CLOSED';  // CLOSED, OPEN, HALF_OPEN
        this.nextAttempt = Date.now();
    }
    
    async execute(fn) {
        if (this.state === 'OPEN') {
            if (Date.now() < this.nextAttempt) {
                throw new Error('Circuit breaker is OPEN');
            }
            this.state = 'HALF_OPEN';
        }
        
        try {
            const result = await fn();
            this.onSuccess();
            return result;
        } catch (error) {
            this.onFailure();
            throw error;
        }
    }
    
    onSuccess() {
        this.failureCount = 0;
        this.state = 'CLOSED';
    }
    
    onFailure() {
        this.failureCount++;
        if (this.failureCount >= this.threshold) {
            this.state = 'OPEN';
            this.nextAttempt = Date.now() + this.timeout;
        }
    }
}

// Usage
const breaker = new CircuitBreaker(3, 5000);

async function makeRequest() {
    try {
        const result = await breaker.execute(() => fetch('/api/data'));
        console.log('Success:', result);
    } catch (error) {
        console.error('Request failed:', error);
    }
}
```

---

### 7. Debugging and Logging

```javascript
// Console methods
console.log('Regular log');
console.info('Information');
console.warn('Warning');
console.error('Error');

// Console with styling
console.log('%cStyled message', 'color: blue; font-size: 20px');

// Console table
const users = [
    { name: 'John', age: 30 },
    { name: 'Jane', age: 25 }
];
console.table(users);

// Console group
console.group('User Details');
console.log('Name: John');
console.log('Age: 30');
console.groupEnd();

// Console time
console.time('operation');
// ... some operation
console.timeEnd('operation');  // operation: 1234ms

// Console trace
function a() { b(); }
function b() { c(); }
function c() { console.trace('Call stack'); }
a();

// Console assert
console.assert(1 === 2, 'Values are not equal');

// Console count
function trackCalls() {
    console.count('Function called');
}
trackCalls();  // Function called: 1
trackCalls();  // Function called: 2

// Debugger statement
function complexFunction() {
    debugger;  // Execution pauses here if DevTools open
    // ... complex logic
}

// Custom logger
class Logger {
    constructor(level = 'info') {
        this.level = level;
        this.levels = {
            error: 0,
            warn: 1,
            info: 2,
            debug: 3
        };
    }
    
    log(level, message, ...args) {
        if (this.levels[level] <= this.levels[this.level]) {
            const timestamp = new Date().toISOString();
            console[level](`[${timestamp}] [${level.toUpperCase()}]`, message, ...args);
        }
    }
    
    error(message, ...args) {
        this.log('error', message, ...args);
    }
    
    warn(message, ...args) {
        this.log('warn', message, ...args);
    }
    
    info(message, ...args) {
        this.log('info', message, ...args);
    }
    
    debug(message, ...args) {
        this.log('debug', message, ...args);
    }
}

const logger = new Logger('info');
logger.info('Application started');
logger.error('An error occurred', { code: 500 });

// Error reporting service
class ErrorReporter {
    static report(error, context = {}) {
        const errorData = {
            message: error.message,
            stack: error.stack,
            timestamp: new Date().toISOString(),
            userAgent: navigator.userAgent,
            url: window.location.href,
            ...context
        };
        
        // Send to logging service
        fetch('/api/errors', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(errorData)
        }).catch(err => {
            console.error('Failed to report error:', err);
        });
    }
}

// Usage
try {
    riskyOperation();
} catch (error) {
    ErrorReporter.report(error, {
        userId: currentUser.id,
        action: 'risky-operation'
    });
}
```

---

## 💡 Real-World Examples

### Example 1: API Error Handler

```javascript
class ApiError extends Error {
    constructor(message, statusCode, data = {}) {
        super(message);
        this.name = 'ApiError';
        this.statusCode = statusCode;
        this.data = data;
    }
}

class ApiClient {
    constructor(baseUrl) {
        this.baseUrl = baseUrl;
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseUrl}${endpoint}`;
        
        try {
            const response = await fetch(url, {
                ...options,
                headers: {
                    'Content-Type': 'application/json',
                    ...options.headers
                }
            });
            
            const data = await response.json();
            
            if (!response.ok) {
                throw new ApiError(
                    data.message || 'Request failed',
                    response.status,
                    data
                );
            }
            
            return data;
        } catch (error) {
            if (error instanceof ApiError) {
                throw error;
            }
            
            // Network error or JSON parse error
            throw new ApiError(
                'Network error occurred',
                0,
                { originalError: error.message }
            );
        }
    }
    
    async get(endpoint) {
        return this.request(endpoint, { method: 'GET' });
    }
    
    async post(endpoint, body) {
        return this.request(endpoint, {
            method: 'POST',
            body: JSON.stringify(body)
        });
    }
}

// Usage with error handling
const api = new ApiClient('https://api.example.com');

async function loadUserData(userId) {
    try {
        const user = await api.get(`/users/${userId}`);
        return user;
    } catch (error) {
        if (error instanceof ApiError) {
            switch (error.statusCode) {
                case 404:
                    console.error('User not found');
                    break;
                case 401:
                    console.error('Unauthorized');
                    // Redirect to login
                    break;
                case 500:
                    console.error('Server error');
                    break;
                default:
                    console.error('API error:', error.message);
            }
        } else {
            console.error('Unexpected error:', error);
        }
        
        return null;
    }
}
```

### Example 2: Form Validation with Custom Errors

```javascript
class ValidationError extends Error {
    constructor(message, field, value) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
        this.value = value;
    }
}

class FormValidator {
    constructor(rules) {
        this.rules = rules;
        this.errors = [];
    }
    
    validate(data) {
        this.errors = [];
        
        for (const [field, value] of Object.entries(data)) {
            const fieldRules = this.rules[field];
            
            if (!fieldRules) continue;
            
            try {
                this.validateField(field, value, fieldRules);
            } catch (error) {
                this.errors.push(error);
            }
        }
        
        if (this.errors.length > 0) {
            const error = new Error('Validation failed');
            error.validationErrors = this.errors;
            throw error;
        }
        
        return true;
    }
    
    validateField(field, value, rules) {
        if (rules.required && !value) {
            throw new ValidationError(
                `${field} is required`,
                field,
                value
            );
        }
        
        if (rules.minLength && value.length < rules.minLength) {
            throw new ValidationError(
                `${field} must be at least ${rules.minLength} characters`,
                field,
                value
            );
        }
        
        if (rules.maxLength && value.length > rules.maxLength) {
            throw new ValidationError(
                `${field} must be at most ${rules.maxLength} characters`,
                field,
                value
            );
        }
        
        if (rules.pattern && !rules.pattern.test(value)) {
            throw new ValidationError(
                `${field} format is invalid`,
                field,
                value
            );
        }
        
        if (rules.custom && !rules.custom(value)) {
            throw new ValidationError(
                rules.message || `${field} is invalid`,
                field,
                value
            );
        }
    }
    
    getErrors() {
        return this.errors;
    }
}

// Usage
const validator = new FormValidator({
    email: {
        required: true,
        pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
        message: 'Invalid email format'
    },
    password: {
        required: true,
        minLength: 8,
        custom: (value) => /[A-Z]/.test(value) && /[0-9]/.test(value),
        message: 'Password must contain uppercase letter and number'
    },
    username: {
        required: true,
        minLength: 3,
        maxLength: 20
    }
});

async function handleFormSubmit(formData) {
    try {
        validator.validate(formData);
        
        // Submit form
        const response = await api.post('/register', formData);
        console.log('Registration successful:', response);
    } catch (error) {
        if (error.validationErrors) {
            error.validationErrors.forEach(err => {
                console.error(`${err.field}: ${err.message}`);
                // Show error in UI
                showFieldError(err.field, err.message);
            });
        } else {
            console.error('Submission failed:', error.message);
        }
    }
}
```

### Example 3: Resilient Data Fetcher

```javascript
class DataFetchError extends Error {
    constructor(message, cause, attempt) {
        super(message);
        this.name = 'DataFetchError';
        this.cause = cause;
        this.attempt = attempt;
    }
}

class ResilientFetcher {
    constructor(options = {}) {
        this.maxRetries = options.maxRetries || 3;
        this.retryDelay = options.retryDelay || 1000;
        this.timeout = options.timeout || 5000;
        this.cache = new Map();
        this.cacheTTL = options.cacheTTL || 60000;
    }
    
    async fetch(url, options = {}) {
        const cacheKey = this.getCacheKey(url, options);
        
        // Check cache
        if (options.cache !== false) {
            const cached = this.getFromCache(cacheKey);
            if (cached) return cached;
        }
        
        // Fetch with retries
        let lastError;
        
        for (let attempt = 1; attempt <= this.maxRetries; attempt++) {
            try {
                const data = await this.fetchWithTimeout(url, options);
                
                // Cache successful response
                if (options.cache !== false) {
                    this.setCache(cacheKey, data);
                }
                
                return data;
            } catch (error) {
                lastError = error;
                
                console.warn(
                    `Attempt ${attempt}/${this.maxRetries} failed:`,
                    error.message
                );
                
                if (attempt < this.maxRetries) {
                    await this.delay(this.retryDelay * attempt);
                }
            }
        }
        
        throw new DataFetchError(
            'Failed to fetch data after multiple attempts',
            lastError,
            this.maxRetries
        );
    }
    
    async fetchWithTimeout(url, options) {
        const controller = new AbortController();
        const timeoutId = setTimeout(
            () => controller.abort(),
            this.timeout
        );
        
        try {
            const response = await fetch(url, {
                ...options,
                signal: controller.signal
            });
            
            clearTimeout(timeoutId);
            
            if (!response.ok) {
                throw new Error(`HTTP ${response.status}: ${response.statusText}`);
            }
            
            return await response.json();
        } catch (error) {
            clearTimeout(timeoutId);
            
            if (error.name === 'AbortError') {
                throw new Error('Request timeout');
            }
            
            throw error;
        }
    }
    
    getCacheKey(url, options) {
        return `${url}:${JSON.stringify(options)}`;
    }
    
    getFromCache(key) {
        const item = this.cache.get(key);
        
        if (!item) return null;
        
        if (Date.now() > item.expires) {
            this.cache.delete(key);
            return null;
        }
        
        return item.data;
    }
    
    setCache(key, data) {
        this.cache.set(key, {
            data,
            expires: Date.now() + this.cacheTTL
        });
    }
    
    delay(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }
    
    clearCache() {
        this.cache.clear();
    }
}

// Usage
const fetcher = new ResilientFetcher({
    maxRetries: 3,
    retryDelay: 1000,
    timeout: 5000,
    cacheTTL: 60000
});

async function loadData() {
    try {
        const data = await fetcher.fetch('https://api.example.com/data');
        console.log('Data loaded:', data);
        return data;
    } catch (error) {
        if (error instanceof DataFetchError) {
            console.error(
                `Failed after ${error.attempt} attempts:`,
                error.message
            );
            console.error('Original error:', error.cause);
        } else {
            console.error('Unexpected error:', error);
        }
        
        // Return cached or default data
        return getDefaultData();
    }
}
```

---

## 🎤 Interview Questions

### Q1: What's the difference between throw and return?

**Answer:**

```javascript
// return - normal flow
function divide(a, b) {
    if (b === 0) {
        return null;  // Caller checks for null
    }
    return a / b;
}

const result = divide(10, 0);
if (result === null) {
    console.log('Error: division by zero');
}

// throw - exceptional flow
function divide2(a, b) {
    if (b === 0) {
        throw new Error('Division by zero');
    }
    return a / b;
}

try {
    const result = divide2(10, 0);
} catch (error) {
    console.error('Error:', error.message);
}

// Use throw when:
// - Exceptional circumstances
// - Cannot continue execution
// - Caller must handle error

// Use return when:
// - Normal alternative outcomes
// - Can provide default value
// - Optional behavior
```

---

### Q2: How do you handle errors in async/await?

**Answer:**

```javascript
// Method 1: try/catch
async function fetchData() {
    try {
        const response = await fetch('/api/data');
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Error:', error);
        return null;
    }
}

// Method 2: .catch() on individual promises
async function fetchData2() {
    const response = await fetch('/api/data')
        .catch(error => {
            console.error('Fetch error:', error);
            return null;
        });
    
    if (!response) return null;
    
    const data = await response.json()
        .catch(error => {
            console.error('Parse error:', error);
            return null;
        });
    
    return data;
}

// Method 3: Wrapper function (Go-style)
async function to(promise) {
    try {
        const data = await promise;
        return [null, data];
    } catch (error) {
        return [error, null];
    }
}

async function fetchData3() {
    const [error, data] = await to(fetch('/api/data'));
    
    if (error) {
        console.error('Error:', error);
        return null;
    }
    
    return data;
}

// Method 4: Promise.allSettled for multiple operations
async function fetchMultiple() {
    const results = await Promise.allSettled([
        fetch('/api/users'),
        fetch('/api/posts'),
        fetch('/api/comments')
    ]);
    
    results.forEach((result, index) => {
        if (result.status === 'rejected') {
            console.error(`Request ${index} failed:`, result.reason);
        }
    });
    
    return results
        .filter(r => r.status === 'fulfilled')
        .map(r => r.value);
}
```

---

### Q3: What are custom errors and when should you use them?

**Answer:**

```javascript
// Custom error for specific scenarios
class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
    }
}

class NetworkError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.name = 'NetworkError';
        this.statusCode = statusCode;
    }
}

// Use custom errors when:
// 1. Need additional context
// 2. Different handling for different errors
// 3. Type checking errors

async function loadUser(id) {
    if (!id) {
        throw new ValidationError('User ID is required', 'id');
    }
    
    const response = await fetch(`/api/users/${id}`);
    
    if (!response.ok) {
        throw new NetworkError(
            'Failed to fetch user',
            response.status
        );
    }
    
    return response.json();
}

// Handle different error types
try {
    const user = await loadUser(null);
} catch (error) {
    if (error instanceof ValidationError) {
        console.error(`Validation error in field ${error.field}`);
    } else if (error instanceof NetworkError) {
        if (error.statusCode === 404) {
            console.error('User not found');
        } else if (error.statusCode === 500) {
            console.error('Server error');
        }
    } else {
        console.error('Unexpected error:', error);
    }
}

// Benefits:
// - Better error classification
// - Additional context
// - Type-safe error handling
// - Clearer code intent
```

---

### Q4: How do you handle unhandled promise rejections?

**Answer:**

```javascript
// Problem: Unhandled rejection
async function riskyOperation() {
    throw new Error('Something went wrong');
}

riskyOperation();  // Unhandled promise rejection!

// Solution 1: Always catch
riskyOperation().catch(error => {
    console.error('Error:', error);
});

// Solution 2: Global handler
process.on('unhandledRejection', (reason, promise) => {
    console.error('Unhandled Rejection at:', promise);
    console.error('Reason:', reason);
    // Log to service, but don't crash
});

// Browser
window.addEventListener('unhandledrejection', event => {
    console.error('Unhandled rejection:', event.reason);
    event.preventDefault();  // Prevent default logging
});

// Solution 3: Wrap in try/catch
async function safeOperation() {
    try {
        await riskyOperation();
    } catch (error) {
        console.error('Error:', error);
    }
}

// Solution 4: Top-level catch
async function main() {
    // All async operations here
}

main().catch(error => {
    console.error('Fatal error:', error);
    process.exit(1);
});

// Best practices:
// - Always handle promise rejections
// - Use global handler as last resort
// - Log unhandled rejections
// - Don't silently ignore errors
```

---

### Q5: What is the finally block and when should you use it?

**Answer:**

```javascript
// finally always executes, regardless of outcome

// Use case 1: Cleanup resources
try {
    const file = openFile('data.txt');
    processFile(file);
} catch (error) {
    console.error('Error:', error);
} finally {
    closeFile();  // Always closes, even if error
}

// Use case 2: Reset state
let isLoading = true;

try {
    await fetchData();
} catch (error) {
    console.error('Error:', error);
} finally {
    isLoading = false;  // Always resets
}

// Use case 3: Release locks
const lock = await acquireLock();

try {
    await criticalSection();
} finally {
    lock.release();  // Always releases
}

// finally runs even with return
function example() {
    try {
        return 'success';
    } finally {
        console.log('This runs first');
    }
}

console.log(example());
// Output:
// This runs first
// success

// finally vs catch
// catch: handles errors
// finally: cleanup, always runs

try {
    throw new Error('Error');
} catch (error) {
    console.log('Caught');
} finally {
    console.log('Cleanup');
}

// Output:
// Caught
// Cleanup

// Can use try/finally without catch
try {
    riskyOperation();
} finally {
    cleanup();  // Runs even if error propagates
}
```

---

## 📚 Summary

**Key Takeaways:**
- Use try/catch/finally for error handling
- Create custom errors for specific scenarios
- Always handle async errors (try/catch or .catch())
- Use finally for cleanup operations
- Implement proper error logging and reporting
- Provide meaningful error messages

**Best Practices:**
- ✅ Always catch and handle errors appropriately
- ✅ Create custom error classes for better context
- ✅ Log errors with sufficient detail
- ✅ Don't swallow errors silently
- ✅ Use finally for cleanup
- ✅ Implement global error handlers
- ✅ Validate input early
- ✅ Provide user-friendly error messages

---

**Next Topic:** [11. Closures](./11-closures.md)
