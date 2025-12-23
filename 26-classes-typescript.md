# 26. Classes in TypeScript

> TypeScript classes extend JavaScript classes with type annotations, access modifiers, and advanced OOP features for building robust, maintainable object-oriented applications.

## 📋 Overview

TypeScript enhances JavaScript classes with static typing, access modifiers, abstract classes, and advanced inheritance patterns. These features enable you to build more structured, maintainable, and type-safe object-oriented applications with better encapsulation and design patterns.

**What You'll Learn:**
- ✅ Class syntax and type annotations
- ✅ Access modifiers (public, private, protected)
- ✅ Constructor parameter properties
- ✅ Abstract classes and methods
- ✅ Static members and methods
- ✅ Inheritance and method overriding
- ✅ Interfaces with classes
- ✅ Getters and setters
- ✅ Class decorators and metadata
- ✅ Generic classes
- ✅ Mixins and composition patterns
- ✅ Advanced OOP design patterns

---

## 🎯 Key Concepts

### 1. Basic Class Syntax with Types

```typescript
class Person {
  // Property declarations with types
  public name: string;
  private age: number;
  protected email: string;
  readonly id: string;

  constructor(name: string, age: number, email: string) {
    this.name = name;
    this.age = age;
    this.email = email;
    this.id = `person_${Date.now()}`;
  }

  // Method with return type annotation
  getInfo(): string {
    return `${this.name} (${this.age} years old)`;
  }

  // Private method
  private validateAge(age: number): boolean {
    return age >= 0 && age <= 150;
  }

  // Protected method (accessible in subclasses)
  protected updateEmail(newEmail: string): void {
    this.email = newEmail;
  }

  // Public method accessing private method
  setAge(newAge: number): void {
    if (this.validateAge(newAge)) {
      this.age = newAge;
    } else {
      throw new Error('Invalid age');
    }
  }
}

const person = new Person("Alice", 30, "alice@example.com");
console.log(person.name);     // OK: public property
// console.log(person.age);   // Error: private property
// console.log(person.email); // Error: protected property
console.log(person.id);       // OK: readonly property
// person.id = "new_id";      // Error: readonly property
```

### 2. Constructor Parameter Properties

```typescript
// Shorthand constructor with parameter properties
class User {
  constructor(
    public readonly id: string,
    public name: string,
    private password: string,
    protected email?: string
  ) {
    // Constructor body can be empty - properties are automatically assigned
  }

  // Method can access all properties
  authenticate(inputPassword: string): boolean {
    return this.password === inputPassword;
  }

  getPublicInfo(): { id: string; name: string } {
    return {
      id: this.id,
      name: this.name
    };
  }
}

// Equivalent to the verbose version above
class UserVerbose {
  public readonly id: string;
  public name: string;
  private password: string;
  protected email?: string;

  constructor(id: string, name: string, password: string, email?: string) {
    this.id = id;
    this.name = name;
    this.password = password;
    this.email = email;
  }
}

const user = new User("123", "John", "secret", "john@example.com");
console.log(user.id);   // "123"
console.log(user.name); // "John"
// console.log(user.password); // Error: private
```

### 3. Abstract Classes and Methods

```typescript
// Abstract base class
abstract class Animal {
  protected name: string;
  protected age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  // Concrete method available to all subclasses
  getInfo(): string {
    return `${this.name} is ${this.age} years old`;
  }

  // Abstract method - must be implemented by subclasses
  abstract makeSound(): string;
  abstract move(): void;

  // Abstract getter
  abstract get species(): string;
}

class Dog extends Animal {
  private breed: string;

  constructor(name: string, age: number, breed: string) {
    super(name, age); // Call parent constructor
    this.breed = breed;
  }

  // Implement abstract method
  makeSound(): string {
    return "Woof! Woof!";
  }

  // Implement abstract method
  move(): void {
    console.log(`${this.name} is running`);
  }

  // Implement abstract getter
  get species(): string {
    return "Canis lupus";
  }

  // Additional method specific to Dog
  fetch(): string {
    return `${this.name} is fetching the ball`;
  }
}

class Cat extends Animal {
  makeSound(): string {
    return "Meow";
  }

  move(): void {
    console.log(`${this.name} is sneaking quietly`);
  }

  get species(): string {
    return "Felis catus";
  }

  climb(): string {
    return `${this.name} is climbing a tree`;
  }
}

// Cannot instantiate abstract class
// const animal = new Animal("Generic", 5); // Error

const dog = new Dog("Buddy", 3, "Golden Retriever");
const cat = new Cat("Whiskers", 2);

console.log(dog.makeSound()); // "Woof! Woof!"
console.log(cat.makeSound()); // "Meow"
console.log(dog.species);     // "Canis lupus"
```

