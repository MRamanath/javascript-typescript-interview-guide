# 12. Prototypes & Inheritance in JavaScript

> Prototypes are the mechanism by which JavaScript objects inherit features from one another.

## 📋 Overview

JavaScript uses prototypal inheritance, which is different from classical inheritance found in languages like Java or C++. Understanding prototypes is crucial for mastering JavaScript's object-oriented capabilities.

**What You'll Learn:**
- ✅ What prototypes are and how they work
- ✅ The prototype chain
- ✅ Constructor functions and prototypes
- ✅ Object.create() and prototypal inheritance
- ✅ ES6 classes vs prototypes
- ✅ Inheritance patterns
- ✅ hasOwnProperty vs in operator
- ✅ Common prototype patterns

---

## 🎯 Key Concepts

### 1. Understanding Prototypes

```javascript
// Every JavaScript object has a prototype
const obj = {};

console.log(obj.__proto__);  // Object.prototype
console.log(Object.getPrototypeOf(obj));  // Object.prototype (preferred)

// Prototype chain: obj -> Object.prototype -> null

// Objects inherit properties from their prototype
const person = {
    greet() {
        return "Hello!";
    }
};

const john = Object.create(person);
john.name = "John";

console.log(john.name);   // "John" (own property)
console.log(john.greet());  // "Hello!" (inherited from person)

// Check where property comes from
console.log(john.hasOwnProperty('name'));   // true
console.log(john.hasOwnProperty('greet'));  // false (inherited)

// Prototype is a regular object
console.log(typeof Object.prototype);  // "object"
console.log(Object.prototype.constructor);  // [Function: Object]

// null is the end of the prototype chain
console.log(Object.getPrototypeOf(Object.prototype));  // null
```

---

### 2. Constructor Functions and Prototypes

```javascript
// Constructor function (pre-ES6 way)
function Person(name, age) {
    this.name = name;
    this.age = age;
}

// Methods on prototype (shared by all instances)
Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

Person.prototype.getAge = function() {
    return this.age;
};

// Creating instances
const john = new Person("John", 30);
const jane = new Person("Jane", 25);

console.log(john.greet());  // "Hello, I'm John"
console.log(jane.greet());  // "Hello, I'm Jane"

// Methods are shared (same reference)
console.log(john.greet === jane.greet);  // true

// What 'new' does:
// 1. Creates empty object
// 2. Sets object's [[Prototype]] to constructor's prototype
// 3. Executes constructor with 'this' bound to new object
// 4. Returns the object (unless constructor returns object)

// Manual implementation of 'new'
function myNew(constructor, ...args) {
    const obj = Object.create(constructor.prototype);
    const result = constructor.apply(obj, args);
    return typeof result === 'object' && result !== null ? result : obj;
}

const alice = myNew(Person, "Alice", 28);
console.log(alice.greet());  // "Hello, I'm Alice"

// Adding to prototype after creation
Person.prototype.celebrateBirthday = function() {
    this.age++;
    return `Happy birthday! Now ${this.age}`;
};

// All existing instances get the new method
console.log(john.celebrateBirthday());  // "Happy birthday! Now 31"

// Constructor property
console.log(john.constructor);  // [Function: Person]
console.log(john.constructor === Person);  // true

// Don't replace entire prototype (breaks constructor)
Person.prototype = {
    // constructor: Person,  // Need to add this back
    newMethod() {
        return "New";
    }
};

// Better: Add methods individually
Person.prototype.anotherMethod = function() {
    return "Another";
};
```

---

### 3. Prototype Chain

