# 04. Functions in JavaScript

> Functions are reusable blocks of code that perform specific tasks.

## 📋 Overview

Functions are one of the fundamental building blocks in JavaScript. They allow you to encapsulate code, make it reusable, and organize your programs into logical units. Understanding functions is essential for writing clean, maintainable, and efficient code.

**What You'll Learn:**
- ✅ Function declarations and expressions
- ✅ Arrow functions and their syntax
- ✅ Parameters and arguments (default, rest, spread)
- ✅ Return values and early returns
- ✅ Function scope and closures
- ✅ Higher-order functions
- ✅ IIFE (Immediately Invoked Function Expressions)
- ✅ Function best practices

---

## 🎯 Key Concepts

### 1. Function Declarations

Traditional way to define functions.

```javascript
// Basic function declaration
function greet() {
    console.log("Hello, World!");
}

greet();  // Hello, World!

// Function with parameters
function greetUser(name) {
    console.log(`Hello, ${name}!`);
}

greetUser("Alice");  // Hello, Alice!

// Function with return value
function add(a, b) {
    return a + b;
}

let result = add(5, 3);
console.log(result);  // 8

// Multiple parameters
function calculateArea(width, height) {
    return width * height;
}

console.log(calculateArea(10, 5));  // 50

// Function without return (returns undefined)
function logMessage(msg) {
    console.log(msg);
    // No return statement
}

let value = logMessage("Test");
console.log(value);  // undefined

// Early return
function divide(a, b) {
    if (b === 0) {
        return "Cannot divide by zero";
    }
    return a / b;
}

console.log(divide(10, 2));  // 5
console.log(divide(10, 0));  // "Cannot divide by zero"

// Function hoisting
sayHello();  // Works! Functions are hoisted

function sayHello() {
    console.log("Hello!");
}
```

---

### 2. Function Expressions

Functions assigned to variables.

```javascript
// Basic function expression
const greet = function() {
    console.log("Hello!");
};

greet();  // Hello!

// Named function expression
const factorial = function fact(n) {
    if (n <= 1) return 1;
    return n * fact(n - 1);  // Can use 'fact' inside
};

console.log(factorial(5));  // 120

// Function expressions are NOT hoisted
// sayHi();  // TypeError: sayHi is not a function

const sayHi = function() {
    console.log("Hi!");
};

sayHi();  // Hi!

// Function expression vs declaration
// Declaration
function add1(a, b) {
    return a + b;
}

// Expression
const add2 = function(a, b) {
    return a + b;
};

// Both work the same way after definition
console.log(add1(2, 3));  // 5
console.log(add2(2, 3));  // 5

// Assigning functions to variables
const multiply = function(a, b) {
    return a * b;
};

const calculate = multiply;  // Assign function to another variable
console.log(calculate(4, 5));  // 20
```

---

### 3. Arrow Functions

Concise syntax for function expressions (ES6+).

```javascript
// Basic arrow function
const greet = () => {
    console.log("Hello!");
};

greet();  // Hello!

// Single parameter (parentheses optional)
const square = x => {
    return x * x;
};

console.log(square(5));  // 25

// Implicit return (single expression)
const square2 = x => x * x;
console.log(square2(5));  // 25

// Multiple parameters
const add = (a, b) => a + b;
console.log(add(3, 4));  // 7

// No parameters
const sayHello = () => "Hello!";
console.log(sayHello());  // "Hello!"

// Returning objects (wrap in parentheses)
const createPerson = (name, age) => ({ name, age });
console.log(createPerson("John", 30));  // { name: "John", age: 30 }

// Multiline arrow function
const calculateDiscount = (price, discount) => {
    const discountAmount = price * (discount / 100);
    return price - discountAmount;
};

console.log(calculateDiscount(100, 20));  // 80

// Arrow functions with array methods
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n * 2);
console.log(doubled);  // [2, 4, 6, 8, 10]

const evens = numbers.filter(n => n % 2 === 0);
console.log(evens);  // [2, 4]

const sum = numbers.reduce((acc, n) => acc + n, 0);
console.log(sum);  // 15

// Arrow functions do NOT have their own 'this'
const obj = {
    name: "Object",
    regularFunc: function() {
        console.log(this.name);  // "Object"
    },
    arrowFunc: () => {
        console.log(this.name);  // undefined (or window.name in browser)
    }
};

obj.regularFunc();  // "Object"
obj.arrowFunc();    // undefined
```