### 4. Static Members and Methods

```typescript
class MathUtils {
  // Static properties
  static readonly PI = 3.14159;
  private static instanceCount = 0;

  // Instance property
  private id: number;

  constructor() {
    MathUtils.instanceCount++;
    this.id = MathUtils.instanceCount;
  }

  // Static methods
  static circleArea(radius: number): number {
    return MathUtils.PI * radius * radius;
  }

  static getInstanceCount(): number {
    return MathUtils.instanceCount;
  }

  // Static method accessing static property
  static compareAreas(radius1: number, radius2: number): string {
    const area1 = MathUtils.circleArea(radius1);
    const area2 = MathUtils.circleArea(radius2);
    
    if (area1 > area2) return "First circle is larger";
    if (area2 > area1) return "Second circle is larger";
    return "Both circles are equal";
  }

  // Instance method
  getId(): number {
    return this.id;
  }

  // Instance method accessing static property
  getCircumference(radius: number): number {
    return 2 * MathUtils.PI * radius;
  }
}

// Using static members without instantiation
console.log(MathUtils.PI);                    // 3.14159
console.log(MathUtils.circleArea(5));         // 78.5398
console.log(MathUtils.getInstanceCount());    // 0

// Creating instances
const math1 = new MathUtils();
const math2 = new MathUtils();

console.log(MathUtils.getInstanceCount());    // 2
console.log(math1.getId());                   // 1
console.log(math2.getId());                   // 2
console.log(math1.getCircumference(10));      // 62.8318
```

### 5. Getters and Setters

```typescript
class Temperature {
  private _celsius: number;

  constructor(celsius: number) {
    this._celsius = celsius;
  }

  // Getter for celsius
  get celsius(): number {
    return this._celsius;
  }

  // Setter for celsius with validation
  set celsius(value: number) {
    if (value < -273.15) {
      throw new Error("Temperature cannot be below absolute zero");
    }
    this._celsius = value;
  }

  // Computed property getter
  get fahrenheit(): number {
    return (this._celsius * 9/5) + 32;
  }

  // Computed property setter
  set fahrenheit(value: number) {
    this.celsius = (value - 32) * 5/9;
  }

  // Computed property getter
  get kelvin(): number {
    return this._celsius + 273.15;
  }

  // Computed property setter
  set kelvin(value: number) {
    this.celsius = value - 273.15;
  }

  // Method using getters
  getTemperatureInfo(): string {
    return `${this.celsius}°C = ${this.fahrenheit}°F = ${this.kelvin}K`;
  }
}

const temp = new Temperature(25);
console.log(temp.celsius);     // 25
console.log(temp.fahrenheit);  // 77
console.log(temp.kelvin);      // 298.15

temp.fahrenheit = 86;          // Sets celsius via conversion
console.log(temp.celsius);     // 30

temp.kelvin = 300;            // Sets celsius via conversion
console.log(temp.celsius);    // 26.85

// temp.celsius = -300;       // Error: below absolute zero
```

### 6. Generic Classes

```typescript
// Generic class with type parameter
class Container<T> {
  private items: T[] = [];

  add(item: T): void {
    this.items.push(item);
  }

  remove(item: T): boolean {
    const index = this.items.indexOf(item);
    if (index > -1) {
      this.items.splice(index, 1);
      return true;
    }
    return false;
  }

  get(index: number): T | undefined {
    return this.items[index];
  }

  getAll(): readonly T[] {
    return [...this.items];
  }

  get count(): number {
    return this.items.length;
  }

  isEmpty(): boolean {
    return this.items.length === 0;
  }

  // Generic method with different type parameter
  map<U>(callback: (item: T, index: number) => U): U[] {
    return this.items.map(callback);
  }

  // Method with type predicate
  filter(predicate: (item: T) => boolean): T[] {
    return this.items.filter(predicate);
  }
}

// Usage with different types
const stringContainer = new Container<string>();
stringContainer.add("hello");
stringContainer.add("world");
console.log(stringContainer.getAll()); // ["hello", "world"]

const numberContainer = new Container<number>();
numberContainer.add(1);
numberContainer.add(2);
const doubled = numberContainer.map(x => x * 2); // number[]
console.log(doubled); // [2, 4]

// Generic class with constraints
interface Identifiable {
  id: string | number;
}

class Repository<T extends Identifiable> {
  private items = new Map<string | number, T>();

  save(item: T): void {
    this.items.set(item.id, item);
  }

  findById(id: string | number): T | undefined {
    return this.items.get(id);
  }

  findAll(): T[] {
    return Array.from(this.items.values());
  }

  delete(id: string | number): boolean {
    return this.items.delete(id);
  }

  // Method using constraint
  exists(id: string | number): boolean {
    return this.items.has(id);
  }
}

interface User extends Identifiable {
  id: string;
  name: string;
  email: string;
}

const userRepo = new Repository<User>();
userRepo.save({ id: "1", name: "Alice", email: "alice@example.com" });
const user = userRepo.findById("1");
console.log(user?.name); // "Alice"
```

