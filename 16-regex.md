# 16. Regular Expressions (Regex) in JavaScript

> Regular expressions are powerful pattern-matching tools for searching, validating, and manipulating strings with concise syntax.

## 📋 Overview

Regular expressions (regex) provide a specialized syntax for defining search patterns in text. They're essential for form validation, data extraction, text parsing, and string manipulation. JavaScript has built-in support through the `RegExp` object and regex literals, making them a fundamental tool for any developer working with strings.

**What You'll Learn:**
- ✅ Creating regex with literals and RegExp constructor
- ✅ Regex flags (global, case-insensitive, multiline, etc.)
- ✅ Basic patterns (literals, metacharacters, escaping)
- ✅ Character classes and predefined sets (\d, \w, \s)
- ✅ Quantifiers (* + ? {n,m}) and greedy vs lazy matching
- ✅ Anchors (^ $) and word boundaries (\b)
- ✅ Groups, capturing, and backreferences
- ✅ Advanced features (lookaheads, lookbehinds)
- ✅ String methods with regex (test, match, replace, split)
- ✅ Common validation patterns (email, phone, URL)
- ✅ TypeScript integration and performance tips
- ✅ Best practices and common pitfalls

---

## 🎯 Key Concepts

## Creating Regular Expressions

### 1. Regex Literals vs RegExp Constructor

```javascript
// Using forward slashes
const pattern = /hello/;
const caseInsensitive = /hello/i;
const global = /hello/g;
```

---

### 2. RegExp Constructor
```javascript
// Using RegExp constructor
const pattern = new RegExp('hello');
const withFlags = new RegExp('hello', 'gi');

// Useful for dynamic patterns
const searchTerm = 'world';
const dynamicPattern = new RegExp(searchTerm, 'i');
```

---

## 🏷️ Regex Flags

### 3. Common Flags and Usage
- `g` - Global: Find all matches
- `i` - Case insensitive
- `m` - Multiline: ^ and $ match line breaks
- `s` - Dotall: . matches newlines
- `u` - Unicode: Full Unicode support
- `y` - Sticky: Match from lastIndex position

```javascript
const text = "Hello World\nHello Universe";

// Global and case insensitive
const globalPattern = /hello/gi;
console.log(text.match(globalPattern)); // ['Hello', 'Hello']

// Multiline
const multilinePattern = /^Hello/gm;
console.log(text.match(multilinePattern)); // ['Hello', 'Hello']
```

---

## 🔤 Basic Patterns and Character Classes

### 4. Literal Characters and Metacharacters
```javascript
const pattern = /cat/;
console.log(pattern.test('cat')); // true
console.log(pattern.test('dog')); // false
```

### Special Characters (Metacharacters)
```javascript
// Dot (.) - matches any character except newline
const dotPattern = /c.t/;
console.log(dotPattern.test('cat')); // true
console.log(dotPattern.test('cut')); // true
console.log(dotPattern.test('c@t')); // true

// Escape special characters with backslash
const literalDot = /3\.14/;
console.log(literalDot.test('3.14')); // true
```

---

### 5. Character Classes

#### Basic Character Classes
```javascript
// Square brackets - match any character inside
const vowels = /[aeiou]/;
console.log(vowels.test('hello')); // true

// Range of characters
const digits = /[0-9]/;
const letters = /[a-z]/;
const alphanumeric = /[a-zA-Z0-9]/;

// Negated character class
const notVowels = /[^aeiou]/;
console.log(notVowels.test('hello')); // true (matches 'h', 'l', 'l')
```

#### Predefined Character Classes
```javascript
// \d - digits (equivalent to [0-9])
const digitPattern = /\d+/;
console.log(digitPattern.test('123')); // true

// \w - word characters (letters, digits, underscore)
const wordPattern = /\w+/;
console.log(wordPattern.test('hello_world123')); // true

// \s - whitespace characters
const spacePattern = /\s+/;
console.log(spacePattern.test('hello world')); // true

// Negated versions: \D, \W, \S
const nonDigit = /\D/;
console.log(nonDigit.test('abc')); // true
```