```javascript
// Understanding the chain

function Animal(name) {
    this.name = name;
}

Animal.prototype.eat = function() {
    return `${this.name} is eating`;
};

function Dog(name, breed) {
    Animal.call(this, name);  // Call parent constructor
    this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;  // Fix constructor reference

Dog.prototype.bark = function() {
    return `${this.name} barks: Woof!`;
};

const rex = new Dog("Rex", "German Shepherd");

console.log(rex.bark());  // "Rex barks: Woof!" (own method)
console.log(rex.eat());   // "Rex is eating" (inherited from Animal)

// Prototype chain: rex -> Dog.prototype -> Animal.prototype -> Object.prototype -> null

// Property lookup
console.log(rex.name);  // Found on rex (own property)
console.log(rex.breed);  // Found on rex (own property)
// console.log(rex.bark());  // Found on Dog.prototype
// console.log(rex.eat());  // Found on Animal.prototype
console.log(rex.toString());  // Found on Object.prototype

// Checking prototype chain
console.log(rex instanceof Dog);  // true
console.log(rex instanceof Animal);  // true
console.log(rex instanceof Object);  // true

console.log(Dog.prototype.isPrototypeOf(rex));  // true
console.log(Animal.prototype.isPrototypeOf(rex));  // true

// Getting prototype
console.log(Object.getPrototypeOf(rex) === Dog.prototype);  // true
console.log(Object.getPrototypeOf(Dog.prototype) === Animal.prototype);  // true

// Shadowing properties
rex.eat = function() {
    return `${this.name} is eating dog food`;
};

console.log(rex.eat());  // Uses own property, not inherited

// Delete to reveal inherited property
delete rex.eat;
console.log(rex.eat());  // Now uses inherited method
```

---

### 4. Object.create()

```javascript
// Object.create() for prototypal inheritance

const animal = {
    type: "Animal",
    
    eat() {
        return `${this.name} is eating`;
    },
    
    sleep() {
        return `${this.name} is sleeping`;
    }
};

// Create object with animal as prototype
const dog = Object.create(animal);
dog.name = "Rex";
dog.bark = function() {
    return "Woof!";
};

console.log(dog.eat());  // "Rex is eating" (inherited)
console.log(dog.bark());  // "Woof!" (own method)

// Create object with null prototype (no inherited properties)
const bareObject = Object.create(null);
bareObject.name = "Bare";

console.log(bareObject.toString);  // undefined (no Object.prototype)
console.log(bareObject.name);  // "Bare"

// Object.create() with property descriptors
const person = Object.create(Object.prototype, {
    name: {
        value: "John",
        writable: true,
        enumerable: true,
        configurable: true
    },
    age: {
        value: 30,
        writable: true,
        enumerable: true,
        configurable: true
    },
    greet: {
        value: function() {
            return `Hello, I'm ${this.name}`;
        },
        writable: true,
        enumerable: false,  // Won't show in for...in
        configurable: true
    }
});

console.log(person.greet());  // "Hello, I'm John"
console.log(Object.keys(person));  // ["name", "age"] (greet is non-enumerable)

// Inheritance pattern with Object.create()
function Shape(x, y) {
    this.x = x;
    this.y = y;
}

Shape.prototype.move = function(dx, dy) {
    this.x += dx;
    this.y += dy;
};

function Circle(x, y, radius) {
    Shape.call(this, x, y);
    this.radius = radius;
}

Circle.prototype = Object.create(Shape.prototype);
Circle.prototype.constructor = Circle;

Circle.prototype.area = function() {
    return Math.PI * this.radius ** 2;
};

const circle = new Circle(10, 20, 5);
circle.move(5, 5);
console.log(circle.x, circle.y);  // 15, 25
console.log(circle.area());  // 78.53981633974483
```

---

### 5. ES6 Classes vs Prototypes

```javascript
// ES6 Class syntax (syntactic sugar over prototypes)

class Animal {
    constructor(name) {
        this.name = name;
    }
    
    eat() {
        return `${this.name} is eating`;
    }
    
    sleep() {
        return `${this.name} is sleeping`;
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name);  // Call parent constructor
        this.breed = breed;
    }
    
    bark() {
        return `${this.name} barks: Woof!`;
    }
    
    eat() {
        // Override parent method
        return `${this.name} is eating dog food`;
    }
}

const rex = new Dog("Rex", "Labrador");
console.log(rex.bark());  // "Rex barks: Woof!"
console.log(rex.eat());   // "Rex is eating dog food"

