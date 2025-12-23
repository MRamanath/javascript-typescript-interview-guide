# 21. TypeScript Interfaces

> Interfaces define contracts for object shapes, enabling flexible and extensible type definitions in TypeScript.

## 📋 Overview

TypeScript interfaces provide a powerful way to define the structure of objects, functions, and classes. They enable contract-based programming, support inheritance and composition, and offer flexibility through optional properties, index signatures, and generic constraints.

**What You'll Learn:**
- ✅ Interface basics and object shape definitions
- ✅ Optional properties and readonly modifiers
- ✅ Function interfaces and call signatures
- ✅ Index signatures for dynamic properties
- ✅ Interface inheritance and composition
- ✅ Generic interfaces and constraints
- ✅ Interface vs type aliases comparison
- ✅ Declaration merging capabilities
- ✅ Implementing interfaces in classes
- ✅ Advanced interface patterns
- ✅ Interface design best practices
- ✅ Real-world interface modeling

---

## 🎯 Key Concepts

### 1. Basic Interface Definitions

```typescript
// Basic object interface
interface User {
  id: number;
  name: string;
  email: string;
  createdAt: Date;
}

// Using the interface
const user: User = {
  id: 1,
  name: "John Doe",
  email: "john@example.com",
  createdAt: new Date()
};

// Function parameter typing
function updateUser(user: User): void {
  console.log(`Updating user ${user.name}`);
}

// Optional properties
interface UserPreferences {
  theme?: 'light' | 'dark';
  language?: string;
  notifications?: boolean;
  autoSave?: boolean;
}

const preferences: UserPreferences = {
  theme: 'dark'
  // other properties are optional
};

// Readonly properties
interface Configuration {
  readonly apiUrl: string;
  readonly version: string;
  readonly features: readonly string[];
  settings: {
    timeout: number;
    retries: number;
  };
}

const config: Configuration = {
  apiUrl: "https://api.example.com",
  version: "1.0.0",
  features: ["auth", "payments"],
  settings: { timeout: 5000, retries: 3 }
};

// config.apiUrl = "new url"; // ❌ Error: Cannot assign to readonly property
```

---

### 2. Function Interfaces

```typescript
// Function type interfaces
interface Calculator {
  (a: number, b: number): number;
}

const add: Calculator = (x, y) => x + y;
const multiply: Calculator = (x, y) => x * y;

// Interface with call signature and properties
interface Counter {
  (start?: number): number;
  reset(): void;
  current: number;
}

function createCounter(): Counter {
  let count = 0;
  
  const counter = (start: number = 0) => {
    count = start;
    return count;
  };
  
  counter.reset = () => { count = 0; };
  counter.current = count;
  
  return counter as Counter;
}

// Method signatures
interface ApiClient {
  get(url: string): Promise<any>;
  post(url: string, data: any): Promise<any>;
  put(url: string, data: any): Promise<any>;
  delete(url: string): Promise<void>;
}

// Constructor interfaces
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface;
}

interface ClockInterface {
  tick(): void;
}

class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("Digital tick");
  }
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
  return new ctor(hour, minute);
}
```

---

### 3. Index Signatures

```typescript
// String index signatures
interface StringDictionary {
  [key: string]: string;
}

const colors: StringDictionary = {
  red: "#FF0000",
  green: "#00FF00",
  blue: "#0000FF"
};

// Number index signatures
interface NumberArray {
  [index: number]: string;
}

const fruits: NumberArray = ["apple", "banana", "cherry"];

// Mixed index signatures
interface MixedDictionary {
  [key: string]: string | number;
  [key: number]: string; // number index must be assignable to string index
}

// More specific properties alongside index signatures
interface ApiResponse {
  [key: string]: any; // catch-all for additional properties
  status: number;     // specific required property
  message: string;    // specific required property
}

// Template literal index signatures (TypeScript 4.1+)
interface Translations {
  [K: `lang_${string}`]: string;
}

const translations: Translations = {
  lang_en: "Hello",
  lang_es: "Hola",
  lang_fr: "Bonjour"
  // lang: "invalid" // ❌ Error: doesn't match pattern
};

// Record utility type alternative
type ColorMap = Record<string, string>;
type StatusCodes = Record<number, string>;
```

