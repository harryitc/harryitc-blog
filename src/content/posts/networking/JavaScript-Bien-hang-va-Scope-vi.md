---
title: Biến, hằng và phạm vi (Scope) trong JavaScript - let, const, var
published: 2025-10-17
tags:
  - JavaScript
  - Variables
  - Scope
  - Programming Fundamentals
  - ES6
toc: true
lang: vi
abbrlink: javascript-bien-hang-scope
description: Hiểu sâu về cách khai báo biến với let, const, var trong JavaScript. Tìm hiểu lexical scope, block scope, function scope và hiện tượng hoisting với ví dụ trực quan.
image:
    url: '/images/deep-to-learn-variable.webp'
    alt: 'JavaScript Variables and Scope'
---

Biến (variables) là nền tảng của mọi ngôn ngữ lập trình, và JavaScript có những đặc điểm rất độc đáo về cách quản lý biến và phạm vi (scope). Trong bài viết này, chúng ta sẽ tìm hiểu sâu về `var`, `let`, `const` và những khái niệm quan trọng như hoisting, scope chain.

## Các cách khai báo biến trong JavaScript

JavaScript cung cấp 3 từ khóa để khai báo biến: `var`, `let`, và `const`. Mỗi cách có những đặc điểm riêng biệt.

### var - Cách khai báo "cổ điển"

```javascript
// Khai báo với var
var name = "JavaScript";
var age = 28;
var isAwesome; // undefined

console.log(name); // "JavaScript"
console.log(age);  // 28
console.log(isAwesome); // undefined

// Có thể khai báo lại
var name = "JS";
console.log(name); // "JS"
```

### let - Khai báo hiện đại (ES6+)

```javascript
// Khai báo với let
let language = "JavaScript";
let version = 2023;

console.log(language); // "JavaScript"

// Có thể thay đổi giá trị
language = "TypeScript";
console.log(language); // "TypeScript"

// Không thể khai báo lại trong cùng scope
// let language = "Python"; // ❌ SyntaxError
```

### const - Hằng số

```javascript
// Khai báo hằng số
const PI = 3.14159;
const COMPANY_NAME = "TechCorp";

console.log(PI); // 3.14159

// Không thể thay đổi giá trị
// PI = 3.14; // ❌ TypeError: Assignment to constant variable

// Phải khởi tạo giá trị ngay khi khai báo
// const SECRET; // ❌ SyntaxError: Missing initializer
```

> [!IMPORTANT]
> `const` không có nghĩa là giá trị không thể thay đổi, mà là tham chiếu (reference) không thể thay đổi!

```javascript
// const với object và array
const user = { name: "John", age: 25 };
const fruits = ["apple", "banana"];

// Có thể thay đổi thuộc tính của object
user.age = 26; // ✅ OK
user.city = "Hanoi"; // ✅ OK

// Có thể thay đổi nội dung array
fruits.push("orange"); // ✅ OK
fruits[0] = "grape"; // ✅ OK

console.log(user); // { name: "John", age: 26, city: "Hanoi" }
console.log(fruits); // ["grape", "banana", "orange"]

// Nhưng không thể gán lại object/array mới
// user = { name: "Jane" }; // ❌ TypeError
// fruits = []; // ❌ TypeError
```

## Phạm vi (Scope) trong JavaScript

Scope xác định nơi biến có thể được truy cập trong code. JavaScript có những loại scope sau:

### 1. Global Scope

Biến được khai báo ngoài tất cả functions hoặc blocks.

```javascript
// Global scope
var globalVar = "Tôi là global với var";
let globalLet = "Tôi là global với let";
const globalConst = "Tôi là global với const";

function showGlobals() {
    console.log(globalVar);   // ✅ Truy cập được
    console.log(globalLet);   // ✅ Truy cập được
    console.log(globalConst); // ✅ Truy cập được
}

showGlobals();

// Có thể truy cập từ bất kỳ đâu
console.log(window.globalVar); // "Tôi là global với var" (chỉ trong browser)
console.log(window.globalLet); // undefined (let/const không tạo thuộc tính window)
```

### 2. Function Scope

Biến khai báo với `var` trong function chỉ có thể truy cập trong function đó.

