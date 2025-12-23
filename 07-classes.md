# 07. Classes & OOP in JavaScript

> Classes provide a blueprint for creating objects with shared properties and methods.

## 📋 Overview

Classes in JavaScript provide a cleaner, more intuitive syntax for creating objects and implementing object-oriented programming (OOP) patterns. Introduced in ES6, classes are syntactic sugar over JavaScript's prototype-based inheritance.

**What You'll Learn:**
- ✅ Class syntax and constructors
- ✅ Instance properties and methods
- ✅ Static methods and properties
- ✅ Inheritance and extends
- ✅ super keyword
- ✅ Private fields and methods
- ✅ Getters and setters in classes
- ✅ OOP principles (encapsulation, inheritance, polymorphism)

---

## 🎯 Key Concepts

### 1. Class Basics

```javascript
// Class declaration
class Person {
    // Constructor
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    // Instance method
    greet() {
        console.log(`Hello, I'm ${this.name}`);
    }
    
    // Another method
    celebrateBirthday() {
        this.age++;
        console.log(`Happy birthday! Now ${this.age}`);
    }
}

// Creating instances
const person1 = new Person("John", 30);
const person2 = new Person("Alice", 25);

person1.greet();  // "Hello, I'm John"
person2.celebrateBirthday();  // "Happy birthday! Now 26"

// Class expression
const Animal = class {
    constructor(name) {
        this.name = name;
    }
    
    makeSound() {
        console.log(`${this.name} makes a sound`);
    }
};

// Named class expression
const Dog = class Canine {
    bark() {
        console.log("Woof!");
    }
};
```

---

### 2. Constructor

```javascript
class User {
    constructor(name, email) {
        // Initialize instance properties
        this.name = name;
        this.email = email;
        this.createdAt = new Date();
        this.active = true;
    }
    
    // Default parameter values
    constructor(name, email, role = "user") {
        this.name = name;
        this.email = email;
        this.role = role;
    }
}

// Constructor validation
class Product {
    constructor(name, price) {
        if (price < 0) {
            throw new Error("Price cannot be negative");
        }
        this.name = name;
        this.price = price;
    }
}

// Constructor can return different object
class Singleton {
    constructor() {
        if (Singleton.instance) {
            return Singleton.instance;
        }
        Singleton.instance = this;
    }
}

const s1 = new Singleton();
const s2 = new Singleton();
console.log(s1 === s2);  // true
```

---

### 3. Methods

```javascript
class Calculator {
    // Regular methods
    add(a, b) {
        return a + b;
    }
    
    subtract(a, b) {
        return a - b;
    }
    
    // Method calling another method
    addAndMultiply(a, b, multiplier) {
        return this.add(a, b) * multiplier;
    }
}

const calc = new Calculator();
console.log(calc.add(5, 3));  // 8
console.log(calc.addAndMultiply(5, 3, 2));  // 16

// Methods with default parameters
class Greeter {
    greet(name = "Guest", greeting = "Hello") {
        return `${greeting}, ${name}!`;
    }
}

// Method overloading (not native, use different approach)
class Formatter {
    format(...args) {
        if (args.length === 1) {
            return this.formatSingle(args[0]);
        } else if (args.length === 2) {
            return this.formatPair(args[0], args[1]);
        }
    }
    
    formatSingle(value) {
        return `Value: ${value}`;
    }
    
    formatPair(key, value) {
        return `${key}: ${value}`;
    }
}
```

---

### 4. Static Members

```javascript
class MathUtils {
    // Static method
    static add(a, b) {
        return a + b;
    }
    
    static multiply(a, b) {
        return a * b;
    }
    
    // Static property
    static PI = 3.14159;
    
    // Static method using static property
    static circleArea(radius) {
        return this.PI * radius * radius;
    }
}

// Call on class, not instance
console.log(MathUtils.add(5, 3));  // 8
console.log(MathUtils.PI);  // 3.14159
console.log(MathUtils.circleArea(5));  // 78.53975

// Cannot call on instance
const utils = new MathUtils();
// utils.add(5, 3);  // TypeError

// Static initialization block (ES2022)
class Config {
    static {
        // Runs when class is evaluated
        this.apiUrl = process.env.API_URL || "http://localhost";
        this.timeout = 5000;
    }
}

// Factory pattern with static method
class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }
    
    static createGuest() {
        return new User("Guest", "guest@example.com");
    }
    
    static createAdmin(name, email) {
        const user = new User(name, email);
        user.role = "admin";
        return user;
    }
}