### 7. Implementing Interfaces

```typescript
// Interface defining contract
interface Flyable {
  altitude: number;
  fly(): void;
  land(): void;
}

interface Swimmable {
  depth: number;
  swim(): void;
  surface(): void;
}

// Class implementing single interface
class Airplane implements Flyable {
  altitude: number = 0;
  private speed: number = 0;

  constructor(private model: string) {}

  fly(): void {
    this.altitude = 30000;
    this.speed = 500;
    console.log(`${this.model} is flying at ${this.altitude} feet`);
  }

  land(): void {
    this.altitude = 0;
    this.speed = 0;
    console.log(`${this.model} has landed`);
  }

  // Additional methods not in interface
  accelerate(speedIncrease: number): void {
    this.speed += speedIncrease;
  }
}

// Class implementing multiple interfaces
class Duck implements Flyable, Swimmable {
  altitude: number = 0;
  depth: number = 0;

  constructor(private name: string) {}

  // Implement Flyable
  fly(): void {
    this.altitude = 100;
    console.log(`${this.name} is flying at ${this.altitude} feet`);
  }

  land(): void {
    this.altitude = 0;
    console.log(`${this.name} has landed`);
  }

  // Implement Swimmable
  swim(): void {
    this.depth = 5;
    console.log(`${this.name} is swimming at ${this.depth} feet deep`);
  }

  surface(): void {
    this.depth = 0;
    console.log(`${this.name} has surfaced`);
  }

  // Duck-specific method
  quack(): void {
    console.log(`${this.name} says quack!`);
  }
}

const airplane = new Airplane("Boeing 747");
const duck = new Duck("Daffy");

airplane.fly();
duck.fly();
duck.swim();
duck.quack();

// Functions accepting interface types
function makeFly(flyer: Flyable): void {
  flyer.fly();
}

function makeSwim(swimmer: Swimmable): void {
  swimmer.swim();
}

makeFly(airplane); // OK
makeFly(duck);     // OK
makeSwim(duck);    // OK
// makeSwim(airplane); // Error: Airplane doesn't implement Swimmable
```

### 8. Mixins and Composition

```typescript
// Mixin type definition
type Constructor = new (...args: any[]) => {};

// Mixin function
function Timestamped<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    timestamp = new Date();

    getAge(): number {
      return Date.now() - this.timestamp.getTime();
    }

    touch(): void {
      this.timestamp = new Date();
    }
  };
}

function Trackable<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    private views: number = 0;

    view(): void {
      this.views++;
    }

    getViews(): number {
      return this.views;
    }

    resetViews(): void {
      this.views = 0;
    }
  };
}

function Cacheable<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    private cache = new Map<string, any>();

    getCached<T>(key: string): T | undefined {
      return this.cache.get(key);
    }

    setCached<T>(key: string, value: T): void {
      this.cache.set(key, value);
    }

    clearCache(): void {
      this.cache.clear();
    }
  };
}

// Base class
class Article {
  constructor(public title: string, public content: string) {}

  getWordCount(): number {
    return this.content.split(/\s+/).length;
  }
}

// Apply mixins
const TimestampedArticle = Timestamped(Article);
const TrackableArticle = Trackable(Article);
const CacheableArticle = Cacheable(Article);

// Combine multiple mixins
const FullFeaturedArticle = Timestamped(Trackable(Cacheable(Article)));

const article = new FullFeaturedArticle("TypeScript Guide", "This is a comprehensive guide...");

// Use base class functionality
console.log(article.title);         // "TypeScript Guide"
console.log(article.getWordCount()); // Word count

// Use mixin functionality
article.view();                      // From Trackable
article.view();
console.log(article.getViews());     // 2

article.setCached("summary", "Great article"); // From Cacheable
console.log(article.getCached("summary"));     // "Great article"

console.log(article.getAge());       // From Timestamped
article.touch();                     // Update timestamp

// Type-safe mixin interfaces
interface ITimestamped {
  timestamp: Date;
  getAge(): number;
  touch(): void;
}

interface ITrackable {
  view(): void;
  getViews(): number;
  resetViews(): void;
}

interface ICacheable {
  getCached<T>(key: string): T | undefined;
  setCached<T>(key: string, value: T): void;
  clearCache(): void;
}

// The article instance has all mixin types
const typedArticle: Article & ITimestamped & ITrackable & ICacheable = article;
```