---

### 4. Parameters and Arguments

```javascript
// Default parameters
function greet(name = "Guest") {
    console.log(`Hello, ${name}!`);
}

greet();           // Hello, Guest!
greet("Alice");    // Hello, Alice!

// Default parameters with expressions
function createUser(name, role = "user", active = true) {
    return { name, role, active };
}

console.log(createUser("John"));
// { name: "John", role: "user", active: true }

console.log(createUser("Admin", "admin", false));
// { name: "Admin", role: "admin", active: false }

// Default parameter using previous parameters
function greet2(firstName, lastName, fullName = `${firstName} ${lastName}`) {
    console.log(fullName);
}

greet2("John", "Doe");  // John Doe

// Rest parameters (collect remaining arguments)
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3));        // 6
console.log(sum(1, 2, 3, 4, 5));  // 15

// Rest parameter with other parameters
function introduce(greeting, ...names) {
    console.log(`${greeting} ${names.join(", ")}!`);
}

introduce("Hello", "Alice", "Bob", "Charlie");
// Hello Alice, Bob, Charlie!

// Arguments object (legacy, avoid in modern code)
function oldSum() {
    let total = 0;
    for (let i = 0; i < arguments.length; i++) {
        total += arguments[i];
    }
    return total;
}

console.log(oldSum(1, 2, 3, 4));  // 10

// Note: Arrow functions don't have arguments object
const arrowSum = () => {
    // console.log(arguments);  // ReferenceError!
};

// Destructuring parameters
function createPerson({ name, age, city = "Unknown" }) {
    console.log(`${name}, ${age}, from ${city}`);
}

createPerson({ name: "Alice", age: 30, city: "NYC" });
// Alice, 30, from NYC

createPerson({ name: "Bob", age: 25 });
// Bob, 25, from Unknown

// Array destructuring in parameters
function printCoordinates([x, y, z = 0]) {
    console.log(`X: ${x}, Y: ${y}, Z: ${z}`);
}

printCoordinates([10, 20]);      // X: 10, Y: 20, Z: 0
printCoordinates([5, 15, 25]);   // X: 5, Y: 15, Z: 25

// Spread operator with function calls
function add(a, b, c) {
    return a + b + c;
}

const numbers = [1, 2, 3];
console.log(add(...numbers));  // 6
```

---

### 5. Return Values

```javascript
// Simple return
function multiply(a, b) {
    return a * b;
}

// Multiple return statements
function getGrade(score) {
    if (score >= 90) return "A";
    if (score >= 80) return "B";
    if (score >= 70) return "C";
    if (score >= 60) return "D";
    return "F";
}

// Return object
function createUser(name, age) {
    return {
        name: name,
        age: age,
        isAdult: age >= 18
    };
}

// Return array
function getMinMax(numbers) {
    return [Math.min(...numbers), Math.max(...numbers)];
}

const [min, max] = getMinMax([5, 2, 8, 1, 9]);
console.log(min, max);  // 1, 9

// Return function (closure)
function multiplier(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Early return for validation
function processPayment(amount, balance) {
    if (amount <= 0) {
        return { success: false, message: "Invalid amount" };
    }
    
    if (balance < amount) {
        return { success: false, message: "Insufficient funds" };
    }
    
    // Process payment
    return { success: true, message: "Payment successful" };
}

// No return statement (returns undefined)
function logMessage(msg) {
    console.log(msg);
}

const result = logMessage("Test");
console.log(result);  // undefined

// Return after code (unreachable code)
function example() {
    return 42;
    console.log("Never executes");  // Unreachable
}
```

---

### 6. Function Scope and Closures

