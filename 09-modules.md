# 09. Modules in JavaScript

> Modules allow you to organize code into reusable, maintainable pieces.

## 📋 Overview

Modules are a way to structure and organize JavaScript code by splitting it into separate files. They enable encapsulation, reusability, and maintainability. JavaScript has evolved from no module system to CommonJS (Node.js) and ES Modules (modern standard).

**What You'll Learn:**
- ✅ CommonJS modules (require/module.exports)
- ✅ ES Modules (import/export)
- ✅ Default and named exports
- ✅ Dynamic imports
- ✅ Module patterns and best practices
- ✅ Circular dependencies
- ✅ Module bundlers (Webpack, Rollup, etc.)

---

## 🎯 Key Concepts

### 1. CommonJS Modules (Node.js)

```javascript
// math.js - Exporting
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

const PI = 3.14159;

// Export individual items
exports.add = add;
exports.subtract = subtract;
exports.PI = PI;

// Or export object
module.exports = {
    add,
    subtract,
    PI
};

// app.js - Importing
const math = require('./math');

console.log(math.add(5, 3));        // 8
console.log(math.subtract(10, 4));  // 6
console.log(math.PI);               // 3.14159

// Destructuring imports
const { add, subtract } = require('./math');

console.log(add(5, 3));      // 8
console.log(subtract(10, 4)); // 6

// Exporting a single function/class
// logger.js
module.exports = function log(message) {
    console.log(`[LOG]: ${message}`);
};

// Or with class
module.exports = class Logger {
    log(message) {
        console.log(`[LOG]: ${message}`);
    }
};

// app.js
const log = require('./logger');
log('Hello');

// Or with class
const Logger = require('./logger');
const logger = new Logger();
logger.log('Hello');
```

---

### 2. ES Modules (ES6+)

```javascript
// math.js - Named exports
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export const PI = 3.14159;

// Or export at end
function multiply(a, b) {
    return a * b;
}

function divide(a, b) {
    if (b === 0) throw new Error('Division by zero');
    return a / b;
}

export { multiply, divide };

// Rename exports
export { multiply as mult, divide as div };

// app.js - Importing
import { add, subtract, PI } from './math.js';

console.log(add(5, 3));      // 8
console.log(subtract(10, 4)); // 6
console.log(PI);             // 3.14159

// Import all as namespace
import * as math from './math.js';

console.log(math.add(5, 3));      // 8
console.log(math.subtract(10, 4)); // 6
console.log(math.PI);             // 3.14159

// Rename imports
import { add as sum, subtract as diff } from './math.js';

console.log(sum(5, 3));  // 8
console.log(diff(10, 4)); // 6

// Import for side effects only (runs module code)
import './setup.js';

// Re-exporting
// calculator.js
export { add, subtract } from './math.js';
export { multiply, divide } from './advanced-math.js';

// Or re-export all
export * from './math.js';
```

---

### 3. Default Exports

```javascript
// user.js - Default export (one per module)
export default class User {
    constructor(name) {
        this.name = name;
    }
    
    greet() {
        return `Hello, I'm ${this.name}`;
    }
}

// app.js - Import default
import User from './user.js';

const user = new User('John');
console.log(user.greet());  // "Hello, I'm John"

// Can name it anything
import Person from './user.js';  // Same import, different name

// Mix default and named exports
// config.js
export default {
    apiUrl: 'https://api.example.com',
    timeout: 5000
};

export const VERSION = '1.0.0';
export const DEBUG = true;

// app.js
import config, { VERSION, DEBUG } from './config.js';

console.log(config.apiUrl);  // "https://api.example.com"
console.log(VERSION);        // "1.0.0"
console.log(DEBUG);          // true

// Default export patterns
// Pattern 1: Class
export default class Calculator {
    add(a, b) { return a + b; }
}

// Pattern 2: Function
export default function log(message) {
    console.log(message);
}

// Pattern 3: Object
export default {
    name: 'MyApp',
    version: '1.0.0'
};

// Pattern 4: Inline expression
export default {
    connect() {
        console.log('Connected');
    }
};
```

---

### 4. Dynamic Imports

```javascript
// Static imports (loaded at parse time)
import { add } from './math.js';

// Dynamic imports (loaded at runtime)
async function loadMath() {
    const math = await import('./math.js');
    console.log(math.add(5, 3));  // 8
}

