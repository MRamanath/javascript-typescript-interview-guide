# 05. Arrays in JavaScript

> Arrays are ordered collections that store multiple values in a single variable.

## 📋 Overview

Arrays are one of the most fundamental and commonly used data structures in JavaScript. They allow you to store and manipulate collections of data efficiently. Understanding arrays and their methods is essential for working with data in JavaScript.

**What You'll Learn:**
- ✅ Creating and accessing arrays
- ✅ Array methods (map, filter, reduce, etc.)
- ✅ Iteration techniques
- ✅ Array destructuring
- ✅ Spread and rest operators with arrays
- ✅ Multi-dimensional arrays
- ✅ Array sorting and searching
- ✅ Performance considerations

---

## 🎯 Key Concepts

### 1. Creating Arrays

```javascript
// Array literal (most common)
let fruits = ["apple", "banana", "orange"];
console.log(fruits);  // ["apple", "banana", "orange"]

// Array constructor
let numbers = new Array(1, 2, 3, 4, 5);
console.log(numbers);  // [1, 2, 3, 4, 5]

// Empty array with specific length
let empty = new Array(5);
console.log(empty);        // [empty × 5]
console.log(empty.length); // 5

// Mixed data types (not recommended)
let mixed = [1, "text", true, null, { name: "John" }, [1, 2]];
console.log(mixed);

// Array.of() - creates array from arguments
let arr1 = Array.of(3);      // [3]
let arr2 = Array.of(1, 2, 3); // [1, 2, 3]

// Array.from() - creates array from iterable
let str = "hello";
let chars = Array.from(str);
console.log(chars);  // ["h", "e", "l", "l", "o"]

// Array.from() with mapping function
let doubled = Array.from([1, 2, 3], x => x * 2);
console.log(doubled);  // [2, 4, 6]

// Creating range of numbers
let range = Array.from({ length: 5 }, (_, i) => i + 1);
console.log(range);  // [1, 2, 3, 4, 5]
```

---

### 2. Accessing and Modifying Arrays

```javascript
let colors = ["red", "green", "blue", "yellow"];

// Access by index (0-based)
console.log(colors[0]);   // "red"
console.log(colors[2]);   // "blue"

// Negative indexing (doesn't work, returns undefined)
console.log(colors[-1]);  // undefined

// Using at() method (ES2022) - supports negative indices
console.log(colors.at(0));   // "red"
console.log(colors.at(-1));  // "yellow" (last element)
console.log(colors.at(-2));  // "blue" (second to last)

// Modify element
colors[1] = "purple";
console.log(colors);  // ["red", "purple", "blue", "yellow"]

// Access length
console.log(colors.length);  // 4

// Add element at specific index (creates holes)
colors[10] = "orange";
console.log(colors);        // ["red", "purple", "blue", "yellow", empty × 6, "orange"]
console.log(colors.length); // 11

// Check if array
console.log(Array.isArray(colors));      // true
console.log(Array.isArray("not array")); // false

// Get first and last elements
let first = colors[0];
let last = colors[colors.length - 1];
console.log(first, last);  // "red", "orange"

// Better way for last element
let lastElement = colors.at(-1);
```

---

### 3. Adding and Removing Elements