```javascript
// Function scope
let globalVar = "global";

function outer() {
    let outerVar = "outer";
    
    function inner() {
        let innerVar = "inner";
        console.log(globalVar);  // Accessible
        console.log(outerVar);   // Accessible
        console.log(innerVar);   // Accessible
    }
    
    inner();
    // console.log(innerVar);  // Error: not accessible
}

outer();

// Closures: inner function accessing outer function's variables
function createCounter() {
    let count = 0;
    
    return {
        increment: function() {
            count++;
            return count;
        },
        decrement: function() {
            count--;
            return count;
        },
        getCount: function() {
            return count;
        }
    };
}

const counter = createCounter();
console.log(counter.increment());  // 1
console.log(counter.increment());  // 2
console.log(counter.getCount());   // 2
console.log(counter.decrement());  // 1

// Private variables with closures
function bankAccount(initialBalance) {
    let balance = initialBalance;  // Private variable
    
    return {
        deposit(amount) {
            if (amount > 0) {
                balance += amount;
                return balance;
            }
            return "Invalid amount";
        },
        withdraw(amount) {
            if (amount > 0 && amount <= balance) {
                balance -= amount;
                return balance;
            }
            return "Invalid amount or insufficient funds";
        },
        getBalance() {
            return balance;
        }
    };
}

const account = bankAccount(1000);
console.log(account.deposit(500));    // 1500
console.log(account.withdraw(200));   // 1300
console.log(account.getBalance());    // 1300
// console.log(account.balance);      // undefined (private!)

// Closure in loop (classic problem)
// ❌ Problem: var is function-scoped
for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 3, 3, 3 (all reference same 'i')
    }, 100);
}

// ✅ Solution 1: Use let (block-scoped)
for (let j = 0; j < 3; j++) {
    setTimeout(function() {
        console.log(j);  // 0, 1, 2
    }, 100);
}

// ✅ Solution 2: IIFE closure
for (var k = 0; k < 3; k++) {
    (function(index) {
        setTimeout(function() {
            console.log(index);  // 0, 1, 2
        }, 100);
    })(k);
}
```

---

### 7. Higher-Order Functions

Functions that take functions as arguments or return functions.

```javascript
// Function as argument
function greet(name, formatter) {
    return formatter(name);
}

function uppercase(str) {
    return str.toUpperCase();
}

function lowercase(str) {
    return str.toLowerCase();
}

console.log(greet("John", uppercase));  // JOHN
console.log(greet("John", lowercase));  // john

// Array methods (higher-order functions)
const numbers = [1, 2, 3, 4, 5];

// map: transform each element
const doubled = numbers.map(n => n * 2);
console.log(doubled);  // [2, 4, 6, 8, 10]

// filter: select elements
const evens = numbers.filter(n => n % 2 === 0);
console.log(evens);  // [2, 4]

// reduce: accumulate value
const sum = numbers.reduce((acc, n) => acc + n, 0);
console.log(sum);  // 15

// find: find first match
const found = numbers.find(n => n > 3);
console.log(found);  // 4

// some: check if any match
const hasEven = numbers.some(n => n % 2 === 0);
console.log(hasEven);  // true

// every: check if all match
const allPositive = numbers.every(n => n > 0);
console.log(allPositive);  // true

// Function returning function
function multiplyBy(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = multiplyBy(2);
const triple = multiplyBy(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Composition
function compose(f, g) {
    return function(x) {
        return f(g(x));
    };
}

const addOne = x => x + 1;
const multiplyByTwo = x => x * 2;

const addOneThenDouble = compose(multiplyByTwo, addOne);
console.log(addOneThenDouble(5));  // 12 ((5 + 1) * 2)

// Currying
function curry(f) {
    return function(a) {
        return function(b) {
            return f(a, b);
        };
    };
}

function add(a, b) {
    return a + b;
}

const curriedAdd = curry(add);
console.log(curriedAdd(5)(3));  // 8

// Practical higher-order function
function retry(fn, times) {
    return function(...args) {
        for (let i = 0; i < times; i++) {
            try {
                return fn(...args);
            } catch (error) {
                if (i === times - 1) throw error;
            }
        }
    };
}

const unreliableFunction = () => {
    if (Math.random() < 0.7) throw new Error("Failed");
    return "Success";
};

const reliableFunction = retry(unreliableFunction, 3);
```