### 9. Advanced Design Patterns

```typescript
// Singleton Pattern
class DatabaseConnection {
  private static instance: DatabaseConnection;
  private connected: boolean = false;

  // Private constructor prevents direct instantiation
  private constructor(private connectionString: string) {}

  static getInstance(connectionString?: string): DatabaseConnection {
    if (!DatabaseConnection.instance) {
      if (!connectionString) {
        throw new Error("Connection string required for first initialization");
      }
      DatabaseConnection.instance = new DatabaseConnection(connectionString);
    }
    return DatabaseConnection.instance;
  }

  connect(): void {
    if (!this.connected) {
      console.log(`Connecting to ${this.connectionString}`);
      this.connected = true;
    }
  }

  disconnect(): void {
    if (this.connected) {
      console.log("Disconnecting from database");
      this.connected = false;
    }
  }

  isConnected(): boolean {
    return this.connected;
  }
}

// Factory Pattern
interface Product {
  name: string;
  price: number;
  getInfo(): string;
}

class Book implements Product {
  constructor(
    public name: string, 
    public price: number, 
    public author: string,
    public isbn: string
  ) {}

  getInfo(): string {
    return `Book: ${this.name} by ${this.author} (ISBN: ${this.isbn}) - $${this.price}`;
  }
}

class Electronics implements Product {
  constructor(
    public name: string, 
    public price: number, 
    public brand: string,
    public warranty: number
  ) {}

  getInfo(): string {
    return `Electronics: ${this.name} by ${this.brand} - $${this.price} (${this.warranty}y warranty)`;
  }
}

abstract class ProductFactory {
  abstract createProduct(name: string, price: number, ...args: any[]): Product;

  // Template method
  processOrder(name: string, price: number, ...args: any[]): Product {
    const product = this.createProduct(name, price, ...args);
    this.logCreation(product);
    return product;
  }

  private logCreation(product: Product): void {
    console.log(`Created: ${product.getInfo()}`);
  }
}

class BookFactory extends ProductFactory {
  createProduct(name: string, price: number, author: string, isbn: string): Book {
    return new Book(name, price, author, isbn);
  }
}

class ElectronicsFactory extends ProductFactory {
  createProduct(name: string, price: number, brand: string, warranty: number): Electronics {
    return new Electronics(name, price, brand, warranty);
  }
}

// Observer Pattern
interface Observer<T> {
  update(data: T): void;
}

class Subject<T> {
  private observers: Observer<T>[] = [];

  addObserver(observer: Observer<T>): void {
    this.observers.push(observer);
  }

  removeObserver(observer: Observer<T>): void {
    const index = this.observers.indexOf(observer);
    if (index > -1) {
      this.observers.splice(index, 1);
    }
  }

  protected notifyObservers(data: T): void {
    this.observers.forEach(observer => observer.update(data));
  }
}

class NewsAgency extends Subject<string> {
  private news: string = "";

  setNews(news: string): void {
    this.news = news;
    this.notifyObservers(news);
  }

  getNews(): string {
    return this.news;
  }
}

class NewsChannel implements Observer<string> {
  constructor(private name: string) {}

  update(news: string): void {
    console.log(`${this.name} received news: ${news}`);
  }
}

// Usage of design patterns
const db = DatabaseConnection.getInstance("mongodb://localhost:27017");
const db2 = DatabaseConnection.getInstance(); // Same instance
console.log(db === db2); // true

const bookFactory = new BookFactory();
const electronicsFactory = new ElectronicsFactory();

const book = bookFactory.processOrder("TypeScript Guide", 29.99, "John Doe", "978-0123456789");
const phone = electronicsFactory.processOrder("iPhone", 999.99, "Apple", 2);

const newsAgency = new NewsAgency();
const cnn = new NewsChannel("CNN");
const bbc = new NewsChannel("BBC");

newsAgency.addObserver(cnn);
newsAgency.addObserver(bbc);
newsAgency.setNews("Breaking: TypeScript 5.0 Released!");
```

## 💻 Real-World Examples

### API Client with Error Handling