---

### 4. Interface Inheritance

```typescript
// Basic inheritance
interface Animal {
  name: string;
  age: number;
}

interface Mammal extends Animal {
  furColor: string;
  giveBirth(): void;
}

interface Dog extends Mammal {
  breed: string;
  bark(): void;
}

const myDog: Dog = {
  name: "Rex",
  age: 3,
  furColor: "brown",
  breed: "German Shepherd",
  giveBirth() { console.log("Giving birth"); },
  bark() { console.log("Woof!"); }
};

// Multiple inheritance
interface Flyable {
  fly(): void;
  wingspan: number;
}

interface Swimmable {
  swim(): void;
  divingDepth: number;
}

interface Duck extends Animal, Flyable, Swimmable {
  quack(): void;
}

// Inheritance with property override
interface Shape {
  color: string;
  area: number;
}

interface ColoredShape extends Shape {
  color: 'red' | 'blue' | 'green'; // More specific type
}

// Interface extending class
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

// Only classes that extend Control can implement this interface
class Button extends Control implements SelectableControl {
  select() { /* implementation */ }
}
```

---

### 5. Generic Interfaces

```typescript
// Basic generic interface
interface Container<T> {
  value: T;
  getValue(): T;
  setValue(value: T): void;
}

const stringContainer: Container<string> = {
  value: "hello",
  getValue() { return this.value; },
  setValue(value: string) { this.value = value; }
};

const numberContainer: Container<number> = {
  value: 42,
  getValue() { return this.value; },
  setValue(value: number) { this.value = value; }
};

// Multiple generic parameters
interface KeyValuePair<K, V> {
  key: K;
  value: V;
}

interface Dictionary<T> {
  [key: string]: T;
  add(key: string, value: T): void;
  get(key: string): T | undefined;
  remove(key: string): boolean;
}

// Generic constraints
interface Lengthwise {
  length: number;
}

interface Processor<T extends Lengthwise> {
  process(item: T): number; // Can access .length on T
}

const stringProcessor: Processor<string> = {
  process(str) { return str.length; }
};

const arrayProcessor: Processor<any[]> = {
  process(arr) { return arr.length; }
};

// Using keyof constraint
interface PropertyGetter<T, K extends keyof T> {
  getProperty(obj: T, key: K): T[K];
}

const personGetter: PropertyGetter<User, 'name' | 'email'> = {
  getProperty(user, key) {
    return user[key]; // TypeScript knows the return type
  }
};

// Conditional types in interfaces
interface ApiResponse<T> {
  data: T;
  error: T extends string ? never : string | null;
  success: boolean;
}
```

---

### 6. Declaration Merging

```typescript
// Declaration merging allows extending interfaces
interface Window {
  customProperty: string;
}

interface Window {
  anotherCustomProperty: number;
}

// Now Window has both properties
declare global {
  interface Window {
    myGlobalFunction(): void;
  }
}

// Merging with modules
declare module "some-library" {
  interface LibraryConfig {
    newOption?: boolean;
  }
}

// Namespace merging
namespace Validation {
  export interface StringValidator {
    isValid(s: string): boolean;
  }
}

namespace Validation {
  export interface NumberValidator {
    isValid(n: number): boolean;
  }
}

// Both interfaces are available in Validation namespace

// Interface merging with functions
interface Counter {
  (): number;
  reset(): void;
}

interface Counter {
  interval: number;
}

// Counter now has call signature, reset method, and interval property
```

---

## 💡 Real-World Examples

### Example 1: RESTful API Client Interface