---

### 8. IIFE (Immediately Invoked Function Expression)

Functions that execute immediately after definition.

```javascript
// Basic IIFE
(function() {
    console.log("This runs immediately!");
})();

// IIFE with parameters
(function(name) {
    console.log(`Hello, ${name}!`);
})("Alice");

// IIFE with return value
const result = (function() {
    return 42;
})();

console.log(result);  // 42

// IIFE for private scope
const counter = (function() {
    let count = 0;  // Private variable
    
    return {
        increment() { return ++count; },
        decrement() { return --count; },
        value() { return count; }
    };
})();

console.log(counter.increment());  // 1
console.log(counter.increment());  // 2
console.log(counter.value());      // 2

// IIFE to avoid global pollution
(function() {
    // All variables here are private
    let privateVar = "I'm private";
    
    function privateFunction() {
        console.log(privateVar);
    }
    
    privateFunction();
})();

// Arrow function IIFE
(() => {
    console.log("Arrow IIFE");
})();

// IIFE in modules (legacy pattern)
const myModule = (function() {
    // Private
    let privateData = [];
    
    function privateMethod() {
        return privateData.length;
    }
    
    // Public API
    return {
        add(item) {
            privateData.push(item);
        },
        get() {
            return [...privateData];  // Return copy
        },
        size() {
            return privateMethod();
        }
    };
})();

myModule.add("item1");
myModule.add("item2");
console.log(myModule.get());   // ["item1", "item2"]
console.log(myModule.size());  // 2
```

---

## 💡 Examples

### Example 1: Calculator Functions

```javascript
const calculator = {
    add: (a, b) => a + b,
    subtract: (a, b) => a - b,
    multiply: (a, b) => a * b,
    divide: (a, b) => b !== 0 ? a / b : "Cannot divide by zero",
    power: (base, exp) => Math.pow(base, exp),
    
    // Method that uses other methods
    calculate(operation, a, b) {
        return this[operation](a, b);
    }
};

console.log(calculator.add(5, 3));           // 8
console.log(calculator.multiply(4, 6));      // 24
console.log(calculator.calculate("add", 10, 5));  // 15
```

### Example 2: Data Processing Pipeline

```javascript
const users = [
    { name: "Alice", age: 25, active: true },
    { name: "Bob", age: 30, active: false },
    { name: "Charlie", age: 35, active: true },
    { name: "David", age: 28, active: true }
];

// Get names of active users older than 26
const result = users
    .filter(user => user.active)
    .filter(user => user.age > 26)
    .map(user => user.name)
    .join(", ");

console.log(result);  // "Charlie, David"

// Calculate average age of active users
const averageAge = users
    .filter(user => user.active)
    .reduce((sum, user, _, arr) => sum + user.age / arr.length, 0);

console.log(averageAge);  // 29.33...
```

### Example 3: Memoization

```javascript
function memoize(fn) {
    const cache = {};
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (key in cache) {
            console.log("Cached result");
            return cache[key];
        }
        
        console.log("Computing result");
        const result = fn(...args);
        cache[key] = result;
        return result;
    };
}

// Expensive fibonacci function
function fibonacci(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

const memoizedFib = memoize(fibonacci);

console.log(memoizedFib(10));  // Computing result, 55
console.log(memoizedFib(10));  // Cached result, 55
```

### Example 4: Debounce Function

```javascript
function debounce(func, delay) {
    let timeoutId;
    
    return function(...args) {
        clearTimeout(timeoutId);
        
        timeoutId = setTimeout(() => {
            func.apply(this, args);
        }, delay);
    };
}

// Usage: search input
const search = debounce((query) => {
    console.log(`Searching for: ${query}`);
}, 500);

// These calls within 500ms will be debounced
search("j");
search("ja");
search("jav");
search("java");  // Only this will execute after 500ms
```

