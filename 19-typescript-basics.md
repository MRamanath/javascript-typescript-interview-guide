# 19. TypeScript Basics

> TypeScript adds static typing to JavaScript, enabling better tooling, error detection, and code documentation.

## 📋 Overview

TypeScript is a superset of JavaScript that adds optional static typing and modern language features. It compiles to plain JavaScript and helps catch errors at development time rather than runtime. TypeScript enhances developer productivity with better IDE support, refactoring tools, and self-documenting code.

**What You'll Learn:**
- ✅ TypeScript setup and configuration
- ✅ Basic type annotations and inference
- ✅ Primitive types (string, number, boolean, etc.)
- ✅ Arrays, tuples, and objects
- ✅ Union and intersection types
- ✅ Literal types and type guards
- ✅ Functions with type annotations
- ✅ Optional and default parameters
- ✅ TypeScript compiler options
- ✅ Working with JavaScript libraries
- ✅ Common TypeScript patterns
- ✅ Migration strategies from JavaScript

---

## 🎯 Key Concepts

### 1. TypeScript Setup and Basic Configuration

```bash
# Install TypeScript globally
npm install -g typescript

# Install for a project
npm install -D typescript @types/node

# Initialize TypeScript configuration
tsc --init

# Compile TypeScript files
tsc app.ts
tsc --watch  # Watch mode
```

Basic `tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

---

### 2. Basic Type Annotations

```typescript
// Explicit type annotations
let name: string = "John";
let age: number = 30;
let isActive: boolean = true;
let score: number | null = null;

// Type inference (TypeScript figures out the type)
let city = "New York"; // inferred as string
let count = 42; // inferred as number
let enabled = false; // inferred as boolean

// Arrays
let numbers: number[] = [1, 2, 3, 4, 5];
let fruits: string[] = ["apple", "banana", "orange"];
let mixed: (string | number)[] = ["hello", 42, "world"];

// Alternative array syntax
let scores: Array<number> = [95, 87, 92];
let names: Array<string> = ["Alice", "Bob", "Charlie"];

// Objects
let person: { name: string; age: number; email?: string } = {
  name: "John",
  age: 30
  // email is optional
};

// Function parameters and return types
function greet(name: string): string {
  return `Hello, ${name}!`;
}

function add(a: number, b: number): number {
  return a + b;
}

// Void for functions that don't return anything
function log(message: string): void {
  console.log(message);
}
```

---

### 3. Primitive Types

```typescript
// Basic types
let id: number = 123;
let username: string = "john_doe";
let isLoggedIn: boolean = true;

// Special types
let value: any = 42; // Can be anything (avoid when possible)
let result: unknown = getData(); // Safer than any
let nothing: null = null;
let notDefined: undefined = undefined;

// Never type - for functions that never return
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {
    // infinite loop
  }
}

// Symbol and BigInt (ES2020)
let sym: symbol = Symbol("id");
let bigNum: bigint = 100n;

// Literal types
let status: "pending" | "approved" | "rejected" = "pending";
let direction: "up" | "down" | "left" | "right" = "up";
let port: 3000 | 8080 = 3000;

// Type assertions (type casting)
let someValue: unknown = "hello world";
let strLength1: number = (someValue as string).length;
let strLength2: number = (<string>someValue).length; // JSX conflicts
```

---

### 4. Complex Types

```typescript
// Tuples - fixed-length arrays with specific types
let coordinate: [number, number] = [10, 20];
let person: [string, number, boolean] = ["John", 30, true];

// Named tuples (TypeScript 4.0+)
let point: [x: number, y: number] = [10, 20];

// Optional tuple elements
let user: [string, number?] = ["Alice"]; // age is optional

// Rest elements in tuples
let scores: [string, ...number[]] = ["Math", 95, 87, 92];

// Enums
enum Color {
  Red,
  Green,
  Blue
}

enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT"
}

let favoriteColor: Color = Color.Red; // 0
let movement: Direction = Direction.Up; // "UP"