const guest = User.createGuest();
const admin = User.createAdmin("Alice", "alice@example.com");
```

---

### 5. Inheritance

```javascript
// Parent class
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    makeSound() {
        console.log(`${this.name} makes a sound`);
    }
    
    sleep() {
        console.log(`${this.name} is sleeping`);
    }
}

// Child class extends parent
class Dog extends Animal {
    constructor(name, breed) {
        super(name);  // Call parent constructor
        this.breed = breed;
    }
    
    // Override parent method
    makeSound() {
        console.log(`${this.name} barks: Woof!`);
    }
    
    // New method specific to Dog
    fetch() {
        console.log(`${this.name} is fetching`);
    }
}

const dog = new Dog("Max", "Golden Retriever");
dog.makeSound();  // "Max barks: Woof!" (overridden)
dog.sleep();      // "Max is sleeping" (inherited)
dog.fetch();      // "Max is fetching" (own method)

// Multi-level inheritance
class GermanShepherd extends Dog {
    constructor(name) {
        super(name, "German Shepherd");
    }
    
    guard() {
        console.log(`${this.name} is guarding`);
    }
}

const shepherd = new GermanShepherd("Rex");
shepherd.makeSound();  // Inherited from Dog
shepherd.guard();      // Own method

// Check inheritance
console.log(dog instanceof Dog);     // true
console.log(dog instanceof Animal);  // true
console.log(dog instanceof Object);  // true
```

---

### 6. Super Keyword

```javascript
class Vehicle {
    constructor(brand) {
        this.brand = brand;
    }
    
    start() {
        console.log(`${this.brand} vehicle starting...`);
    }
}

class Car extends Vehicle {
    constructor(brand, model) {
        super(brand);  // Must call super before using 'this'
        this.model = model;
    }
    
    // Override and extend
    start() {
        super.start();  // Call parent method
        console.log(`${this.model} engine started`);
    }
    
    display() {
        console.log(`${this.brand} ${this.model}`);
    }
}

const car = new Car("Toyota", "Camry");
car.start();
// "Toyota vehicle starting..."
// "Camry engine started"

// Super in static methods
class Parent {
    static greet() {
        console.log("Hello from Parent");
    }
}

class Child extends Parent {
    static greet() {
        super.greet();  // Call parent static method
        console.log("Hello from Child");
    }
}

Child.greet();
// "Hello from Parent"
// "Hello from Child"
```

---

### 7. Private Fields and Methods

```javascript
class BankAccount {
    // Private field (ES2022)
    #balance = 0;
    #accountNumber;
    
    constructor(accountNumber, initialBalance = 0) {
        this.#accountNumber = accountNumber;
        this.#balance = initialBalance;
    }
    
    // Private method
    #validateAmount(amount) {
        if (amount <= 0) {
            throw new Error("Amount must be positive");
        }
    }
    
    // Public methods to interact with private fields
    deposit(amount) {
        this.#validateAmount(amount);
        this.#balance += amount;
        return this.#balance;
    }
    
    withdraw(amount) {
        this.#validateAmount(amount);
        if (amount > this.#balance) {
            throw new Error("Insufficient funds");
        }
        this.#balance -= amount;
        return this.#balance;
    }
    
    getBalance() {
        return this.#balance;
    }
}

const account = new BankAccount("12345", 1000);
console.log(account.getBalance());  // 1000
account.deposit(500);
console.log(account.getBalance());  // 1500

// Cannot access private fields
// console.log(account.#balance);  // SyntaxError
// account.#validateAmount(100);   // SyntaxError

// Private static fields and methods
class Counter {
    static #count = 0;
    
    static #validateIncrement(value) {
        if (value <= 0) throw new Error("Must be positive");
    }
    
    static increment(value = 1) {
        this.#validateIncrement(value);
        this.#count += value;
    }
    
    static getCount() {
        return this.#count;
    }
}
```

---

### 8. Getters and Setters

```javascript
class User {
    constructor(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
        this._email = "";  // Convention: underscore for "private"
    }
    
    // Getter
    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
    
    // Setter
    set fullName(value) {
        [this.firstName, this.lastName] = value.split(" ");
    }
    
    // Getter with validation
    get email() {
        return this._email;
    }
    
