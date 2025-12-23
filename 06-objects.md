# 06. Objects in JavaScript

> Objects are collections of key-value pairs that represent real-world entities.

## 📋 Overview

Objects are the foundation of JavaScript. Almost everything in JavaScript is an object or behaves like one. Understanding objects is crucial for mastering JavaScript and working with real-world data.

**What You'll Learn:**
- ✅ Creating and accessing objects
- ✅ Object properties and methods
- ✅ Object destructuring
- ✅ Object methods (Object.keys, values, entries, etc.)
- ✅ Property descriptors and getters/setters
- ✅ Object cloning and merging
- ✅ this keyword in objects
- ✅ JSON and object serialization

---

## 🎯 Key Concepts

### 1. Creating Objects

```javascript
// Object literal (most common)
let person = {
    name: "John",
    age: 30,
    city: "New York"
};

// Empty object
let obj = {};

// Object constructor
let user = new Object();
user.name = "Alice";
user.age = 25;

// Object.create()
let proto = { greet: function() { console.log("Hello!"); } };
let obj2 = Object.create(proto);
obj2.name = "Bob";

// Computed property names
let key = "dynamicKey";
let obj3 = {
    [key]: "value",
    ["prop" + 1]: "value1"
};
console.log(obj3);  // { dynamicKey: "value", prop1: "value1" }

// Shorthand properties
let name = "Charlie";
let age = 35;
let person2 = { name, age };  // { name: "Charlie", age: 35 }

// Shorthand methods
let calculator = {
    add(a, b) {
        return a + b;
    },
    subtract(a, b) {
        return a - b;
    }
};
```

---

### 2. Accessing Properties

```javascript
let user = {
    name: "John",
    age: 30,
    "favorite color": "blue"
};

// Dot notation
console.log(user.name);  // "John"
console.log(user.age);   // 30

// Bracket notation
console.log(user["name"]);  // "John"
console.log(user["favorite color"]);  // "blue" (needed for spaces)

// Dynamic property access
let prop = "age";
console.log(user[prop]);  // 30

// Optional chaining
let user2 = {};
console.log(user2?.address?.city);  // undefined (no error)

// Nullish coalescing with optional chaining
let city = user2?.address?.city ?? "Unknown";
console.log(city);  // "Unknown"

// Checking if property exists
console.log("name" in user);  // true
console.log("salary" in user);  // false

// hasOwnProperty (doesn't check prototype chain)
console.log(user.hasOwnProperty("name"));  // true
console.log(user.hasOwnProperty("toString"));  // false (inherited)
```

---

### 3. Adding, Modifying, and Deleting Properties

```javascript
let person = {
    name: "John",
    age: 30
};

// Add property
person.city = "New York";
person["country"] = "USA";

console.log(person);
// { name: "John", age: 30, city: "New York", country: "USA" }

// Modify property
person.age = 31;
person["name"] = "John Doe";

// Delete property
delete person.country;
console.log(person);  // { name: "John Doe", age: 31, city: "New York" }

// Dynamic property addition
let key = "email";
person[key] = "john@example.com";

// Prevent extensions
Object.preventExtensions(person);
person.phone = "123-456";  // Silently fails (strict mode throws error)
console.log(person.phone);  // undefined

// Seal object (can't add/remove, can modify)
let sealed = { name: "Alice" };
Object.seal(sealed);
sealed.name = "Bob";  // Works
sealed.age = 25;  // Fails
delete sealed.name;  // Fails

// Freeze object (completely immutable)
let frozen = { name: "Charlie" };
Object.freeze(frozen);
frozen.name = "David";  // Fails
frozen.age = 30;  // Fails

console.log(Object.isExtensible(person));  // false
console.log(Object.isSealed(sealed));  // true
console.log(Object.isFrozen(frozen));  // true
```

---

### 4. Object Methods

```javascript
let person = {
    name: "John",
    age: 30,
    greet() {
        console.log(`Hello, I'm ${this.name}`);
    },
    celebrateBirthday() {
        this.age++;
        console.log(`Happy birthday! Now ${this.age}`);
    }
};