---

## 🔢 Quantifiers

### 6. Basic Quantifiers
```javascript
// * - zero or more
const zeroOrMore = /ab*c/;
console.log(zeroOrMore.test('ac')); // true
console.log(zeroOrMore.test('abc')); // true
console.log(zeroOrMore.test('abbbbc')); // true

// + - one or more
const oneOrMore = /ab+c/;
console.log(oneOrMore.test('ac')); // false
console.log(oneOrMore.test('abc')); // true

// ? - zero or one (optional)
const optional = /colou?r/;
console.log(optional.test('color')); // true
console.log(optional.test('colour')); // true
```

---

### 7. Specific Quantifiers
```javascript
// {n} - exactly n times
const exactly = /\d{3}/;
console.log(exactly.test('123')); // true
console.log(exactly.test('12')); // false

// {n,} - n or more times
const nOrMore = /\d{3,}/;
console.log(nOrMore.test('1234')); // true

// {n,m} - between n and m times
const range = /\d{2,4}/;
console.log(range.test('12')); // true
console.log(range.test('1234')); // true
console.log(range.test('12345')); // true (matches first 4 digits)
```

---

### 8. Greedy vs Lazy Quantifiers
```javascript
const html = '<div>Hello</div><span>World</span>';

// Greedy - matches as much as possible
const greedy = /<.*>/;
console.log(html.match(greedy)[0]); // '<div>Hello</div><span>World</span>'

// Lazy - matches as little as possible
const lazy = /<.*?>/;
console.log(html.match(lazy)[0]); // '<div>'
```

---

## ⚓ Anchors and Boundaries

### 9. Position Anchors
```javascript
// ^ - start of string/line
const startPattern = /^Hello/;
console.log(startPattern.test('Hello World')); // true
console.log(startPattern.test('Say Hello')); // false

// $ - end of string/line
const endPattern = /World$/;
console.log(endPattern.test('Hello World')); // true
console.log(endPattern.test('World Hello')); // false

// Exact match
const exactMatch = /^Hello World$/;
console.log(exactMatch.test('Hello World')); // true
console.log(exactMatch.test('Hello World!')); // false
```

---

### 10. Word Boundaries
```javascript
// \b - word boundary
const wordBoundary = /\bcat\b/;
console.log(wordBoundary.test('cat')); // true
console.log(wordBoundary.test('the cat runs')); // true
console.log(wordBoundary.test('category')); // false

// \B - non-word boundary
const nonWordBoundary = /\Bcat\B/;
console.log(nonWordBoundary.test('concatenate')); // true
```

---

## 👥 Groups and Capturing

### 11. Basic Grouping
```javascript
// Parentheses create groups
const groupPattern = /(cat|dog)/;
console.log(groupPattern.test('I have a cat')); // true
console.log(groupPattern.test('I have a dog')); // true

// Groups with quantifiers
const repeatedGroup = /(ha)+/;
console.log(repeatedGroup.test('hahaha')); // true
```

---

### 12. Capturing Groups
```javascript
const namePattern = /(\w+)\s+(\w+)/;
const match = 'John Doe'.match(namePattern);
console.log(match[0]); // 'John Doe' (full match)
console.log(match[1]); // 'John' (first group)
console.log(match[2]); // 'Doe' (second group)

// Named capturing groups
const namedPattern = /(?<first>\w+)\s+(?<last>\w+)/;
const namedMatch = 'John Doe'.match(namedPattern);
console.log(namedMatch.groups.first); // 'John'
console.log(namedMatch.groups.last); // 'Doe'
```

---