```javascript
function calculateArea(radius) {
    var pi = 3.14159; // Function scoped
    var area = pi * radius * radius;
    
    console.log("Trong function:", pi); // ✅ OK
    return area;
}

calculateArea(5);

// console.log(pi); // ❌ ReferenceError: pi is not defined
// console.log(area); // ❌ ReferenceError: area is not defined
```

### 3. Block Scope (ES6+)

Biến khai báo với `let` và `const` chỉ có thể truy cập trong block `{}` chứa nó.

```javascript
function demonstrateBlockScope() {
    if (true) {
        var varVariable = "var có function scope";
        let letVariable = "let có block scope";
        const constVariable = "const có block scope";
        
        console.log(varVariable);   // ✅ OK
        console.log(letVariable);   // ✅ OK
        console.log(constVariable); // ✅ OK
    }
    
    console.log(varVariable);   // ✅ OK - var có function scope
    // console.log(letVariable);   // ❌ ReferenceError
    // console.log(constVariable); // ❌ ReferenceError
}

demonstrateBlockScope();
```

### Ví dụ thực tế với loop

```javascript
// Vấn đề với var trong loop
console.log("=== Với var ===");
for (var i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log("var i:", i); // In ra: 3, 3, 3
    }, 100);
}

// Giải pháp với let
console.log("=== Với let ===");
for (let j = 0; j < 3; j++) {
    setTimeout(() => {
        console.log("let j:", j); // In ra: 0, 1, 2
    }, 200);
}

// Tại sao lại khác nhau?
// - var có function scope, nên tất cả callback đều tham chiếu đến cùng một biến i
// - let có block scope, mỗi lần lặp tạo ra một biến j mới
```

## Lexical Scope (Static Scope)

JavaScript sử dụng **lexical scope**, có nghĩa là scope được xác định bởi vị trí khai báo biến trong code, không phải nơi hàm được gọi.

```javascript
const globalMessage = "Tôi là global";

function outerFunction() {
    const outerMessage = "Tôi ở outer function";
    
    function innerFunction() {
        const innerMessage = "Tôi ở inner function";
        
        // Có thể truy cập tất cả biến từ scope bên ngoài
        console.log(innerMessage);  // "Tôi ở inner function"
        console.log(outerMessage);  // "Tôi ở outer function"
        console.log(globalMessage); // "Tôi là global"
    }
    
    // Không thể truy cập biến của inner function
    // console.log(innerMessage); // ❌ ReferenceError
    
    return innerFunction;
}

const myFunction = outerFunction();
myFunction(); // In ra tất cả các message
```

### Scope Chain

JavaScript tìm kiếm biến theo **scope chain** - từ trong ra ngoài:

```javascript
let level1 = "Global";

function first() {
    let level2 = "First function";
    
    function second() {
        let level3 = "Second function";
        
        function third() {
            let level4 = "Third function";
            
            // Tìm kiếm theo thứ tự: level4 -> level3 -> level2 -> level1
            console.log(level4); // "Third function"
            console.log(level3); // "Second function"
            console.log(level2); // "First function"
            console.log(level1); // "Global"
            
            // Nếu biến không tồn tại
            // console.log(nonExistent); // ❌ ReferenceError
        }
        
        third();
    }
    
    second();
}

first();
```

## Hoisting - "Kéo lên đầu"

Hoisting là hiện tượng JavaScript "kéo" khai báo biến và hàm lên đầu scope của chúng.

### Hoisting với var

```javascript
// Code viết
console.log(message); // undefined (không phải lỗi!)
var message = "Hello World";
console.log(message); // "Hello World"

// JavaScript thực tế "hiểu" như này:
// var message; // undefined
// console.log(message); // undefined
// message = "Hello World";
// console.log(message); // "Hello World"
```

### Hoisting với let và const

```javascript
// Temporal Dead Zone
console.log(letVar); // ❌ ReferenceError: Cannot access 'letVar' before initialization
let letVar = "Let variable";

console.log(constVar); // ❌ ReferenceError: Cannot access 'constVar' before initialization
const constVar = "Const variable";

// let và const cũng bị hoisted, nhưng không thể truy cập trước khi khởi tạo
```

### Temporal Dead Zone (TDZ)

```javascript
function demonstrateTDZ() {
    // TDZ starts here for 'temporalVar'
    
    console.log(typeof temporalVar); // ❌ ReferenceError
    
    let temporalVar = "Now I exist!";
    // TDZ ends here for 'temporalVar'
    
    console.log(temporalVar); // ✅ "Now I exist!"
}

demonstrateTDZ();
```