person.greet();  // "Hello, I'm John"
person.celebrateBirthday();  // "Happy birthday! Now 31"

// Method shorthand vs function property
let obj = {
    // Method shorthand
    method1() {
        return "method";
    },
    // Function property
    method2: function() {
        return "function";
    },
    // Arrow function (no own 'this')
    method3: () => {
        return "arrow";
    }
};

// this in methods
let user = {
    name: "Alice",
    sayHi() {
        console.log(this.name);  // 'this' refers to user
    }
};

user.sayHi();  // "Alice"

// Losing 'this' context
let sayHi = user.sayHi;
// sayHi();  // undefined or error (this is not user)

// Bind to preserve context
let boundSayHi = user.sayHi.bind(user);
boundSayHi();  // "Alice"
```

---

### 5. Object Iteration

```javascript
let person = {
    name: "John",
    age: 30,
    city: "New York"
};

// for...in loop
for (let key in person) {
    console.log(`${key}: ${person[key]}`);
}

// Object.keys() - array of keys
let keys = Object.keys(person);
console.log(keys);  // ["name", "age", "city"]

keys.forEach(key => {
    console.log(`${key}: ${person[key]}`);
});

// Object.values() - array of values
let values = Object.values(person);
console.log(values);  // ["John", 30, "New York"]

// Object.entries() - array of [key, value] pairs
let entries = Object.entries(person);
console.log(entries);
// [["name", "John"], ["age", 30], ["city", "New York"]]

entries.forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
});

// Convert back to object
let obj = Object.fromEntries(entries);
console.log(obj);  // { name: "John", age: 30, city: "New York" }

// Filter object properties
let filtered = Object.fromEntries(
    Object.entries(person).filter(([key, value]) => typeof value === "string")
);
console.log(filtered);  // { name: "John", city: "New York" }

// Map object values
let uppercased = Object.fromEntries(
    Object.entries(person).map(([key, value]) => 
        [key, typeof value === "string" ? value.toUpperCase() : value]
    )
);
console.log(uppercased);  // { name: "JOHN", age: 30, city: "NEW YORK" }
```

---

### 6. Object Destructuring

```javascript
let person = {
    name: "John",
    age: 30,
    city: "New York",
    country: "USA"
};

// Basic destructuring
let { name, age } = person;
console.log(name);  // "John"
console.log(age);   // 30

// Rename variables
let { name: fullName, age: years } = person;
console.log(fullName);  // "John"
console.log(years);     // 30

// Default values
let { name: n, salary = 50000 } = person;
console.log(salary);  // 50000

// Rest operator
let { name: personName, ...rest } = person;
console.log(rest);  // { age: 30, city: "New York", country: "USA" }

// Nested destructuring
let user = {
    id: 1,
    name: "Alice",
    address: {
        city: "Boston",
        country: "USA"
    }
};

let { address: { city, country } } = user;
console.log(city);     // "Boston"
console.log(country);  // "USA"

// Destructuring in function parameters
function greet({ name, age }) {
    console.log(`Hello, ${name}, age ${age}`);
}

greet(person);  // "Hello, John, age 30"

// With defaults in parameters
function createUser({ name, age = 18, role = "user" }) {
    return { name, age, role };
}

console.log(createUser({ name: "Bob" }));
// { name: "Bob", age: 18, role: "user" }
```

---

### 7. Object Cloning and Merging

```javascript
let original = {
    name: "John",
    age: 30,
    address: {
        city: "New York"
    }
};

// Shallow copy methods

// 1. Spread operator
let copy1 = { ...original };
copy1.name = "Jane";
console.log(original.name);  // "John" (unchanged)
copy1.address.city = "Boston";
console.log(original.address.city);  // "Boston" (nested object shared!)

// 2. Object.assign()
let copy2 = Object.assign({}, original);

// 3. Merge objects
let obj1 = { a: 1, b: 2 };
let obj2 = { b: 3, c: 4 };
let merged = { ...obj1, ...obj2 };
console.log(merged);  // { a: 1, b: 3, c: 4 } (obj2.b overwrites obj1.b)