// Const enums (inlined at compile time)
const enum Size {
  Small = 1,
  Medium = 2,
  Large = 3
}

let shirtSize: Size = Size.Medium;

// Objects with index signatures
interface StringDictionary {
  [key: string]: string;
}

let dict: StringDictionary = {
  name: "John",
  city: "New York"
};

// Record utility type (preferred over index signatures)
let userRoles: Record<string, string> = {
  admin: "Administrator",
  user: "Regular User",
  guest: "Guest"
};
```

---

### 5. Union and Intersection Types

```typescript
// Union types (OR)
type StringOrNumber = string | number;

function printId(id: string | number) {
  console.log(`ID: ${id}`);
  
  // Type narrowing
  if (typeof id === "string") {
    console.log(`Length: ${id.length}`); // string methods available
  } else {
    console.log(`Doubled: ${id * 2}`); // number methods available
  }
}

// Discriminated unions
type NetworkState = 
  | { state: "loading" }
  | { state: "success"; data: string }
  | { state: "error"; error: string };

function handleNetworkState(state: NetworkState) {
  switch (state.state) {
    case "loading":
      console.log("Loading...");
      break;
    case "success":
      console.log(`Data: ${state.data}`);
      break;
    case "error":
      console.log(`Error: ${state.error}`);
      break;
  }
}

// Intersection types (AND)
interface Name {
  name: string;
}

interface Age {
  age: number;
}

type Person = Name & Age;

let person: Person = {
  name: "John",
  age: 30
};

// Mixing interfaces and types
interface Contact {
  email: string;
  phone?: string;
}

type Employee = Person & Contact & {
  employeeId: number;
  department: string;
};
```

---

### 6. Functions with TypeScript

```typescript
// Function declarations
function add(a: number, b: number): number {
  return a + b;
}

// Function expressions
const multiply = function(a: number, b: number): number {
  return a * b;
};

// Arrow functions
const subtract = (a: number, b: number): number => a - b;

// Optional parameters
function greet(name: string, greeting?: string): string {
  return `${greeting || "Hello"}, ${name}!`;
}

// Default parameters
function createUser(name: string, role: string = "user"): object {
  return { name, role };
}

// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

// Function overloads
function combine(a: string, b: string): string;
function combine(a: number, b: number): number;
function combine(a: any, b: any): any {
  return a + b;
}

console.log(combine("Hello", "World")); // string
console.log(combine(1, 2)); // number

// Function types
type MathOperation = (a: number, b: number) => number;

const divide: MathOperation = (a, b) => a / b;

// Higher-order functions
function createMultiplier(factor: number): (value: number) => number {
  return (value: number) => value * factor;
}

const double = createMultiplier(2);
console.log(double(5)); // 10

// Callbacks with types
function processArray<T>(
  arr: T[], 
  callback: (item: T, index: number) => T
): T[] {
  return arr.map(callback);
}

const numbers = [1, 2, 3];
const doubled = processArray(numbers, (num, index) => num * 2);
```

---

### 7. Type Guards and Narrowing

```typescript
// typeof type guards
function printValue(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // string methods
  } else {
    console.log(value.toFixed(2)); // number methods
  }
}

// instanceof type guards
class Dog {
  bark() { console.log("Woof!"); }
}

class Cat {
  meow() { console.log("Meow!"); }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark();
  } else {
    animal.meow();
  }
}

// Custom type guards
interface User {
  name: string;
  email: string;
}

interface Admin {
  name: string;
  permissions: string[];
}

function isAdmin(user: User | Admin): user is Admin {
  return 'permissions' in user;
}

function handleUser(user: User | Admin) {
  if (isAdmin(user)) {
    console.log(user.permissions); // Admin type
  } else {
    console.log(user.email); // User type
  }
}

// in operator
function processInput(input: { name: string } | { id: number }) {
  if ('name' in input) {
    console.log(input.name);
  } else {
    console.log(input.id);
  }
}