// Conditional loading
async function loadModule(moduleName) {
    if (moduleName === 'math') {
        const math = await import('./math.js');
        return math;
    } else if (moduleName === 'utils') {
        const utils = await import('./utils.js');
        return utils;
    }
}

// Code splitting (load on demand)
button.addEventListener('click', async () => {
    const { default: Modal } = await import('./Modal.js');
    const modal = new Modal();
    modal.show();
});

// Lazy loading with error handling
async function lazyLoad() {
    try {
        const module = await import('./heavy-module.js');
        module.init();
    } catch (error) {
        console.error('Failed to load module:', error);
    }
}

// Dynamic import with destructuring
async function loadUtils() {
    const { formatDate, formatCurrency } = await import('./utils.js');
    
    console.log(formatDate(new Date()));
    console.log(formatCurrency(1234.56));
}

// Parallel dynamic imports
async function loadMultiple() {
    const [math, utils, ui] = await Promise.all([
        import('./math.js'),
        import('./utils.js'),
        import('./ui.js')
    ]);
    
    return { math, utils, ui };
}

// Dynamic module name
async function loadPlugin(name) {
    const module = await import(`./plugins/${name}.js`);
    return module.default;
}

// Use with top-level await (ES2022)
const config = await import('./config.js');
console.log(config.default);
```

---

### 5. Module Patterns

```javascript
// Pattern 1: Singleton
// database.js
class Database {
    constructor() {
        if (Database.instance) {
            return Database.instance;
        }
        
        this.connection = null;
        Database.instance = this;
    }
    
    connect() {
        if (!this.connection) {
            this.connection = 'Connected';
            console.log('Database connected');
        }
    }
}

export default new Database();  // Export single instance

// app.js
import db from './database.js';
db.connect();

// Pattern 2: Factory
// user-factory.js
class User {
    constructor(name, role) {
        this.name = name;
        this.role = role;
    }
}

class Admin extends User {
    constructor(name) {
        super(name, 'admin');
    }
}

class Guest extends User {
    constructor(name) {
        super(name, 'guest');
    }
}

export function createUser(name, type) {
    switch (type) {
        case 'admin':
            return new Admin(name);
        case 'guest':
            return new Guest(name);
        default:
            return new User(name, 'user');
    }
}

// Pattern 3: Revealing Module
// counter.js
let count = 0;

function increment() {
    count++;
}

function decrement() {
    count--;
}

function getCount() {
    return count;
}

export { increment, decrement, getCount };

// Pattern 4: Namespace
// utils/index.js
export * as string from './string.js';
export * as array from './array.js';
export * as date from './date.js';

// app.js
import * as utils from './utils/index.js';

utils.string.capitalize('hello');
utils.array.shuffle([1, 2, 3]);
utils.date.format(new Date());

// Pattern 5: Barrel exports (index.js)
// components/index.js
export { Button } from './Button.js';
export { Input } from './Input.js';
export { Modal } from './Modal.js';
export { Card } from './Card.js';

// app.js - Clean imports
import { Button, Input, Modal } from './components/index.js';
```

---

### 6. Module Scope and Privacy

```javascript
// Everything in a module is private by default
// Only exported items are accessible

// private.js
// Private variable
let privateCounter = 0;

// Private function
function privateIncrement() {
    privateCounter++;
}

// Public API
export function increment() {
    privateIncrement();
}

export function getCount() {
    return privateCounter;
}

// app.js - Cannot access private items
import { increment, getCount } from './private.js';

increment();
console.log(getCount());  // 1

// Cannot access:
// console.log(privateCounter);  // Error
// privateIncrement();           // Error

// Using WeakMap for privacy
// user.js
const privateData = new WeakMap();

export class User {
    constructor(name, password) {
        privateData.set(this, { password });
        this.name = name;
    }
    
    validatePassword(password) {
        return privateData.get(this).password === password;
    }
}

// Private class fields (ES2022)
export class BankAccount {
    #balance = 0;  // Private field
    
    deposit(amount) {
        this.#balance += amount;
    }
    
    getBalance() {
        return this.#balance;
    }
}
```

---

### 7. Circular Dependencies

```javascript
// Circular dependencies can cause issues

// a.js
import { b } from './b.js';

export const a = 'A';

console.log('In A, b is:', b);

