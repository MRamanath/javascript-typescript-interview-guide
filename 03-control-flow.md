# 03. Control Flow in JavaScript

> Control flow determines the order in which statements are executed in a program.

## 📋 Overview

Control flow statements allow you to control the execution path of your code based on conditions, repeat actions, and handle different scenarios. Mastering control flow is essential for writing dynamic and responsive applications.

**What You'll Learn:**
- ✅ Conditional statements (if, else if, else)
- ✅ Switch statements for multiple conditions
- ✅ Ternary operator for concise conditions
- ✅ For loops (for, for...in, for...of)
- ✅ While and do...while loops
- ✅ Break and continue statements
- ✅ Labels and nested loop control

---

## 🎯 Key Concepts

### 1. If, Else If, Else Statements

Execute code blocks based on conditions.

```javascript
// Basic if statement
let age = 20;

if (age >= 18) {
    console.log("You are an adult");
}

// if...else
let temperature = 15;

if (temperature > 25) {
    console.log("It's hot outside");
} else {
    console.log("It's cool outside");
}

// if...else if...else
let score = 85;

if (score >= 90) {
    console.log("Grade: A");
} else if (score >= 80) {
    console.log("Grade: B");
} else if (score >= 70) {
    console.log("Grade: C");
} else if (score >= 60) {
    console.log("Grade: D");
} else {
    console.log("Grade: F");
}

// Multiple conditions with logical operators
let hour = 14;
let isWeekend = true;

if (hour >= 9 && hour <= 17 && !isWeekend) {
    console.log("Office hours");
} else if (hour >= 9 && hour <= 17 && isWeekend) {
    console.log("Weekend, but within business hours");
} else {
    console.log("Outside office hours");
}

// Nested if statements
let isLoggedIn = true;
let hasPermission = true;

if (isLoggedIn) {
    if (hasPermission) {
        console.log("Access granted");
    } else {
        console.log("Access denied: No permission");
    }
} else {
    console.log("Please log in");
}

// Truthy and falsy values in conditions
let username = "John";

if (username) {  // Non-empty string is truthy
    console.log(`Welcome, ${username}!`);
}

let count = 0;

if (count) {  // 0 is falsy
    console.log("Count is positive");
} else {
    console.log("Count is zero");
}

// Guard clauses (early return)
function processPayment(amount, balance) {
    if (amount <= 0) {
        return "Invalid amount";
    }
    
    if (balance < amount) {
        return "Insufficient funds";
    }
    
    // Process payment
    return "Payment successful";
}

console.log(processPayment(50, 100));  // "Payment successful"
console.log(processPayment(50, 30));   // "Insufficient funds"
console.log(processPayment(-10, 100)); // "Invalid amount"
```

---

### 2. Switch Statements

Handle multiple conditions based on a single value.

```javascript
// Basic switch
let day = "Monday";

switch (day) {
    case "Monday":
        console.log("Start of the work week");
        break;
    case "Tuesday":
    case "Wednesday":
    case "Thursday":
        console.log("Midweek");
        break;
    case "Friday":
        console.log("TGIF!");
        break;
    case "Saturday":
    case "Sunday":
        console.log("Weekend!");
        break;
    default:
        console.log("Invalid day");
}

// Switch with numbers
let month = 2;

switch (month) {
    case 1:
        console.log("January");
        break;
    case 2:
        console.log("February");
        break;
    case 3:
        console.log("March");
        break;
    default:
        console.log("Other month");
}

// Switch with return (no break needed)
function getDaysInMonth(month) {
    switch (month) {
        case 1: case 3: case 5: case 7: case 8: case 10: case 12:
            return 31;
        case 4: case 6: case 9: case 11:
            return 30;
        case 2:
            return 28; // Ignoring leap years for simplicity
        default:
            return "Invalid month";
    }
}

console.log(getDaysInMonth(1));  // 31
console.log(getDaysInMonth(4));  // 30
console.log(getDaysInMonth(2));  // 28

// Fall-through behavior (intentional)
let grade = "B";

switch (grade) {
    case "A":
        console.log("Excellent!");
        // Intentional fall-through
    case "B":
        console.log("Good job!");
        // Intentional fall-through
    case "C":
        console.log("You passed!");
        break;
    case "D":
        console.log("Barely passed");
        break;
    default:
        console.log("Failed");
}
// Output for "B": "Good job!" and "You passed!"

// Switch with strict equality (===)
let value = "5";

switch (value) {
    case 5:  // This won't match (number vs string)
        console.log("Number 5");
        break;
    case "5":  // This will match
        console.log("String '5'");
        break;
}
// Output: "String '5'"

// Modern alternative: Object lookup
const dayMessages = {
    Monday: "Start of the work week",
    Tuesday: "Midweek",
    Wednesday: "Midweek",
    Thursday: "Midweek",
    Friday: "TGIF!",
    Saturday: "Weekend!",
    Sunday: "Weekend!"
};

console.log(dayMessages[day] || "Invalid day");
```