```javascript
let numbers = [1, 2, 3];

// push() - add to end (mutates array)
numbers.push(4);
console.log(numbers);  // [1, 2, 3, 4]

numbers.push(5, 6);
console.log(numbers);  // [1, 2, 3, 4, 5, 6]

// pop() - remove from end (mutates array)
let removed = numbers.pop();
console.log(removed);  // 6
console.log(numbers);  // [1, 2, 3, 4, 5]

// unshift() - add to beginning (mutates array)
numbers.unshift(0);
console.log(numbers);  // [0, 1, 2, 3, 4, 5]

numbers.unshift(-2, -1);
console.log(numbers);  // [-2, -1, 0, 1, 2, 3, 4, 5]

// shift() - remove from beginning (mutates array)
let first = numbers.shift();
console.log(first);    // -2
console.log(numbers);  // [-1, 0, 1, 2, 3, 4, 5]

// splice() - add/remove at any position (mutates array)
let items = ["a", "b", "c", "d", "e"];

// Remove 2 elements starting at index 1
let removed2 = items.splice(1, 2);
console.log(removed2);  // ["b", "c"]
console.log(items);     // ["a", "d", "e"]

// Insert elements at index 1
items.splice(1, 0, "x", "y");
console.log(items);  // ["a", "x", "y", "d", "e"]

// Replace elements
items.splice(1, 2, "b", "c");
console.log(items);  // ["a", "b", "c", "d", "e"]

// slice() - extract portion (doesn't mutate)
let arr = [1, 2, 3, 4, 5];
let portion = arr.slice(1, 4);
console.log(portion);  // [2, 3, 4]
console.log(arr);      // [1, 2, 3, 4, 5] (unchanged)

// Copy array with slice
let copy = arr.slice();
console.log(copy);  // [1, 2, 3, 4, 5]

// Negative indices with slice
console.log(arr.slice(-2));     // [4, 5]
console.log(arr.slice(1, -1));  // [2, 3, 4]
```

---

### 4. Array Iteration Methods

```javascript
let numbers = [1, 2, 3, 4, 5];

// forEach() - execute function for each element
numbers.forEach((num, index) => {
    console.log(`Index ${index}: ${num}`);
});

// map() - transform each element, return new array
let doubled = numbers.map(num => num * 2);
console.log(doubled);  // [2, 4, 6, 8, 10]

let squared = numbers.map(num => num ** 2);
console.log(squared);  // [1, 4, 9, 16, 25]

// filter() - select elements that pass test
let evens = numbers.filter(num => num % 2 === 0);
console.log(evens);  // [2, 4]

let greaterThan2 = numbers.filter(num => num > 2);
console.log(greaterThan2);  // [3, 4, 5]

// reduce() - reduce array to single value
let sum = numbers.reduce((acc, num) => acc + num, 0);
console.log(sum);  // 15

let product = numbers.reduce((acc, num) => acc * num, 1);
console.log(product);  // 120

// reduce() for complex operations
let users = [
    { name: "Alice", age: 25 },
    { name: "Bob", age: 30 },
    { name: "Charlie", age: 25 }
];

let ageGroups = users.reduce((acc, user) => {
    acc[user.age] = acc[user.age] || [];
    acc[user.age].push(user.name);
    return acc;
}, {});
console.log(ageGroups);
// { 25: ["Alice", "Charlie"], 30: ["Bob"] }

// find() - return first element that passes test
let found = numbers.find(num => num > 3);
console.log(found);  // 4

// findIndex() - return index of first match
let index = numbers.findIndex(num => num > 3);
console.log(index);  // 3

// findLast() and findLastIndex() (ES2023)
let lastFound = numbers.findLast(num => num > 2);
console.log(lastFound);  // 5

// some() - test if any element passes
let hasEven = numbers.some(num => num % 2 === 0);
console.log(hasEven);  // true

// every() - test if all elements pass
let allPositive = numbers.every(num => num > 0);
console.log(allPositive);  // true

let allEven = numbers.every(num => num % 2 === 0);
console.log(allEven);  // false
```

---

### 5. Searching and Sorting

