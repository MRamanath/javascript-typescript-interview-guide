# 28. Modules and Namespaces in TypeScript

## **What You'll Learn:**
- Understanding TypeScript modules vs namespaces and when to use each
- Creating and consuming ES6 modules in TypeScript
- Working with different module systems (CommonJS, AMD, UMD, ES6)
- Organizing code with internal and external modules
- Using namespaces for logical grouping and avoiding naming conflicts
- Module resolution strategies and configuration options
- Best practices for structuring large TypeScript applications

## Overview

TypeScript provides powerful mechanisms for organizing and structuring code through modules and namespaces. Modules are the preferred way to organize code in modern TypeScript applications, following the ES6 module standard, while namespaces offer an alternative for grouping related functionality.

Understanding the difference between these two approaches is crucial for building scalable applications. Modules provide file-based organization with explicit imports and exports, making dependencies clear and enabling better tree-shaking. Namespaces, while less commonly used in modern development, still have their place for certain scenarios like organizing declaration files or creating logical groupings within a single file.

This guide will help you master both concepts and understand when and how to use them effectively in your TypeScript projects.

## Table of Contents
- [Modules vs Namespaces](#modules-vs-namespaces)
- [ES6 Modules in TypeScript](#es6-modules-in-typescript)
- [Export Statements](#export-statements)
- [Import Statements](#import-statements)
- [Default Exports](#default-exports)
- [Re-exports](#re-exports)
- [Dynamic Imports](#dynamic-imports)
- [Module Resolution](#module-resolution)
- [Namespaces](#namespaces)
- [Nested Namespaces](#nested-namespaces)
- [Namespace Merging](#namespace-merging)
- [Ambient Modules](#ambient-modules)
- [Module Augmentation](#module-augmentation)
- [Best Practices](#best-practices)

## Modules vs Namespaces

### When to Use Modules
- File-based organization (preferred approach)
- Working with external libraries
- Building applications for bundlers (webpack, rollup)
- Following ES6 standards

### When to Use Namespaces
- Organizing code within a single file
- Creating declaration files
- Legacy codebases
- Logical grouping without file separation

## ES6 Modules in TypeScript

TypeScript fully supports ES6 modules, which are the standard way to organize code in modern applications.

### Basic Module Structure

**math.ts**:
```typescript
// Named exports
export function add(a: number, b: number): number {
    return a + b;
}

export function subtract(a: number, b: number): number {
    return a - b;
}

export const PI = 3.14159;

// Export interface
export interface Calculator {
    add(a: number, b: number): number;
    subtract(a: number, b: number): number;
}

// Export class
export class BasicCalculator implements Calculator {
    add(a: number, b: number): number {
        return a + b;
    }
    
    subtract(a: number, b: number): number {
        return a - b;
    }
}

// Export type alias
export type Operation = 'add' | 'subtract' | 'multiply' | 'divide';
```

**main.ts**:
```typescript
// Named imports
import { add, subtract, PI, Calculator, BasicCalculator } from './math';

console.log(add(5, 3)); // 8
console.log(PI); // 3.14159

const calc: Calculator = new BasicCalculator();
console.log(calc.add(10, 5)); // 15
```

## Export Statements

### Named Exports

```typescript
// Direct exports
export const API_URL = 'https://api.example.com';
export function fetchData(url: string) {
    return fetch(url);
}

// Export declaration
function validateEmail(email: string): boolean {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

function validatePassword(password: string): boolean {
    return password.length >= 8;
}

// Export list
export { validateEmail, validatePassword };

// Export with alias
export { validateEmail as isValidEmail };
```

### Export All

```typescript
// utils.ts
export function formatDate(date: Date): string {
    return date.toISOString().split('T')[0];
}

export function formatCurrency(amount: number): string {
    return `$${amount.toFixed(2)}`;
}

// index.ts - barrel export
export * from './utils';
export * from './validation';
export * from './api';
```

### Conditional Exports

```typescript
// environment-based exports
const isDevelopment = process.env.NODE_ENV === 'development';

export const config = {
    apiUrl: isDevelopment ? 'http://localhost:3000' : 'https://api.prod.com',
    debug: isDevelopment
};

// Export functions conditionally
if (isDevelopment) {
    export function debugLog(...args: any[]): void {
        console.log('[DEBUG]', ...args);
    }
}
```

## Import Statements

### Named Imports

```typescript
// Basic named import
import { add, subtract } from './math';

// Import with alias
import { add as sum, subtract as diff } from './math';

// Multiple imports
import { 
    add, 
    subtract, 
    PI, 
    Calculator,
    BasicCalculator 
} from './math';

// Import type
import type { Calculator, Operation } from './math';

// Mixed imports (values and types)
import { add, type Calculator, type Operation } from './math';
```

### Import All

```typescript
// Import everything as namespace
import * as MathUtils from './math';

console.log(MathUtils.add(5, 3));
console.log(MathUtils.PI);

const calc = new MathUtils.BasicCalculator();
```

### Side Effect Imports

```typescript
// Import for side effects only
import './polyfills';
import './global-styles.css';

// Module that runs code on import
// logger.ts
console.log('Logger module loaded');

export function log(message: string): void {
    console.log(`[LOG] ${message}`);
}

// main.ts
import './logger'; // Logs: "Logger module loaded"
import { log } from './logger';
```

## Default Exports

### Exporting Defaults

```typescript
// default-export.ts

// Default function export
export default function greet(name: string): string {
    return `Hello, ${name}!`;
}

// Or export separately
function greet(name: string): string {
    return `Hello, ${name}!`;
}

export default greet;

// Default class export
export default class User {
    constructor(public name: string, public email: string) {}
    
    getInfo(): string {
        return `${this.name} (${this.email})`;
    }
}

// Default object export
export default {
    name: 'MyApp',
    version: '1.0.0',
    author: 'Developer'
};
```

### Importing Defaults

```typescript
// Import default export
import greet from './default-export';
import User from './user';
import config from './config';

// Mixed default and named imports
import greet, { add, subtract } from './math-with-default';

// Rename default import
import { default as greeting } from './default-export';
```

## Re-exports

### Barrel Exports

```typescript
// models/index.ts - Barrel file
export { User } from './user';
export { Product } from './product';
export { Order } from './order';
export type { UserRole } from './user';

// Or export all
export * from './user';
export * from './product';
export * from './order';

// Usage
import { User, Product, Order } from './models';
```

### Selective Re-exports

```typescript
// api/index.ts
export { fetchUser, createUser } from './user-api';
export { fetchProducts } from './product-api';

// Re-export with alias
export { default as ApiClient } from './client';
export { validateRequest as validate } from './validation';
```

## Dynamic Imports

### Lazy Loading

```typescript
// Dynamic import for code splitting
async function loadMathModule() {
    const mathModule = await import('./math');
    return mathModule;
}

// Conditional loading
async function loadFeature(featureName: string) {
    switch (featureName) {
        case 'charts':
            const chartModule = await import('./chart-feature');
            return chartModule.default;
        case 'analytics':
            const analyticsModule = await import('./analytics-feature');
            return analyticsModule.default;
        default:
            throw new Error(`Unknown feature: ${featureName}`);
    }
}

// Usage
async function initializeApp() {
    const math = await loadMathModule();
    console.log(math.add(5, 3));
    
    const chartFeature = await loadFeature('charts');
    chartFeature.initialize();
}
```

### Dynamic Import with Types

```typescript
// Type-safe dynamic imports
type MathModule = typeof import('./math');

async function getMathModule(): Promise<MathModule> {
    return await import('./math');
}

// Using dynamic import in functions
async function calculate(operation: 'add' | 'subtract', a: number, b: number) {
    const { add, subtract } = await import('./math');
    
    switch (operation) {
        case 'add':
            return add(a, b);
        case 'subtract':
            return subtract(a, b);
    }
}
```

## Module Resolution

### Module Resolution Strategies

**tsconfig.json**:
```json
{
  "compilerOptions": {
    "moduleResolution": "node",
    "baseUrl": "./src",
    "paths": {
      "@/*": ["*"],
      "@components/*": ["components/*"],
      "@utils/*": ["utils/*"],
      "@types/*": ["types/*"]
    },
    "resolveJsonModule": true,
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true
  }
}
```

### Path Mapping Usage

```typescript
// Instead of relative imports
import { Button } from '../../../components/ui/Button';
import { formatDate } from '../../../utils/date';

// Use path mapping
import { Button } from '@components/ui/Button';
import { formatDate } from '@utils/date';
import config from '@/config.json';
```

### Module Formats

```typescript
// CommonJS style (Node.js)
const fs = require('fs');
module.exports = { readFile: fs.readFile };

// ES6 style (preferred)
import fs from 'fs';
export { readFile } from 'fs';

// AMD style
define(['fs'], function(fs) {
    return { readFile: fs.readFile };
});
```

## Namespaces

### Basic Namespace

```typescript
// Single file organization
namespace Geometry {
    export interface Point {
        x: number;
        y: number;
    }
    
    export interface Circle {
        center: Point;
        radius: number;
    }
    
    export function distance(p1: Point, p2: Point): number {
        return Math.sqrt(
            Math.pow(p2.x - p1.x, 2) + Math.pow(p2.y - p1.y, 2)
        );
    }
    
    export function area(circle: Circle): number {
        return Math.PI * circle.radius * circle.radius;
    }
    
    // Internal (not exported) function
    function validatePoint(point: Point): boolean {
        return typeof point.x === 'number' && typeof point.y === 'number';
    }
}

// Usage
const point1: Geometry.Point = { x: 0, y: 0 };
const point2: Geometry.Point = { x: 3, y: 4 };
const circle: Geometry.Circle = { center: point1, radius: 5 };

console.log(Geometry.distance(point1, point2)); // 5
console.log(Geometry.area(circle)); // ~78.54
```

### Multi-file Namespaces

**shapes.ts**:
```typescript
namespace Shapes {
    export class Rectangle {
        constructor(
            public width: number,
            public height: number
        ) {}
        
        area(): number {
            return this.width * this.height;
        }
    }
}
```

**geometry.ts**:
```typescript
/// <reference path="shapes.ts" />

namespace Shapes {
    export class Circle {
        constructor(public radius: number) {}
        
        area(): number {
            return Math.PI * this.radius * this.radius;
        }
    }
    
    export function totalArea(...shapes: Array<Rectangle | Circle>): number {
        return shapes.reduce((total, shape) => total + shape.area(), 0);
    }
}
```

## Nested Namespaces

```typescript
namespace MyCompany {
    export namespace UI {
        export namespace Components {
            export class Button {
                constructor(public label: string) {}
                
                render(): string {
                    return `<button>${this.label}</button>`;
                }
            }
            
            export class Input {
                constructor(public type: string = 'text') {}
                
                render(): string {
                    return `<input type="${this.type}">`;
                }
            }
        }
        
        export namespace Utils {
            export function createElement(tag: string, content: string): string {
                return `<${tag}>${content}</${tag}>`;
            }
        }
    }
    
    export namespace API {
        export interface ApiResponse<T> {
            data: T;
            status: number;
            message: string;
        }
        
        export class Client {
            constructor(private baseUrl: string) {}
            
            async get<T>(endpoint: string): Promise<ApiResponse<T>> {
                // Implementation
                throw new Error('Not implemented');
            }
        }
    }
}

// Usage with aliases
import Components = MyCompany.UI.Components;
import ApiClient = MyCompany.API.Client;

const button = new Components.Button('Click me');
const client = new ApiClient('https://api.example.com');
```

## Namespace Merging

```typescript
// First declaration
namespace Animals {
    export class Dog {
        name: string;
        constructor(name: string) {
            this.name = name;
        }
        
        bark(): void {
            console.log(`${this.name} barks!`);
        }
    }
}

// Second declaration (merged with first)
namespace Animals {
    export class Cat {
        name: string;
        constructor(name: string) {
            this.name = name;
        }
        
        meow(): void {
            console.log(`${this.name} meows!`);
        }
    }
    
    export function createPet(type: 'dog' | 'cat', name: string): Dog | Cat {
        return type === 'dog' ? new Dog(name) : new Cat(name);
    }
}

// Both Dog and Cat are available
const dog = new Animals.Dog('Buddy');
const cat = new Animals.Cat('Whiskers');
const pet = Animals.createPet('dog', 'Rex');
```

## Ambient Modules

### Declaration Files

```typescript
// types/jquery.d.ts
declare namespace JQuery {
    interface JQueryStatic {
        (selector: string): JQuery;
        ajax(settings: any): any;
    }
}

declare var $: JQuery.JQueryStatic;

// Or using ambient modules
declare module 'jquery' {
    export = $;
    
    interface JQueryStatic {
        (selector: string): JQuery;
        ajax(settings: any): any;
    }
    
    var $: JQueryStatic;
}
```

### Global Augmentation

```typescript
// Extending global namespace
declare global {
    interface Window {
        myCustomProperty: string;
    }
    
    namespace NodeJS {
        interface ProcessEnv {
            CUSTOM_ENV_VAR: string;
        }
    }
}

// Usage
window.myCustomProperty = 'Hello World';
console.log(process.env.CUSTOM_ENV_VAR);
```

## Module Augmentation

### Extending Third-party Modules

```typescript
// Extending existing module
declare module 'lodash' {
    interface LoDashStatic {
        customFunction(value: any): any;
    }
}

// Implementation (in a separate file)
import * as _ from 'lodash';

(_.as any).customFunction = function(value: any) {
    return value;
};
```

### Interface Merging with Modules

```typescript
// express-extensions.d.ts
import 'express';

declare module 'express' {
    interface Request {
        user?: {
            id: string;
            email: string;
        };
    }
}

// Now you can use req.user in Express middleware
import { Request, Response } from 'express';

function authMiddleware(req: Request, res: Response, next: Function) {
    // req.user is now available with proper typing
    if (req.user) {
        next();
    } else {
        res.status(401).send('Unauthorized');
    }
}
```

## Best Practices

### 1. Prefer Modules Over Namespaces

```typescript
// ✅ Good: Use modules for file organization
// math/operations.ts
export function add(a: number, b: number): number {
    return a + b;
}

// math/index.ts
export * from './operations';

// ❌ Avoid: Namespaces for file organization
namespace Math {
    export function add(a: number, b: number): number {
        return a + b;
    }
}
```

### 2. Use Barrel Exports for Clean APIs

```typescript
// ✅ Good: Clean barrel exports
// components/index.ts
export { Button } from './Button';
export { Input } from './Input';
export { Modal } from './Modal';

// Usage
import { Button, Input, Modal } from '@components';
```

### 3. Consistent Import/Export Style

```typescript
// ✅ Good: Consistent named exports
export const API_URL = 'https://api.example.com';
export function fetchData() { /* ... */ }
export class ApiClient { /* ... */ }

// ✅ Good: Clear default export
export default class UserService {
    // Main service class
}
```

### 4. Use Type-Only Imports When Appropriate

```typescript
// ✅ Good: Separate type and value imports
import { createUser } from './user-service';
import type { User, CreateUserRequest } from './types';

function handleCreateUser(request: CreateUserRequest): Promise<User> {
    return createUser(request);
}
```

### 5. Configure Module Resolution Properly

```typescript
// tsconfig.json
{
  "compilerOptions": {
    "moduleResolution": "node",
    "baseUrl": "./src",
    "paths": {
      "@/*": ["*"],
      "@components/*": ["components/*"],
      "@services/*": ["services/*"],
      "@types/*": ["types/*"]
    }
  }
}
```

### 6. Namespace Usage Guidelines

```typescript
// ✅ Good: Namespaces for declaration files
declare namespace MyLibrary {
    interface Config {
        apiKey: string;
        timeout: number;
    }
    
    function initialize(config: Config): void;
}

// ✅ Good: Namespaces for grouping related constants
namespace Colors {
    export const PRIMARY = '#007bff';
    export const SECONDARY = '#6c757d';
    export const SUCCESS = '#28a745';
}
```

### 7. Avoid Deep Namespace Nesting

```typescript
// ❌ Avoid: Too deeply nested
namespace MyApp.UI.Components.Forms.Inputs {
    export class TextInput { }
}

// ✅ Better: Flatten structure
namespace Forms {
    export class TextInput { }
    export class SelectInput { }
    export class CheckboxInput { }
}
```

## Summary

TypeScript provides powerful mechanisms for organizing code through modules and namespaces:

**Modules** (Preferred):
- File-based organization following ES6 standards
- Explicit imports and exports
- Better for modern applications and bundlers
- Support for dynamic imports and code splitting

**Namespaces**:
- Logical grouping within files
- Useful for declaration files and legacy code
- Support for merging and nested structures

**Key Takeaways**:
- Use modules for new applications and file organization
- Configure module resolution with path mapping
- Use barrel exports for clean APIs
- Leverage type-only imports for better performance
- Reserve namespaces for specific use cases like declaration files

Both approaches have their place in TypeScript development, but modules are the recommended approach for modern applications due to their alignment with JavaScript standards and better tooling support.