```typescript
// HTTP method types
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'PATCH' | 'DELETE';

// Request configuration
interface RequestConfig {
  method: HttpMethod;
  headers?: Record<string, string>;
  params?: Record<string, any>;
  body?: any;
  timeout?: number;
  retries?: number;
}

// Response interface
interface ApiResponse<T = any> {
  data: T;
  status: number;
  statusText: string;
  headers: Record<string, string>;
}

// Error response
interface ApiError {
  message: string;
  code?: string;
  status: number;
  details?: Record<string, any>;
}

// Main API client interface
interface ApiClient {
  baseURL: string;
  defaultHeaders: Record<string, string>;
  
  // HTTP methods
  get<T = any>(url: string, config?: Partial<RequestConfig>): Promise<ApiResponse<T>>;
  post<T = any>(url: string, data?: any, config?: Partial<RequestConfig>): Promise<ApiResponse<T>>;
  put<T = any>(url: string, data?: any, config?: Partial<RequestConfig>): Promise<ApiResponse<T>>;
  patch<T = any>(url: string, data?: any, config?: Partial<RequestConfig>): Promise<ApiResponse<T>>;
  delete<T = any>(url: string, config?: Partial<RequestConfig>): Promise<ApiResponse<T>>;
  
  // Utility methods
  setHeader(key: string, value: string): void;
  removeHeader(key: string): void;
  setBaseURL(url: string): void;
}

// Interceptor interfaces
interface RequestInterceptor {
  onRequest?(config: RequestConfig): RequestConfig | Promise<RequestConfig>;
  onRequestError?(error: any): any;
}

interface ResponseInterceptor {
  onResponse?<T>(response: ApiResponse<T>): ApiResponse<T> | Promise<ApiResponse<T>>;
  onResponseError?(error: ApiError): any;
}

// Extended client with interceptors
interface ExtendedApiClient extends ApiClient {
  interceptors: {
    request: RequestInterceptor[];
    response: ResponseInterceptor[];
  };
  
  addRequestInterceptor(interceptor: RequestInterceptor): number;
  addResponseInterceptor(interceptor: ResponseInterceptor): number;
  removeRequestInterceptor(id: number): void;
  removeResponseInterceptor(id: number): void;
}

// Resource-specific interfaces
interface UserApi {
  getUsers(params?: { page?: number; limit?: number }): Promise<ApiResponse<User[]>>;
  getUser(id: string): Promise<ApiResponse<User>>;
  createUser(user: Omit<User, 'id'>): Promise<ApiResponse<User>>;
  updateUser(id: string, updates: Partial<User>): Promise<ApiResponse<User>>;
  deleteUser(id: string): Promise<ApiResponse<void>>;
}

// Implementation example
class HttpApiClient implements ExtendedApiClient {
  baseURL: string = '';
  defaultHeaders: Record<string, string> = {};
  interceptors = { request: [], response: [] };

  async get<T>(url: string, config?: Partial<RequestConfig>): Promise<ApiResponse<T>> {
    // Implementation
    throw new Error("Not implemented");
  }

  // ... other method implementations

  addRequestInterceptor(interceptor: RequestInterceptor): number {
    return this.interceptors.request.push(interceptor) - 1;
  }

  addResponseInterceptor(interceptor: ResponseInterceptor): number {
    return this.interceptors.response.push(interceptor) - 1;
  }

  setHeader(key: string, value: string): void {
    this.defaultHeaders[key] = value;
  }

  removeHeader(key: string): void {
    delete this.defaultHeaders[key];
  }

  setBaseURL(url: string): void {
    this.baseURL = url;
  }

  removeRequestInterceptor(id: number): void {
    this.interceptors.request.splice(id, 1);
  }

  removeResponseInterceptor(id: number): void {
    this.interceptors.response.splice(id, 1);
  }

  // Placeholder implementations
  post = this.get;
  put = this.get;
  patch = this.get;
  delete = this.get;
}
```

### Example 2: Plugin System Interface