    // Setter with validation
    set email(value) {
        if (!value.includes("@")) {
            throw new Error("Invalid email");
        }
        this._email = value;
    }
}

const user = new User("John", "Doe");
console.log(user.fullName);  // "John Doe" (no parentheses!)

user.fullName = "Jane Smith";
console.log(user.firstName);  // "Jane"

user.email = "jane@example.com";  // Works
// user.email = "invalid";  // Error: Invalid email

// Private fields with getters/setters
class Circle {
    #radius = 0;
    
    get radius() {
        return this.#radius;
    }
    
    set radius(value) {
        if (value < 0) {
            throw new Error("Radius cannot be negative");
        }
        this.#radius = value;
    }
    
    get area() {
        return Math.PI * this.#radius ** 2;
    }
    
    get circumference() {
        return 2 * Math.PI * this.#radius;
    }
}

const circle = new Circle();
circle.radius = 5;
console.log(circle.area);  // 78.53981633974483
console.log(circle.circumference);  // 31.41592653589793
```

---

## 💡 Examples

### Example 1: Complete User System

```javascript
class User {
    static #users = [];
    static #nextId = 1;
    
    #id;
    #password;
    
    constructor(username, email, password) {
        this.#id = User.#nextId++;
        this.username = username;
        this.email = email;
        this.#password = this.#hashPassword(password);
        this.createdAt = new Date();
        User.#users.push(this);
    }
    
    #hashPassword(password) {
        // Simple hash (use proper hashing in production)
        return btoa(password);
    }
    
    validatePassword(password) {
        return this.#hashPassword(password) === this.#password;
    }
    
    get id() {
        return this.#id;
    }
    
    static findById(id) {
        return User.#users.find(user => user.id === id);
    }
    
    static findByEmail(email) {
        return User.#users.find(user => user.email === email);
    }
    
    static getAllUsers() {
        return User.#users.map(user => ({
            id: user.id,
            username: user.username,
            email: user.email
        }));
    }
}

const user1 = new User("john_doe", "john@example.com", "password123");
const user2 = new User("jane_smith", "jane@example.com", "secret456");

console.log(User.findById(1));
console.log(user1.validatePassword("password123"));  // true
console.log(User.getAllUsers());
```

### Example 2: Shape Hierarchy

```javascript
class Shape {
    constructor(color) {
        this.color = color;
    }
    
    area() {
        throw new Error("area() must be implemented");
    }
    
    perimeter() {
        throw new Error("perimeter() must be implemented");
    }
    
    describe() {
        return `A ${this.color} ${this.constructor.name}`;
    }
}

class Rectangle extends Shape {
    constructor(width, height, color) {
        super(color);
        this.width = width;
        this.height = height;
    }
    
    area() {
        return this.width * this.height;
    }
    
    perimeter() {
        return 2 * (this.width + this.height);
    }
}

class Circle extends Shape {
    constructor(radius, color) {
        super(color);
        this.radius = radius;
    }
    
    area() {
        return Math.PI * this.radius ** 2;
    }
    
    perimeter() {
        return 2 * Math.PI * this.radius;
    }
}

class Square extends Rectangle {
    constructor(side, color) {
        super(side, side, color);
    }
}

const shapes = [
    new Rectangle(5, 10, "red"),
    new Circle(7, "blue"),
    new Square(4, "green")
];

shapes.forEach(shape => {
    console.log(shape.describe());
    console.log(`Area: ${shape.area().toFixed(2)}`);
    console.log(`Perimeter: ${shape.perimeter().toFixed(2)}`);
    console.log("---");
});
```

### Example 3: Task Management System

```javascript
class Task {
    static #nextId = 1;
    
    #id;
    #completed = false;
    
    constructor(title, description, priority = "medium") {
        this.#id = Task.#nextId++;
        this.title = title;
        this.description = description;
        this.priority = priority;
        this.createdAt = new Date();
    }
    
    get id() {
        return this.#id;
    }
    
    get isCompleted() {
        return this.#completed;
    }
    
    complete() {
        this.#completed = true;
        this.completedAt = new Date();
    }
    
    uncomplete() {
        this.#completed = false;
        delete this.completedAt;
    }
}

class TaskList {
    #tasks = [];
    
    constructor(name) {
        this.name = name;
    }
    
    addTask(title, description, priority) {
        const task = new Task(title, description, priority);
        this.#tasks.push(task);
        return task;
    }
    