```javascript
let fruits = ["apple", "banana", "orange", "banana", "grape"];

// indexOf() - first occurrence
console.log(fruits.indexOf("banana"));      // 1
console.log(fruits.indexOf("mango"));       // -1 (not found)
console.log(fruits.indexOf("banana", 2));   // 3 (start from index 2)

// lastIndexOf() - last occurrence
console.log(fruits.lastIndexOf("banana"));  // 3

// includes() - check if element exists
console.log(fruits.includes("orange"));     // true
console.log(fruits.includes("mango"));      // false

// sort() - sort in place (mutates array)
let numbers = [3, 1, 4, 1, 5, 9, 2, 6];
numbers.sort();
console.log(numbers);  // [1, 1, 2, 3, 4, 5, 6, 9]

// sort() with compare function
let nums = [10, 5, 40, 25, 100];

// Ascending order
nums.sort((a, b) => a - b);
console.log(nums);  // [5, 10, 25, 40, 100]

// Descending order
nums.sort((a, b) => b - a);
console.log(nums);  // [100, 40, 25, 10, 5]

// Sort strings
let words = ["banana", "apple", "cherry"];
words.sort();
console.log(words);  // ["apple", "banana", "cherry"]

// Sort objects
let users = [
    { name: "Charlie", age: 35 },
    { name: "Alice", age: 25 },
    { name: "Bob", age: 30 }
];

// Sort by age
users.sort((a, b) => a.age - b.age);
console.log(users);
// [{ name: "Alice", age: 25 }, { name: "Bob", age: 30 }, { name: "Charlie", age: 35 }]

// Sort by name
users.sort((a, b) => a.name.localeCompare(b.name));
console.log(users);
// [{ name: "Alice", ... }, { name: "Bob", ... }, { name: "Charlie", ... }]

// reverse() - reverse array in place
let arr = [1, 2, 3, 4, 5];
arr.reverse();
console.log(arr);  // [5, 4, 3, 2, 1]

// toSorted() and toReversed() (ES2023) - non-mutating versions
let original = [3, 1, 4, 1, 5];
let sorted = original.toSorted((a, b) => a - b);
console.log(original);  // [3, 1, 4, 1, 5] (unchanged)
console.log(sorted);    // [1, 1, 3, 4, 5]
```

---

### 6. Array Transformation

```javascript
// concat() - merge arrays (doesn't mutate)
let arr1 = [1, 2];
let arr2 = [3, 4];
let arr3 = [5, 6];

let merged = arr1.concat(arr2, arr3);
console.log(merged);  // [1, 2, 3, 4, 5, 6]

// join() - convert to string
let words = ["Hello", "World"];
console.log(words.join(" "));    // "Hello World"
console.log(words.join("-"));    // "Hello-World"
console.log(words.join(""));     // "HelloWorld"

// split() - string to array (string method)
let sentence = "Hello World";
let wordsArray = sentence.split(" ");
console.log(wordsArray);  // ["Hello", "World"]

// flat() - flatten nested arrays
let nested = [1, [2, 3], [4, [5, 6]]];
console.log(nested.flat());     // [1, 2, 3, 4, [5, 6]]
console.log(nested.flat(2));    // [1, 2, 3, 4, 5, 6]
console.log(nested.flat(Infinity)); // Flatten all levels

// flatMap() - map + flat
let numbers = [1, 2, 3];
let result = numbers.flatMap(n => [n, n * 2]);
console.log(result);  // [1, 2, 2, 4, 3, 6]

// fill() - fill with static value (mutates)
let arr = new Array(5).fill(0);
console.log(arr);  // [0, 0, 0, 0, 0]

let arr2 = [1, 2, 3, 4, 5];
arr2.fill("x", 1, 4);
console.log(arr2);  // [1, "x", "x", "x", 5]

// copyWithin() - copy part of array to another location (mutates)
let arr3 = [1, 2, 3, 4, 5];
arr3.copyWithin(0, 3);  // Copy from index 3 to index 0
console.log(arr3);  // [4, 5, 3, 4, 5]

// with() (ES2023) - non-mutating element replacement
let original = [1, 2, 3, 4, 5];
let modified = original.with(2, 99);
console.log(original);  // [1, 2, 3, 4, 5] (unchanged)
console.log(modified);  // [1, 2, 99, 4, 5]
```

---

### 7. Array Destructuring and Spread