// Multiple objects
let obj3 = { d: 5 };
let merged2 = Object.assign({}, obj1, obj2, obj3);

// Deep copy methods

// 1. JSON method (doesn't work with functions, undefined, symbols)
let deepCopy1 = JSON.parse(JSON.stringify(original));
deepCopy1.address.city = "LA";
console.log(original.address.city);  // "New York" (unchanged)

// 2. structuredClone() (modern, recommended)
let deepCopy2 = structuredClone(original);

// 3. Custom deep clone function
function deepClone(obj) {
    if (obj === null || typeof obj !== "object") return obj;
    
    if (obj instanceof Date) return new Date(obj);
    if (obj instanceof Array) return obj.map(item => deepClone(item));
    
    let clone = {};
    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
            clone[key] = deepClone(obj[key]);
        }
    }
    return clone;
}

// Comparing objects
let a = { x: 1 };
let b = { x: 1 };
console.log(a === b);  // false (different references)
console.log(JSON.stringify(a) === JSON.stringify(b));  // true (same content)
```

---

### 8. Getters and Setters

```javascript
let person = {
    firstName: "John",
    lastName: "Doe",
    
    // Getter
    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    },
    
    // Setter
    set fullName(value) {
        [this.firstName, this.lastName] = value.split(" ");
    }
};

// Use like properties (not methods)
console.log(person.fullName);  // "John Doe"

person.fullName = "Jane Smith";
console.log(person.firstName);  // "Jane"
console.log(person.lastName);   // "Smith"

// Computed property with getter
let user = {
    name: "Alice",
    birthYear: 1990,
    
    get age() {
        return new Date().getFullYear() - this.birthYear;
    }
};

console.log(user.age);  // Computed dynamically

// Data validation with setter
let account = {
    _balance: 0,
    
    get balance() {
        return this._balance;
    },
    
    set balance(value) {
        if (value < 0) {
            console.log("Balance cannot be negative");
            return;
        }
        this._balance = value;
    }
};

account.balance = 100;
console.log(account.balance);  // 100

account.balance = -50;  // "Balance cannot be negative"
console.log(account.balance);  // 100 (unchanged)

// Property descriptors
Object.defineProperty(person, "age", {
    get() {
        return new Date().getFullYear() - this.birthYear;
    },
    enumerable: true,
    configurable: true
});
```

---

### 9. Property Descriptors

```javascript
let obj = { name: "John" };

// Get property descriptor
let descriptor = Object.getOwnPropertyDescriptor(obj, "name");
console.log(descriptor);
// {
//   value: "John",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }

// Define property with descriptor
Object.defineProperty(obj, "age", {
    value: 30,
    writable: false,      // Can't modify
    enumerable: true,     // Shows in loops
    configurable: false   // Can't delete or reconfigure
});

obj.age = 40;  // Silently fails (strict mode throws error)
console.log(obj.age);  // 30

// delete obj.age;  // Fails (not configurable)

// Define multiple properties
Object.defineProperties(obj, {
    city: {
        value: "New York",
        writable: true,
        enumerable: true
    },
    country: {
        value: "USA",
        writable: false,
        enumerable: false
    }
});

// Get all descriptors
let descriptors = Object.getOwnPropertyDescriptors(obj);
console.log(descriptors);

// Non-enumerable properties don't show in loops
for (let key in obj) {
    console.log(key);  // name, age, city (not country)
}

console.log(Object.keys(obj));  // ["name", "age", "city"]
```

---

### 10. Object Static Methods

```javascript
// Object.assign() - copy/merge
let target = { a: 1 };
let source = { b: 2, c: 3 };
Object.assign(target, source);
console.log(target);  // { a: 1, b: 2, c: 3 }

// Object.create() - create with prototype
let proto = { greet() { console.log("Hello!"); } };
let obj = Object.create(proto);
obj.name = "John";
obj.greet();  // "Hello!"