---

### 3. Ternary Operator

Concise conditional expression.

```javascript
// Basic ternary
let age = 20;
let status = age >= 18 ? "Adult" : "Minor";
console.log(status);  // "Adult"

// With function call
let isLoggedIn = true;
let greeting = isLoggedIn ? getWelcomeMessage() : "Please log in";

function getWelcomeMessage() {
    return "Welcome back!";
}

// Nested ternary (use sparingly)
let score = 85;
let grade = score >= 90 ? "A" : 
            score >= 80 ? "B" : 
            score >= 70 ? "C" : 
            score >= 60 ? "D" : "F";
console.log(grade);  // "B"

// Ternary in JSX/template strings
let username = "John";
console.log(`Hello, ${username ? username : "Guest"}!`);

// Multiple ternary operators (avoid if possible)
let time = 14;
let meal = time < 11 ? "breakfast" : 
           time < 15 ? "lunch" : "dinner";
console.log(meal);  // "lunch"

// Ternary for assignment
let discountPercentage = isPremiumMember ? 20 : 10;

// Ternary for return
function getDiscount(isPremium) {
    return isPremium ? 0.2 : 0.1;
}

// Complex ternary (prefer if/else for readability)
let result = condition1 ? value1 :
             condition2 ? value2 :
             condition3 ? value3 : defaultValue;

// Better alternative with if/else
let result2;
if (condition1) {
    result2 = value1;
} else if (condition2) {
    result2 = value2;
} else if (condition3) {
    result2 = value3;
} else {
    result2 = defaultValue;
}
```

---

### 4. For Loops

Repeat code a specific number of times.

```javascript
// Basic for loop
for (let i = 0; i < 5; i++) {
    console.log(i);  // 0, 1, 2, 3, 4
}

// Counting backwards
for (let i = 5; i > 0; i--) {
    console.log(i);  // 5, 4, 3, 2, 1
}

// Step by 2
for (let i = 0; i < 10; i += 2) {
    console.log(i);  // 0, 2, 4, 6, 8
}

// Loop through array
let fruits = ["apple", "banana", "orange"];

for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}

// Multiple variables
for (let i = 0, j = 10; i < j; i++, j--) {
    console.log(`i: ${i}, j: ${j}`);
}

// Nested for loops
for (let i = 1; i <= 3; i++) {
    for (let j = 1; j <= 3; j++) {
        console.log(`${i} x ${j} = ${i * j}`);
    }
}

// Creating a multiplication table
for (let i = 1; i <= 5; i++) {
    let row = "";
    for (let j = 1; j <= 5; j++) {
        row += `${i * j}\t`;
    }
    console.log(row);
}

// For loop with array methods
let numbers = [1, 2, 3, 4, 5];
let doubled = [];

for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
}
console.log(doubled);  // [2, 4, 6, 8, 10]

// Empty initialization or increment
let k = 0;
for (; k < 5;) {
    console.log(k);
    k++;
}

// Infinite loop (be careful!)
// for (;;) {
//     console.log("This runs forever!");
//     break;  // Need break to exit
// }
```