### Hoisting với function

```javascript
// Function declarations được hoisted hoàn toàn
console.log(add(2, 3)); // ✅ 5

function add(a, b) {
    return a + b;
}

// Function expressions không được hoisted
console.log(multiply(2, 3)); // ❌ TypeError: multiply is not a function

var multiply = function(a, b) {
    return a * b;
};

// Arrow functions cũng không được hoisted
console.log(divide(6, 2)); // ❌ ReferenceError: Cannot access 'divide' before initialization

const divide = (a, b) => a / b;
```

## So sánh chi tiết var vs let vs const

| Đặc điểm | var | let | const |
|----------|-----|-----|-------|
| **Scope** | Function/Global | Block | Block |
| **Hoisting** | Yes (undefined) | Yes (TDZ) | Yes (TDZ) |
| **Re-declaration** | ✅ Allowed | ❌ Not allowed | ❌ Not allowed |
| **Re-assignment** | ✅ Allowed | ✅ Allowed | ❌ Not allowed |
| **Initialization** | Optional | Optional | Required |
| **TDZ** | ❌ No | ✅ Yes | ✅ Yes |

### Ví dụ thực tế

```javascript
// ❌ Những gì KHÔNG nên làm với var
for (var i = 0; i < 3; i++) {
    // var tạo ra global pollution
}
console.log(i); // 3 - vẫn truy cập được!

// ✅ Cách ĐÚNG với let
for (let j = 0; j < 3; j++) {
    // let có block scope
}
// console.log(j); // ❌ ReferenceError - không truy cập được

// ✅ Sử dụng const cho giá trị không đổi
const API_URL = "https://api.example.com";
const MAX_RETRIES = 3;

// ✅ Sử dụng const cho object/array
const config = {
    debug: true,
    apiUrl: API_URL
};

config.debug = false; // ✅ OK - thay đổi thuộc tính
// config = {}; // ❌ Error - không thể gán lại
```

## Best Practices - Thực hành tốt

### 1. Ưu tiên const > let > var

```javascript
// ✅ Ưu tiên const
const userName = "john_doe";
const userRoles = ["admin", "user"];

// ✅ Dùng let khi cần thay đổi giá trị
let currentPage = 1;
currentPage++; // OK

// ❌ Tránh var trong code hiện đại
// var oldStyle = "deprecated";
```

### 2. Khai báo biến gần nơi sử dụng

```javascript
// ❌ Khai báo xa
function processUsers() {
    let users, filteredUsers, result;
    
    // ... 50 dòng code khác
    
    users = fetchUsers();
    filteredUsers = users.filter(u => u.active);
    result = filteredUsers.map(u => u.name);
    
    return result;
}

// ✅ Khai báo gần nơi sử dụng
function processUsers() {
    // ... các logic khác
    
    const users = fetchUsers();
    const filteredUsers = users.filter(u => u.active);
    const result = filteredUsers.map(u => u.name);
    
    return result;
}
```

### 3. Sử dụng meaningful names

```javascript
// ❌ Tên biến không rõ ràng
const d = new Date();
const u = users.filter(x => x.a);

// ✅ Tên biến có ý nghĩa
const currentDate = new Date();
const activeUsers = users.filter(user => user.isActive);
```

## Debugging Scope Issues

### Tool 1: Console.log scope chain

```javascript
function debugScope() {
    const level1 = "Function scope";
    
    if (true) {
        const level2 = "Block scope";
        
        // Debug current scope
        console.log("Current scope variables:");
        console.log({ level1, level2 });
        
        // Check global scope
        console.log("Global scope:", typeof window !== 'undefined' ? window : global);
    }
}

debugScope();
```

### Tool 2: Sử dụng debugger

```javascript
function complexScope() {
    let outerVar = "outer";
    
    function inner() {
        let innerVar = "inner";
        
        debugger; // Pause tại đây để inspect scope
        
        return outerVar + innerVar;
    }
    
    return inner();
}

complexScope();
```

## Real-world Examples

### Example 1: Module Pattern với IIFE