### 13. Non-Capturing Groups
```javascript
// (?:...) - non-capturing group
const nonCapturing = /(?:cat|dog)s?/;
const result = 'cats'.match(nonCapturing);
console.log(result[0]); // 'cats'
console.log(result[1]); // undefined (no capture group)
```

---

## 👀 Lookaheads and Lookbehinds

### 14. Positive and Negative Lookahead
```javascript
// (?=...) - positive lookahead
const positiveLookahead = /\d+(?=px)/;
console.log('100px'.match(positiveLookahead)[0]); // '100'
console.log('100em'.match(positiveLookahead)); // null

// (?!...) - negative lookahead
const negativeLookahead = /\d+(?!px)/;
console.log('100em'.match(negativeLookahead)[0]); // '100'
console.log('100px'.match(negativeLookahead)); // null
```

---

### 15. Positive and Negative Lookbehind
```javascript
// (?<=...) - positive lookbehind
const positiveLookbehind = /(?<=\$)\d+/;
console.log('$100'.match(positiveLookbehind)[0]); // '100'

// (?<!...) - negative lookbehind
const negativeLookbehind = /(?<!\$)\d+/;
console.log('€100'.match(negativeLookbehind)[0]); // '100'
```

---

## 🔧 String Methods with Regex

### 16. test() Method
```javascript
const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
console.log(emailPattern.test('user@example.com')); // true
console.log(emailPattern.test('invalid-email')); // false
```

---

### 17. match() Method
```javascript
const text = 'The quick brown fox jumps over the lazy dog';
const wordPattern = /\b\w{4}\b/g;
console.log(text.match(wordPattern)); // ['over', 'lazy']

// Without global flag, returns match object
const singleMatch = text.match(/\b\w{5}\b/);
console.log(singleMatch[0]); // 'quick'
console.log(singleMatch.index); // 4
```

---

### 18. search() Method
```javascript
const text = 'Hello World';
console.log(text.search(/world/i)); // 6
console.log(text.search(/xyz/)); // -1
```

---

### 19. replace() Method
```javascript
const text = 'Hello World, Hello Universe';

// Simple replacement
console.log(text.replace(/Hello/g, 'Hi')); // 'Hi World, Hi Universe'

// Using capture groups
const pattern = /(\w+)\s+(\w+)/;
console.log('John Doe'.replace(pattern, '$2, $1')); // 'Doe, John'

// Using function replacement
const result = text.replace(/Hello/g, (match, offset) => {
    return `Hi (at position ${offset})`;
});
console.log(result); // 'Hi (at position 0) World, Hi (at position 13) Universe'
```

---

### 20. split() Method
```javascript
const csv = 'apple,banana,cherry';
console.log(csv.split(/,/)); // ['apple', 'banana', 'cherry']

// Split on multiple delimiters
const text = 'apple,banana;cherry:date';
console.log(text.split(/[,:;]/)); // ['apple', 'banana', 'cherry', 'date']
```

---

### 21. matchAll() Method
```javascript
const text = 'cat bat rat';
const pattern = /(\w)at/g;

for (const match of text.matchAll(pattern)) {
    console.log(`Found ${match[0]} with first letter ${match[1]}`);
}
// Found cat with first letter c
// Found bat with first letter b
// Found rat with first letter r
```

---

## 💡 Real-World Examples

### Example 1: Email Validation
```javascript
const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;

function validateEmail(email) {
    return emailRegex.test(email);
}

console.log(validateEmail('user@example.com')); // true
console.log(validateEmail('invalid.email')); // false
```

### Example 2: Phone Number Validation
```javascript
const phoneRegex = /^\(?(\d{3})\)?[-.\s]?(\d{3})[-.\s]?(\d{4})$/;

function formatPhone(phone) {
    const match = phone.match(phoneRegex);
    if (match) {
        return `(${match[1]}) ${match[2]}-${match[3]}`;
    }
    return null;
}

console.log(formatPhone('1234567890')); // '(123) 456-7890'
console.log(formatPhone('123-456-7890')); // '(123) 456-7890'
```