---

### 5. For...in Loop

Iterate over enumerable properties of an object.

```javascript
// Iterate over object properties
let person = {
    name: "John",
    age: 30,
    city: "New York"
};

for (let key in person) {
    console.log(`${key}: ${person[key]}`);
}
// Output:
// name: John
// age: 30
// city: New York

// Checking own properties
for (let key in person) {
    if (person.hasOwnProperty(key)) {
        console.log(`${key}: ${person[key]}`);
    }
}

// For...in with arrays (not recommended)
let colors = ["red", "green", "blue"];

for (let index in colors) {
    console.log(`${index}: ${colors[index]}`);
}
// Output:
// 0: red
// 1: green
// 2: blue

// Problem with for...in and arrays
Array.prototype.customMethod = function() {};

for (let index in colors) {
    console.log(index);  // Will include "customMethod"!
}

// Better: Use for...of for arrays
for (let color of colors) {
    console.log(color);  // red, green, blue
}

// Iterating over nested objects
let company = {
    name: "TechCorp",
    employees: {
        john: { age: 30 },
        jane: { age: 25 }
    }
};

for (let key in company) {
    if (typeof company[key] === "object" && company[key] !== null) {
        for (let subKey in company[key]) {
            console.log(`${key}.${subKey}:`, company[key][subKey]);
        }
    } else {
        console.log(`${key}:`, company[key]);
    }
}
```

---

### 6. For...of Loop

Iterate over iterable objects (arrays, strings, maps, sets, etc.).

```javascript
// Iterate over array
let fruits = ["apple", "banana", "orange"];

for (let fruit of fruits) {
    console.log(fruit);
}

// Iterate over string
let message = "Hello";

for (let char of message) {
    console.log(char);  // H, e, l, l, o
}

// With array entries
let colors = ["red", "green", "blue"];

for (let [index, color] of colors.entries()) {
    console.log(`${index}: ${color}`);
}

// Iterate over Set
let uniqueNumbers = new Set([1, 2, 3, 4, 5]);

for (let num of uniqueNumbers) {
    console.log(num);
}

// Iterate over Map
let userRoles = new Map([
    ["john", "admin"],
    ["jane", "user"],
    ["bob", "moderator"]
]);

for (let [username, role] of userRoles) {
    console.log(`${username}: ${role}`);
}

// For...of with break and continue
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

for (let num of numbers) {
    if (num === 5) {
        continue;  // Skip 5
    }
    if (num === 8) {
        break;  // Stop at 8
    }
    console.log(num);  // 1, 2, 3, 4, 6, 7
}

// Difference between for...in and for...of
let array = [10, 20, 30];

// for...in: iterates over indices
for (let index in array) {
    console.log(index);  // "0", "1", "2" (strings!)
}

// for...of: iterates over values
for (let value of array) {
    console.log(value);  // 10, 20, 30
}

// For...of doesn't work with plain objects
let obj = { a: 1, b: 2 };

// for (let item of obj) {  // TypeError: obj is not iterable
//     console.log(item);
// }

// Use Object.keys(), Object.values(), or Object.entries()
for (let key of Object.keys(obj)) {
    console.log(key);  // a, b
}

for (let value of Object.values(obj)) {
    console.log(value);  // 1, 2
}

for (let [key, value] of Object.entries(obj)) {
    console.log(`${key}: ${value}`);  // a: 1, b: 2
}
```

---

### 7. While Loops

Repeat code while a condition is true.