// Object.freeze() - make immutable
let frozen = { name: "Alice" };
Object.freeze(frozen);
frozen.name = "Bob";  // Fails
frozen.age = 30;  // Fails

// Object.seal() - prevent add/delete
let sealed = { name: "Charlie" };
Object.seal(sealed);
sealed.name = "David";  // Works
sealed.age = 35;  // Fails

// Object.is() - strict equality
console.log(Object.is(25, 25));  // true
console.log(Object.is(NaN, NaN));  // true (different from ===)
console.log(Object.is(0, -0));  // false (different from ===)

// Object.keys(), values(), entries()
let user = { name: "John", age: 30 };
console.log(Object.keys(user));     // ["name", "age"]
console.log(Object.values(user));   // ["John", 30]
console.log(Object.entries(user));  // [["name", "John"], ["age", 30]]

// Object.fromEntries() - from entries to object
let entries = [["name", "Alice"], ["age", 25]];
let obj2 = Object.fromEntries(entries);
console.log(obj2);  // { name: "Alice", age: 25 }

// Object.getPrototypeOf() and setPrototypeOf()
let proto2 = { type: "animal" };
let dog = Object.create(proto2);
console.log(Object.getPrototypeOf(dog) === proto2);  // true

// Object.hasOwn() (ES2022) - better than hasOwnProperty
console.log(Object.hasOwn(user, "name"));  // true
console.log(Object.hasOwn(user, "toString"));  // false
```

---

## 💡 Examples

### Example 1: User Management System

```javascript
class UserManager {
    constructor() {
        this.users = {};
    }
    
    addUser(id, userData) {
        this.users[id] = {
            ...userData,
            createdAt: new Date(),
            active: true
        };
    }
    
    getUser(id) {
        return this.users[id];
    }
    
    updateUser(id, updates) {
        if (this.users[id]) {
            this.users[id] = { ...this.users[id], ...updates };
        }
    }
    
    deleteUser(id) {
        delete this.users[id];
    }
    
    getActiveUsers() {
        return Object.values(this.users).filter(user => user.active);
    }
    
    getUsersByRole(role) {
        return Object.values(this.users).filter(user => user.role === role);
    }
}

const manager = new UserManager();
manager.addUser(1, { name: "Alice", role: "admin" });
manager.addUser(2, { name: "Bob", role: "user" });
console.log(manager.getActiveUsers());
```

### Example 2: Configuration Object

```javascript
const config = {
    api: {
        baseUrl: "https://api.example.com",
        timeout: 5000,
        headers: {
            "Content-Type": "application/json"
        }
    },
    features: {
        darkMode: true,
        notifications: true
    },
    
    get apiUrl() {
        return `${this.api.baseUrl}/v1`;
    },
    
    toggleFeature(feature) {
        if (feature in this.features) {
            this.features[feature] = !this.features[feature];
        }
    },
    
    reset() {
        Object.assign(this, defaultConfig);
    }
};

const defaultConfig = structuredClone(config);
```

### Example 3: Object Validation

```javascript
function validateUser(user) {
    const requiredFields = ["name", "email", "age"];
    const errors = {};
    
    // Check required fields
    for (const field of requiredFields) {
        if (!Object.hasOwn(user, field)) {
            errors[field] = `${field} is required`;
        }
    }
    
    // Validate email
    if (user.email && !user.email.includes("@")) {
        errors.email = "Invalid email format";
    }
    
    // Validate age
    if (user.age && (user.age < 0 || user.age > 120)) {
        errors.age = "Age must be between 0 and 120";
    }
    
    return {
        isValid: Object.keys(errors).length === 0,
        errors
    };
}

console.log(validateUser({ name: "John", email: "invalid", age: 25 }));
// { isValid: false, errors: { email: "Invalid email format" } }
```

### Example 4: Object Transformation

```javascript
function transformObject(obj, transformers) {
    return Object.fromEntries(
        Object.entries(obj).map(([key, value]) => {
            const transformer = transformers[key];
            return [key, transformer ? transformer(value) : value];
        })
    );
}