// b.js
import { a } from './a.js';

export const b = 'B';

console.log('In B, a is:', a);

// main.js
import { a } from './a.js';
import { b } from './b.js';

// Output (may vary):
// In B, a is: undefined  // Not yet initialized!
// In A, b is: B

// Solution 1: Restructure to avoid circular deps
// common.js
export const shared = {
    value: 'shared'
};

// a.js
import { shared } from './common.js';
export const a = 'A';

// b.js
import { shared } from './common.js';
export const b = 'B';

// Solution 2: Use dynamic imports
// a.js
export const a = 'A';

export async function getB() {
    const { b } = await import('./b.js');
    return b;
}

// Solution 3: Lazy initialization
// a.js
import { getB } from './b.js';

export const a = 'A';

export function useB() {
    const b = getB();  // Call function instead of direct import
    console.log(b);
}

// b.js
export function getB() {
    return 'B';
}
```

---

### 8. CommonJS vs ES Modules

```javascript
// CommonJS (Node.js traditional)
// --------------------------------
// Synchronous loading
// require() returns module.exports
// Can be called conditionally
// .js extension

// math.cjs
module.exports = {
    add: (a, b) => a + b
};

// app.cjs
const math = require('./math.cjs');
console.log(math.add(5, 3));

// Conditional require
if (condition) {
    const module = require('./module.cjs');
}

// ES Modules (Modern standard)
// -----------------------------
// Asynchronous loading
// Static analysis possible
// Tree-shaking support
// .mjs extension (or .js with "type": "module")

// math.mjs
export const add = (a, b) => a + b;

// app.mjs
import { add } from './math.mjs';
console.log(add(5, 3));

// Cannot be conditional (static)
// if (condition) {
//     import { add } from './math.mjs';  // Error!
// }

// Use dynamic import for conditional
if (condition) {
    const { add } = await import('./math.mjs');
}

// Interoperability in Node.js
// ---------------------------

// Import CommonJS in ES Module
// cjs-module.cjs
module.exports = { hello: 'world' };

// es-module.mjs
import cjsModule from './cjs-module.cjs';
console.log(cjsModule.hello);  // 'world'

// Import ES Module in CommonJS (async)
// es-module.mjs
export const hello = 'world';

// cjs-module.cjs
(async () => {
    const esModule = await import('./es-module.mjs');
    console.log(esModule.hello);  // 'world'
})();

// package.json configuration
// {
//   "type": "module",     // All .js files are ES modules
//   "type": "commonjs"    // All .js files are CommonJS (default)
// }
```

---

## 💡 Real-World Examples

### Example 1: API Service Module

```javascript
// api.js - API service module
const BASE_URL = 'https://api.example.com';

class ApiService {
    constructor() {
        this.token = null;
    }
    
    setToken(token) {
        this.token = token;
    }
    
    async request(endpoint, options = {}) {
        const headers = {
            'Content-Type': 'application/json',
            ...(this.token && { 'Authorization': `Bearer ${this.token}` }),
            ...options.headers
        };
        
        const response = await fetch(`${BASE_URL}${endpoint}`, {
            ...options,
            headers
        });
        
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }
        
        return response.json();
    }
    
    async get(endpoint) {
        return this.request(endpoint);
    }
    
    async post(endpoint, data) {
        return this.request(endpoint, {
            method: 'POST',
            body: JSON.stringify(data)
        });
    }
    
    async put(endpoint, data) {
        return this.request(endpoint, {
            method: 'PUT',
            body: JSON.stringify(data)
        });
    }
    
    async delete(endpoint) {
        return this.request(endpoint, {
            method: 'DELETE'
        });
    }
}

// Export singleton instance
export default new ApiService();

// Also export class for testing
export { ApiService };

// users.js - Users API
import api from './api.js';

export async function getUsers() {
    return api.get('/users');
}

export async function getUser(id) {
    return api.get(`/users/${id}`);
}

export async function createUser(userData) {
    return api.post('/users', userData);
}

export async function updateUser(id, userData) {
    return api.put(`/users/${id}`, userData);
}

export async function deleteUser(id) {
    return api.delete(`/users/${id}`);
}

// app.js
import api from './api.js';
import { getUsers, createUser } from './users.js';

// Set auth token
api.setToken('your-token-here');