```javascript
// Array destructuring
let colors = ["red", "green", "blue"];

let [first, second, third] = colors;
console.log(first);   // "red"
console.log(second);  // "green"
console.log(third);   // "blue"

// Skip elements
let [color1, , color3] = colors;
console.log(color1);  // "red"
console.log(color3);  // "blue"

// Rest in destructuring
let numbers = [1, 2, 3, 4, 5];
let [one, two, ...rest] = numbers;
console.log(one);   // 1
console.log(two);   // 2
console.log(rest);  // [3, 4, 5]

// Default values
let [a, b, c = 10] = [1, 2];
console.log(c);  // 10

// Swapping variables
let x = 5, y = 10;
[x, y] = [y, x];
console.log(x, y);  // 10, 5

// Spread operator - expand array
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];

// Combine arrays
let combined = [...arr1, ...arr2];
console.log(combined);  // [1, 2, 3, 4, 5, 6]

// Copy array
let copy = [...arr1];
console.log(copy);  // [1, 2, 3]

// Add elements
let newArr = [0, ...arr1, 4];
console.log(newArr);  // [0, 1, 2, 3, 4]

// Spread in function calls
function add(a, b, c) {
    return a + b + c;
}

let nums = [1, 2, 3];
console.log(add(...nums));  // 6

// Clone and modify
let original = [1, 2, 3];
let modified = [...original, 4, 5];
console.log(original);  // [1, 2, 3] (unchanged)
console.log(modified);  // [1, 2, 3, 4, 5]

// Remove duplicates with Set
let withDuplicates = [1, 2, 2, 3, 3, 4];
let unique = [...new Set(withDuplicates)];
console.log(unique);  // [1, 2, 3, 4]
```

---

### 8. Multi-dimensional Arrays

```javascript
// 2D array (matrix)
let matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];

// Access elements
console.log(matrix[0][0]);  // 1
console.log(matrix[1][2]);  // 6
console.log(matrix[2][1]);  // 8

// Iterate 2D array
for (let i = 0; i < matrix.length; i++) {
    for (let j = 0; j < matrix[i].length; j++) {
        console.log(`[${i}][${j}] = ${matrix[i][j]}`);
    }
}

// Using forEach
matrix.forEach((row, i) => {
    row.forEach((value, j) => {
        console.log(`[${i}][${j}] = ${value}`);
    });
});

// Create 2D array dynamically
function create2DArray(rows, cols, value = 0) {
    return Array.from({ length: rows }, () => 
        Array.from({ length: cols }, () => value)
    );
}

let grid = create2DArray(3, 4, 0);
console.log(grid);
// [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]

// Flatten 2D array
let flat = matrix.flat();
console.log(flat);  // [1, 2, 3, 4, 5, 6, 7, 8, 9]

// Sum all elements in 2D array
let sum = matrix.flat().reduce((acc, num) => acc + num, 0);
console.log(sum);  // 45

// Transpose matrix
function transpose(matrix) {
    return matrix[0].map((_, colIndex) => 
        matrix.map(row => row[colIndex])
    );
}

let transposed = transpose(matrix);
console.log(transposed);
// [[1, 4, 7], [2, 5, 8], [3, 6, 9]]

// 3D array
let cube = [
    [[1, 2], [3, 4]],
    [[5, 6], [7, 8]]
];

console.log(cube[0][1][0]);  // 3
```

---

## 💡 Examples

### Example 1: Shopping Cart

```javascript
class ShoppingCart {
    constructor() {
        this.items = [];
    }
    
    addItem(product, quantity = 1) {
        const existingItem = this.items.find(item => item.product.id === product.id);
        
        if (existingItem) {
            existingItem.quantity += quantity;
        } else {
            this.items.push({ product, quantity });
        }
    }
    
    removeItem(productId) {
        this.items = this.items.filter(item => item.product.id !== productId);
    }
    
    updateQuantity(productId, quantity) {
        const item = this.items.find(item => item.product.id === productId);
        if (item) {
            item.quantity = quantity;
        }
    }
    
    getTotal() {
        return this.items.reduce((total, item) => 
            total + item.product.price * item.quantity, 0
        );
    }
    
    getItemCount() {
        return this.items.reduce((count, item) => count + item.quantity, 0);
    }
}

// Usage
const cart = new ShoppingCart();
cart.addItem({ id: 1, name: "Laptop", price: 1000 }, 1);
cart.addItem({ id: 2, name: "Mouse", price: 25 }, 2);

console.log(`Total: $${cart.getTotal()}`);  // Total: $1050
console.log(`Items: ${cart.getItemCount()}`);  // Items: 3
```