```javascript
// Basic while loop
let count = 0;

while (count < 5) {
    console.log(count);
    count++;
}

// Reading user input (conceptual)
let password = "";

while (password !== "secret") {
    // password = prompt("Enter password:");  // Browser only
    break;  // Break to prevent infinite loop in this example
}

// Processing array items
let tasks = ["task1", "task2", "task3"];
let i = 0;

while (i < tasks.length) {
    console.log(`Processing ${tasks[i]}`);
    i++;
}

// While with complex condition
let attempts = 0;
let maxAttempts = 3;
let success = false;

while (attempts < maxAttempts && !success) {
    console.log(`Attempt ${attempts + 1}`);
    // success = tryOperation();
    attempts++;
}

// Infinite loop with break
let j = 0;

while (true) {
    console.log(j);
    j++;
    
    if (j >= 5) {
        break;  // Exit the loop
    }
}

// While loop with continue
let num = 0;

while (num < 10) {
    num++;
    
    if (num % 2 === 0) {
        continue;  // Skip even numbers
    }
    
    console.log(num);  // 1, 3, 5, 7, 9
}
```

---

### 8. Do...While Loops

Execute code at least once, then repeat while condition is true.

```javascript
// Basic do...while
let count = 0;

do {
    console.log(count);
    count++;
} while (count < 5);

// Difference from while: executes at least once
let value = 10;

do {
    console.log("This runs once");  // Executes even though condition is false
} while (value < 5);

// Menu system example
let choice;

do {
    // choice = prompt("Choose option (1-4):");  // Browser only
    choice = "4";  // Simulating user input
    
    switch (choice) {
        case "1":
            console.log("Option 1 selected");
            break;
        case "2":
            console.log("Option 2 selected");
            break;
        case "3":
            console.log("Option 3 selected");
            break;
        case "4":
            console.log("Exiting...");
            break;
        default:
            console.log("Invalid choice");
    }
} while (choice !== "4");

// Input validation
let age;

do {
    // age = parseInt(prompt("Enter your age:"));  // Browser only
    age = 25;  // Simulating valid input
    
    if (age < 0 || age > 120 || isNaN(age)) {
        console.log("Invalid age. Please try again.");
    }
} while (age < 0 || age > 120 || isNaN(age));

console.log(`Valid age: ${age}`);

// Do...while with array processing
let items = ["item1", "item2", "item3"];
let index = 0;

do {
    console.log(items[index]);
    index++;
} while (index < items.length);
```

---

### 9. Break and Continue

Control loop execution flow.

```javascript
// Break: exit loop immediately
for (let i = 0; i < 10; i++) {
    if (i === 5) {
        break;  // Stop the loop
    }
    console.log(i);  // 0, 1, 2, 3, 4
}

// Continue: skip current iteration
for (let i = 0; i < 10; i++) {
    if (i % 2 === 0) {
        continue;  // Skip even numbers
    }
    console.log(i);  // 1, 3, 5, 7, 9
}

// Break in nested loops
outerLoop: for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        if (i === 1 && j === 1) {
            break outerLoop;  // Break out of both loops
        }
        console.log(`i: ${i}, j: ${j}`);
    }
}

// Continue with nested loops
for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        if (j === 1) {
            continue;  // Skip j = 1
        }
        console.log(`i: ${i}, j: ${j}`);
    }
}

// Break in while loop
let n = 0;
while (true) {
    console.log(n);
    n++;
    
    if (n >= 5) {
        break;
    }
}

// Continue in while loop
let m = 0;
while (m < 10) {
    m++;
    
    if (m % 2 === 0) {
        continue;
    }
    
    console.log(m);  // 1, 3, 5, 7, 9
}

// Finding item in array
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];
let target = 5;
let found = false;

for (let num of numbers) {
    if (num === target) {
        found = true;
        break;  // Stop searching once found
    }
}

console.log(found ? "Found!" : "Not found");

// Processing with conditions
let scores = [85, 92, 78, 95, 88, 65, 90];

for (let score of scores) {
    if (score < 70) {
        console.log("Failing grade found!");
        break;  // Stop checking if failing grade found
    }
    
    if (score < 80) {
        continue;  // Skip processing for scores < 80
    }
    
    console.log(`Good score: ${score}`);
}
```