const user = { name: "john", age: "30", active: "true" };

const transformed = transformObject(user, {
    name: v => v.toUpperCase(),
    age: v => parseInt(v),
    active: v => v === "true"
});

console.log(transformed);
// { name: "JOHN", age: 30, active: true }
```

### Example 5: Deep Merge

```javascript
function deepMerge(target, source) {
    const result = { ...target };
    
    for (const key in source) {
        if (source.hasOwnProperty(key)) {
            if (source[key] instanceof Object && !Array.isArray(source[key])) {
                result[key] = deepMerge(result[key] || {}, source[key]);
            } else {
                result[key] = source[key];
            }
        }
    }
    
    return result;
}

const defaults = {
    api: { url: "http://localhost", timeout: 5000 },
    theme: { color: "blue" }
};

const userSettings = {
    api: { timeout: 10000 },
    theme: { mode: "dark" }
};

const settings = deepMerge(defaults, userSettings);
console.log(settings);
// { api: { url: "http://localhost", timeout: 10000 }, theme: { color: "blue", mode: "dark" } }
```

---

## 🎤 Interview Questions

### Q1: What's the difference between dot notation and bracket notation?

**Answer:**
- **Dot notation**: Simple, readable, but limited to valid identifiers
- **Bracket notation**: Can use any string, variables, and computed names

```javascript
let obj = {
    name: "John",
    "favorite color": "blue"
};

// Dot notation
console.log(obj.name);  // "John"
// console.log(obj.favorite color);  // Syntax error!

// Bracket notation
console.log(obj["favorite color"]);  // "blue"

// Dynamic access
let prop = "name";
console.log(obj[prop]);  // "John" (dot notation can't do this)

// Computed property names
let key = "age";
let person = {
    [key]: 30  // Must use brackets
};
```

---

### Q2: How do you check if a property exists in an object?

**Answer:**

```javascript
let obj = { name: "John", age: 30 };

// 1. in operator (checks own + inherited)
console.log("name" in obj);  // true
console.log("toString" in obj);  // true (inherited)

// 2. hasOwnProperty (only own properties)
console.log(obj.hasOwnProperty("name"));  // true
console.log(obj.hasOwnProperty("toString"));  // false

// 3. Object.hasOwn() (ES2022, recommended)
console.log(Object.hasOwn(obj, "name"));  // true

// 4. Accessing property (can be undefined for other reasons)
console.log(obj.name !== undefined);  // true

// 5. Using optional chaining
if (obj?.name) {
    // property exists and is truthy
}
```

**Best Practice:** Use `Object.hasOwn()` for own properties, `in` for inherited check.

---

### Q3: What's the difference between shallow and deep copy?

**Answer:**

```javascript
let original = {
    name: "John",
    address: {
        city: "New York"
    }
};

// Shallow copy - nested objects are shared
let shallow = { ...original };
shallow.name = "Jane";  // ✅ Independent
shallow.address.city = "Boston";  // ❌ Affects original!
console.log(original.address.city);  // "Boston"

// Deep copy - everything is independent
let deep = structuredClone(original);
deep.address.city = "LA";
console.log(original.address.city);  // "Boston" (unchanged)

// JSON method (limitations: no functions, undefined, dates)
let deepJSON = JSON.parse(JSON.stringify(original));
```

---

### Q4: How do getters and setters work?

**Answer:**

```javascript
let user = {
    firstName: "John",
    lastName: "Doe",
    
    // Getter - accessed like property
    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    },
    
    // Setter - assigned like property
    set fullName(value) {
        [this.firstName, this.lastName] = value.split(" ");
    }
};

console.log(user.fullName);  // "John Doe" (no parentheses!)
user.fullName = "Jane Smith";  // Assignment, not function call
console.log(user.firstName);  // "Jane"

// Use cases:
// 1. Computed properties
// 2. Validation
// 3. Logging/debugging
// 4. Backwards compatibility