### Example 5: Function Factory

```javascript
function createGreeter(greeting) {
    return function(name) {
        return `${greeting}, ${name}!`;
    };
}

const sayHello = createGreeter("Hello");
const sayHi = createGreeter("Hi");
const sayHola = createGreeter("Hola");

console.log(sayHello("Alice"));   // Hello, Alice!
console.log(sayHi("Bob"));        // Hi, Bob!
console.log(sayHola("Charlie"));  // Hola, Charlie!

// More complex factory
function createValidator(rules) {
    return function(value) {
        for (let rule of rules) {
            if (!rule.test(value)) {
                return { valid: false, message: rule.message };
            }
        }
        return { valid: true };
    };
}

const emailValidator = createValidator([
    { test: v => v.length > 0, message: "Email required" },
    { test: v => v.includes("@"), message: "Invalid email format" },
    { test: v => v.length < 100, message: "Email too long" }
]);

console.log(emailValidator("test@email.com"));  // { valid: true }
console.log(emailValidator("invalid"));         // { valid: false, message: "Invalid email format" }
```

---

## 🎤 Interview Questions

### Q1: What's the difference between function declarations and expressions?

**Answer:**
- **Function declarations** are hoisted; can be called before definition
- **Function expressions** are not hoisted; must be defined before use

```javascript
// Declaration: Hoisted
sayHello();  // Works!

function sayHello() {
    console.log("Hello");
}

// Expression: NOT hoisted
// sayHi();  // TypeError: sayHi is not a function

const sayHi = function() {
    console.log("Hi");
};

sayHi();  // Works now
```

**Key Difference:** Function declarations are processed before code execution, function expressions are created when execution reaches them.

---

### Q2: How do arrow functions differ from regular functions?

**Answer:**
1. **Syntax**: More concise
2. **`this` binding**: No own `this` (lexically bound)
3. **`arguments`**: Don't have `arguments` object
4. **`new`**: Cannot be used as constructors

```javascript
// Regular function
const obj1 = {
    name: "Object 1",
    getName: function() {
        return this.name;  // 'this' refers to obj1
    }
};

// Arrow function
const obj2 = {
    name: "Object 2",
    getName: () => {
        return this.name;  // 'this' is from outer scope
    }
};

console.log(obj1.getName());  // "Object 1"
console.log(obj2.getName());  // undefined

// Arrow functions in callbacks
class Timer {
    constructor() {
        this.seconds = 0;
    }
    
    start() {
        // Arrow function: 'this' refers to Timer instance
        setInterval(() => {
            this.seconds++;
        }, 1000);
    }
}
```

---

### Q3: What is a closure?

**Answer:**
A closure is a function that has access to variables in its outer (lexical) scope, even after the outer function has returned.

```javascript
function createCounter() {
    let count = 0;  // Private variable
    
    return {
        increment: () => ++count,
        decrement: () => --count,
        value: () => count
    };
}

const counter = createCounter();
console.log(counter.increment());  // 1
console.log(counter.increment());  // 2
console.log(counter.value());      // 2
// console.log(count);             // ReferenceError: count is not defined

// Practical use: Data privacy
function bankAccount(initial) {
    let balance = initial;
    
    return {
        deposit: amount => balance += amount,
        withdraw: amount => {
            if (amount <= balance) {
                balance -= amount;
                return true;
            }
            return false;
        },
        getBalance: () => balance
    };
}
```

---

### Q4: What are higher-order functions?

**Answer:**
Functions that either:
1. Take functions as arguments
2. Return functions
3. Or both

```javascript
// Takes function as argument
function repeat(n, action) {
    for (let i = 0; i < n; i++) {
        action(i);
    }
}

repeat(3, console.log);  // 0, 1, 2

// Returns function
function multiplier(factor) {
    return num => num * factor;
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Built-in higher-order functions
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);
```

---

### Q5: Explain the rest parameter vs spread operator.