```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface ApiError {
  code: string;
  message: string;
  details?: any;
}

abstract class BaseApiClient {
  protected baseUrl: string;
  protected headers: Record<string, string> = {};

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }

  protected setHeader(key: string, value: string): void {
    this.headers[key] = value;
  }

  protected async request<T>(
    method: string, 
    endpoint: string, 
    data?: any
  ): Promise<ApiResponse<T>> {
    try {
      const url = `${this.baseUrl}${endpoint}`;
      const response = await fetch(url, {
        method,
        headers: {
          'Content-Type': 'application/json',
          ...this.headers
        },
        body: data ? JSON.stringify(data) : undefined
      });

      const result = await response.json();

      if (!response.ok) {
        throw this.createApiError(result);
      }

      return {
        data: result,
        status: response.status,
        message: 'Success'
      };
    } catch (error) {
      if (error instanceof ApiError) {
        throw error;
      }
      throw this.createApiError({ code: 'NETWORK_ERROR', message: 'Network request failed' });
    }
  }

  private createApiError(errorData: any): ApiError {
    return {
      code: errorData.code || 'UNKNOWN_ERROR',
      message: errorData.message || 'An unknown error occurred',
      details: errorData.details
    };
  }

  // Abstract methods that must be implemented by subclasses
  abstract authenticate(credentials: any): Promise<void>;
}

class UserApiClient extends BaseApiClient {
  constructor(baseUrl: string) {
    super(baseUrl);
  }

  async authenticate(credentials: { username: string; password: string }): Promise<void> {
    const response = await this.request<{ token: string }>('POST', '/auth/login', credentials);
    this.setHeader('Authorization', `Bearer ${response.data.token}`);
  }

  async getUser(id: string): Promise<ApiResponse<User>> {
    return this.request<User>('GET', `/users/${id}`);
  }

  async createUser(userData: Omit<User, 'id'>): Promise<ApiResponse<User>> {
    return this.request<User>('POST', '/users', userData);
  }

  async updateUser(id: string, updates: Partial<User>): Promise<ApiResponse<User>> {
    return this.request<User>('PUT', `/users/${id}`, updates);
  }

  async deleteUser(id: string): Promise<ApiResponse<void>> {
    return this.request<void>('DELETE', `/users/${id}`);
  }
}

// Usage
const apiClient = new UserApiClient('https://api.example.com');

async function manageUser() {
  try {
    await apiClient.authenticate({ username: 'admin', password: 'secret' });
    
    const userResponse = await apiClient.getUser('123');
    console.log('User:', userResponse.data);

    const newUser = await apiClient.createUser({
      name: 'John Doe',
      email: 'john@example.com'
    });
    console.log('Created user:', newUser.data);
  } catch (error) {
    if (error instanceof Error) {
      console.error('API Error:', error.message);
    }
  }
}
```

## 🎯 Best Practices

1. **Use Access Modifiers Appropriately**
   ```typescript
   class BankAccount {
     public readonly accountNumber: string;
     private balance: number;
     protected interestRate: number;

     // Public interface
     public deposit(amount: number): void { }
     public withdraw(amount: number): boolean { }
     public getBalance(): number { return this.balance; }

     // Private implementation details
     private validateAmount(amount: number): boolean { return amount > 0; }
   }
   ```

2. **Prefer Composition over Inheritance**
   ```typescript
   // Good: Composition
   class Car {
     constructor(
       private engine: Engine,
       private transmission: Transmission
     ) {}
   }

   // Use inheritance only for true "is-a" relationships
   class SportsCar extends Car {
     // SportsCar IS-A Car
   }
   ```

3. **Use Abstract Classes for Shared Implementation**
   ```typescript
   abstract class Shape {
     protected abstract calculateArea(): number;
     
     // Shared behavior
     displayInfo(): string {
       return `Area: ${this.calculateArea()}`;
     }
   }
   ```

4. **Implement Interfaces for Contracts**
   ```typescript
   interface Serializable {
     serialize(): string;
     deserialize(data: string): void;
   }

   class User implements Serializable {
     // Must implement interface methods
   }
   ```

## 📚 Additional Resources

- [TypeScript Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html)
- [Abstract Classes vs Interfaces](https://www.typescriptlang.org/docs/handbook/2/classes.html#abstract-classes)
- [Mixins in TypeScript](https://www.typescriptlang.org/docs/handbook/mixins.html)
- [OOP Design Patterns](https://refactoring.guru/design-patterns)

---

[← Previous: Advanced Types](./25-advanced-types.md) | [Next: Decorators →](./27-decorators.md) | [Home](./README.md)