let account = {
    _balance: 0,
    
    set balance(value) {
        if (value < 0) throw new Error("Negative balance");
        this._balance = value;
    },
    
    get balance() {
        return this._balance;
    }
};
```

---

### Q5: What is object destructuring?

**Answer:**

```javascript
let user = {
    name: "John",
    age: 30,
    city: "New York"
};

// Basic destructuring
let { name, age } = user;
console.log(name);  // "John"

// Rename variables
let { name: userName, age: userAge } = user;

// Default values
let { name: n, country = "USA" } = user;

// Rest operator
let { name: personName, ...rest } = user;
console.log(rest);  // { age: 30, city: "New York" }

// Nested destructuring
let employee = {
    id: 1,
    info: { name: "Alice", role: "Developer" }
};

let { info: { name: empName, role } } = employee;

// In function parameters
function greet({ name, age = 18 }) {
    console.log(`${name}, ${age}`);
}

greet(user);  // "John, 30"
```

---

### Q6: How do you merge objects?

**Answer:**

```javascript
let obj1 = { a: 1, b: 2 };
let obj2 = { b: 3, c: 4 };

// 1. Spread operator (shallow)
let merged1 = { ...obj1, ...obj2 };
console.log(merged1);  // { a: 1, b: 3, c: 4 }

// 2. Object.assign() (shallow, mutates first arg)
let merged2 = Object.assign({}, obj1, obj2);

// 3. Later values override earlier ones
let merged3 = { ...obj2, ...obj1 };
console.log(merged3);  // { b: 2, c: 4, a: 1 }

// Deep merge (custom function needed)
function deepMerge(target, source) {
    const result = { ...target };
    for (let key in source) {
        if (source[key] instanceof Object && !Array.isArray(source[key])) {
            result[key] = deepMerge(result[key] || {}, source[key]);
        } else {
            result[key] = source[key];
        }
    }
    return result;
}
```

---

### Q7: What are property descriptors?

**Answer:**

```javascript
let obj = { name: "John" };

// Get descriptor
let descriptor = Object.getOwnPropertyDescriptor(obj, "name");
console.log(descriptor);
// {
//   value: "John",
//   writable: true,     // Can modify value
//   enumerable: true,   // Shows in loops
//   configurable: true  // Can delete/reconfigure
// }

// Define property with custom descriptor
Object.defineProperty(obj, "age", {
    value: 30,
    writable: false,      // Read-only
    enumerable: false,    // Hidden in loops
    configurable: false   // Can't delete
});

obj.age = 40;  // Fails (not writable)
delete obj.age;  // Fails (not configurable)

for (let key in obj) {
    console.log(key);  // Only "name" (age not enumerable)
}

// Use cases:
// - Create constants (writable: false)
// - Hide internal properties (enumerable: false)
// - Prevent deletion (configurable: false)
```

---

### Q8: How do you iterate over object properties?

**Answer:**

```javascript
let obj = { name: "John", age: 30, city: "NYC" };

// 1. for...in (includes inherited enumerable properties)
for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
        console.log(`${key}: ${obj[key]}`);
    }
}

// 2. Object.keys() (own enumerable properties)
Object.keys(obj).forEach(key => {
    console.log(`${key}: ${obj[key]}`);
});

// 3. Object.values() (values only)
Object.values(obj).forEach(value => {
    console.log(value);
});

// 4. Object.entries() (key-value pairs)
Object.entries(obj).forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
});

// 5. for...of with entries
for (let [key, value] of Object.entries(obj)) {
    console.log(`${key}: ${value}`);
}

// Comparison:
// - for...in: checks inherited, needs hasOwnProperty check
// - Object.keys/values/entries: only own properties, cleaner
```

---

### Q9: What's the difference between Object.freeze(), Object.seal(), and Object.preventExtensions()?

**Answer:**

```javascript
// preventExtensions: Can't add properties
let obj1 = { name: "John" };
Object.preventExtensions(obj1);
obj1.age = 30;  // ❌ Fails
obj1.name = "Jane";  // ✅ Can modify
delete obj1.name;  // ✅ Can delete

