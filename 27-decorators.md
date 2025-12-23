# 27. Decorators in TypeScript

## **What You'll Learn:**
- How to enable and use decorators in TypeScript
- Different types of decorators: class, method, accessor, property, and parameter
- Creating decorator factories for configurable decorators
- Composing multiple decorators and understanding execution order
- Working with the Metadata API for advanced decorator scenarios
- Implementing common patterns like dependency injection, validation, and caching
- Best practices for writing maintainable and type-safe decorators

## Overview

Decorators are a powerful TypeScript feature that allows you to add metadata and modify the behavior of classes, methods, properties, and parameters in a declarative way. They provide a clean syntax for implementing cross-cutting concerns such as logging, validation, caching, and dependency injection.

This experimental feature, which is a stage 3 proposal for JavaScript, uses the `@expression` syntax where the expression evaluates to a function called at runtime. Decorators are widely used in popular frameworks like Angular and NestJS to create elegant, maintainable code with separation of concerns.

Whether you're building enterprise applications, APIs, or working with modern TypeScript frameworks, understanding decorators will help you write more expressive and maintainable code.

## Table of Contents
- [Introduction to Decorators](#introduction-to-decorators)
- [Enabling Decorators](#enabling-decorators)
- [Decorator Factories](#decorator-factories)
- [Class Decorators](#class-decorators)
- [Method Decorators](#method-decorators)
- [Accessor Decorators](#accessor-decorators)
- [Property Decorators](#property-decorators)
- [Parameter Decorators](#parameter-decorators)
- [Decorator Composition](#decorator-composition)
- [Metadata API](#metadata-api)
- [Common Use Cases](#common-use-cases)
- [Best Practices](#best-practices)

## Introduction to Decorators

Decorators are a special kind of declaration that can be attached to classes, methods, accessors, properties, or parameters. They use the form `@expression`, where `expression` must evaluate to a function that will be called at runtime with information about the decorated declaration.

Decorators are an experimental feature in TypeScript and are currently a stage 3 proposal for JavaScript.

## Enabling Decorators

To use decorators, you need to enable them in your TypeScript configuration:

**tsconfig.json**:
```json
{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

## Decorator Factories

A decorator factory is a function that returns the expression that will be called by the decorator at runtime:

```typescript
// Decorator factory
function color(value: string) {
    // This is the decorator
    return function (target: any) {
        // Implementation
        target.color = value;
    };
}

@color("red")
class Car {
    // Class implementation
}
```

## Class Decorators

Class decorators are applied to the constructor of the class and can be used to observe, modify, or replace a class definition:

```typescript
// Simple class decorator
function sealed(constructor: Function) {
    Object.seal(constructor);
    Object.seal(constructor.prototype);
}

@sealed
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

// Class decorator with factory
function classDecorator<T extends { new (...args: any[]): {} }>(constructor: T) {
    return class extends constructor {
        newProperty = "new property";
        hello = "override";
    };
}

@classDecorator
class OriginalClass {
    property = "property";
    hello: string;
    
    constructor(m: string) {
        this.hello = m;
    }
}

// Logging class decorator
function LogClass(target: any) {
    console.log(`Class ${target.name} was decorated`);
    
    // Return a new constructor
    return class extends target {
        constructor(...args: any[]) {
            console.log(`Creating instance of ${target.name}`);
            super(...args);
        }
    };
}

@LogClass
class Product {
    constructor(public name: string, public price: number) {}
}
```

## Method Decorators

Method decorators are applied to the property descriptor for the method and can observe, modify, or replace a method definition:

```typescript
// Method decorator
function enumerable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.enumerable = value;
    };
}

// Timing decorator
function measureTime(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;
    
    descriptor.value = function (...args: any[]) {
        const start = performance.now();
        const result = originalMethod.apply(this, args);
        const end = performance.now();
        console.log(`${propertyKey} took ${end - start} milliseconds`);
        return result;
    };
}

// Validation decorator
function validate(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;
    
    descriptor.value = function (...args: any[]) {
        // Perform validation
        if (args.some(arg => arg == null)) {
            throw new Error(`Invalid arguments for ${propertyKey}`);
        }
        return originalMethod.apply(this, args);
    };
}

class Calculator {
    @enumerable(false)
    @measureTime
    add(a: number, b: number): number {
        return a + b;
    }
    
    @validate
    divide(a: number, b: number): number {
        if (b === 0) {
            throw new Error("Division by zero");
        }
        return a / b;
    }
}

// Async method decorator
function asyncErrorHandler(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;
    
    descriptor.value = async function (...args: any[]) {
        try {
            return await originalMethod.apply(this, args);
        } catch (error) {
            console.error(`Error in ${propertyKey}:`, error);
            throw error;
        }
    };
}

class ApiService {
    @asyncErrorHandler
    async fetchData(url: string): Promise<any> {
        const response = await fetch(url);
        return response.json();
    }
}
```

## Accessor Decorators

Accessor decorators are applied to the property descriptor for the accessor:

```typescript
function configurable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.configurable = value;
    };
}

// Validation accessor decorator
function range(min: number, max: number) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        const originalSetter = descriptor.set;
        
        descriptor.set = function (value: number) {
            if (value < min || value > max) {
                throw new Error(`Value must be between ${min} and ${max}`);
            }
            originalSetter?.call(this, value);
        };
    };
}

class Temperature {
    private _celsius: number = 0;
    
    @configurable(false)
    @range(-273.15, 1000)
    get celsius(): number {
        return this._celsius;
    }
    
    set celsius(value: number) {
        this._celsius = value;
    }
    
    get fahrenheit(): number {
        return (this._celsius * 9/5) + 32;
    }
}
```

## Property Decorators

Property decorators are applied to property declarations:

```typescript
// Property decorator
function defaultValue(value: any) {
    return function (target: any, propertyKey: string) {
        target[propertyKey] = value;
    };
}

// Required property decorator
function required(target: any, propertyKey: string) {
    let value: any;
    
    const getter = () => {
        if (value === undefined) {
            throw new Error(`Property ${propertyKey} is required`);
        }
        return value;
    };
    
    const setter = (newValue: any) => {
        value = newValue;
    };
    
    Object.defineProperty(target, propertyKey, {
        get: getter,
        set: setter,
        enumerable: true,
        configurable: true
    });
}

// Format decorator
function format(formatString: string) {
    return function (target: any, propertyKey: string) {
        let value: any;
        
        Object.defineProperty(target, propertyKey, {
            get: () => value,
            set: (newValue: any) => {
                value = formatString.replace('%s', newValue);
            },
            enumerable: true,
            configurable: true
        });
    };
}

class User {
    @required
    id: number;
    
    @defaultValue("Anonymous")
    name: string;
    
    @format("Email: %s")
    email: string;
    
    constructor() {
        // Properties are initialized by decorators
    }
}
```

## Parameter Decorators

Parameter decorators are applied to the parameter of a class constructor or method:

```typescript
import "reflect-metadata";

// Parameter decorator
function inject(token: string) {
    return function (target: any, propertyKey: string | symbol | undefined, parameterIndex: number) {
        const existingTokens = Reflect.getMetadata("inject-tokens", target) || [];
        existingTokens[parameterIndex] = token;
        Reflect.defineMetadata("inject-tokens", existingTokens, target);
    };
}

// Validation parameter decorator
function validate(target: any, propertyKey: string, parameterIndex: number) {
    const existingValidators = Reflect.getMetadata("validators", target, propertyKey) || [];
    existingValidators[parameterIndex] = true;
    Reflect.defineMetadata("validators", existingValidators, target, propertyKey);
}

class OrderService {
    constructor(@inject("database") private db: any) {}
    
    createOrder(@validate userId: string, @validate amount: number) {
        // Method implementation
        const validators = Reflect.getMetadata("validators", OrderService.prototype, "createOrder");
        console.log("Validators:", validators);
    }
}
```

## Decorator Composition

Multiple decorators can be composed on a single declaration:

```typescript
// Multiple decorators on a method
class MathService {
    @measureTime
    @validate
    @asyncErrorHandler
    async complexCalculation(a: number, b: number): Promise<number> {
        // Simulate complex async operation
        return new Promise(resolve => {
            setTimeout(() => resolve(a * b + Math.random()), 1000);
        });
    }
}

// Decorator evaluation order
function first() {
    console.log("first(): factory evaluated");
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("first(): called");
    };
}

function second() {
    console.log("second(): factory evaluated");
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("second(): called");
    };
}

class Example {
    @first()
    @second()
    method() {}
}
// Output:
// first(): factory evaluated
// second(): factory evaluated
// second(): called
// first(): called
```

## Metadata API

Using the `reflect-metadata` library to work with metadata:

```typescript
import "reflect-metadata";

// Metadata decorator
function metadata(key: string, value: any) {
    return function (target: any, propertyKey?: string) {
        if (propertyKey) {
            Reflect.defineMetadata(key, value, target, propertyKey);
        } else {
            Reflect.defineMetadata(key, value, target);
        }
    };
}

// Route decorator using metadata
function route(path: string) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        Reflect.defineMetadata("route:path", path, target, propertyKey);
        Reflect.defineMetadata("route:method", "GET", target, propertyKey);
    };
}

function httpMethod(method: string) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        Reflect.defineMetadata("route:method", method, target, propertyKey);
    };
}

