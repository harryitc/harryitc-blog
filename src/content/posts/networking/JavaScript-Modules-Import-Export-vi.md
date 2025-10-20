---
title: Module hóa trong JavaScript - import/export, CommonJS vs ESModule
published: 2025-10-18
tags:
  - JavaScript
  - Modules
  - ES6
  - CommonJS
  - ESModule
  - Webpack
  - Vite
toc: true
lang: vi
abbrlink: javascript-modules-import-export
description: Hiểu rõ cơ chế module trong JavaScript, sự khác biệt giữa CommonJS và ES Modules, tree-shaking và cách tổ chức project hiện đại.
image:
    url: '/images/commonjs-esmodules.png'
    alt: 'CommonJS vs ES Modules'
---

Module hóa giúp chia code thành các file nhỏ, dễ quản lý và tái sử dụng. Hãy cùng tìm hiểu 2 hệ thống module phổ biến trong JavaScript và cách áp dụng vào project thực tế.

## Tại sao cần Modules? 🤔

**Trước khi có modules** - Tất cả code chung 1 global scope:

```html
<!-- ❌ Cách cũ - Global scope pollution -->
<script src="utils.js"></script>
<script src="user.js"></script>
<script src="app.js"></script>

<script>
// utils.js
function formatDate(date) { /* ... */ }

// user.js  
function formatDate(date) { /* ... */ } // ❌ Conflict!

// app.js
formatDate(); // Gọi function nào đây? 🤷
</script>
```

**Với modules** - Mỗi file có scope riêng:

```javascript
// ✅ utils.js - Module riêng
export function formatDate(date) { /* ... */ }

// ✅ user.js - Module riêng
export function formatDate(date) { /* ... */ }

// ✅ app.js - Import rõ ràng
import { formatDate as formatDateUtil } from './utils.js';
import { formatDate as formatDateUser } from './user.js';
```

## CommonJS (Node.js truyền thống) 📦

Hệ thống module của Node.js, dùng `require()` và `module.exports`.

### Export trong CommonJS

```javascript
// utils.js
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

// Cách 1: Export từng cái
exports.add = add;
exports.subtract = subtract;

// Cách 2: Export một object
module.exports = {
    add,
    subtract
};

// Cách 3: Export default
module.exports = add;
```

### Import trong CommonJS

```javascript
// app.js
const utils = require('./utils');
console.log(utils.add(2, 3)); // 5

// Destructuring
const { add, subtract } = require('./utils');
console.log(subtract(5, 2)); // 3

// Import default
const add = require('./utils');
console.log(add(2, 3)); // 5
```

### Đặc điểm CommonJS

- ✅ **Synchronous**: Load module đồng bộ (phù hợp server)
- ✅ **Dynamic**: Có thể `require()` trong if/else
- ❌ **Không tree-shaking**: Bundle cả module dù không dùng hết
- ❌ **Không chạy được trên browser** (cần bundler)

```javascript
// Dynamic import
if (condition) {
    const utils = require('./utils'); // ✅ OK
}
```

## ES Modules (ESM) - Modern JavaScript 🚀

Chuẩn module chính thức của JavaScript (ES6+), dùng `import` và `export`.

### Named Export/Import

```javascript
// math.js - Export nhiều thứ
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export const PI = 3.14159;

// Hoặc export sau khi định nghĩa
function multiply(a, b) { return a * b; }
function divide(a, b) { return a / b; }

export { multiply, divide };
```

```javascript
// app.js - Import
import { add, subtract, PI } from './math.js';

console.log(add(2, 3));    // 5
console.log(PI);           // 3.14159

// Import và đổi tên
import { add as sum } from './math.js';
console.log(sum(2, 3));    // 5

// Import tất cả
import * as Math from './math.js';
console.log(Math.add(2, 3));  // 5
console.log(Math.PI);         // 3.14159
```

### Default Export/Import

Mỗi module chỉ có **1 default export**:

```javascript
// user.js - Default export
export default class User {
    constructor(name) {
        this.name = name;
    }
}

// Hoặc
class User { /* ... */ }
export default User;

// Hoặc inline
export default function() { /* ... */ }
```

```javascript
// app.js - Import default (tên tùy chọn)
import User from './user.js';
import MyUser from './user.js'; // ✅ Tên khác cũng OK

const user = new User('Harry');
```

### Mix Named và Default Export

```javascript
// api.js
export default class API { /* ... */ }
export const BASE_URL = 'https://api.example.com';
export function request() { /* ... */ }
```

```javascript
// app.js
import API, { BASE_URL, request } from './api.js';
```

### Đặc điểm ES Modules

- ✅ **Static**: Import phải ở top-level (tree-shaking tốt)
- ✅ **Asynchronous**: Phù hợp browser
- ✅ **Tree-shaking**: Chỉ bundle code được dùng
- ✅ **Strict mode**: Tự động chạy trong strict mode
- ✅ **Live binding**: Export là reference, không phải copy

```javascript
// ❌ Dynamic import không được (top-level)
if (condition) {
    import { add } from './math.js'; // ❌ Error!
}

// ✅ Dùng dynamic import() function
if (condition) {
    const { add } = await import('./math.js'); // ✅ OK
}
```

## CommonJS vs ES Modules 🔄

| Feature | CommonJS | ES Modules |
|---------|----------|------------|
| Syntax | `require()` / `module.exports` | `import` / `export` |
| Load | Synchronous | Asynchronous |
| Browser | ❌ (cần bundler) | ✅ Native support |
| Tree-shaking | ❌ | ✅ |
| Dynamic import | ✅ Anywhere | ✅ `import()` function |
| Default in | Node.js (< v13) | Browser, Node.js (≥ v13) |
| File extension | `.js` | `.mjs` hoặc `"type": "module"` |