// Under the hood, it's still prototypes
console.log(typeof Dog);  // "function"
console.log(Dog.prototype.bark);  // [Function: bark]
console.log(rex instanceof Dog);  // true
console.log(rex instanceof Animal);  // true

// Equivalent prototype-based code
function AnimalProto(name) {
    this.name = name;
}

AnimalProto.prototype.eat = function() {
    return `${this.name} is eating`;
};

function DogProto(name, breed) {
    AnimalProto.call(this, name);
    this.breed = breed;
}

DogProto.prototype = Object.create(AnimalProto.prototype);
DogProto.prototype.constructor = DogProto;

DogProto.prototype.bark = function() {
    return `${this.name} barks: Woof!`;
};

// Classes are more readable but work the same way
```

---

### 6. Inheritance Patterns

```javascript
// Pattern 1: Prototypal Inheritance
const parent = {
    greet() {
        return "Hello from parent";
    }
};

const child = Object.create(parent);
child.name = "Child";

console.log(child.greet());  // "Hello from parent"

// Pattern 2: Constructor Pattern
function Parent(name) {
    this.name = name;
    this.friends = [];
}

Parent.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

function Child(name, age) {
    Parent.call(this, name);  // Inherit instance properties
    this.age = age;
}

Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

Child.prototype.getAge = function() {
    return this.age;
};

// Pattern 3: Parasitic Inheritance
function createStudent(name) {
    const student = Object.create(parent);
    student.name = name;
    student.study = function() {
        return `${this.name} is studying`;
    };
    return student;
}

const student = createStudent("Alice");
console.log(student.study());  // "Alice is studying"

// Pattern 4: Mixin Pattern
const canEat = {
    eat() {
        return `${this.name} is eating`;
    }
};

const canWalk = {
    walk() {
        return `${this.name} is walking`;
    }
};

const canSwim = {
    swim() {
        return `${this.name} is swimming`;
    }
};

// Create object with multiple mixins
function createPerson(name) {
    const person = { name };
    Object.assign(person, canEat, canWalk);
    return person;
}

function createFish(name) {
    const fish = { name };
    Object.assign(fish, canEat, canSwim);
    return fish;
}

const john = createPerson("John");
console.log(john.walk());  // "John is walking"

const nemo = createFish("Nemo");
console.log(nemo.swim());  // "Nemo is swimming"

// Pattern 5: Factory Pattern
function createAnimal(type, name) {
    const animal = {
        type,
        name,
        
        describe() {
            return `${this.name} is a ${this.type}`;
        }
    };
    
    if (type === "dog") {
        animal.bark = function() {
            return "Woof!";
        };
    } else if (type === "cat") {
        animal.meow = function() {
            return "Meow!";
        };
    }
    
    return animal;
}

const dog = createAnimal("dog", "Rex");
console.log(dog.bark());  // "Woof!"
```

---

### 7. Property Lookup and Checking

```javascript
const animal = {
    eats: true
};

const rabbit = Object.create(animal);
rabbit.jumps = true;

// Property lookup
console.log(rabbit.jumps);  // true (own property)
console.log(rabbit.eats);   // true (inherited property)

// hasOwnProperty - checks own properties only
console.log(rabbit.hasOwnProperty('jumps'));  // true
console.log(rabbit.hasOwnProperty('eats'));   // false

// in operator - checks own and inherited properties
console.log('jumps' in rabbit);  // true
console.log('eats' in rabbit);   // true

// Object.keys() - only own enumerable properties
console.log(Object.keys(rabbit));  // ["jumps"]

// Object.getOwnPropertyNames() - all own properties
console.log(Object.getOwnPropertyNames(rabbit));  // ["jumps"]

// for...in - iterates own and inherited enumerable properties
for (let key in rabbit) {
    console.log(key);  // "jumps", "eats"
}

// for...in with hasOwnProperty filter
for (let key in rabbit) {
    if (rabbit.hasOwnProperty(key)) {
        console.log(key);  // Only "jumps"
    }
}