### Example 3: URL Validation
```javascript
const urlRegex = /^https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)$/;

function validateUrl(url) {
    return urlRegex.test(url);
}

console.log(validateUrl('https://www.example.com')); // true
console.log(validateUrl('not-a-url')); // false
```

### Example 4: Password Strength
```javascript
// At least 8 characters, one uppercase, one lowercase, one digit
const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d@$!%*?&]{8,}$/;

function validatePassword(password) {
    return passwordRegex.test(password);
}

console.log(validatePassword('Password123')); // true
console.log(validatePassword('password')); // false
```

### Example 5: Extract Data from Text
```javascript
// Extract all hashtags from text
const hashtagRegex = /#\w+/g;
const text = 'Check out #javascript and #typescript for web development!';
console.log(text.match(hashtagRegex)); // ['#javascript', '#typescript']

// Extract URLs from text
const urlRegex = /https?:\/\/[^\s]+/g;
const content = 'Visit https://example.com or https://github.com for more info';
console.log(content.match(urlRegex)); // ['https://example.com', 'https://github.com']
```

---

## 🎤 Interview Questions

### Q1: What's the difference between `match()` and `exec()`?

**Answer:**

`match()` is a string method, `exec()` is a RegExp method. With global flag, `match()` returns all matches at once, while `exec()` returns one match per call and maintains state.

```javascript
const str = "cat bat rat";
const regex = /\w+at/g;

// match() returns all at once
console.log(str.match(regex)); // ['cat', 'bat', 'rat']

// exec() returns one by one
console.log(regex.exec(str)); // ['cat', ...]
console.log(regex.exec(str)); // ['bat', ...]
```

---

### Q2: How do you make a quantifier non-greedy (lazy)?

**Answer:**

Add a `?` after the quantifier: `*?`, `+?`, `{n,m}?`

```javascript
const html = '<div>Hello</div>';
console.log(html.match(/<.*>/)[0]);  // '<div>Hello</div>' (greedy)
console.log(html.match(/<.*?>/)[0]); // '<div>' (lazy)
```

---

### Q3: What's the difference between `\b` and `\B`?

**Answer:**

`\b` matches word boundaries (between word and non-word characters), `\B` matches non-word boundaries.

```javascript
console.log(/\bcat\b/.test('category')); // false
console.log(/\Bcat\B/.test('concatenate')); // true
```

---

### Q4: How do you escape special characters in regex?

**Answer:**

Use backslash `\` or create a helper function:

```javascript
function escapeRegex(string) {
    return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
}

const literal = escapeRegex('What is 2 + 2?');
const pattern = new RegExp(literal);
```

---

### Q5: When should you use capturing vs non-capturing groups?

**Answer:**

Use capturing `()` when you need to extract parts of the match. Use non-capturing `(?:)` for grouping without memory overhead.

```javascript
// Capturing - extracts parts
const match = 'John Doe'.match(/(\w+) (\w+)/);
console.log(match[1], match[2]); // 'John' 'Doe'

// Non-capturing - just grouping
const pattern = /(?:Mr|Ms|Dr)\. \w+/;
```

---

## 📚 Summary

**Key Takeaways:**
- Regex literals (`/pattern/flags`) vs RegExp constructor for dynamic patterns
- Flags control matching behavior: `g` (global), `i` (case-insensitive), `m` (multiline)
- Character classes `[abc]`, predefined classes `\d \w \s`, and quantifiers `* + ? {n,m}`
- Anchors `^ $` and boundaries `\b` control position matching
- Groups `()` capture, lookaheads `(?=)` assert without consuming
- String methods: `test()`, `match()`, `replace()`, `split()`, `matchAll()`
- Always consider performance and escape user input when building dynamic patterns

---

**Next Topic:** [17. JSON & Local Storage](./17-json-storage.md)