// Truthiness narrowing
function printLength(str: string | null | undefined) {
  if (str) {
    console.log(str.length); // string type
  } else {
    console.log("No string provided");
  }
}
```

---

## 💡 Real-World Examples

### Example 1: E-commerce Product System

```typescript
// Product types
interface BaseProduct {
  id: string;
  name: string;
  price: number;
  description: string;
  inStock: boolean;
}

interface PhysicalProduct extends BaseProduct {
  type: "physical";
  weight: number;
  dimensions: {
    length: number;
    width: number;
    height: number;
  };
  shippingCost: number;
}

interface DigitalProduct extends BaseProduct {
  type: "digital";
  downloadUrl: string;
  fileSize: number;
  format: "pdf" | "mp4" | "zip" | "mp3";
}

type Product = PhysicalProduct | DigitalProduct;

// Shopping cart
interface CartItem {
  product: Product;
  quantity: number;
  addedAt: Date;
}

class ShoppingCart {
  private items: CartItem[] = [];

  addItem(product: Product, quantity: number = 1): void {
    const existingItem = this.items.find(item => item.product.id === product.id);
    
    if (existingItem) {
      existingItem.quantity += quantity;
    } else {
      this.items.push({
        product,
        quantity,
        addedAt: new Date()
      });
    }
  }

  removeItem(productId: string): boolean {
    const index = this.items.findIndex(item => item.product.id === productId);
    if (index > -1) {
      this.items.splice(index, 1);
      return true;
    }
    return false;
  }

  getTotal(): number {
    return this.items.reduce((total, item) => {
      const productCost = item.product.price * item.quantity;
      const shipping = item.product.type === "physical" 
        ? item.product.shippingCost * item.quantity 
        : 0;
      return total + productCost + shipping;
    }, 0);
  }

  getItems(): readonly CartItem[] {
    return [...this.items]; // Return immutable copy
  }
}

// Usage
const laptop: PhysicalProduct = {
  type: "physical",
  id: "laptop-1",
  name: "Gaming Laptop",
  price: 1200,
  description: "High-performance gaming laptop",
  inStock: true,
  weight: 2.5,
  dimensions: { length: 35, width: 25, height: 2 },
  shippingCost: 25
};

const ebook: DigitalProduct = {
  type: "digital",
  id: "ebook-1",
  name: "TypeScript Guide",
  price: 29.99,
  description: "Complete TypeScript guide",
  inStock: true,
  downloadUrl: "https://example.com/download",
  fileSize: 15,
  format: "pdf"
};

const cart = new ShoppingCart();
cart.addItem(laptop, 1);
cart.addItem(ebook, 1);
console.log(cart.getTotal()); // 1254.99
```

### Example 2: API Response Handling

```typescript
// API response types
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: string;
  timestamp: string;
}

interface User {
  id: number;
  username: string;
  email: string;
  profile: {
    firstName: string;
    lastName: string;
    avatar?: string;
  };
  createdAt: string;
}