// Object.getPrototypeOf()
console.log(Object.getPrototypeOf(rabbit) === animal);  // true

// isPrototypeOf()
console.log(animal.isPrototypeOf(rabbit));  // true

// instanceof (checks constructor's prototype in chain)
function Animal() {}
const cat = new Animal();

console.log(cat instanceof Animal);  // true
console.log(cat instanceof Object);  // true

// Setting prototype
const bird = {};
Object.setPrototypeOf(bird, animal);
console.log(bird.eats);  // true

// Note: setPrototypeOf is slow, prefer Object.create()

// propertyIsEnumerable
rabbit.color = "white";
console.log(rabbit.propertyIsEnumerable('color'));  // true
console.log(rabbit.propertyIsEnumerable('jumps'));  // true
console.log(rabbit.propertyIsEnumerable('eats'));   // false (inherited)
```

---

### 8. Common Patterns and Best Practices

```javascript
// Pattern 1: Extending built-in objects (generally avoid)
// DON'T DO THIS in production
Array.prototype.first = function() {
    return this[0];
};

const arr = [1, 2, 3];
console.log(arr.first());  // 1

// Better: Create utility function
function first(array) {
    return array[0];
}

// Pattern 2: Method chaining
function Calculator() {
    this.value = 0;
}

Calculator.prototype.add = function(num) {
    this.value += num;
    return this;  // Return 'this' for chaining
};

Calculator.prototype.subtract = function(num) {
    this.value -= num;
    return this;
};

Calculator.prototype.multiply = function(num) {
    this.value *= num;
    return this;
};

Calculator.prototype.getResult = function() {
    return this.value;
};

const calc = new Calculator();
const result = calc.add(10).multiply(2).subtract(5).getResult();
console.log(result);  // 15

// Pattern 3: Protecting constructor
function SecurePerson(name) {
    if (!(this instanceof SecurePerson)) {
        return new SecurePerson(name);
    }
    this.name = name;
}

// Works with or without 'new'
const p1 = new SecurePerson("John");
const p2 = SecurePerson("Jane");  // Automatically uses 'new'

// Pattern 4: Private state with WeakMap
const privateData = new WeakMap();

function Person(name) {
    privateData.set(this, { name });
}

Person.prototype.getName = function() {
    return privateData.get(this).name;
};

Person.prototype.setName = function(name) {
    privateData.get(this).name = name;
};

// Pattern 5: Delegation pattern
const controller = {
    errors: [],
    
    showDialog(title, msg) {
        console.log(`Dialog: ${title} - ${msg}`);
    },
    
    success(msg) {
        this.showDialog("Success", msg);
    },
    
    failure(err) {
        this.errors.push(err);
        this.showDialog("Error", err);
    }
};

const loginController = Object.create(controller);

loginController.getUser = function() {
    return "user";
};

loginController.login = function() {
    const user = this.getUser();
    if (user) {
        this.success("Login successful");
    } else {
        this.failure("Login failed");
    }
};

// Pattern 6: Composition over inheritance
function canEat(state) {
    return {
        eat(food) {
            console.log(`${state.name} is eating ${food}`);
        }
    };
}

function canWalk(state) {
    return {
        walk() {
            console.log(`${state.name} is walking`);
        }
    };
}

function canSwim(state) {
    return {
        swim() {
            console.log(`${state.name} is swimming`);
        }
    };
}

function createDog(name) {
    const state = { name };
    return Object.assign(
        {},
        canEat(state),
        canWalk(state)
    );
}

function createFish(name) {
    const state = { name };
    return Object.assign(
        {},
        canEat(state),
        canSwim(state)
    );
}

const myDog = createDog("Rex");
myDog.walk();  // "Rex is walking"

const myFish = createFish("Nemo");
myFish.swim();  // "Nemo is swimming"
```

---

## 💡 Real-World Examples

### Example 1: Observable Pattern

```javascript
function Observable() {
    this.observers = [];
}

Observable.prototype.subscribe = function(fn) {
    this.observers.push(fn);
    
    return {
        unsubscribe: () => {
            this.observers = this.observers.filter(observer => observer !== fn);
        }
    };
};