---

### 10. Labels

Name loops for precise control with break and continue.

```javascript
// Basic label usage
firstLoop: for (let i = 0; i < 3; i++) {
    secondLoop: for (let j = 0; j < 3; j++) {
        if (i === 1 && j === 1) {
            break firstLoop;  // Break out of firstLoop
        }
        console.log(`i: ${i}, j: ${j}`);
    }
}

// Label with continue
outer: for (let i = 0; i < 3; i++) {
    inner: for (let j = 0; j < 3; j++) {
        if (j === 1) {
            continue outer;  // Continue with next iteration of outer
        }
        console.log(`i: ${i}, j: ${j}`);
    }
}

// Finding in 2D array
let matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];

let searchValue = 5;
let foundAt = null;

searchLoop: for (let i = 0; i < matrix.length; i++) {
    for (let j = 0; j < matrix[i].length; j++) {
        if (matrix[i][j] === searchValue) {
            foundAt = [i, j];
            break searchLoop;  // Exit both loops
        }
    }
}

console.log(foundAt ? `Found at [${foundAt}]` : "Not found");

// Label with blocks (rarely used)
myBlock: {
    console.log("Start");
    if (someCondition) {
        break myBlock;  // Jump to end of block
    }
    console.log("This might not execute");
}
console.log("After block");

// Note: Labels are rarely needed in modern JavaScript
// Array methods (find, some, every) are usually better
```

---

## 💡 Examples

### Example 1: Grade Calculator

```javascript
function calculateGrade(score) {
    if (score < 0 || score > 100) {
        return "Invalid score";
    }
    
    if (score >= 90) return "A";
    if (score >= 80) return "B";
    if (score >= 70) return "C";
    if (score >= 60) return "D";
    return "F";
}

let students = [
    { name: "Alice", score: 92 },
    { name: "Bob", score: 85 },
    { name: "Charlie", score: 78 },
    { name: "David", score: 65 },
    { name: "Eve", score: 55 }
];

for (let student of students) {
    let grade = calculateGrade(student.score);
    console.log(`${student.name}: ${student.score} - Grade ${grade}`);
}
```

### Example 2: Prime Number Checker

```javascript
function isPrime(num) {
    if (num <= 1) return false;
    if (num === 2) return true;
    if (num % 2 === 0) return false;
    
    for (let i = 3; i <= Math.sqrt(num); i += 2) {
        if (num % i === 0) {
            return false;
        }
    }
    
    return true;
}

// Find first 10 prime numbers
let primes = [];
let num = 2;

while (primes.length < 10) {
    if (isPrime(num)) {
        primes.push(num);
    }
    num++;
}

console.log("First 10 primes:", primes);
// [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
```

### Example 3: FizzBuzz

```javascript
function fizzBuzz(n) {
    for (let i = 1; i <= n; i++) {
        if (i % 15 === 0) {
            console.log("FizzBuzz");
        } else if (i % 3 === 0) {
            console.log("Fizz");
        } else if (i % 5 === 0) {
            console.log("Buzz");
        } else {
            console.log(i);
        }
    }
}

fizzBuzz(15);

// Alternative with ternary
function fizzBuzzTernary(n) {
    for (let i = 1; i <= n; i++) {
        console.log(
            i % 15 === 0 ? "FizzBuzz" :
            i % 3 === 0 ? "Fizz" :
            i % 5 === 0 ? "Buzz" : i
        );
    }
}
```

### Example 4: Pattern Printing

```javascript
// Right triangle
function printTriangle(rows) {
    for (let i = 1; i <= rows; i++) {
        let pattern = "";
        for (let j = 1; j <= i; j++) {
            pattern += "* ";
        }
        console.log(pattern);
    }
}

printTriangle(5);
// *
// * *
// * * *
// * * * *
// * * * * *

// Pyramid
function printPyramid(rows) {
    for (let i = 1; i <= rows; i++) {
        let pattern = "";
        
        // Add spaces
        for (let j = 1; j <= rows - i; j++) {
            pattern += " ";
        }
        
        // Add stars
        for (let k = 1; k <= 2 * i - 1; k++) {
            pattern += "*";
        }
        
        console.log(pattern);
    }
}

printPyramid(5);
//     *
//    ***
//   *****
//  *******
// *********
```