### Example 2: Array Utility Functions

```javascript
// Remove duplicates
function removeDuplicates(arr) {
    return [...new Set(arr)];
}

// Chunk array
function chunk(arr, size) {
    return Array.from({ length: Math.ceil(arr.length / size) }, (_, i) =>
        arr.slice(i * size, i * size + size)
    );
}

console.log(chunk([1, 2, 3, 4, 5, 6, 7], 3));
// [[1, 2, 3], [4, 5, 6], [7]]

// Shuffle array
function shuffle(arr) {
    const shuffled = [...arr];
    for (let i = shuffled.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
    }
    return shuffled;
}

// Get random element
function randomElement(arr) {
    return arr[Math.floor(Math.random() * arr.length)];
}

// Partition array
function partition(arr, predicate) {
    return arr.reduce(([pass, fail], elem) => {
        return predicate(elem) ? [[...pass, elem], fail] : [pass, [...fail, elem]];
    }, [[], []]);
}

const numbers = [1, 2, 3, 4, 5, 6];
const [evens, odds] = partition(numbers, n => n % 2 === 0);
console.log(evens);  // [2, 4, 6]
console.log(odds);   // [1, 3, 5]

// Group by property
function groupBy(arr, key) {
    return arr.reduce((acc, obj) => {
        const groupKey = typeof key === 'function' ? key(obj) : obj[key];
        acc[groupKey] = acc[groupKey] || [];
        acc[groupKey].push(obj);
        return acc;
    }, {});
}

const users = [
    { name: "Alice", role: "admin" },
    { name: "Bob", role: "user" },
    { name: "Charlie", role: "admin" }
];

console.log(groupBy(users, "role"));
// { admin: [{name: "Alice", ...}, {name: "Charlie", ...}], user: [{name: "Bob", ...}] }
```

### Example 3: Data Processing Pipeline

```javascript
const employees = [
    { name: "Alice", department: "IT", salary: 80000, years: 5 },
    { name: "Bob", department: "HR", salary: 60000, years: 3 },
    { name: "Charlie", department: "IT", salary: 90000, years: 7 },
    { name: "David", department: "Sales", salary: 70000, years: 4 },
    { name: "Eve", department: "IT", salary: 85000, years: 6 }
];

// Get IT employees with salary > 80000, sorted by years
const result = employees
    .filter(emp => emp.department === "IT")
    .filter(emp => emp.salary > 80000)
    .sort((a, b) => b.years - a.years)
    .map(emp => ({ name: emp.name, salary: emp.salary }));

console.log(result);
// [{ name: "Charlie", salary: 90000 }, { name: "Eve", salary: 85000 }]

// Calculate average salary by department
const avgSalaries = Object.entries(
    employees.reduce((acc, emp) => {
        acc[emp.department] = acc[emp.department] || [];
        acc[emp.department].push(emp.salary);
        return acc;
    }, {})
).map(([dept, salaries]) => ({
    department: dept,
    avgSalary: salaries.reduce((sum, sal) => sum + sal, 0) / salaries.length
}));

console.log(avgSalaries);
```

### Example 4: Matrix Operations

```javascript
// Matrix addition
function addMatrices(m1, m2) {
    return m1.map((row, i) => 
        row.map((val, j) => val + m2[i][j])
    );
}

// Matrix multiplication
function multiplyMatrices(m1, m2) {
    const result = [];
    for (let i = 0; i < m1.length; i++) {
        result[i] = [];
        for (let j = 0; j < m2[0].length; j++) {
            let sum = 0;
            for (let k = 0; k < m1[0].length; k++) {
                sum += m1[i][k] * m2[k][j];
            }
            result[i][j] = sum;
        }
    }
    return result;
}

const matrix1 = [[1, 2], [3, 4]];
const matrix2 = [[5, 6], [7, 8]];

console.log(addMatrices(matrix1, matrix2));
// [[6, 8], [10, 12]]
```

### Example 5: Pagination