// Use the API
async function main() {
    try {
        const users = await getUsers();
        console.log('Users:', users);
        
        const newUser = await createUser({
            name: 'John Doe',
            email: 'john@example.com'
        });
        console.log('Created:', newUser);
    } catch (error) {
        console.error('Error:', error.message);
    }
}

main();
```

### Example 2: Plugin System

```javascript
// plugin-manager.js
class PluginManager {
    constructor() {
        this.plugins = new Map();
    }
    
    async register(name, path) {
        try {
            const module = await import(path);
            const plugin = module.default;
            
            if (typeof plugin.init !== 'function') {
                throw new Error('Plugin must have an init method');
            }
            
            this.plugins.set(name, plugin);
            console.log(`Plugin '${name}' registered`);
        } catch (error) {
            console.error(`Failed to register plugin '${name}':`, error);
        }
    }
    
    async initialize() {
        for (const [name, plugin] of this.plugins) {
            try {
                await plugin.init();
                console.log(`Plugin '${name}' initialized`);
            } catch (error) {
                console.error(`Failed to initialize plugin '${name}':`, error);
            }
        }
    }
    
    get(name) {
        return this.plugins.get(name);
    }
    
    has(name) {
        return this.plugins.has(name);
    }
}

export default new PluginManager();

// plugins/logger.js
export default {
    name: 'Logger',
    
    init() {
        console.log('Logger plugin initialized');
    },
    
    log(message) {
        console.log(`[LOG] ${new Date().toISOString()}: ${message}`);
    },
    
    error(message) {
        console.error(`[ERROR] ${new Date().toISOString()}: ${message}`);
    }
};

// plugins/cache.js
export default {
    name: 'Cache',
    cache: new Map(),
    
    init() {
        console.log('Cache plugin initialized');
    },
    
    set(key, value, ttl = 60000) {
        this.cache.set(key, {
            value,
            expires: Date.now() + ttl
        });
    },
    
    get(key) {
        const item = this.cache.get(key);
        
        if (!item) return null;
        
        if (Date.now() > item.expires) {
            this.cache.delete(key);
            return null;
        }
        
        return item.value;
    },
    
    clear() {
        this.cache.clear();
    }
};

// app.js
import pluginManager from './plugin-manager.js';

async function main() {
    // Register plugins
    await pluginManager.register('logger', './plugins/logger.js');
    await pluginManager.register('cache', './plugins/cache.js');
    
    // Initialize all plugins
    await pluginManager.initialize();
    
    // Use plugins
    const logger = pluginManager.get('logger');
    const cache = pluginManager.get('cache');
    
    logger.log('Application started');
    
    cache.set('user', { id: 1, name: 'John' });
    console.log('Cached user:', cache.get('user'));
}

main();
```

### Example 3: Feature Flags Module

```javascript
// feature-flags.js
const flags = new Map();

export function define(name, enabled = false, config = {}) {
    flags.set(name, {
        enabled,
        config,
        usageCount: 0
    });
}

export function isEnabled(name) {
    const flag = flags.get(name);
    
    if (!flag) {
        console.warn(`Feature flag '${name}' not defined`);
        return false;
    }
    
    flag.usageCount++;
    return flag.enabled;
}

export function enable(name) {
    const flag = flags.get(name);
    if (flag) flag.enabled = true;
}

export function disable(name) {
    const flag = flags.get(name);
    if (flag) flag.enabled = false;
}

export function getConfig(name) {
    const flag = flags.get(name);
    return flag?.config || {};
}

export function getStats() {
    const stats = {};
    
    for (const [name, flag] of flags) {
        stats[name] = {
            enabled: flag.enabled,
            usageCount: flag.usageCount
        };
    }
    
    return stats;
}

export function reset() {
    flags.clear();
}

// features/new-dashboard.js
import { define, isEnabled, getConfig } from '../feature-flags.js';

// Define feature flag
define('newDashboard', false, {
    version: '2.0',
    rolloutPercentage: 10
});

export function loadDashboard() {
    if (isEnabled('newDashboard')) {
        const config = getConfig('newDashboard');
        console.log('Loading new dashboard', config.version);
        return import('./dashboard-v2.js');
    } else {
        console.log('Loading legacy dashboard');
        return import('./dashboard-v1.js');
    }
}

// app.js
import { enable, getStats } from './feature-flags.js';
import { loadDashboard } from './features/new-dashboard.js';