### Example 5: Array Search with Multiple Conditions

```javascript
let products = [
    { id: 1, name: "Laptop", price: 1000, inStock: true },
    { id: 2, name: "Phone", price: 500, inStock: false },
    { id: 3, name: "Tablet", price: 300, inStock: true },
    { id: 4, name: "Monitor", price: 200, inStock: true }
];

// Find affordable products in stock
let affordableProducts = [];

for (let product of products) {
    if (!product.inStock) {
        continue;  // Skip out of stock items
    }
    
    if (product.price > 500) {
        continue;  // Skip expensive items
    }
    
    affordableProducts.push(product);
}

console.log(affordableProducts);
// [{ id: 3, name: "Tablet", price: 300, inStock: true },
//  { id: 4, name: "Monitor", price: 200, inStock: true }]
```

---

## 🎤 Interview Questions

### Q1: What's the difference between `for...in` and `for...of`?

**Answer:**
- `for...in` iterates over enumerable **property keys** (indices for arrays)
- `for...of` iterates over **iterable values** (elements for arrays)

```javascript
let array = ["a", "b", "c"];

// for...in: iterates over indices (as strings)
for (let index in array) {
    console.log(index);  // "0", "1", "2"
}

// for...of: iterates over values
for (let value of array) {
    console.log(value);  // "a", "b", "c"
}

// for...in with objects
let obj = { x: 1, y: 2 };

for (let key in obj) {
    console.log(key);  // "x", "y"
}

// for...of doesn't work with plain objects
// for (let value of obj) {}  // TypeError!
```

**Best Practice:** Use `for...of` for arrays, `for...in` for object properties (with `hasOwnProperty` check).

---

### Q2: Explain the difference between `while` and `do...while`.

**Answer:**
- `while` checks condition **before** executing (may not execute at all)
- `do...while` executes **once first**, then checks condition

```javascript
// while: might not execute
let x = 10;
while (x < 5) {
    console.log("This never runs");
}

// do...while: always executes at least once
let y = 10;
do {
    console.log("This runs once");
} while (y < 5);

// Practical use: input validation
let input;
do {
    // input = prompt("Enter a number:");
    input = "5";  // Simulating
} while (isNaN(input));
```

**Use Case:** Use `do...while` when you need at least one execution (menus, validation).

---

### Q3: How do `break` and `continue` differ?

**Answer:**
- `break` **exits** the loop completely
- `continue` **skips** the current iteration and moves to the next

```javascript
// break: stops the loop
for (let i = 0; i < 10; i++) {
    if (i === 5) break;
    console.log(i);  // 0, 1, 2, 3, 4
}

// continue: skips iteration
for (let i = 0; i < 10; i++) {
    if (i === 5) continue;
    console.log(i);  // 0, 1, 2, 3, 4, 6, 7, 8, 9
}

// With labeled statements
outer: for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        if (i === 1 && j === 1) {
            break outer;  // Breaks out of outer loop
        }
        console.log(i, j);
    }
}
```

---

### Q4: What is short-circuit evaluation in conditional statements?

**Answer:**
Logical operators stop evaluating as soon as the result is determined.

```javascript
// AND (&&): stops at first falsy value
let result1 = false && expensiveFunction();  // expensiveFunction not called

// OR (||): stops at first truthy value
let result2 = true || expensiveFunction();  // expensiveFunction not called

// Practical use: conditional execution
isLoggedIn && redirectToProfile();

// Default values
let username = inputName || "Guest";

// Null checks
user && user.profile && user.profile.email
// Better: optional chaining
user?.profile?.email
```