    removeTask(taskId) {
        const index = this.#tasks.findIndex(t => t.id === taskId);
        if (index !== -1) {
            this.#tasks.splice(index, 1);
            return true;
        }
        return false;
    }
    
    getTask(taskId) {
        return this.#tasks.find(t => t.id === taskId);
    }
    
    getAllTasks() {
        return [...this.#tasks];
    }
    
    getCompletedTasks() {
        return this.#tasks.filter(t => t.isCompleted);
    }
    
    getPendingTasks() {
        return this.#tasks.filter(t => !t.isCompleted);
    }
    
    getTasksByPriority(priority) {
        return this.#tasks.filter(t => t.priority === priority);
    }
}

const myTasks = new TaskList("Work Tasks");
myTasks.addTask("Write report", "Q4 financial report", "high");
myTasks.addTask("Team meeting", "Weekly sync", "medium");
myTasks.addTask("Code review", "Review PR #123", "high");

const task = myTasks.getTask(1);
task.complete();

console.log(`Completed: ${myTasks.getCompletedTasks().length}`);
console.log(`Pending: ${myTasks.getPendingTasks().length}`);
```

---

## 🎤 Interview Questions

### Q1: What's the difference between a class and a function constructor?

**Answer:**

```javascript
// Function constructor (old way)
function PersonFunc(name, age) {
    this.name = name;
    this.age = age;
}

PersonFunc.prototype.greet = function() {
    console.log(`Hello, I'm ${this.name}`);
};

// Class (modern way)
class PersonClass {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        console.log(`Hello, I'm ${this.name}`);
    }
}

// Key differences:
// 1. Classes must be called with 'new'
// const p = PersonClass("John");  // Error
const p = new PersonClass("John");  // Works

// 2. Class methods are non-enumerable
console.log(Object.keys(new PersonFunc("A", 20)));  // ["name", "age", "greet"]
console.log(Object.keys(new PersonClass("A", 20)));  // ["name", "age"]

// 3. Classes have implicit strict mode
// 4. Classes are not hoisted
```

**Best Practice:** Use classes for cleaner, more maintainable code.

---

### Q2: How do private fields work in JavaScript?

**Answer:**

```javascript
class BankAccount {
    // Private field with # prefix
    #balance = 0;
    
    constructor(initialBalance) {
        this.#balance = initialBalance;
    }
    
    deposit(amount) {
        this.#balance += amount;
    }
    
    getBalance() {
        return this.#balance;
    }
}

const account = new BankAccount(1000);
account.deposit(500);
console.log(account.getBalance());  // 1500

// Cannot access from outside
// console.log(account.#balance);  // SyntaxError
// console.log(account["#balance"]);  // undefined

// Private fields:
// - Must be declared in class body
// - Cannot be created dynamically
// - Not accessible via this, bracket notation, or reflection
// - Truly private (unlike _convention)
```

---

### Q3: What is the purpose of static methods?

**Answer:**

```javascript
class MathUtils {
    // Static method - belongs to class, not instances
    static add(a, b) {
        return a + b;
    }
    
    // Instance method - belongs to instances
    multiply(a, b) {
        return a * b;
    }
}

// Static: called on class
console.log(MathUtils.add(5, 3));  // 8

// Instance: called on object
const utils = new MathUtils();
console.log(utils.multiply(5, 3));  // 15

// Cannot call static on instance
// utils.add(5, 3);  // TypeError

// Use cases:
// 1. Factory methods
class User {
    static createGuest() {
        return new User("Guest");
    }
}

// 2. Utility functions
class StringUtils {
    static capitalize(str) {
        return str.charAt(0).toUpperCase() + str.slice(1);
    }
}

// 3. Shared data/configuration
class Config {
    static apiUrl = "https://api.example.com";
}
```

---

### Q4: How does inheritance work with the `extends` keyword?

**Answer:**

```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    makeSound() {
        console.log(`${this.name} makes a sound`);
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name);  // Must call super first!
        this.breed = breed;
    }
    
    // Override parent method
    makeSound() {
        console.log(`${this.name} barks`);
    }
    
    // Call parent method from override
    makeAnimalSound() {
        super.makeSound();
    }
}

const dog = new Dog("Max", "Labrador");
dog.makeSound();  // "Max barks" (overridden)
dog.makeAnimalSound();  // "Max makes a sound" (parent method)

// Inheritance chain
console.log(dog instanceof Dog);     // true
console.log(dog instanceof Animal);  // true
console.log(dog instanceof Object);  // true

