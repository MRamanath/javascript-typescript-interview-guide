# 17. JSON & Local Storage in JavaScript

> JSON enables data exchange between systems, while local storage provides client-side data persistence in web browsers.

## 📋 Overview

JavaScript Object Notation (JSON) is a lightweight, text-based data interchange format that's easy for humans to read and write. Combined with browser storage APIs like localStorage and sessionStorage, JSON becomes essential for client-side data persistence, API communication, and configuration management.

**What You'll Learn:**
- ✅ JSON syntax, parsing, and stringification
- ✅ Working with complex nested JSON structures
- ✅ Error handling in JSON operations
- ✅ localStorage and sessionStorage APIs
- ✅ Storage event handling and cross-tab communication
- ✅ Data serialization strategies
- ✅ Performance considerations and storage limits
- ✅ Security considerations for client-side storage
- ✅ Practical patterns for data persistence
- ✅ TypeScript integration with JSON
- ✅ Common pitfalls and debugging techniques

---

## 🎯 Key Concepts

### 1. JSON Syntax and Structure

```javascript
// Valid JSON structure
const validJSON = {
  "name": "John Doe",
  "age": 30,
  "isActive": true,
  "address": {
    "street": "123 Main St",
    "city": "New York"
  },
  "hobbies": ["reading", "swimming"],
  "spouse": null
};

// Invalid JSON (comments, trailing commas, undefined)
const invalidJSON = {
  name: "John", // no comments allowed
  age: undefined, // undefined not allowed
  active: true, // trailing comma not allowed in JSON
};
```

---

### 2. JSON.stringify() - Converting to JSON

```javascript
const user = {
  name: "Alice",
  age: 25,
  loginCount: 42,
  lastLogin: new Date(),
  preferences: {
    theme: "dark",
    notifications: true
  }
};

// Basic stringification
console.log(JSON.stringify(user));

// Pretty printing with indentation
console.log(JSON.stringify(user, null, 2));

// Using replacer function
const jsonString = JSON.stringify(user, (key, value) => {
  if (key === 'loginCount') return undefined; // exclude this key
  if (value instanceof Date) return value.toISOString(); // convert dates
  return value;
});

// Using replacer array (whitelist)
const limited = JSON.stringify(user, ['name', 'age']);
console.log(limited); // {"name":"Alice","age":25}
```

---

### 3. JSON.parse() - Parsing JSON Strings

```javascript
const jsonString = '{"name":"Bob","age":30,"active":true}';

// Basic parsing
const user = JSON.parse(jsonString);
console.log(user.name); // "Bob"

// Using reviver function
const parsed = JSON.parse(jsonString, (key, value) => {
  if (key === 'age') return value + 1; // modify age
  if (typeof value === 'string' && value.includes('T')) {
    return new Date(value); // convert ISO strings to Date
  }
  return value;
});

// Error handling
function safeJSONParse(str, fallback = null) {
  try {
    return JSON.parse(str);
  } catch (error) {
    console.error('Invalid JSON:', error.message);
    return fallback;
  }
}

const result = safeJSONParse('invalid json', {});
```

---

### 4. Working with Complex JSON Structures

```javascript
// Nested data transformation
const apiResponse = {
  "users": [
    {
      "id": 1,
      "profile": {
        "personal": {
          "firstName": "John",
          "lastName": "Doe"
        },
        "contact": {
          "email": "john@example.com"
        }
      }
    }
  ]
};

// Deep access with optional chaining
const email = apiResponse.users?.[0]?.profile?.contact?.email;

// Flattening nested structures
function flattenUser(user) {
  return {
    id: user.id,
    firstName: user.profile?.personal?.firstName,
    lastName: user.profile?.personal?.lastName,
    email: user.profile?.contact?.email
  };
}

// Validating JSON structure
function validateUserStructure(user) {
  const required = ['id', 'profile.personal.firstName', 'profile.contact.email'];
  
  for (const path of required) {
    const keys = path.split('.');
    let current = user;
    
    for (const key of keys) {
      if (!current || typeof current !== 'object' || !(key in current)) {
        return { valid: false, missing: path };
      }
      current = current[key];
    }
  }
  
  return { valid: true };
}
```