---

### Q5: What are the use cases for the ternary operator?

**Answer:**
Use ternary for simple conditional assignments or expressions. Avoid for complex logic.

```javascript
// ✅ Good: Simple conditional
let status = age >= 18 ? "Adult" : "Minor";

// ✅ Good: Inline in JSX/templates
console.log(`You are ${age >= 18 ? "an adult" : "a minor"}`);

// ✅ Good: Return values
function getDiscount(isMember) {
    return isMember ? 0.1 : 0;
}

// ❌ Bad: Complex nested ternary
let result = a ? b ? c ? d : e : f : g;

// ✅ Better: Use if/else for complex logic
let result;
if (a) {
    if (b) {
        result = c ? d : e;
    } else {
        result = f;
    }
} else {
    result = g;
}
```

---

### Q6: When should you use a `switch` statement vs. `if...else`?

**Answer:**
- Use `switch` for **multiple discrete values** of a single variable
- Use `if...else` for **complex conditions** or **range checks**

```javascript
// ✅ Good for switch: discrete values
switch (day) {
    case "Monday":
        return "Work";
    case "Saturday":
    case "Sunday":
        return "Weekend";
}

// ❌ Bad for switch: ranges
switch (true) {
    case age < 18:
        return "Minor";
    case age < 65:
        return "Adult";
}

// ✅ Better with if...else: ranges
if (age < 18) return "Minor";
if (age < 65) return "Adult";
return "Senior";

// Modern alternative: object lookup
const dayType = {
    Monday: "Work",
    Tuesday: "Work",
    Saturday: "Weekend",
    Sunday: "Weekend"
}[day] || "Unknown";
```

---

### Q7: What are labeled statements and when should you use them?

**Answer:**
Labels allow breaking/continuing specific loops in nested structures. Rarely needed in modern JavaScript.

```javascript
// Breaking outer loop from inner loop
outer: for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        if (i === 1 && j === 1) {
            break outer;  // Breaks outer loop
        }
    }
}

// Modern alternative: use functions
function findValue(matrix, target) {
    for (let row of matrix) {
        for (let value of row) {
            if (value === target) {
                return [row, value];  // Returns from function
            }
        }
    }
    return null;
}

// Array methods (even better)
let found = matrix.some(row => row.includes(target));
```

**Best Practice:** Prefer functions and array methods over labeled statements.

---

### Q8: How do you optimize loops for performance?

**Answer:**
Several strategies can improve loop performance:

```javascript
// 1. Cache array length
// ❌ Bad: length calculated each iteration
for (let i = 0; i < arr.length; i++) {}

// ✅ Good: length cached
let len = arr.length;
for (let i = 0; i < len; i++) {}

// 2. Use appropriate loop type
// For simple iteration: for...of or forEach
for (let item of items) {}

// For index access: traditional for
for (let i = 0; i < items.length; i++) {}

// 3. Avoid work in loops
// ❌ Bad: expensive operation inside loop
for (let i = 0; i < arr.length; i++) {
    let result = expensiveCalculation();  // Calculated every time
    process(arr[i], result);
}

// ✅ Good: hoist invariant code
let result = expensiveCalculation();  // Calculated once
for (let i = 0; i < arr.length; i++) {
    process(arr[i], result);
}

// 4. Early exit with break
for (let item of items) {
    if (found) break;  // Stop when done
}

// 5. Use array methods for clarity
let result = array.find(item => item.id === targetId);
// vs nested loops searching
```

---

### Q9: What's the difference between `for` loop and array methods like `forEach`?

**Answer:**

```javascript
let numbers = [1, 2, 3, 4, 5];

// for loop
for (let i = 0; i < numbers.length; i++) {
    console.log(numbers[i]);
    if (numbers[i] === 3) break;  // Can use break
}

// forEach
numbers.forEach(num => {
    console.log(num);
    // Cannot break or continue!
    // if (num === 3) break;  // SyntaxError!
});

// for...of (best of both)
for (let num of numbers) {
    console.log(num);
    if (num === 3) break;  // Can use break
}
```