```typescript
// Plugin lifecycle hooks
interface PluginHooks {
  onInit?(): void | Promise<void>;
  onDestroy?(): void | Promise<void>;
  onActivate?(): void | Promise<void>;
  onDeactivate?(): void | Promise<void>;
}

// Plugin metadata
interface PluginMetadata {
  name: string;
  version: string;
  description?: string;
  author?: string;
  dependencies?: string[];
  peerDependencies?: string[];
}

// Plugin configuration
interface PluginConfig {
  [key: string]: any;
}

// Base plugin interface
interface Plugin extends PluginHooks {
  metadata: PluginMetadata;
  config?: PluginConfig;
  
  // Plugin must implement initialize method
  initialize(context: PluginContext): void | Promise<void>;
}

// Plugin context provided to plugins
interface PluginContext {
  app: Application;
  logger: Logger;
  config: PluginConfig;
  emit(event: string, ...args: any[]): void;
  on(event: string, handler: (...args: any[]) => void): void;
  off(event: string, handler: (...args: any[]) => void): void;
}

// Different types of plugins
interface UIPlugin extends Plugin {
  renderComponent?(): React.ComponentType<any>;
  getMenuItems?(): MenuItem[];
  getToolbarButtons?(): ToolbarButton[];
}

interface DataPlugin extends Plugin {
  processData?(data: any): any | Promise<any>;
  validateData?(data: any): boolean | Promise<boolean>;
}

interface AuthPlugin extends Plugin {
  authenticate(credentials: any): Promise<AuthResult>;
  authorize(user: User, resource: string, action: string): Promise<boolean>;
}

// Plugin manager interface
interface PluginManager {
  // Plugin lifecycle
  register(plugin: Plugin): Promise<void>;
  unregister(pluginName: string): Promise<void>;
  activate(pluginName: string): Promise<void>;
  deactivate(pluginName: string): Promise<void>;
  
  // Plugin queries
  getPlugin(name: string): Plugin | undefined;
  getActivePlugins(): Plugin[];
  getAllPlugins(): Plugin[];
  isActive(pluginName: string): boolean;
  
  // Dependency management
  resolveDependencies(plugin: Plugin): Promise<Plugin[]>;
  checkCompatibility(plugin: Plugin): Promise<boolean>;
}

// Event system for plugins
interface PluginEventSystem {
  emit<T extends keyof PluginEvents>(event: T, ...args: PluginEvents[T]): void;
  on<T extends keyof PluginEvents>(event: T, handler: (...args: PluginEvents[T]) => void): void;
  off<T extends keyof PluginEvents>(event: T, handler: (...args: PluginEvents[T]) => void): void;
}

interface PluginEvents {
  'plugin:registered': [Plugin];
  'plugin:activated': [Plugin];
  'plugin:deactivated': [Plugin];
  'plugin:error': [Plugin, Error];
  'data:processed': [any, Plugin];
  'ui:rendered': [React.ComponentType, Plugin];
}

// Example plugin implementation
class ExampleUIPlugin implements UIPlugin {
  metadata: PluginMetadata = {
    name: 'example-ui',
    version: '1.0.0',
    description: 'Example UI plugin'
  };

  async initialize(context: PluginContext): Promise<void> {
    context.logger.info('Example UI plugin initialized');
  }

  renderComponent(): React.ComponentType<any> {
    return () => React.createElement('div', null, 'Example Plugin UI');
  }

  getMenuItems(): MenuItem[] {
    return [
      { id: 'example', label: 'Example', action: () => console.log('Example clicked') }
    ];
  }

  getToolbarButtons(): ToolbarButton[] {
    return [
      { id: 'example-btn', icon: 'example', tooltip: 'Example button' }
    ];
  }
}

// Supporting interfaces
interface Application {
  version: string;
  plugins: PluginManager;
  logger: Logger;
}

interface Logger {
  info(message: string): void;
  warn(message: string): void;
  error(message: string, error?: Error): void;
}

interface MenuItem {
  id: string;
  label: string;
  action: () => void;
  children?: MenuItem[];
}

interface ToolbarButton {
  id: string;
  icon: string;
  tooltip?: string;
  disabled?: boolean;
}

interface AuthResult {
  success: boolean;
  user?: User;
  token?: string;
  error?: string;
}
```

### Example 3: Data Validation Framework