### Ví dụ so sánh

**CommonJS**:

```javascript
// math.js
module.exports = {
    add: (a, b) => a + b,
    subtract: (a, b) => a - b
};

// app.js
const math = require('./math');
console.log(math.add(2, 3));
```

**ES Modules**:

```javascript
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// app.js
import { add } from './math.js';
console.log(add(2, 3));
```

## Tree-shaking: Loại bỏ code không dùng 🌳

Tree-shaking chỉ hoạt động với **ES Modules** vì cấu trúc static.

```javascript
// utils.js
export function usedFunction() { /* được dùng */ }
export function unusedFunction() { /* không dùng */ }

// app.js
import { usedFunction } from './utils.js';
usedFunction();

// ✅ Build output chỉ chứa usedFunction
// unusedFunction bị loại bỏ → Bundle nhỏ hơn
```

**CommonJS không tree-shake được**:

```javascript
// utils.js
module.exports = {
    usedFunction() { /* được dùng */ },
    unusedFunction() { /* không dùng */ }
};

// app.js
const { usedFunction } = require('./utils');
usedFunction();

// ❌ Build output vẫn chứa cả 2 functions
```

## Tổ chức Project với Modules 📁

### Cấu trúc thư mục tốt

```
src/
├── components/
│   ├── Button.js
│   ├── Card.js
│   └── index.js          # Barrel export
├── utils/
│   ├── date.js
│   ├── string.js
│   └── index.js
├── services/
│   ├── api.js
│   └── auth.js
├── config/
│   └── constants.js
└── main.js               # Entry point
```

### Barrel Export Pattern

Tập trung exports từ nhiều file:

```javascript
// components/index.js - Barrel file
export { default as Button } from './Button.js';
export { default as Card } from './Card.js';
export * from './Modal.js';

// main.js - Import gọn
import { Button, Card } from './components';
// Thay vì:
// import Button from './components/Button.js';
// import Card from './components/Card.js';
```

## Demo: Setup Project với Vite ⚡

Vite là build tool hiện đại, hỗ trợ ES Modules native.

### 1. Khởi tạo project

```bash
# Tạo project mới
npm create vite@latest my-project -- --template vanilla

cd my-project
npm install
```

### 2. Cấu trúc project

```
my-project/
├── index.html
├── package.json
├── vite.config.js
└── src/
    ├── main.js
    ├── utils/
    │   ├── math.js
    │   └── index.js
    └── components/
        └── greeting.js
```

### 3. Code ví dụ

```javascript
// src/utils/math.js
export function add(a, b) {
    return a + b;
}

export function multiply(a, b) {
    return a * b;
}

// src/utils/index.js (barrel)
export * from './math.js';

// src/components/greeting.js
export default function greeting(name) {
    return `Hello, ${name}!`;
}

// src/main.js
import greeting from './components/greeting.js';
import { add, multiply } from './utils';

console.log(greeting('Harry'));
console.log('2 + 3 =', add(2, 3));
console.log('2 * 3 =', multiply(2, 3));
```

### 4. Chạy project

```bash
# Dev server với HMR
npm run dev

# Build production (tree-shaking tự động)
npm run build

# Preview build
npm run preview
```

### 5. Vite config (optional)

```javascript
// vite.config.js
import { defineConfig } from 'vite';

export default defineConfig({
    build: {
        // Tùy chỉnh build
        minify: 'terser',
        sourcemap: true
    }
});
```

## Demo: Setup với Webpack (Alternative) 📦

```bash
# Khởi tạo project
mkdir my-webpack-project && cd my-webpack-project
npm init -y

# Install webpack
npm install --save-dev webpack webpack-cli webpack-dev-server
```

```javascript
// webpack.config.js
module.exports = {
    entry: './src/main.js',
    output: {
        filename: 'bundle.js',
        path: __dirname + '/dist'
    },
    mode: 'production' // Tree-shaking enabled
};
```

```json
// package.json
{
    "scripts": {
        "dev": "webpack serve",
        "build": "webpack"
    }
}
```

## Dynamic Import: Load khi cần 🎯

```javascript
// Lazy loading
async function loadUtils() {
    const { add, multiply } = await import('./utils/math.js');
    console.log(add(2, 3));
}

// Load khi click button
button.addEventListener('click', async () => {
    const module = await import('./heavy-module.js');
    module.default();
});

// Conditional import
if (user.isAdmin) {
    const adminPanel = await import('./admin-panel.js');
    adminPanel.init();
}
```

## Best Practices 💡

1. **Ưu tiên ES Modules** cho code mới (CommonJS chỉ dùng cho Node.js cũ)
2. **Named exports > Default exports** (dễ refactor, tree-shake tốt hơn)
3. **Barrel exports** cho nhiều components
4. **Dynamic import** cho code splitting (giảm bundle size)
5. **1 file = 1 responsibility** (dễ test, maintain)

```javascript
// ✅ Good - Named exports
export function add() { }
export function subtract() { }

// ❌ Avoid - Default export object
export default { add, subtract };
```

## Kết luận

| Aspect | CommonJS | ES Modules |
|--------|----------|------------|
| **Khi nào dùng** | Node.js legacy | Mọi project mới |
| **Performance** | Tốt (server) | Tốt (browser) |
| **Bundle size** | Lớn hơn | Nhỏ hơn (tree-shaking) |
| **Học hỏi** | Dễ hơn | Phức tạp hơn một chút |

**Khuyến nghị**:
- 🚀 **Dùng ES Modules** cho mọi project mới
- 📦 **Vite** cho setup nhanh (khuyên dùng)
- 🌳 **Tree-shaking** tự động giảm bundle size
- 🎯 **Dynamic import** cho lazy loading

Module hóa đúng cách giúp code dễ maintain, test và scale! 🎉