```javascript
function paginate(items, pageNumber, pageSize) {
    const startIndex = (pageNumber - 1) * pageSize;
    const endIndex = startIndex + pageSize;
    
    return {
        page: pageNumber,
        pageSize: pageSize,
        totalItems: items.length,
        totalPages: Math.ceil(items.length / pageSize),
        items: items.slice(startIndex, endIndex),
        hasNext: endIndex < items.length,
        hasPrev: pageNumber > 1
    };
}

const items = Array.from({ length: 50 }, (_, i) => `Item ${i + 1}`);

console.log(paginate(items, 2, 10));
// { page: 2, pageSize: 10, totalItems: 50, totalPages: 5, 
//   items: ["Item 11", ..., "Item 20"], hasNext: true, hasPrev: true }
```

---

## 🎤 Interview Questions

### Q1: What's the difference between `map()` and `forEach()`?

**Answer:**
- `map()` returns a **new array** with transformed elements
- `forEach()` returns **undefined**, used for side effects only

```javascript
const numbers = [1, 2, 3];

// map: returns new array
const doubled = numbers.map(n => n * 2);
console.log(doubled);  // [2, 4, 6]

// forEach: returns undefined
const result = numbers.forEach(n => n * 2);
console.log(result);  // undefined

// forEach is for side effects
numbers.forEach(n => console.log(n));  // Just logging

// Use map when you need transformation
const transformed = numbers.map(n => n ** 2);
```

**When to use:**
- Use `map()` when transforming data
- Use `forEach()` when performing actions (logging, updating DOM, etc.)

---

### Q2: Explain `reduce()` and its use cases.

**Answer:**
`reduce()` processes array elements to produce a single output value.

```javascript
const numbers = [1, 2, 3, 4, 5];

// Sum
const sum = numbers.reduce((acc, num) => acc + num, 0);
console.log(sum);  // 15

// Max value
const max = numbers.reduce((max, num) => num > max ? num : max);
console.log(max);  // 5

// Object from array
const users = ["Alice", "Bob", "Charlie"];
const userObj = users.reduce((acc, name, index) => {
    acc[index] = name;
    return acc;
}, {});
// { 0: "Alice", 1: "Bob", 2: "Charlie" }

// Flatten array
const nested = [[1, 2], [3, 4], [5]];
const flat = nested.reduce((acc, arr) => acc.concat(arr), []);
// [1, 2, 3, 4, 5]

// Count occurrences
const fruits = ["apple", "banana", "apple", "orange", "banana", "apple"];
const count = fruits.reduce((acc, fruit) => {
    acc[fruit] = (acc[fruit] || 0) + 1;
    return acc;
}, {});
// { apple: 3, banana: 2, orange: 1 }
```

---

### Q3: What's the difference between `slice()` and `splice()`?

**Answer:**
- `slice()` **doesn't mutate**, returns portion of array
- `splice()` **mutates** original array, can add/remove elements

```javascript
let arr = [1, 2, 3, 4, 5];

// slice: doesn't mutate
let portion = arr.slice(1, 4);
console.log(portion);  // [2, 3, 4]
console.log(arr);      // [1, 2, 3, 4, 5] (unchanged)

// splice: mutates
let removed = arr.splice(1, 2);
console.log(removed);  // [2, 3]
console.log(arr);      // [1, 4, 5] (modified!)

// splice can add elements
arr.splice(1, 0, 2, 3);
console.log(arr);  // [1, 2, 3, 4, 5]
```

---

### Q4: How do you remove duplicates from an array?

**Answer:**
Multiple approaches:

```javascript
const arr = [1, 2, 2, 3, 3, 4];

// 1. Using Set (most efficient)
const unique1 = [...new Set(arr)];

// 2. Using filter
const unique2 = arr.filter((item, index) => arr.indexOf(item) === index);

// 3. Using reduce
const unique3 = arr.reduce((acc, item) => 
    acc.includes(item) ? acc : [...acc, item], []
);

// For array of objects (by property)
const users = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
    { id: 1, name: "Alice" }
];

const uniqueUsers = users.filter((user, index, self) =>
    index === self.findIndex(u => u.id === user.id)
);
```