interface PaginatedResponse<T> {
  items: T[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

// API service
class ApiService {
  private baseUrl: string;

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }

  async request<T>(endpoint: string, options?: RequestInit): Promise<ApiResponse<T>> {
    try {
      const response = await fetch(`${this.baseUrl}${endpoint}`, {
        headers: {
          'Content-Type': 'application/json',
          ...options?.headers,
        },
        ...options,
      });

      const data = await response.json();

      if (!response.ok) {
        return {
          success: false,
          error: data.message || `HTTP ${response.status}`,
          timestamp: new Date().toISOString(),
        };
      }

      return {
        success: true,
        data,
        timestamp: new Date().toISOString(),
      };
    } catch (error) {
      return {
        success: false,
        error: error instanceof Error ? error.message : 'Unknown error',
        timestamp: new Date().toISOString(),
      };
    }
  }

  async getUser(id: number): Promise<ApiResponse<User>> {
    return this.request<User>(`/users/${id}`);
  }

  async getUsers(page: number = 1, limit: number = 10): Promise<ApiResponse<PaginatedResponse<User>>> {
    return this.request<PaginatedResponse<User>>(`/users?page=${page}&limit=${limit}`);
  }

  async createUser(userData: Omit<User, 'id' | 'createdAt'>): Promise<ApiResponse<User>> {
    return this.request<User>('/users', {
      method: 'POST',
      body: JSON.stringify(userData),
    });
  }
}

// Usage with proper error handling
async function displayUser(api: ApiService, userId: number): Promise<void> {
  const response = await api.getUser(userId);
  
  if (response.success && response.data) {
    const user = response.data;
    console.log(`${user.profile.firstName} ${user.profile.lastName} (${user.username})`);
  } else {
    console.error(`Failed to load user: ${response.error}`);
  }
}
```

### Example 3: Configuration Management

```typescript
// Environment-specific configuration
interface DatabaseConfig {
  host: string;
  port: number;
  database: string;
  username: string;
  password: string;
  ssl: boolean;
  poolSize: number;
}

interface ApiConfig {
  baseUrl: string;
  timeout: number;
  retryAttempts: number;
  apiKey: string;
}

interface AppConfig {
  environment: "development" | "staging" | "production";
  port: number;
  database: DatabaseConfig;
  api: ApiConfig;
  features: {
    enableLogging: boolean;
    enableMetrics: boolean;
    enableCaching: boolean;
  };
  logging: {
    level: "error" | "warn" | "info" | "debug";
    format: "json" | "text";
  };
}

// Configuration factory
class ConfigManager {
  private config: AppConfig;

  constructor() {
    this.config = this.loadConfig();
  }

  private loadConfig(): AppConfig {
    const env = (process.env.NODE_ENV as AppConfig["environment"]) || "development";
    
    const baseConfig: AppConfig = {
      environment: env,
      port: parseInt(process.env.PORT || "3000"),
      database: {
        host: process.env.DB_HOST || "localhost",
        port: parseInt(process.env.DB_PORT || "5432"),
        database: process.env.DB_NAME || "app_db",
        username: process.env.DB_USER || "user",
        password: process.env.DB_PASSWORD || "password",
        ssl: env === "production",
        poolSize: parseInt(process.env.DB_POOL_SIZE || "10"),
      },
      api: {
        baseUrl: process.env.API_BASE_URL || "http://localhost:3000/api",
        timeout: parseInt(process.env.API_TIMEOUT || "5000"),
        retryAttempts: parseInt(process.env.API_RETRY_ATTEMPTS || "3"),
        apiKey: process.env.API_KEY || "dev-key",
      },
      features: {
        enableLogging: env !== "test",
        enableMetrics: env === "production",
        enableCaching: env !== "development",
      },
      logging: {
        level: (process.env.LOG_LEVEL as AppConfig["logging"]["level"]) || "info",
        format: env === "production" ? "json" : "text",
      },
    };

    return this.validateConfig(baseConfig);
  }

  private validateConfig(config: AppConfig): AppConfig {
    // Validate required fields
    const requiredFields: (keyof AppConfig)[] = ["environment", "port"];
    
    for (const field of requiredFields) {
      if (config[field] === undefined) {
        throw new Error(`Configuration error: ${field} is required`);
      }
    }

    // Validate port range
    if (config.port < 1 || config.port > 65535) {
      throw new Error("Configuration error: port must be between 1 and 65535");
    }

    return config;
  }

  getConfig(): Readonly<AppConfig> {
    return { ...this.config };
  }

  getDatabaseConfig(): Readonly<DatabaseConfig> {
    return { ...this.config.database };
  }

  getApiConfig(): Readonly<ApiConfig> {
    return { ...this.config.api };
  }

  isProduction(): boolean {
    return this.config.environment === "production";
  }