// seal: Can't add or delete properties
let obj2 = { name: "John" };
Object.seal(obj2);
obj2.age = 30;  // ❌ Fails
obj2.name = "Jane";  // ✅ Can modify
delete obj2.name;  // ❌ Fails

// freeze: Completely immutable
let obj3 = { name: "John" };
Object.freeze(obj3);
obj3.age = 30;  // ❌ Fails
obj3.name = "Jane";  // ❌ Fails
delete obj3.name;  // ❌ Fails

// Summary:
// preventExtensions: modify ✅, delete ✅, add ❌
// seal: modify ✅, delete ❌, add ❌
// freeze: modify ❌, delete ❌, add ❌

// Check status
console.log(Object.isExtensible(obj1));  // false
console.log(Object.isSealed(obj2));  // true
console.log(Object.isFrozen(obj3));  // true

// Note: All are shallow (nested objects not affected)
```

---

### Q10: How does the `this` keyword work in objects?

**Answer:**

```javascript
let user = {
    name: "John",
    
    // Regular function: 'this' is user
    greet: function() {
        console.log(this.name);
    },
    
    // Arrow function: 'this' from outer scope
    greetArrow: () => {
        console.log(this.name);  // undefined (or global)
    }
};

user.greet();  // "John"
user.greetArrow();  // undefined

// Losing context
let greet = user.greet;
// greet();  // undefined or error ('this' is not user)

// Solutions:
// 1. Bind
let boundGreet = user.greet.bind(user);
boundGreet();  // "John"

// 2. Arrow function wrapper
setTimeout(() => user.greet(), 1000);

// 3. call/apply
user.greet.call(user);  // "John"

// In nested functions
let obj = {
    name: "Alice",
    outer() {
        function inner() {
            console.log(this.name);  // undefined (this is not obj)
        }
        inner();
        
        // Solution: arrow function
        const innerArrow = () => {
            console.log(this.name);  // "Alice"
        };
        innerArrow();
    }
};
```

---

## ✅ Best Practices

### 1. Use Object Literals for Simple Objects
```javascript
// ❌ Verbose
let obj = new Object();
obj.name = "John";
obj.age = 30;

// ✅ Concise
let obj = { name: "John", age: 30 };
```

### 2. Use Shorthand Properties and Methods
```javascript
let name = "John";
let age = 30;

// ❌ Redundant
let user = {
    name: name,
    age: age,
    greet: function() {}
};

// ✅ Shorthand
let user = {
    name,
    age,
    greet() {}
};
```

### 3. Use Optional Chaining
```javascript
// ❌ Manual checks
if (user && user.address && user.address.city) {
    console.log(user.address.city);
}

// ✅ Optional chaining
console.log(user?.address?.city);
```

### 4. Use Object Destructuring
```javascript
// ❌ Repetitive
function greet(user) {
    console.log(user.name);
    console.log(user.age);
}

// ✅ Destructured
function greet({ name, age }) {
    console.log(name);
    console.log(age);
}
```

### 5. Prefer Immutable Operations
```javascript
// ❌ Mutates original
obj.newProp = value;

// ✅ Creates new object
const updated = { ...obj, newProp: value };
```

---

## 📚 Resources

- [MDN: Working with Objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects)
- [MDN: Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)
- [JavaScript.info: Objects](https://javascript.info/object)

---

## 📝 Summary

- Objects store **key-value pairs** representing entities
- Access properties with **dot** or **bracket** notation
- Use **destructuring** for clean code
- **Getters/setters** for computed and validated properties
- **Object.keys/values/entries** for iteration
- **Spread operator** for shallow copy/merge
- **structuredClone()** for deep copy
- **freeze/seal** for immutability
- **this** refers to object in methods (not in arrow functions)
- Property **descriptors** control behavior (writable, enumerable, configurable)

---

<div align="center">

**[⬅️ Previous: Arrays](./05-arrays.md)** | **[🏠 Home](./README.md)** | **[Next: Classes & OOP ➡️](./07-classes.md)**

</div>