---

### Q5: What's the difference between `for...of` and `forEach()`?

**Answer:**

```javascript
const numbers = [1, 2, 3, 4, 5];

// for...of: can break/continue
for (const num of numbers) {
    if (num === 3) break;  // Works!
    console.log(num);  // 1, 2
}

// forEach: cannot break/continue
numbers.forEach(num => {
    // if (num === 3) break;  // SyntaxError!
    console.log(num);  // All elements
});

// for...of: works with any iterable
for (const char of "hello") {
    console.log(char);
}

// forEach: only arrays
```

**Key Differences:**
- `for...of` allows `break`/`continue`, `forEach` doesn't
- `for...of` works with any iterable, `forEach` is array method
- `forEach` has index parameter, `for...of` needs `.entries()`

---

### Q6: How does array sorting work in JavaScript?

**Answer:**
`sort()` converts elements to strings and sorts lexicographically by default.

```javascript
// Default sort (lexicographic)
let numbers = [10, 5, 40, 25, 100];
numbers.sort();
console.log(numbers);  // [10, 100, 25, 40, 5] (wrong!)

// Correct numeric sort
numbers.sort((a, b) => a - b);
console.log(numbers);  // [5, 10, 25, 40, 100]

// How compare function works:
// - Return < 0: a comes before b
// - Return 0: keep original order
// - Return > 0: b comes before a

// Descending
numbers.sort((a, b) => b - a);

// Sort objects
const users = [
    { name: "Charlie", age: 35 },
    { name: "Alice", age: 25 },
    { name: "Bob", age: 30 }
];

// By age (ascending)
users.sort((a, b) => a.age - b.age);

// By name (alphabetically)
users.sort((a, b) => a.name.localeCompare(b.name));

// Stable sort (ES2019+): maintains relative order of equal elements
```

---

### Q7: What are some performance considerations with arrays?

**Answer:**

```javascript
// ❌ Slow: length calculated every iteration
for (let i = 0; i < arr.length; i++) {
    // process
}

// ✅ Fast: cache length
const len = arr.length;
for (let i = 0; i < len; i++) {
    // process
}

// push() is O(1), unshift() is O(n)
arr.push(item);     // Fast: add to end
arr.unshift(item);  // Slow: shifts all elements

// delete creates holes (avoid)
const arr = [1, 2, 3];
delete arr[1];      // [1, empty, 3] - bad!

// Use splice instead
arr.splice(1, 1);   // [1, 3] - good!

// Sparse arrays are slower
const sparse = new Array(1000);
sparse[0] = 1;
sparse[999] = 2;    // Holes slow down iteration

// Dense arrays are faster
const dense = Array.from({ length: 1000 }, (_, i) => i);
```

---

### Q8: How do you flatten a nested array?

**Answer:**

```javascript
const nested = [1, [2, 3], [4, [5, 6]]];

// 1. flat() (ES2019)
console.log(nested.flat());      // [1, 2, 3, 4, [5, 6]]
console.log(nested.flat(2));     // [1, 2, 3, 4, 5, 6]
console.log(nested.flat(Infinity)); // Flatten all

// 2. reduce + concat (recursive)
function flattenDeep(arr) {
    return arr.reduce((acc, val) => 
        Array.isArray(val) ? acc.concat(flattenDeep(val)) : acc.concat(val),
        []
    );
}

// 3. Stack approach (iterative)
function flattenIterative(arr) {
    const stack = [...arr];
    const result = [];
    
    while (stack.length) {
        const item = stack.pop();
        if (Array.isArray(item)) {
            stack.push(...item);
        } else {
            result.unshift(item);
        }
    }
    
    return result;
}

// 4. toString + split (only for numbers)
const numArray = [1, [2, [3, 4]]];
const flattened = numArray.toString().split(',').map(Number);
```

---

### Q9: What's the difference between `Array.from()` and the spread operator?

**Answer:**