```typescript
// Validation result interface
interface ValidationResult {
  isValid: boolean;
  errors: ValidationError[];
  warnings?: ValidationWarning[];
}

interface ValidationError {
  field: string;
  message: string;
  code?: string;
  value?: any;
}

interface ValidationWarning {
  field: string;
  message: string;
  code?: string;
}

// Base validator interface
interface Validator<T = any> {
  validate(value: T, context?: ValidationContext): ValidationResult | Promise<ValidationResult>;
}

// Validation context
interface ValidationContext {
  field: string;
  parent?: any;
  root?: any;
  path: string[];
  options?: ValidationOptions;
}

interface ValidationOptions {
  strict?: boolean;
  skipWarnings?: boolean;
  abortEarly?: boolean;
  context?: Record<string, any>;
}

// Specific validator types
interface StringValidator extends Validator<string> {
  minLength?(min: number): StringValidator;
  maxLength?(max: number): StringValidator;
  pattern?(regex: RegExp): StringValidator;
  email?(): StringValidator;
  url?(): StringValidator;
  required?(): StringValidator;
}

interface NumberValidator extends Validator<number> {
  min?(min: number): NumberValidator;
  max?(max: number): NumberValidator;
  integer?(): NumberValidator;
  positive?(): NumberValidator;
  required?(): NumberValidator;
}

interface ArrayValidator<T> extends Validator<T[]> {
  minItems?(min: number): ArrayValidator<T>;
  maxItems?(max: number): ArrayValidator<T>;
  items?(validator: Validator<T>): ArrayValidator<T>;
  unique?(): ArrayValidator<T>;
}

interface ObjectValidator<T extends object> extends Validator<T> {
  shape<S extends Record<keyof T, Validator>>(validators: S): ObjectValidator<T>;
  strict?(): ObjectValidator<T>;
  noUnknown?(): ObjectValidator<T>;
}

// Schema definition interface
interface Schema<T = any> extends Validator<T> {
  string(): StringValidator;
  number(): NumberValidator;
  boolean(): Validator<boolean>;
  array<U>(): ArrayValidator<U>;
  object<U extends object>(): ObjectValidator<U>;
  optional(): Schema<T | undefined>;
  nullable(): Schema<T | null>;
  default(value: T): Schema<T>;
}

// Validation schema builder
interface SchemaBuilder {
  create<T>(): Schema<T>;
  string(): StringValidator;
  number(): NumberValidator;
  boolean(): Validator<boolean>;
  array<T>(): ArrayValidator<T>;
  object<T extends object>(): ObjectValidator<T>;
  union<T extends readonly any[]>(...schemas: { [K in keyof T]: Validator<T[K]> }): Validator<T[number]>;
  intersection<T extends readonly any[]>(...schemas: { [K in keyof T]: Validator<T[K]> }): Validator<T[0] & T[1]>;
}

// Form validation specific interfaces
interface FormValidator<T extends Record<string, any>> {
  validateField<K extends keyof T>(field: K, value: T[K]): Promise<ValidationResult>;
  validateForm(data: Partial<T>): Promise<Record<keyof T, ValidationResult>>;
  addRule<K extends keyof T>(field: K, validator: Validator<T[K]>): void;
  removeRule<K extends keyof T>(field: K): void;
}

interface FormField<T = any> {
  name: string;
  value: T;
  error?: string;
  touched: boolean;
  validator?: Validator<T>;
}

interface Form<T extends Record<string, any> = Record<string, any>> {
  fields: { [K in keyof T]: FormField<T[K]> };
  isValid: boolean;
  isDirty: boolean;
  isSubmitting: boolean;
  
  setValue<K extends keyof T>(field: K, value: T[K]): void;
  setError<K extends keyof T>(field: K, error: string): void;
  clearError<K extends keyof T>(field: K): void;
  validate(): Promise<boolean>;
  reset(): void;
  submit(): Promise<void>;
}

// Usage example interfaces
interface UserRegistrationData {
  email: string;
  password: string;
  confirmPassword: string;
  firstName: string;
  lastName: string;
  age: number;
  terms: boolean;
}

// Example implementation
const userRegistrationSchema: ObjectValidator<UserRegistrationData> = {
  async validate(value: UserRegistrationData, context?: ValidationContext): Promise<ValidationResult> {
    const errors: ValidationError[] = [];
    
    // Email validation
    if (!value.email || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value.email)) {
      errors.push({ field: 'email', message: 'Valid email is required' });
    }
    
    // Password validation
    if (!value.password || value.password.length < 8) {
      errors.push({ field: 'password', message: 'Password must be at least 8 characters' });
    }
    
    // Confirm password
    if (value.password !== value.confirmPassword) {
      errors.push({ field: 'confirmPassword', message: 'Passwords do not match' });
    }
    
    // Other validations...
    
    return {
      isValid: errors.length === 0,
      errors
    };
  },

  // Fluent interface methods
  shape: function<S extends Record<keyof UserRegistrationData, Validator>>(validators: S) {
    // Implementation would combine validators
    return this;
  },

  strict: function() { return this; },
  noUnknown: function() { return this; }
};
```