**Key Differences:**
- `for` loop: Full control, can `break`/`continue`
- `forEach`: Cannot `break`/`continue`, always iterates all
- `for...of`: Modern, cleaner syntax, can `break`/`continue`

---

### Q10: How do you handle nested conditions without deep nesting?

**Answer:**
Use guard clauses (early returns) and avoid else after return.

```javascript
// ❌ Bad: Deep nesting
function processPayment(amount, user) {
    if (amount > 0) {
        if (user) {
            if (user.isActive) {
                if (user.balance >= amount) {
                    // Process payment
                    return "Success";
                } else {
                    return "Insufficient funds";
                }
            } else {
                return "Inactive user";
            }
        } else {
            return "No user";
        }
    } else {
        return "Invalid amount";
    }
}

// ✅ Good: Guard clauses
function processPayment(amount, user) {
    if (amount <= 0) return "Invalid amount";
    if (!user) return "No user";
    if (!user.isActive) return "Inactive user";
    if (user.balance < amount) return "Insufficient funds";
    
    // Process payment
    return "Success";
}
```

---

## ✅ Best Practices

### 1. Use Guard Clauses
```javascript
// ❌ Bad: Nested if statements
function divide(a, b) {
    if (b !== 0) {
        return a / b;
    } else {
        return "Cannot divide by zero";
    }
}

// ✅ Good: Guard clause with early return
function divide(a, b) {
    if (b === 0) return "Cannot divide by zero";
    return a / b;
}
```

### 2. Choose the Right Loop
```javascript
// Object properties: for...in
for (let key in object) {
    if (object.hasOwnProperty(key)) {
        // process
    }
}

// Array values: for...of
for (let item of array) {
    // process
}

// Index needed: traditional for
for (let i = 0; i < array.length; i++) {
    // process
}

// Transformation: array methods
let doubled = array.map(x => x * 2);
```

### 3. Avoid Deep Nesting
```javascript
// ❌ Bad: Deep nesting
if (a) {
    if (b) {
        if (c) {
            // code
        }
    }
}

// ✅ Good: Early returns or combined conditions
if (!a) return;
if (!b) return;
if (!c) return;
// code

// Or combine
if (a && b && c) {
    // code
}
```

### 4. Use Clear Variable Names
```javascript
// ❌ Bad
for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr[i].length; j++) {
        // confusing
    }
}

// ✅ Good
for (let rowIndex = 0; rowIndex < matrix.length; rowIndex++) {
    for (let colIndex = 0; colIndex < matrix[rowIndex].length; colIndex++) {
        // clear intent
    }
}

// ✅ Better: for...of with descriptive names
for (let row of matrix) {
    for (let value of row) {
        // very clear
    }
}
```

### 5. Prefer Array Methods for Readability
```javascript
// ❌ Acceptable but verbose
let evens = [];
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] % 2 === 0) {
        evens.push(numbers[i]);
    }
}

// ✅ Better: More declarative
let evens = numbers.filter(n => n % 2 === 0);
```

---

## 📚 Resources

- [MDN: Control Flow](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling)
- [MDN: Loops and Iteration](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration)
- [JavaScript.info: Loops](https://javascript.info/while-for)

---

## 📝 Summary

- **if/else** for conditional execution
- **switch** for multiple discrete values
- **ternary** for simple conditional expressions
- **for** loop for counted iterations
- **for...in** for object properties
- **for...of** for iterable values
- **while** for condition-based loops
- **do...while** for at-least-once execution
- **break** exits loop, **continue** skips iteration
- Use guard clauses to reduce nesting
- Choose the right loop for the task
- Prefer array methods for common operations

---

<div align="center">

**[⬅️ Previous: Operators](./02-operators.md)** | **[🏠 Home](./README.md)** | **[Next: Functions ➡️](./04-functions.md)**

</div>