// Enable feature for testing
enable('newDashboard');

// Load appropriate dashboard
async function main() {
    const dashboard = await loadDashboard();
    dashboard.render();
    
    // Log feature flag stats
    console.log('Feature flag stats:', getStats());
}

main();
```

---

## 🎤 Interview Questions

### Q1: What's the difference between CommonJS and ES Modules?

**Answer:**

```javascript
// CommonJS (Node.js)
// - Synchronous loading
// - Dynamic (runtime)
// - module.exports / require()
const module = require('./module');
module.exports = { value: 42 };

// ES Modules
// - Asynchronous loading
// - Static (compile time)
// - import / export
import { value } from './module.js';
export const value = 42;

// Key differences:
// 1. Syntax
// 2. Loading time (runtime vs compile time)
// 3. Tree shaking (ES modules support it)
// 4. Browser support (ES modules native)
// 5. Conditional loading (CommonJS yes, ES modules need dynamic import)
```

**Use CommonJS for:**
- Legacy Node.js projects
- When you need synchronous, conditional loading

**Use ES Modules for:**
- Modern applications
- Browser code
- Better tree-shaking and optimization
- Static analysis tools

---

### Q2: How do default and named exports differ?

**Answer:**

```javascript
// Named exports - multiple per module
// module.js
export const name = 'John';
export function greet() {}

// import with exact names
import { name, greet } from './module.js';

// Default export - one per module
// module.js
export default class User {}

// import with any name
import MyUser from './module.js';

// Mix both
// module.js
export default class User {}
export const VERSION = '1.0';

// import
import User, { VERSION } from './module.js';

// Best practices:
// - Use named exports for utility functions
// - Use default export for main class/component
// - Don't mix too many of both
```

---

### Q3: Explain dynamic imports and when to use them.

**Answer:**

```javascript
// Static imports - loaded at parse time
import { heavy } from './heavy.js';  // Always loaded

// Dynamic imports - loaded on demand
button.addEventListener('click', async () => {
    const { heavy } = await import('./heavy.js');
    heavy();
});

// Use cases:

// 1. Code splitting
async function loadFeature(featureName) {
    const module = await import(`./features/${featureName}.js`);
    return module.default;
}

// 2. Conditional loading
if (user.isAdmin) {
    const { AdminPanel } = await import('./AdminPanel.js');
    new AdminPanel().render();
}

// 3. Performance optimization
// Load heavy dependencies only when needed
async function processImage(file) {
    const imageLib = await import('./image-processing.js');
    return imageLib.process(file);
}

// 4. Lazy loading routes
const routes = {
    '/home': () => import('./pages/Home.js'),
    '/about': () => import('./pages/About.js'),
    '/contact': () => import('./pages/Contact.js')
};

async function navigate(path) {
    const page = await routes[path]();
    page.default.render();
}
```

**Benefits:**
- Smaller initial bundle size
- Faster initial load time
- Better performance
- Load features on demand

---

### Q4: How do you handle circular dependencies?

**Answer:**

```javascript
// Problem: Circular dependency
// a.js
import { b } from './b.js';
export const a = 'A';
console.log(b);  // undefined!

// b.js
import { a } from './a.js';
export const b = 'B';
console.log(a);  // Works

// Solution 1: Restructure code
// common.js
export const shared = {};

// a.js
import { shared } from './common.js';
export const a = 'A';

// b.js
import { shared } from './common.js';
export const b = 'B';

// Solution 2: Use dependency injection
// a.js
export function createA(b) {
    return {
        value: 'A',
        getB: () => b
    };
}

// b.js
export function createB(a) {
    return {
        value: 'B',
        getA: () => a
    };
}

// main.js
import { createA } from './a.js';
import { createB } from './b.js';

const a = createA(null);
const b = createB(a);
a.setB = () => b;

// Solution 3: Dynamic imports
// a.js
export const a = 'A';

export async function getB() {
    const { b } = await import('./b.js');
    return b;
}

// Best practice: Avoid circular dependencies
// Redesign your module structure
```

---

### Q5: What are barrel exports and when should you use them?

**Answer:**

```javascript
// Barrel export - index.js in a directory
// components/index.js
export { Button } from './Button.js';
export { Input } from './Input.js';
export { Modal } from './Modal.js';
export { Card } from './Card.js';