// Access parent properties
console.log(dog.name);   // "Max" (from Animal)
console.log(dog.breed);  // "Labrador" (from Dog)
```

---

### Q5: What's the difference between getters/setters and regular methods?

**Answer:**

```javascript
class Rectangle {
    constructor(width, height) {
        this.width = width;
        this.height = height;
    }
    
    // Getter - accessed like property
    get area() {
        return this.width * this.height;
    }
    
    // Regular method - called with parentheses
    calculateArea() {
        return this.width * this.height;
    }
}

const rect = new Rectangle(5, 10);

// Getter: no parentheses
console.log(rect.area);  // 50

// Method: requires parentheses
console.log(rect.calculateArea());  // 50

// Setters provide validation
class User {
    constructor() {
        this._age = 0;
    }
    
    get age() {
        return this._age;
    }
    
    set age(value) {
        if (value < 0 || value > 120) {
            throw new Error("Invalid age");
        }
        this._age = value;
    }
}

const user = new User();
user.age = 25;  // Looks like assignment
console.log(user.age);  // 25

// user.age = -5;  // Error: Invalid age
```

**Use getters/setters when:**
- Computed properties
- Validation needed
- Want property-like syntax
- Backwards compatibility

---

### Q6: Can you have multiple constructors in a class?

**Answer:**

```javascript
// No, JavaScript doesn't support multiple constructors
// But you can simulate it:

class User {
    constructor(data) {
        if (typeof data === "string") {
            // String constructor
            this.name = data;
            this.email = "";
        } else if (typeof data === "object") {
            // Object constructor
            this.name = data.name;
            this.email = data.email;
        }
    }
    
    // Better: static factory methods
    static fromString(name) {
        return new User({ name, email: "" });
    }
    
    static fromObject(obj) {
        return new User(obj);
    }
    
    static fromJSON(json) {
        return new User(JSON.parse(json));
    }
}

// Usage
const user1 = new User("John");
const user2 = new User({ name: "Jane", email: "jane@example.com" });
const user3 = User.fromString("Bob");
const user4 = User.fromJSON('{"name":"Alice","email":"alice@example.com"}');
```

---

### Q7: What happens if you don't call `super()` in a derived class?

**Answer:**

```javascript
class Parent {
    constructor(name) {
        this.name = name;
    }
}

class Child extends Parent {
    constructor(name, age) {
        // ❌ Forgetting super()
        // this.age = age;  // ReferenceError: Must call super before accessing 'this'
        
        // ✅ Correct: call super first
        super(name);
        this.age = age;
    }
}

// Rules:
// 1. Must call super() before using 'this'
// 2. super() must be called if you define a constructor
// 3. super() can only be called in derived class constructors

// If you don't define constructor, it's called automatically
class Child2 extends Parent {
    // Implicit: constructor(...args) { super(...args); }
}

const child = new Child2("Alice");
console.log(child.name);  // "Alice"
```

---

### Q8: How do you implement abstract classes in JavaScript?

**Answer:**

```javascript
// JavaScript doesn't have abstract keyword, but we can simulate it

class AbstractShape {
    constructor(color) {
        if (new.target === AbstractShape) {
            throw new Error("Cannot instantiate abstract class");
        }
        this.color = color;
    }
    
    // Abstract method (must be overridden)
    area() {
        throw new Error("Method 'area()' must be implemented");
    }
    
    // Concrete method (can be used as-is)
    describe() {
        return `A ${this.color} shape with area ${this.area()}`;
    }
}

class Circle extends AbstractShape {
    constructor(radius, color) {
        super(color);
        this.radius = radius;
    }
    
    area() {
        return Math.PI * this.radius ** 2;
    }
}

// const shape = new AbstractShape("red");  // Error: Cannot instantiate
const circle = new Circle(5, "blue");
console.log(circle.describe());  // Works
```

---

### Q9: What are mixins and how do you use them?

**Answer:**

```javascript
// Mixin: reusable functionality for multiple classes

// Define mixins
const canEat = {
    eat(food) {
        console.log(`${this.name} is eating ${food}`);
    }
};

const canWalk = {
    walk() {
        console.log(`${this.name} is walking`);
    }
};

const canSwim = {
    swim() {
        console.log(`${this.name} is swimming`);
    }
};

// Apply mixins to class
function applyMixins(targetClass, ...mixins) {
    mixins.forEach(mixin => {
        Object.assign(targetClass.prototype, mixin);
    });
}