---

## 🗄️ Local Storage API

### 5. localStorage Basics

```javascript
// Storing data
localStorage.setItem('username', 'john_doe');
localStorage.setItem('preferences', JSON.stringify({
  theme: 'dark',
  language: 'en'
}));

// Retrieving data
const username = localStorage.getItem('username');
const preferences = JSON.parse(localStorage.getItem('preferences') || '{}');

// Removing data
localStorage.removeItem('username');

// Clearing all data
localStorage.clear();

// Check if key exists
if (localStorage.getItem('username') !== null) {
  console.log('Username exists');
}

// Get all keys
const allKeys = Object.keys(localStorage);
console.log('Stored keys:', allKeys);
```

---

### 6. sessionStorage vs localStorage

```javascript
// localStorage persists until manually cleared
localStorage.setItem('persistent', 'stays forever');

// sessionStorage clears when tab closes
sessionStorage.setItem('temporary', 'gone when tab closes');

// Check storage availability
function isStorageAvailable(type) {
  try {
    const storage = window[type];
    const test = '__storage_test__';
    storage.setItem(test, test);
    storage.removeItem(test);
    return true;
  } catch (e) {
    return false;
  }
}

console.log('localStorage available:', isStorageAvailable('localStorage'));
console.log('sessionStorage available:', isStorageAvailable('sessionStorage'));
```

---

### 7. Storage Event Handling

```javascript
// Listen for storage changes (cross-tab communication)
window.addEventListener('storage', (event) => {
  console.log('Storage changed:', {
    key: event.key,
    oldValue: event.oldValue,
    newValue: event.newValue,
    storageArea: event.storageArea
  });
  
  // React to specific changes
  if (event.key === 'user-preferences') {
    updateUI(JSON.parse(event.newValue));
  }
});

// Custom storage wrapper with events
class StorageManager {
  constructor(storage = localStorage) {
    this.storage = storage;
    this.listeners = {};
  }
  
  setItem(key, value) {
    const oldValue = this.storage.getItem(key);
    this.storage.setItem(key, value);
    this.notifyListeners(key, oldValue, value);
  }
  
  getItem(key) {
    return this.storage.getItem(key);
  }
  
  removeItem(key) {
    const oldValue = this.storage.getItem(key);
    this.storage.removeItem(key);
    this.notifyListeners(key, oldValue, null);
  }
  
  addEventListener(key, callback) {
    if (!this.listeners[key]) this.listeners[key] = [];
    this.listeners[key].push(callback);
  }
  
  notifyListeners(key, oldValue, newValue) {
    if (this.listeners[key]) {
      this.listeners[key].forEach(callback => {
        callback({ key, oldValue, newValue });
      });
    }
  }
}

const storage = new StorageManager();
storage.addEventListener('user', (event) => {
  console.log('User data changed:', event);
});
```

---

## 💡 Real-World Examples

### Example 1: User Preferences System

```javascript
class UserPreferences {
  constructor() {
    this.key = 'user-preferences';
    this.defaults = {
      theme: 'light',
      language: 'en',
      notifications: {
        email: true,
        push: false,
        sms: false
      },
      privacy: {
        analytics: false,
        cookies: true
      }
    };
  }
  
  load() {
    try {
      const stored = localStorage.getItem(this.key);
      if (!stored) return { ...this.defaults };
      
      const parsed = JSON.parse(stored);
      return this.mergeDefaults(parsed);
    } catch (error) {
      console.warn('Failed to load preferences:', error);
      return { ...this.defaults };
    }
  }
  
  save(preferences) {
    try {
      localStorage.setItem(this.key, JSON.stringify(preferences));
      return true;
    } catch (error) {
      console.error('Failed to save preferences:', error);
      return false;
    }
  }
  
  update(path, value) {
    const prefs = this.load();
    this.setNestedValue(prefs, path, value);
    return this.save(prefs);
  }
  
  mergeDefaults(stored) {
    return this.deepMerge(this.defaults, stored);
  }
  
  deepMerge(target, source) {
    const result = { ...target };
    for (const key in source) {
      if (source[key] && typeof source[key] === 'object' && !Array.isArray(source[key])) {
        result[key] = this.deepMerge(target[key] || {}, source[key]);
      } else {
        result[key] = source[key];
      }
    }
    return result;
  }
  
  setNestedValue(obj, path, value) {
    const keys = path.split('.');
    const lastKey = keys.pop();
    let current = obj;
    
    for (const key of keys) {
      if (!(key in current) || typeof current[key] !== 'object') {
        current[key] = {};
      }
      current = current[key];
    }
    
    current[lastKey] = value;
  }
}

// Usage
const prefs = new UserPreferences();
const current = prefs.load();
prefs.update('theme', 'dark');
prefs.update('notifications.email', false);
```