// Without barrel exports
import { Button } from './components/Button.js';
import { Input } from './components/Input.js';
import { Modal } from './components/Modal.js';

// With barrel exports
import { Button, Input, Modal } from './components/index.js';

// Benefits:
// 1. Cleaner imports
// 2. Single entry point
// 3. Easier refactoring
// 4. Better organization

// Advanced patterns
// components/index.js
export * from './Button.js';
export * from './Input.js';

// Re-export with rename
export { Button as PrimaryButton } from './Button.js';

// Group exports
export * as form from './form/index.js';
export * as layout from './layout/index.js';

// Usage
import { form, layout } from './components/index.js';
form.Input();
layout.Grid();

// Caution:
// Can impact tree-shaking if not careful
// Don't use export * too liberally
```

---

### Q6: How do you create a singleton module?

**Answer:**

```javascript
// Pattern 1: Export instance
// database.js
class Database {
    constructor() {
        this.connection = null;
    }
    
    connect() {
        if (!this.connection) {
            this.connection = 'Connected';
        }
    }
}

export default new Database();

// Pattern 2: Module scope
// config.js
let instance = null;

class Config {
    constructor() {
        if (instance) {
            return instance;
        }
        
        this.settings = {};
        instance = this;
    }
    
    set(key, value) {
        this.settings[key] = value;
    }
    
    get(key) {
        return this.settings[key];
    }
}

export default Config;

// Usage
import Config from './config.js';

const config1 = new Config();
const config2 = new Config();

console.log(config1 === config2);  // true

// Pattern 3: Function with closure
// logger.js
const createLogger = () => {
    let logs = [];
    
    return {
        log(message) {
            logs.push({ message, timestamp: Date.now() });
        },
        
        getLogs() {
            return [...logs];
        },
        
        clear() {
            logs = [];
        }
    };
};

export default createLogger();

// All imports get the same instance
import logger from './logger.js';
```

---

### Q7: How do you organize a large project's modules?

**Answer:**

```javascript
// Recommended structure

project/
├── src/
│   ├── components/       # UI components
│   │   ├── Button/
│   │   │   ├── Button.js
│   │   │   ├── Button.test.js
│   │   │   └── index.js
│   │   └── index.js      # Barrel export
│   │
│   ├── services/         # Business logic
│   │   ├── api/
│   │   │   ├── users.js
│   │   │   ├── posts.js
│   │   │   └── index.js
│   │   └── auth/
│   │       ├── auth.js
│   │       └── index.js
│   │
│   ├── utils/            # Helper functions
│   │   ├── string.js
│   │   ├── date.js
│   │   └── index.js
│   │
│   ├── config/           # Configuration
│   │   ├── constants.js
│   │   ├── env.js
│   │   └── index.js
│   │
│   ├── hooks/            # React hooks (if using React)
│   │   ├── useAuth.js
│   │   └── index.js
│   │
│   ├── store/            # State management
│   │   ├── slices/
│   │   └── index.js
│   │
│   └── types/            # TypeScript types
│       └── index.ts
│
└── package.json

// Best practices:

// 1. Group by feature
features/
├── auth/
│   ├── components/
│   ├── services/
│   ├── hooks/
│   └── index.js

// 2. Keep related files together
// 3. Use index.js for barrel exports
// 4. Consistent naming
// 5. Clear separation of concerns
```

---

### Q8: How do you test modules?

**Answer:**

```javascript
// math.js
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

// math.test.js
import { add, subtract } from './math.js';

describe('Math utils', () => {
    test('add should sum two numbers', () => {
        expect(add(2, 3)).toBe(5);
        expect(add(-1, 1)).toBe(0);
    });
    
    test('subtract should subtract two numbers', () => {
        expect(subtract(5, 3)).toBe(2);
        expect(subtract(0, 5)).toBe(-5);
    });
});

// Mocking modules
// user-service.js
import api from './api.js';

export async function getUser(id) {
    return api.get(`/users/${id}`);
}

// user-service.test.js
import { getUser } from './user-service.js';
import api from './api.js';

jest.mock('./api.js');

test('getUser should fetch user from API', async () => {
    const mockUser = { id: 1, name: 'John' };
    api.get.mockResolvedValue(mockUser);
    
    const user = await getUser(1);
    
    expect(api.get).toHaveBeenCalledWith('/users/1');
    expect(user).toEqual(mockUser);
});