@metadata("controller", "api")
class UserController {
    @route("/users")
    @httpMethod("GET")
    getUsers() {
        return [];
    }
    
    @route("/users/:id")
    @httpMethod("POST")
    createUser() {
        // Implementation
    }
}

// Reading metadata
const controllerMetadata = Reflect.getMetadata("controller", UserController);
const routePath = Reflect.getMetadata("route:path", UserController.prototype, "getUsers");
const routeMethod = Reflect.getMetadata("route:method", UserController.prototype, "getUsers");

console.log(controllerMetadata); // "api"
console.log(routePath); // "/users"
console.log(routeMethod); // "GET"
```

## Common Use Cases

### 1. Dependency Injection

```typescript
interface Service {
    name: string;
}

const serviceRegistry = new Map<string, any>();

function Injectable(name: string) {
    return function <T extends { new (...args: any[]): {} }>(constructor: T) {
        serviceRegistry.set(name, constructor);
        return constructor;
    };
}

function Inject(serviceName: string) {
    return function (target: any, propertyKey: string | symbol | undefined, parameterIndex: number) {
        const service = serviceRegistry.get(serviceName);
        if (service) {
            // Inject the service instance
            target[`__inject_${parameterIndex}`] = new service();
        }
    };
}

@Injectable("userService")
class UserService implements Service {
    name = "UserService";
    