Observable.prototype.notify = function(data) {
    this.observers.forEach(observer => observer(data));
};

// Usage
const observable = new Observable();

const subscription = observable.subscribe(data => {
    console.log("Observer 1:", data);
});

observable.subscribe(data => {
    console.log("Observer 2:", data);
});

observable.notify("Hello!");  // Both observers called
subscription.unsubscribe();
observable.notify("World!");  // Only observer 2 called
```

### Example 2: Plugin System

```javascript
function App() {
    this.plugins = [];
    this.initialized = false;
}

App.prototype.use = function(plugin) {
    this.plugins.push(plugin);
    return this;
};

App.prototype.init = function() {
    if (this.initialized) return;
    
    this.plugins.forEach(plugin => {
        if (typeof plugin.init === 'function') {
            plugin.init(this);
        }
    });
    
    this.initialized = true;
};

// Plugins
const loggerPlugin = {
    init(app) {
        app.log = function(message) {
            console.log(`[LOG]: ${message}`);
        };
    }
};

const routerPlugin = {
    init(app) {
        app.routes = {};
        app.route = function(path, handler) {
            this.routes[path] = handler;
        };
        app.navigate = function(path) {
            if (this.routes[path]) {
                this.routes[path]();
            }
        };
    }
};

// Usage
const app = new App();
app.use(loggerPlugin).use(routerPlugin).init();

app.log("App initialized");
app.route("/home", () => console.log("Home page"));
app.navigate("/home");
```

### Example 3: Event Emitter

```javascript
function EventEmitter() {
    this.events = {};
}

EventEmitter.prototype.on = function(event, listener) {
    if (!this.events[event]) {
        this.events[event] = [];
    }
    this.events[event].push(listener);
    return this;
};

EventEmitter.prototype.emit = function(event, ...args) {
    if (!this.events[event]) return;
    
    this.events[event].forEach(listener => {
        listener.apply(this, args);
    });
    return this;
};

EventEmitter.prototype.off = function(event, listenerToRemove) {
    if (!this.events[event]) return;
    
    this.events[event] = this.events[event].filter(
        listener => listener !== listenerToRemove
    );
    return this;
};

EventEmitter.prototype.once = function(event, listener) {
    const wrapper = (...args) => {
        listener.apply(this, args);
        this.off(event, wrapper);
    };
    
    return this.on(event, wrapper);
};

// Usage
const emitter = new EventEmitter();

emitter.on("login", (user) => {
    console.log(`${user.name} logged in`);
});

emitter.once("welcome", (user) => {
    console.log(`Welcome ${user.name}!`);
});

emitter.emit("login", { name: "John" });
emitter.emit("welcome", { name: "John" });
emitter.emit("welcome", { name: "John" });  // Won't trigger (once)
```

---

## 🎤 Interview Questions

### Q1: What is the prototype chain?

**Answer:**

The prototype chain is the mechanism by which JavaScript objects inherit properties and methods from other objects.

```javascript
const animal = {
    eats: true
};

const rabbit = Object.create(animal);
rabbit.jumps = true;

// Chain: rabbit -> animal -> Object.prototype -> null

console.log(rabbit.jumps);  // true (own property)
console.log(rabbit.eats);   // true (inherited from animal)
console.log(rabbit.toString());  // "[object Object]" (inherited from Object.prototype)

// When accessing rabbit.eats:
// 1. Check rabbit itself - not found
// 2. Check rabbit.__proto__ (animal) - found!
// 3. If not found, check animal.__proto__ (Object.prototype)
// 4. If not found, check Object.prototype.__proto__ (null) - end of chain
```

---

### Q2: What's the difference between `__proto__` and `prototype`?

**Answer:**

```javascript
// prototype: Property of constructor functions
function Person(name) {
    this.name = name;
}

Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

// __proto__: Reference to object's prototype (actual prototype chain link)
const john = new Person("John");