**Answer:**
- **Rest parameter** (`...args` in function params): Collects arguments into array
- **Spread operator** (`...array` in function call): Expands array into arguments

```javascript
// Rest parameter: collect arguments
function sum(...numbers) {
    return numbers.reduce((total, n) => total + n, 0);
}

console.log(sum(1, 2, 3));        // 6
console.log(sum(1, 2, 3, 4, 5));  // 15

// Spread operator: expand array
function add(a, b, c) {
    return a + b + c;
}

const nums = [1, 2, 3];
console.log(add(...nums));  // 6

// Combining
function process(first, second, ...rest) {
    console.log(first);   // 1
    console.log(second);  // 2
    console.log(rest);    // [3, 4, 5]
}

process(1, 2, 3, 4, 5);
```

---

### Q6: What is an IIFE and why use it?

**Answer:**
IIFE (Immediately Invoked Function Expression) is a function that runs immediately after it's defined. Used for:
1. Creating private scope
2. Avoiding global pollution
3. Module pattern (legacy)

```javascript
// Basic IIFE
(function() {
    let private = "I'm private";
    console.log(private);
})();

// console.log(private);  // ReferenceError

// IIFE with return
const module = (function() {
    let privateData = [];
    
    return {
        add: item => privateData.push(item),
        get: () => [...privateData]
    };
})();

module.add("item1");
console.log(module.get());  // ["item1"]

// Modern alternative: block scope with let/const
{
    let blockScoped = "I'm also private";
    console.log(blockScoped);
}
// console.log(blockScoped);  // ReferenceError
```

---

### Q7: What is function currying?

**Answer:**
Currying transforms a function with multiple arguments into a sequence of functions, each taking a single argument.

```javascript
// Regular function
function add(a, b, c) {
    return a + b + c;
}

console.log(add(1, 2, 3));  // 6

// Curried version
function curriedAdd(a) {
    return function(b) {
        return function(c) {
            return a + b + c;
        };
    };
}

console.log(curriedAdd(1)(2)(3));  // 6

// Arrow function currying
const curriedAddArrow = a => b => c => a + b + c;
console.log(curriedAddArrow(1)(2)(3));  // 6

// Practical use: Partial application
const add5 = curriedAdd(5);
const add5And3 = add5(3);

console.log(add5And3(2));  // 10
console.log(add5And3(7));  // 15

// Generic curry function
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        }
        return function(...moreArgs) {
            return curried.apply(this, args.concat(moreArgs));
        };
    };
}

const sum = (a, b, c) => a + b + c;
const curriedSum = curry(sum);

console.log(curriedSum(1)(2)(3));    // 6
console.log(curriedSum(1, 2)(3));    // 6
console.log(curriedSum(1, 2, 3));    // 6
```

---

### Q8: What is function composition?

**Answer:**
Function composition is combining multiple functions to create a new function where the output of one function becomes the input of the next.

```javascript
// Basic composition
const add1 = x => x + 1;
const multiply2 = x => x * 2;
const square = x => x * x;

// Manual composition
const result1 = square(multiply2(add1(5)));  // ((5 + 1) * 2)² = 144
console.log(result1);

// Compose function (right-to-left)
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);

const calculate = compose(square, multiply2, add1);
console.log(calculate(5));  // 144

// Pipe function (left-to-right)
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);

const process = pipe(add1, multiply2, square);
console.log(process(5));  // 144

// Practical example
const trim = str => str.trim();
const lowercase = str => str.toLowerCase();
const removeSpaces = str => str.replace(/\s/g, '');

const cleanUsername = pipe(trim, lowercase, removeSpaces);
console.log(cleanUsername("  User Name  "));  // "username"
```

---

### Q9: Explain the `this` keyword in different function types.

**Answer:**
`this` behavior differs between regular functions and arrow functions:

```javascript
// Regular function: 'this' depends on how it's called
const obj = {
    name: "Object",
    regularFunc: function() {
        console.log(this.name);
    }
};

obj.regularFunc();  // "Object"

const fn = obj.regularFunc;
fn();  // undefined (or error in strict mode)

// Arrow function: 'this' is lexically bound
const obj2 = {
    name: "Object 2",
    arrowFunc: () => {
        console.log(this.name);
    }
};

obj2.arrowFunc();  // undefined ('this' is from outer scope)

// Practical difference in callbacks
class Timer {
    constructor() {
        this.seconds = 0;
    }
    
    // ❌ Problem with regular function
    startWrong() {
        setInterval(function() {
            this.seconds++;  // 'this' is not Timer instance!
        }, 1000);
    }
    
    // ✅ Solution: Arrow function
    start() {
        setInterval(() => {
            this.seconds++;  // 'this' is Timer instance
        }, 1000);
    }
    
    // ✅ Alternative: bind
    startWithBind() {
        setInterval(function() {
            this.seconds++;
        }.bind(this), 1000);
    }
}
```

---

### Q10: What is memoization?

**Answer:**
Memoization is an optimization technique that caches function results to avoid redundant calculations.

```javascript
// Without memoization (slow)
function fibonacci(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

// With memoization (fast)
function memoize(fn) {
    const cache = {};
    return function(...args) {
        const key = JSON.stringify(args);
        if (key in cache) {
            return cache[key];
        }
        const result = fn(...args);
        cache[key] = result;
        return result;
    };
}

const memoizedFib = memoize(function fib(n) {
    if (n <= 1) return n;
    return memoizedFib(n - 1) + memoizedFib(n - 2);
});

console.time("Without memo");
fibonacci(35);  // Slow
console.timeEnd("Without memo");

console.time("With memo");
memoizedFib(35);  // Fast
console.timeEnd("With memo");

// Practical use: expensive API calls
const memoizedFetch = memoize(async function(url) {
    const response = await fetch(url);
    return response.json();
});
```

---

## ✅ Best Practices

### 1. Use Arrow Functions for Callbacks
```javascript
// ❌ Verbose
numbers.map(function(n) {
    return n * 2;
});

// ✅ Concise
numbers.map(n => n * 2);
```

### 2. Use Default Parameters
```javascript
// ❌ Manual defaults
function greet(name) {
    name = name || "Guest";
    return `Hello, ${name}!`;
}

// ✅ Default parameters
function greet(name = "Guest") {
    return `Hello, ${name}!`;
}
```

### 3. Single Responsibility
```javascript
// ❌ Doing too much
function processUser(user) {
    validateUser(user);
    saveToDatabase(user);
    sendEmail(user);
    updateCache(user);
}

// ✅ Single responsibility
function processUser(user) {
    const validated = validateUser(user);
    return saveUser(validated);
}
```

### 4. Avoid Side Effects
```javascript
// ❌ Modifies external state
let total = 0;
function addToTotal(amount) {
    total += amount;  // Side effect!
}

// ✅ Pure function
function add(a, b) {
    return a + b;  // No side effects
}

const total = add(currentTotal, amount);
```

### 5. Use Meaningful Names
```javascript
// ❌ Unclear
function fn(x, y) {
    return x * y * 0.1;
}

// ✅ Clear
function calculateDiscount(price, quantity) {
    return price * quantity * 0.1;
}
```

---

## 📚 Resources

- [MDN: Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)
- [MDN: Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- [JavaScript.info: Functions](https://javascript.info/function-basics)
- [MDN: Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)

---

## 📝 Summary

- **Function declarations** are hoisted, **expressions** are not
- **Arrow functions** have concise syntax and lexical `this`
- **Default parameters** provide default values for missing arguments
- **Rest parameters** collect arguments, **spread** expands them
- **Closures** allow functions to access outer scope variables
- **Higher-order functions** take or return functions
- **IIFE** creates private scope immediately
- **Pure functions** have no side effects
- **Memoization** caches results for optimization
- **Composition** combines functions for complex operations

---

<div align="center">

**[⬅️ Previous: Control Flow](./03-control-flow.md)** | **[🏠 Home](./README.md)** | **[Next: Arrays ➡️](./05-arrays.md)**

</div>