    getUser(id: string) {
        return { id, name: "John Doe" };
    }
}

class UserController {
    constructor(@Inject("userService") private userService: UserService) {}
    
    handleGetUser(id: string) {
        return this.userService.getUser(id);
    }
}
```

### 2. Validation

```typescript
function IsEmail(target: any, propertyKey: string) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    
    Object.defineProperty(target, `__validate_${propertyKey}`, {
        value: (value: string) => emailRegex.test(value),
        writable: false
    });
}

function MinLength(length: number) {
    return function (target: any, propertyKey: string) {
        Object.defineProperty(target, `__validate_${propertyKey}`, {
            value: (value: string) => value && value.length >= length,
            writable: false
        });
    };
}

class CreateUserDto {
    @IsEmail
    email: string;
    
    @MinLength(8)
    password: string;
    
    validate(): boolean {
        const validators = Object.getOwnPropertyNames(this)
            .filter(key => key.startsWith('__validate_'));
        
        return validators.every(validator => {
            const propertyName = validator.replace('__validate_', '');
            return (this as any)[validator]((this as any)[propertyName]);
        });
    }
}
```

### 3. Caching

```typescript
function Cache(duration: number = 5000) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        const originalMethod = descriptor.value;
        const cache = new Map<string, { result: any; timestamp: number }>();
        
        descriptor.value = function (...args: any[]) {
            const key = JSON.stringify(args);
            const cached = cache.get(key);
            
            if (cached && Date.now() - cached.timestamp < duration) {
                console.log(`Cache hit for ${propertyKey}`);
                return cached.result;
            }
            
            console.log(`Cache miss for ${propertyKey}`);
            const result = originalMethod.apply(this, args);
            cache.set(key, { result, timestamp: Date.now() });
            
            return result;
        };
    };
}

class DataService {
    @Cache(10000) // Cache for 10 seconds
    async fetchUserData(userId: string): Promise<any> {
        // Simulate API call
        await new Promise(resolve => setTimeout(resolve, 1000));
        return { id: userId, name: "User " + userId };
    }
}
```

## Best Practices

### 1. Keep Decorators Simple
```typescript
// Good: Simple, focused decorator
function readonly(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.writable = false;
}

// Avoid: Complex decorator with too many responsibilities
```

### 2. Use Decorator Factories for Configuration
```typescript
// Good: Configurable decorator
function throttle(delay: number) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        // Implementation
    };
}

// Usage
@throttle(1000)
method() {}
```

### 3. Preserve Method Context
```typescript
function logCalls(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;
    
    descriptor.value = function (...args: any[]) {
        console.log(`Calling ${propertyKey}`);
        // Use apply to preserve 'this' context
        return originalMethod.apply(this, args);
    };
}
```

### 4. Handle Async Methods Properly
```typescript
function retryAsync(attempts: number) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        const originalMethod = descriptor.value;
        
        descriptor.value = async function (...args: any[]) {
            for (let i = 0; i < attempts; i++) {
                try {
                    return await originalMethod.apply(this, args);
                } catch (error) {
                    if (i === attempts - 1) throw error;
                    console.log(`Attempt ${i + 1} failed, retrying...`);
                }
            }
        };
    };
}
```

### 5. Type Safety with Decorators
```typescript
// Type-safe decorator
function apiEndpoint<T>(config: { path: string; method: 'GET' | 'POST' | 'PUT' | 'DELETE' }) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        // Store configuration
        Reflect.defineMetadata('api:config', config, target, propertyKey);
    };
}

class ApiController {
    @apiEndpoint({ path: '/users', method: 'GET' })
    getUsers(): User[] {
        return [];
    }
}
```

## Summary

Decorators provide a powerful way to add metadata, modify behavior, and implement cross-cutting concerns in TypeScript applications. Key points:

- Enable decorators in `tsconfig.json` with `experimentalDecorators: true`
- Use decorator factories for configurable decorators
- Understand the execution order when composing multiple decorators
- Preserve method context when wrapping functions
- Use the Reflect Metadata API for advanced scenarios
- Common use cases include dependency injection, validation, caching, and logging
- Follow best practices for maintainable and type-safe decorators

Decorators are particularly useful in frameworks like Angular, NestJS, and other TypeScript-based systems where declarative programming patterns are beneficial.