### Example 2: Shopping Cart with Persistence

```javascript
class ShoppingCart {
  constructor() {
    this.key = 'shopping-cart';
    this.items = this.loadCart();
  }
  
  loadCart() {
    try {
      const stored = localStorage.getItem(this.key);
      return stored ? JSON.parse(stored) : [];
    } catch (error) {
      console.error('Failed to load cart:', error);
      return [];
    }
  }
  
  saveCart() {
    try {
      localStorage.setItem(this.key, JSON.stringify(this.items));
      this.dispatchEvent('cartUpdated', this.items);
    } catch (error) {
      console.error('Failed to save cart:', error);
      throw new Error('Unable to save cart data');
    }
  }
  
  addItem(product, quantity = 1) {
    const existingIndex = this.items.findIndex(item => item.id === product.id);
    
    if (existingIndex >= 0) {
      this.items[existingIndex].quantity += quantity;
    } else {
      this.items.push({
        id: product.id,
        name: product.name,
        price: product.price,
        quantity,
        addedAt: new Date().toISOString()
      });
    }
    
    this.saveCart();
  }
  
  removeItem(productId) {
    this.items = this.items.filter(item => item.id !== productId);
    this.saveCart();
  }
  
  updateQuantity(productId, quantity) {
    const item = this.items.find(item => item.id === productId);
    if (item) {
      if (quantity <= 0) {
        this.removeItem(productId);
      } else {
        item.quantity = quantity;
        this.saveCart();
      }
    }
  }
  
  getTotal() {
    return this.items.reduce((total, item) => total + (item.price * item.quantity), 0);
  }
  
  clear() {
    this.items = [];
    this.saveCart();
  }
  
  dispatchEvent(type, data) {
    window.dispatchEvent(new CustomEvent(type, { detail: data }));
  }
}

// Usage
const cart = new ShoppingCart();

// Listen for cart changes
window.addEventListener('cartUpdated', (event) => {
  console.log('Cart updated:', event.detail);
  updateCartUI(event.detail);
});
```

### Example 3: Offline Data Sync

```javascript
class OfflineDataManager {
  constructor() {
    this.onlineKey = 'online-data';
    this.pendingKey = 'pending-changes';
    this.lastSyncKey = 'last-sync';
  }
  
  // Store data when offline
  storePendingChange(action, data) {
    const pending = this.getPendingChanges();
    pending.push({
      id: Date.now(),
      action,
      data,
      timestamp: new Date().toISOString()
    });
    
    localStorage.setItem(this.pendingKey, JSON.stringify(pending));
  }
  
  getPendingChanges() {
    try {
      return JSON.parse(localStorage.getItem(this.pendingKey) || '[]');
    } catch {
      return [];
    }
  }
  
  // Sync when back online
  async syncPendingChanges() {
    const pending = this.getPendingChanges();
    if (pending.length === 0) return;
    
    const successful = [];
    const failed = [];
    
    for (const change of pending) {
      try {
        await this.sendToServer(change);
        successful.push(change.id);
      } catch (error) {
        console.error('Failed to sync change:', change, error);
        failed.push(change);
      }
    }
    
    // Remove successful syncs
    const remaining = pending.filter(change => !successful.includes(change.id));
    localStorage.setItem(this.pendingKey, JSON.stringify(remaining));
    
    if (successful.length > 0) {
      localStorage.setItem(this.lastSyncKey, new Date().toISOString());
    }
    
    return { synced: successful.length, failed: failed.length };
  }
  
  async sendToServer(change) {
    const response = await fetch('/api/sync', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(change)
    });
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    
    return response.json();
  }
  
  // Check online status and auto-sync
  initAutoSync() {
    window.addEventListener('online', () => {
      console.log('Back online, syncing...');
      this.syncPendingChanges();
    });
    
    window.addEventListener('offline', () => {
      console.log('Gone offline, storing changes locally');
    });
  }
}
```