---

## 🎤 Interview Questions

### Q1: What's the difference between interface and type aliases?

**Answer:**

**Interfaces** are more object-oriented and extensible, while **type aliases** are more functional and flexible:

```typescript
// Interface - can be extended and merged
interface User {
  name: string;
}
interface User { age: number; } // Declaration merging
interface Admin extends User { role: string; } // Extension

// Type - more flexible, supports unions/intersections
type Status = 'pending' | 'approved'; // Union
type UserWithStatus = User & { status: Status }; // Intersection
type StringOrNumber = string | number; // Can't do this with interface
```

---

### Q2: How does declaration merging work with interfaces?

**Answer:**

Declaration merging allows you to add properties to existing interfaces across multiple declarations:

```typescript
interface Window {
  myProperty: string;
}

interface Window {
  anotherProperty: number;
}

// Window now has both properties
window.myProperty = "value";
window.anotherProperty = 42;

// Useful for extending third-party libraries
declare global {
  interface Array<T> {
    myCustomMethod(): T[];
  }
}
```

---

### Q3: When should you use index signatures in interfaces?

**Answer:**

Use index signatures when you need dynamic property access or don't know all property names at compile time:

```typescript
// Dynamic properties
interface ApiResponse {
  [key: string]: any; // Any additional properties
  status: number;     // But status is required
}

// Dictionary pattern
interface UserPreferences {
  [setting: string]: boolean | string | number;
}

// Avoid when you know exact properties - use specific interfaces instead
```

---

### Q4: How do generic constraints work in interfaces?

**Answer:**

Generic constraints limit what types can be used with generics using `extends`:

```typescript
interface Lengthwise {
  length: number;
}

interface Processor<T extends Lengthwise> {
  process(item: T): number;
}

// T must have a length property
const stringProcessor: Processor<string> = {
  process: (str) => str.length // ✅ string has length
};

// const numberProcessor: Processor<number> = ... // ❌ number has no length
```

---

### Q5: How do you model optional vs required properties effectively?

**Answer:**

Use optional properties (`?`) judiciously and consider utility types for transformations:

```typescript
interface User {
  id: string;           // Always required
  name: string;         // Always required
  email?: string;       // Optional in some contexts
  lastLogin?: Date;     // Optional (might not exist)
}

// Transform interfaces for different contexts
type CreateUser = Omit<User, 'id' | 'lastLogin'>; // id generated, lastLogin not set yet
type UpdateUser = Partial<Pick<User, 'name' | 'email'>>; // Only name/email can be updated
type UserSummary = Required<Pick<User, 'id' | 'name'>>; // id and name always needed
```

---

### Q6: What are the best practices for interface design?

**Answer:**

**Best practices:**
- Keep interfaces focused and cohesive
- Use composition over large interfaces
- Prefer readonly for immutable data
- Use generic constraints to ensure type safety
- Document complex interfaces with JSDoc

```typescript
// ✅ Good - focused interface
interface User {
  readonly id: string;
  name: string;
  email: string;
}

interface UserActions {
  updateProfile(data: Partial<User>): Promise<User>;
  deleteAccount(): Promise<void>;
}

// ✅ Good - composed interface
interface UserService extends UserActions {
  getUser(id: string): Promise<User>;
}

// ❌ Avoid - too large and mixed concerns
interface MegaUserInterface {
  // ... 50+ properties and methods mixing data and behavior
}
```

---

## 📚 Summary

**Key Takeaways:**
- Interfaces define object shapes and contracts for TypeScript code
- Optional properties (`?`) and readonly modifiers provide flexibility and immutability
- Index signatures enable dynamic property access when property names aren't known
- Interface inheritance supports code reuse and hierarchical modeling
- Generic interfaces enable type-safe, reusable components
- Declaration merging allows extending interfaces across multiple declarations
- Interfaces are preferred for object shapes; types for unions and complex transformations
- Well-designed interfaces improve code maintainability and type safety

---

**Next Topic:** [22. Type Aliases](./22-type-aliases.md)