```javascript
// Sử dụng scope để tạo private variables
const UserModule = (function() {
    // Private variables
    let users = [];
    let currentId = 0;
    
    // Private functions
    function generateId() {
        return ++currentId;
    }
    
    // Public API
    return {
        addUser(name, email) {
            const user = {
                id: generateId(),
                name,
                email,
                createdAt: new Date()
            };
            users.push(user);
            return user;
        },
        
        getUsers() {
            return [...users]; // Return copy
        },
        
        getUserById(id) {
            return users.find(user => user.id === id);
        }
    };
})();

// Sử dụng
const user1 = UserModule.addUser("John", "john@example.com");
console.log(UserModule.getUsers()); // [{ id: 1, name: "John", ... }]

// Không thể truy cập private variables
// console.log(users); // ❌ ReferenceError
// console.log(currentId); // ❌ ReferenceError
```

### Example 2: Event Handlers với proper scope

```javascript
// ❌ Vấn đề với var trong event handlers
function setupButtonsWithVar() {
    for (var i = 0; i < 3; i++) {
        const button = document.createElement('button');
        button.textContent = `Button ${i}`;
        
        button.addEventListener('click', function() {
            alert(`Button ${i} clicked`); // Luôn alert "Button 3 clicked"
        });
        
        document.body.appendChild(button);
    }
}

// ✅ Giải pháp với let
function setupButtonsWithLet() {
    for (let i = 0; i < 3; i++) {
        const button = document.createElement('button');
        button.textContent = `Button ${i}`;
        
        button.addEventListener('click', function() {
            alert(`Button ${i} clicked`); // Đúng button number
        });
        
        document.body.appendChild(button);
    }
}

// ✅ Giải pháp với closure (cách cũ)
function setupButtonsWithClosure() {
    for (var i = 0; i < 3; i++) {
        (function(index) {
            const button = document.createElement('button');
            button.textContent = `Button ${index}`;
            
            button.addEventListener('click', function() {
                alert(`Button ${index} clicked`);
            });
            
            document.body.appendChild(button);
        })(i);
    }
}
```

## Performance Considerations

### 1. Avoid unnecessary global variables

```javascript
// ❌ Global pollution
var cache = {};
var utils = {};
var helpers = {};

// ✅ Namespace pattern
const MyApp = {
    cache: {},
    utils: {},
    helpers: {}
};

// ✅ Module pattern
const AppModule = (function() {
    const cache = {};
    const utils = {};
    
    return {
        // Expose only what's needed
        clearCache() {
            Object.keys(cache).forEach(key => delete cache[key]);
        }
    };
})();
```

### 2. Memory management với closures

```javascript
// ❌ Memory leak potential
function createHandler() {
    const largeData = new Array(1000000).fill('data');
    
    return function() {
        console.log('Handler called');
        // largeData vẫn được giữ trong memory vì closure
    };
}

// ✅ Better memory management
function createHandler() {
    const largeData = new Array(1000000).fill('data');
    
    // Process data immediately
    const processedResult = largeData.reduce((acc, item) => acc + item.length, 0);
    
    return function() {
        console.log('Result:', processedResult);
        // largeData có thể được garbage collected
    };
}
```

## Kết luận

Hiểu rõ về biến và scope trong JavaScript là cực kỳ quan trọng để viết code chất lượng cao:

### 🎯 **Key Takeaways**

1. **Ưu tiên `const` > `let` > `var`** - Sử dụng theo thứ tự này
2. **Block scope tốt hơn function scope** - `let`/`const` an toàn hơn `var`
3. **Hoisting exists but avoid relying on it** - Khai báo biến trước khi sử dụng
4. **Lexical scope is powerful** - Sử dụng để tạo closures và modules
5. **Temporal Dead Zone protects you** - `let`/`const` giúp tránh bugs

### ⚡ **Best Practices**

- Luôn khai báo biến trước khi sử dụng
- Sử dụng tên biến có ý nghĩa
- Khai báo biến gần nơi sử dụng
- Tránh global variables
- Hiểu scope chain để debug hiệu quả

> [!TIP]
> Scope và variable declarations là nền tảng để hiểu các khái niệm nâng cao như closures, modules, và async programming trong JavaScript!

Nắm vững những khái niệm này sẽ giúp bạn viết JavaScript code an toàn, hiệu quả và dễ maintain hơn rất nhiều!

## Tài liệu tham khảo

- [MDN - var](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var)
- [MDN - let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
- [MDN - const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
- [Understanding Hoisting in JavaScript](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)