```javascript
const str = "hello";

// Both work on iterables
console.log(Array.from(str));  // ["h", "e", "l", "l", "o"]
console.log([...str]);         // ["h", "e", "l", "l", "o"]

// Array.from() has mapping function
console.log(Array.from(str, char => char.toUpperCase()));
// ["H", "E", "L", "L", "O"]

// Array.from() works with array-like objects
const arrayLike = { 0: 'a', 1: 'b', length: 2 };
console.log(Array.from(arrayLike));  // ["a", "b"]
// console.log([...arrayLike]);      // TypeError!

// Creating ranges
const range1 = Array.from({ length: 5 }, (_, i) => i + 1);
// [1, 2, 3, 4, 5]

// Spread is simpler for arrays
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = [...arr1, ...arr2];  // [1, 2, 3, 4]
```

---

### Q10: How do you find the intersection of two arrays?

**Answer:**

```javascript
const arr1 = [1, 2, 3, 4, 5];
const arr2 = [3, 4, 5, 6, 7];

// Method 1: filter + includes
const intersection1 = arr1.filter(item => arr2.includes(item));
console.log(intersection1);  // [3, 4, 5]

// Method 2: Set (more efficient for large arrays)
const set2 = new Set(arr2);
const intersection2 = arr1.filter(item => set2.has(item));

// For unique values only
const intersection3 = [...new Set(arr1)].filter(item => arr2.includes(item));

// Union
const union = [...new Set([...arr1, ...arr2])];
// [1, 2, 3, 4, 5, 6, 7]

// Difference (arr1 - arr2)
const difference = arr1.filter(item => !arr2.includes(item));
// [1, 2]

// Symmetric difference (elements in either but not both)
const symDiff = [...arr1, ...arr2].filter(item =>
    !arr1.includes(item) || !arr2.includes(item)
);
// [1, 2, 6, 7]
```

---

## ✅ Best Practices

### 1. Use Appropriate Methods
```javascript
// ❌ Manual loop for transformation
let doubled = [];
for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
}

// ✅ Use map
let doubled = numbers.map(n => n * 2);
```

### 2. Avoid Mutating Methods When Possible
```javascript
// ❌ Mutates original
arr.sort();
arr.reverse();

// ✅ Create new array (ES2023)
const sorted = arr.toSorted();
const reversed = arr.toReversed();

// ✅ Use slice for older code
const sorted = [...arr].sort();
```

### 3. Check for Empty Arrays
```javascript
// ❌ Truthy check doesn't work
if (arr) {  // Always true, even if empty!
    // ...
}

// ✅ Check length
if (arr.length > 0) {
    // ...
}

// ✅ Or use optional chaining
if (arr?.length) {
    // ...
}
```

### 4. Use Destructuring
```javascript
// ❌ Manual access
const first = arr[0];
const second = arr[1];

// ✅ Destructuring
const [first, second] = arr;

// ✅ With rest
const [first, ...rest] = arr;
```

### 5. Prefer Immutable Operations
```javascript
// ❌ Modifies original
arr.push(4);

// ✅ Create new array
const newArr = [...arr, 4];

// ❌ Mutates
arr.splice(1, 1);

// ✅ Immutable
const newArr = arr.filter((_, i) => i !== 1);
```

---

## 📚 Resources

- [MDN: Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [JavaScript.info: Arrays](https://javascript.info/array)
- [MDN: Array Methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#instance_methods)

---

## 📝 Summary

- Arrays store ordered collections of values
- Use array literals `[]` for creation
- **Transform**: `map()`, **Filter**: `filter()`, **Reduce**: `reduce()`
- **Add**: `push()`, `unshift()`, **Remove**: `pop()`, `shift()`, `splice()`
- **Search**: `find()`, `findIndex()`, `indexOf()`, `includes()`
- **Sort**: `sort()` with compare function for numbers
- **Destructuring** and **spread** operator for clean code
- `slice()` doesn't mutate, `splice()` does
- Prefer immutable operations for predictable code
- Use appropriate method for the task (don't loop manually)

---

<div align="center">

**[⬅️ Previous: Functions](./04-functions.md)** | **[🏠 Home](./README.md)** | **[Next: Objects ➡️](./06-objects.md)**

</div>