// Testing with dependency injection
// user-service.js
export function createUserService(api) {
    return {
        async getUser(id) {
            return api.get(`/users/${id}`);
        }
    };
}

// user-service.test.js
test('getUser should fetch user', async () => {
    const mockApi = {
        get: jest.fn().mockResolvedValue({ id: 1, name: 'John' })
    };
    
    const service = createUserService(mockApi);
    const user = await service.getUser(1);
    
    expect(user).toEqual({ id: 1, name: 'John' });
});
```

---

### Q9: What is tree-shaking and how do modules affect it?

**Answer:**

```javascript
// Tree-shaking removes unused code

// utils.js
export function used() {
    return 'This is used';
}

export function unused() {
    return 'This is NOT used';
}

export function alsoUnused() {
    return 'This is also NOT used';
}

// app.js
import { used } from './utils.js';

console.log(used());

// After tree-shaking (in production build):
// Only 'used' function is included
// 'unused' and 'alsoUnused' are removed

// Best practices for tree-shaking:

// 1. Use named exports
export function a() {}
export function b() {}

// Not: export default { a, b }  // Harder to tree-shake

// 2. Avoid side effects
// bad.js
console.log('Side effect!');  // Prevents tree-shaking
export function func() {}

// good.js
export function func() {}

// 3. Use pure functions
export function pure(x) {
    return x * 2;  // Pure - can be tree-shaken if unused
}

let count = 0;
export function impure() {
    count++;  // Side effect - harder to tree-shake
}

// 4. Package.json configuration
{
    "sideEffects": false  // No side effects
}

// Or specify files with side effects
{
    "sideEffects": ["*.css", "*.scss"]
}

// 5. Import only what you need
// Bad - imports everything
import _ from 'lodash';
_.map([1, 2, 3], x => x * 2);

// Good - imports only map
import map from 'lodash/map';
map([1, 2, 3], x => x * 2);
```

---

### Q10: How do you handle module versioning?

**Answer:**

```javascript
// Pattern 1: Version in filename
// api-v1.js
export class ApiV1 {
    getUser(id) {
        return fetch(`/v1/users/${id}`);
    }
}

// api-v2.js
export class ApiV2 {
    getUser(id) {
        return fetch(`/v2/users/${id}`);
    }
}

// Pattern 2: Version namespace
// api.js
export const v1 = {
    getUser(id) {
        return fetch(`/v1/users/${id}`);
    }
};

export const v2 = {
    getUser(id) {
        return fetch(`/v2/users/${id}`);
    }
};

// Usage
import { v1, v2 } from './api.js';

// Pattern 3: Factory with version
// api-factory.js
export function createApi(version = 'v2') {
    const baseUrl = `/api/${version}`;
    
    return {
        getUser(id) {
            return fetch(`${baseUrl}/users/${id}`);
        }
    };
}

// Usage
import { createApi } from './api-factory.js';

const apiV1 = createApi('v1');
const apiV2 = createApi('v2');

// Pattern 4: Feature detection
// utils.js
export function sort(array) {
    if (Array.prototype.toSorted) {
        // Modern version
        return array.toSorted();
    } else {
        // Legacy version
        return [...array].sort();
    }
}

// Pattern 5: Deprecation warnings
// legacy-api.js
export function oldMethod() {
    console.warn('oldMethod is deprecated, use newMethod instead');
    // ... legacy implementation
}

export function newMethod() {
    // ... new implementation
}
```

---

## 📚 Summary

**Key Takeaways:**
- Modules organize code into reusable, maintainable pieces
- ES Modules are the modern standard (import/export)
- CommonJS is still used in Node.js (require/module.exports)
- Use named exports for multiple items, default for main export
- Dynamic imports enable code splitting and lazy loading
- Avoid circular dependencies through proper architecture

**Best Practices:**
- ✅ Use ES Modules for new projects
- ✅ One module per file with clear responsibility
- ✅ Use barrel exports (index.js) for clean imports
- ✅ Keep modules small and focused
- ✅ Avoid circular dependencies
- ✅ Use dynamic imports for code splitting
- ✅ Export only what's needed (encapsulation)
- ✅ Use consistent naming conventions

---

**Next Topic:** [10. Error Handling](./10-error-handling.md)