  isDevelopment(): boolean {
    return this.config.environment === "development";
  }
}

// Usage
const configManager = new ConfigManager();
const dbConfig = configManager.getDatabaseConfig();
const isProduction = configManager.isProduction();
```

---

## 🎤 Interview Questions

### Q1: What's the difference between `interface` and `type` in TypeScript?

**Answer:**

Both define object shapes, but `interface` is more extensible while `type` is more flexible:

```typescript
// Interface - can be extended and merged
interface User {
  name: string;
}

interface User {  // Declaration merging
  age: number;
}

interface Admin extends User {  // Extension
  permissions: string[];
}

// Type - more flexible, supports unions
type Status = "pending" | "approved" | "rejected";
type User2 = { name: string } & { age: number };  // Intersection
```

---

### Q2: What is type assertion and when should you use it?

**Answer:**

Type assertion tells TypeScript to treat a value as a specific type. Use sparingly when you know more than TypeScript:

```typescript
// Type assertion
let someValue: unknown = "hello world";
let strLength = (someValue as string).length;

// Use cases
const canvas = document.getElementById('canvas') as HTMLCanvasElement;
const data = JSON.parse(jsonString) as User[];

// Avoid when possible - prefer type guards
function isString(value: unknown): value is string {
  return typeof value === 'string';
}
```

---

### Q3: How do you handle null and undefined in TypeScript?

**Answer:**

Use strict null checks, optional chaining, and nullish coalescing:

```typescript
// tsconfig.json: "strict": true or "strictNullChecks": true

let name: string | null = getName();

// Type guards
if (name !== null) {
  console.log(name.length); // safe
}

// Optional chaining
console.log(user?.profile?.email);

// Nullish coalescing
const displayName = user?.name ?? 'Anonymous';

// Non-null assertion (use carefully)
console.log(name!.length); // tells TS name is not null
```

---

### Q4: What are generics and why are they useful?

**Answer:**

Generics allow creating reusable components that work with multiple types while maintaining type safety:

```typescript
// Generic function
function identity<T>(value: T): T {
  return value;
}

let num = identity(42);        // number
let str = identity("hello");   // string

// Generic interface
interface ApiResponse<T> {
  data: T;
  status: number;
}

let userResponse: ApiResponse<User> = {
  data: { name: "John", email: "john@example.com" },
  status: 200
};
```

---

### Q5: How do union types work with type narrowing?

**Answer:**

Union types represent values that can be one of several types. Type narrowing helps TypeScript understand which specific type you're working with:

```typescript
function processInput(input: string | number) {
  // Type narrowing with typeof
  if (typeof input === 'string') {
    return input.toUpperCase(); // string methods available
  } else {
    return input.toFixed(2);    // number methods available
  }
}

// Discriminated unions
type Shape = 
  | { kind: 'circle'; radius: number }
  | { kind: 'rectangle'; width: number; height: number };

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2;
    case 'rectangle':
      return shape.width * shape.height;
  }
}
```

---

### Q6: What's the difference between `any` and `unknown`?

**Answer:**

`any` disables type checking, while `unknown` requires type checking before use:

```typescript
// any - no type safety
let anyValue: any = 42;
anyValue.foo.bar; // No error, but runtime error possible

// unknown - type-safe
let unknownValue: unknown = 42;
// unknownValue.toFixed(); // Error: need to narrow type first

if (typeof unknownValue === 'number') {
  unknownValue.toFixed(); // Safe after type guard
}
```

---

## 📚 Summary

**Key Takeaways:**
- TypeScript adds static typing to JavaScript without changing runtime behavior
- Type annotations are optional; TypeScript infers types when possible
- Union types (`|`) and intersection types (`&`) provide flexible type composition
- Type guards and narrowing help work safely with union types
- Interfaces define object shapes and can be extended and merged
- Generics enable reusable, type-safe components
- `strict` mode catches more potential errors and is recommended
- TypeScript improves developer experience with better IDE support and refactoring

---

**Next Topic:** [20. TypeScript Types](./20-typescript-types.md)