class Animal {
    constructor(name) {
        this.name = name;
    }
}

class Dog extends Animal {}
class Fish extends Animal {}

// Apply different mixins
applyMixins(Dog, canEat, canWalk);
applyMixins(Fish, canEat, canSwim);

const dog = new Dog("Buddy");
dog.eat("bone");   // Works
dog.walk();        // Works
// dog.swim();     // Error: not a function

const fish = new Fish("Nemo");
fish.eat("plankton");  // Works
fish.swim();           // Works
// fish.walk();        // Error: not a function
```

---

### Q10: How do you create a singleton class?

**Answer:**

```javascript
// Singleton: only one instance allowed

class Database {
    static #instance = null;
    
    constructor() {
        if (Database.#instance) {
            return Database.#instance;
        }
        
        // Initialize
        this.connection = null;
        this.connected = false;
        
        Database.#instance = this;
    }
    
    connect() {
        this.connected = true;
        console.log("Connected to database");
    }
    
    disconnect() {
        this.connected = false;
        console.log("Disconnected from database");
    }
    
    static getInstance() {
        if (!Database.#instance) {
            Database.#instance = new Database();
        }
        return Database.#instance;
    }
}

// Both create/return same instance
const db1 = new Database();
const db2 = new Database();
const db3 = Database.getInstance();

console.log(db1 === db2);  // true
console.log(db2 === db3);  // true

// Alternative: module singleton
// database.js
class Database {
    connect() { console.log("Connected"); }
}

export default new Database();  // Export instance, not class

// Other files import the same instance
// import db from './database.js';
```

---

## ✅ Best Practices

### 1. Use Class Fields
```javascript
// ❌ Old way
class User {
    constructor() {
        this.active = true;
    }
}

// ✅ Modern class fields
class User {
    active = true;
    
    constructor() {
        // Constructor for parameters only
    }
}
```

### 2. Use Private Fields for Encapsulation
```javascript
// ❌ Convention (not truly private)
class Account {
    constructor() {
        this._balance = 0;  // Can still be accessed
    }
}

// ✅ True privacy
class Account {
    #balance = 0;  // Truly private
    
    getBalance() {
        return this.#balance;
    }
}
```

### 3. Use Static Factory Methods
```javascript
// ❌ Complex constructor
class User {
    constructor(data) {
        if (typeof data === "string") {
            this.name = data;
        } else {
            Object.assign(this, data);
        }
    }
}

// ✅ Clear factory methods
class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }
    
    static fromName(name) {
        return new User(name, "");
    }
    
    static fromObject(obj) {
        return new User(obj.name, obj.email);
    }
}
```

### 4. Prefer Composition Over Inheritance
```javascript
// ❌ Deep inheritance hierarchy
class Animal {}
class Mammal extends Animal {}
class Dog extends Mammal {}

// ✅ Composition
class Animal {
    constructor() {
        this.behaviors = [];
    }
    
    addBehavior(behavior) {
        this.behaviors.push(behavior);
    }
}

const dog = new Animal();
dog.addBehavior(new CanBark());
dog.addBehavior(new CanRun());
```

### 5. Keep Classes Focused (Single Responsibility)
```javascript
// ❌ Too many responsibilities
class User {
    save() {}
    validate() {}
    sendEmail() {}
    generateReport() {}
}

// ✅ Single responsibility
class User {}
class UserRepository {
    save(user) {}
}
class UserValidator {
    validate(user) {}
}
class EmailService {
    sendEmail(user) {}
}
```

---

## 📚 Resources

- [MDN: Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
- [JavaScript.info: Classes](https://javascript.info/classes)
- [MDN: Private Class Features](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Private_class_fields)

---

## 📝 Summary

- **Classes** provide clean syntax for object creation
- **Constructor** initializes instance properties
- **Methods** define instance behavior
- **Static** members belong to class, not instances
- **Extends** creates inheritance relationships
- **Super** accesses parent class features
- **Private fields** (#) ensure true encapsulation
- **Getters/setters** provide property-like access with logic
- **Inheritance** enables code reuse and polymorphism
- Prefer **composition** over deep inheritance

---

<div align="center">

**[⬅️ Previous: Objects](./06-objects.md)** | **[🏠 Home](./README.md)** | **[Next: Async Programming ➡️](./08-async-programming.md)**

</div>