---

## 🎤 Interview Questions

### Q1: What's the difference between localStorage and sessionStorage?

**Answer:**

`localStorage` persists until manually cleared and is shared across tabs of the same origin. `sessionStorage` is cleared when the tab closes and is isolated per tab.

```javascript
// localStorage - persists across sessions
localStorage.setItem('persistent', 'value');

// sessionStorage - cleared when tab closes
sessionStorage.setItem('temporary', 'value');

// Check lifetime
console.log('localStorage survives browser restart');
console.log('sessionStorage cleared on tab close');
```

---

### Q2: How do you handle JSON parsing errors safely?

**Answer:**

Always wrap `JSON.parse()` in try-catch blocks or create a safe parsing function:

```javascript
function safeJsonParse(str, fallback = null) {
  try {
    return JSON.parse(str);
  } catch (error) {
    console.error('JSON parse error:', error.message);
    return fallback;
  }
}

// Usage
const data = safeJsonParse(userInput, {});
```

---

### Q3: What happens when localStorage quota is exceeded?

**Answer:**

A `QuotaExceededError` is thrown. Handle this by cleaning up old data or notifying the user:

```javascript
function safeStorageSet(key, value) {
  try {
    localStorage.setItem(key, value);
  } catch (error) {
    if (error.name === 'QuotaExceededError') {
      console.log('Storage quota exceeded');
      // Clean up old data or notify user
      clearOldEntries();
    }
    throw error;
  }
}
```

---

### Q4: How can you detect when localStorage changes in another tab?

**Answer:**

Use the `storage` event which fires when localStorage is modified in another tab:

```javascript
window.addEventListener('storage', (event) => {
  if (event.key === 'user-data') {
    console.log('User data changed in another tab');
    // Update UI accordingly
    updateUserInterface(JSON.parse(event.newValue));
  }
});
```

---

### Q5: What are the security considerations for client-side storage?

**Answer:**

- Never store sensitive data (passwords, tokens, PII) in localStorage
- Data is accessible to any script on the same origin
- Use HTTPS to prevent man-in-the-middle attacks
- Validate and sanitize data before storing

```javascript
// Bad - never do this
localStorage.setItem('password', userPassword);

// Good - only store non-sensitive data
localStorage.setItem('preferences', JSON.stringify({
  theme: 'dark',
  language: 'en'
}));
```

---

### Q6: How do you implement data versioning for localStorage?

**Answer:**

Include version information and handle migrations:

```javascript
const DATA_VERSION = 2;

function loadData() {
  const stored = JSON.parse(localStorage.getItem('app-data') || '{}');
  
  if (!stored.version || stored.version < DATA_VERSION) {
    return migrateData(stored);
  }
  
  return stored.data;
}

function migrateData(oldData) {
  // Handle version upgrades
  if (oldData.version === 1) {
    // Migrate from v1 to v2
    oldData.data = transformV1ToV2(oldData.data);
  }
  
  const newData = {
    version: DATA_VERSION,
    data: oldData.data || {}
  };
  
  localStorage.setItem('app-data', JSON.stringify(newData));
  return newData.data;
}
```

---

## 📚 Summary

**Key Takeaways:**
- JSON is language-independent but has strict syntax rules (no comments, trailing commas, or undefined)
- `JSON.stringify()` and `JSON.parse()` support replacer/reviver functions for custom serialization
- localStorage persists data across sessions; sessionStorage is tab-specific
- Always handle JSON parsing errors and storage quota limits
- Use storage events for cross-tab communication
- Never store sensitive data in client-side storage
- Implement data versioning for long-term storage compatibility

---

**Next Topic:** [18. Functional Programming](./18-functional-programming.md)