console.log(Person.prototype);  // { greet: [Function], constructor: Person }
console.log(john.__proto__);    // Same as Person.prototype
console.log(john.__proto__ === Person.prototype);  // true

// Key differences:
// - prototype: On constructor functions (Function.prototype)
// - __proto__: On object instances (actual link in chain)

// Better way than __proto__:
console.log(Object.getPrototypeOf(john) === Person.prototype);  // true
```

---

### Q3: How does the `new` keyword work?

**Answer:**

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

const john = new Person("John");

// What 'new' does:
// 1. Creates a new empty object
// 2. Sets the object's [[Prototype]] to Person.prototype
// 3. Calls Person() with 'this' bound to the new object
// 4. Returns the object (unless constructor explicitly returns an object)

// Manual implementation:
function myNew(Constructor, ...args) {
    // Step 1: Create new object with correct prototype
    const obj = Object.create(Constructor.prototype);
    
    // Step 2: Execute constructor
    const result = Constructor.apply(obj, args);
    
    // Step 3: Return object (or constructor's return value if it's an object)
    return (typeof result === 'object' && result !== null) ? result : obj;
}

const jane = myNew(Person, "Jane");
console.log(jane.greet());  // "Hello, I'm Jane"
```

---

### Q4: What's the difference between classical and prototypal inheritance?

**Answer:**

```javascript
// Classical Inheritance (Java, C++)
// - Classes are blueprints
// - Objects are instances of classes
// - Inheritance through class hierarchy

// Prototypal Inheritance (JavaScript)
// - Objects inherit directly from objects
// - No classes (even ES6 classes are syntactic sugar)
// - More flexible

// Example: Multiple inheritance (easy with prototypes)
const canEat = {
    eat() { return "eating"; }
};

const canWalk = {
    walk() { return "walking"; }
};

// Composition: combine multiple objects
const person = Object.assign(
    {},
    canEat,
    canWalk,
    { name: "John" }
);

console.log(person.eat());  // "eating"
console.log(person.walk());  // "walking"

// Classical inheritance would require complex multiple inheritance
// Prototypal inheritance allows flexible composition
```

---

### Q5: How do you check if a property exists on an object vs its prototype?

**Answer:**

```javascript
const animal = {
    eats: true
};

const rabbit = Object.create(animal);
rabbit.jumps = true;

// 1. hasOwnProperty() - own properties only
console.log(rabbit.hasOwnProperty('jumps'));  // true
console.log(rabbit.hasOwnProperty('eats'));   // false

// 2. in operator - own and inherited
console.log('jumps' in rabbit);  // true
console.log('eats' in rabbit);   // true

// 3. Object.getOwnPropertyNames() - own properties
console.log(Object.getOwnPropertyNames(rabbit));  // ["jumps"]

// 4. Object.keys() - own enumerable properties
console.log(Object.keys(rabbit));  // ["jumps"]

// 5. for...in - own and inherited enumerable
for (let key in rabbit) {
    console.log(key);  // "jumps", "eats"
}

// Best practice: Check own properties in for...in
for (let key in rabbit) {
    if (rabbit.hasOwnProperty(key)) {
        console.log(key);  // Only "jumps"
    }
}
```

---

## 📚 Summary

**Key Takeaways:**
- Every JavaScript object has a prototype
- Prototype chain enables inheritance
- Constructor functions use `.prototype` property
- Objects link to prototypes via `[[Prototype]]` (__proto__)
- ES6 classes are syntactic sugar over prototypes
- Object.create() for clean prototypal inheritance
- Composition often better than deep inheritance

**Best Practices:**
- ✅ Use Object.getPrototypeOf() instead of __proto__
- ✅ Use Object.create() for setting up inheritance
- ✅ Add methods to prototype, not in constructor
- ✅ Use ES6 classes for cleaner syntax
- ✅ Check hasOwnProperty() in for...in loops
- ✅ Prefer composition over deep inheritance
- ✅ Don't modify built-in prototypes
- ✅ Understand the prototype chain for debugging

---

**Next Topic:** [13. This Keyword](./13-this-keyword.md)
