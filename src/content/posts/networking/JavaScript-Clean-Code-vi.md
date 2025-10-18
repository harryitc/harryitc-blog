---
title: Clean Code trong JavaScript – Viết code dễ hiểu, dễ bảo trì
published: 2025-10-18
tags:
  - JavaScript
  - Clean Code
  - Best Practices
  - ESLint
  - Prettier
  - Code Quality
toc: true
lang: vi
abbrlink: javascript-clean-code
description: Hướng dẫn viết Clean Code JavaScript với naming conventions, function design, error handling. Kèm ESLint + Prettier config để maintain code quality tự động.
image:
    url: '/images/js.png'
    alt: 'JavaScript Clean Code'
---

Code không chỉ để máy tính hiểu mà còn để con người đọc. **Clean Code** giúp team phát triển nhanh hơn, ít bug hơn và dễ maintain hơn. Hãy cùng học cách viết JavaScript "sạch sẽ"!

## Tại sao cần Clean Code? 🤔

### Code tệ - Khó đọc, khó hiểu

```javascript
// ❌ Code tệ - Ai đọc cũng muốn khóc
function calc(a, b, c) {
    let r;
    if (c == 1) {
        r = a + b;
    } else if (c == 2) {
        r = a - b;
    } else if (c == 3) {
        r = a * b;
    } else {
        r = a / b;
    }
    return r;
}

const x = calc(10, 5, 2); // 2 là gì? 🤷‍♂️
```

### Clean Code - Tự giải thích

```javascript
// ✅ Clean Code - Đọc như văn xuôi
const OPERATIONS = {
    ADD: 'add',
    SUBTRACT: 'subtract', 
    MULTIPLY: 'multiply',
    DIVIDE: 'divide'
};

function calculate(firstNumber, secondNumber, operation) {
    const operations = {
        [OPERATIONS.ADD]: () => firstNumber + secondNumber,
        [OPERATIONS.SUBTRACT]: () => firstNumber - secondNumber,
        [OPERATIONS.MULTIPLY]: () => firstNumber * secondNumber,
        [OPERATIONS.DIVIDE]: () => firstNumber / secondNumber
    };
    
    const calculator = operations[operation];
    
    if (!calculator) {
        throw new Error(`Unsupported operation: ${operation}`);
    }
    
    return calculator();
}

const result = calculate(10, 5, OPERATIONS.SUBTRACT); // Rõ ràng! ✨
```

## 1. Naming: Đặt tên như thơ 📝

### Variables - Dùng danh từ

```javascript
// ❌ Tên tệ
const d = new Date();
const u = users.filter(x => x.active);
const temp = calculateMonthlyRevenue();

// ✅ Tên tốt - Rõ nghĩa
const currentDate = new Date();
const activeUsers = users.filter(user => user.isActive);
const monthlyRevenue = calculateMonthlyRevenue();
```

### Functions - Dùng động từ

```javascript
// ❌ Không rõ làm gì
const userData = (id) => { /* ... */ };
const validation = (email) => { /* ... */ };

// ✅ Rõ hành động
const fetchUserById = (userId) => { /* ... */ };
const validateEmailFormat = (email) => { /* ... */ };
```

### Constants - UPPER_SNAKE_CASE

```javascript
// ✅ Constants rõ ràng
const MAX_RETRY_ATTEMPTS = 3;
const API_ENDPOINTS = {
    USERS: '/api/users',
    ORDERS: '/api/orders'
};

const DEFAULT_PAGINATION = {
    PAGE_SIZE: 20,
    CURRENT_PAGE: 1
};
```

### Boolean - is/has/can/should

```javascript
// ✅ Boolean naming conventions
const isUserLoggedIn = checkAuthStatus();
const hasPermission = checkUserPermissions(user, 'write');
const canEditPost = user.role === 'admin' || post.authorId === user.id;
const shouldShowModal = !user.hasSeenWelcome;

// Usage tự nhiên
if (isUserLoggedIn && hasPermission) {
    // ...
}
```

### Searchable Names

```javascript
// ❌ Magic numbers/strings
setTimeout(doSomething, 86400000);
if (user.status === 7) { /* ... */ }

// ✅ Named constants
const MILLISECONDS_IN_DAY = 24 * 60 * 60 * 1000;
const USER_STATUS = {
    ACTIVE: 7,
    INACTIVE: 2,
    PENDING: 1
};

setTimeout(doSomething, MILLISECONDS_IN_DAY);
if (user.status === USER_STATUS.ACTIVE) { /* ... */ }
```

## 2. Functions: Nhỏ, chuyên biệt, thuần khiết 🎯

### Single Responsibility Principle

```javascript
// ❌ Function làm quá nhiều việc
function processUser(userData) {
    // Validate
    if (!userData.email || !userData.name) {
        throw new Error('Invalid data');
    }
    
    // Transform
    userData.email = userData.email.toLowerCase();
    userData.name = userData.name.trim();
    
    // Save to database
    database.save(userData);
    
    // Send email
    emailService.sendWelcome(userData.email);
    
    // Log
    logger.info(`User ${userData.name} created`);
    
    return userData;
}
```

```javascript
// ✅ Tách thành functions nhỏ
function validateUserData(userData) {
    if (!userData.email || !userData.name) {
        throw new Error('Email and name are required');
    }
}

function normalizeUserData(userData) {
    return {
        ...userData,
        email: userData.email.toLowerCase().trim(),
        name: userData.name.trim()
    };
}

async function createUser(userData) {
    validateUserData(userData);
    const normalizedData = normalizeUserData(userData);
    
    const savedUser = await database.save(normalizedData);
    
    // Side effects riêng biệt
    await emailService.sendWelcome(savedUser.email);
    logger.info(`User ${savedUser.name} created`);
    
    return savedUser;
}
```

### Pure Functions - Tránh Side Effects

```javascript
// ❌ Impure - Side effects
let total = 0;
function addToTotal(amount) {
    total += amount; // Modify global state
    console.log(`Added ${amount}`); // Side effect
    return total;
}

// ✅ Pure function - Predictable
function add(a, b) {
    return a + b; // Chỉ return, không side effect
}

function calculateTotal(items) {
    return items.reduce((sum, item) => sum + item.price, 0);
}

// Side effects tách riêng
function logTransaction(amount, newTotal) {
    console.log(`Added ${amount}. New total: ${newTotal}`);
}
```

### Early Returns - Giảm nesting

```javascript
// ❌ Nhiều nesting
function processOrder(order) {
    if (order) {
        if (order.items && order.items.length > 0) {
            if (order.user && order.user.isActive) {
                if (order.paymentMethod) {
                    // Process logic here...
                    return processPayment(order);
                } else {
                    throw new Error('Payment method required');
                }
            } else {
                throw new Error('User not active');
            }
        } else {
            throw new Error('Order has no items');
        }
    } else {
        throw new Error('Order not found');
    }
}
```

```javascript
// ✅ Early returns - Flat structure
function processOrder(order) {
    if (!order) {
        throw new Error('Order not found');
    }
    
    if (!order.items?.length) {
        throw new Error('Order has no items');
    }
    
    if (!order.user?.isActive) {
        throw new Error('User not active');
    }
    
    if (!order.paymentMethod) {
        throw new Error('Payment method required');
    }
    
    return processPayment(order);
}
```

### Function Parameters - Ít hơn 3 tham số

```javascript
// ❌ Quá nhiều parameters
function createUser(name, email, age, address, phone, role, department, startDate) {
    // Khó nhớ thứ tự, dễ nhầm lẫn
}

// ✅ Dùng object parameter
function createUser({ name, email, age, address, phone, role, department, startDate }) {
    // Rõ ràng, không cần nhớ thứ tự
    return {
        name,
        email,
        age,
        contact: { address, phone },
        work: { role, department, startDate }
    };
}

// Usage
const newUser = createUser({
    name: 'Harry',
    email: 'harry@example.com',
    role: 'Developer',
    startDate: new Date()
    // Có thể bỏ qua optional params
});
```

## 3. Error Handling: Rõ ràng và graceful 🛡️

### Descriptive Error Messages

```javascript
// ❌ Error messages tệ
function divide(a, b) {
    if (b === 0) throw new Error('Error'); // Quá chung chung
    return a / b;
}

// ✅ Descriptive errors
function divide(dividend, divisor) {
    if (typeof dividend !== 'number' || typeof divisor !== 'number') {
        throw new TypeError('Both arguments must be numbers');
    }
    
    if (divisor === 0) {
        throw new RangeError('Cannot divide by zero');
    }
    
    return dividend / divisor;
}
```

### Custom Error Classes

```javascript
// ✅ Custom errors cho từng case
class ValidationError extends Error {
    constructor(field, value, rule) {
        super(`Validation failed for field '${field}': ${rule}`);
        this.name = 'ValidationError';
        this.field = field;
        this.value = value;
        this.rule = rule;
    }
}

class NetworkError extends Error {
    constructor(url, status) {
        super(`Network request failed: ${status} for ${url}`);
        this.name = 'NetworkError';
        this.url = url;
        this.status = status;
    }
}

// Usage
function validateEmail(email) {
    if (!email.includes('@')) {
        throw new ValidationError('email', email, 'must contain @ symbol');
    }
}
```

### Error Boundaries

```javascript
// ✅ Wrapper function xử lý errors
async function safeApiCall(apiFunction, ...args) {
    try {
        const result = await apiFunction(...args);
        return { success: true, data: result };
    } catch (error) {
        console.error('API call failed:', error);
        return { 
            success: false, 
            error: error.message,
            code: error.code 
        };
    }
}

// Usage
const result = await safeApiCall(fetchUser, userId);
if (result.success) {
    console.log('User data:', result.data);
} else {
    showErrorMessage(result.error);
}
```

## 4. Comments: Giải thích WHY, không phải WHAT 💭

```javascript
// ❌ Comments tệ - Lặp lại code
function calculatePrice(quantity, unitPrice) {
    // Multiply quantity by unit price
    const total = quantity * unitPrice;
    
    // Apply 10% discount if quantity > 100
    if (quantity > 100) {
        return total * 0.9; // Multiply by 0.9 to get 90%
    }
    
    return total;
}

// ✅ Comments tốt - Giải thích business logic
function calculatePrice(quantity, unitPrice) {
    const total = quantity * unitPrice;
    
    // Business rule: Bulk orders (>100 items) get 10% discount
    // to encourage larger purchases and reduce shipping costs
    if (quantity > BULK_ORDER_THRESHOLD) {
        return applyBulkDiscount(total);
    }
    
    return total;
}

// ✅ Tốt nhất - Code tự giải thích
const BULK_ORDER_THRESHOLD = 100;
const BULK_DISCOUNT_RATE = 0.1;

function calculatePrice(quantity, unitPrice) {
    const subtotal = quantity * unitPrice;
    return isBulkOrder(quantity) 
        ? applyBulkDiscount(subtotal)
        : subtotal;
}

function isBulkOrder(quantity) {
    return quantity > BULK_ORDER_THRESHOLD;
}

function applyBulkDiscount(amount) {
    return amount * (1 - BULK_DISCOUNT_RATE);
}
```

### JSDoc cho APIs

```javascript
/**
 * Fetches user data from the API with caching support
 * 
 * @param {string} userId - The unique identifier for the user
 * @param {Object} options - Configuration options
 * @param {boolean} [options.useCache=true] - Whether to use cached data
 * @param {number} [options.timeout=5000] - Request timeout in milliseconds
 * @returns {Promise<User>} Promise that resolves to user object
 * @throws {NetworkError} When API request fails
 * @throws {ValidationError} When userId is invalid
 * 
 * @example
 * const user = await fetchUser('123', { useCache: false });
 * console.log(user.name);
 */
async function fetchUser(userId, options = {}) {
    // Implementation...
}
```

## 5. Code Organization: Cấu trúc rõ ràng 📁

### Module Organization

```javascript
// ✅ user.service.js - Single responsibility
class UserService {
    constructor(apiClient, cache) {
        this.apiClient = apiClient;
        this.cache = cache;
    }
    
    async getById(userId) {
        const cached = this.cache.get(`user:${userId}`);
        if (cached) return cached;
        
        const user = await this.apiClient.get(`/users/${userId}`);
        this.cache.set(`user:${userId}`, user);
        return user;
    }
    
    async create(userData) {
        const user = await this.apiClient.post('/users', userData);
        this.cache.invalidate('users:*');
        return user;
    }
}

export { UserService };
```

### Barrel Exports

```javascript
// services/index.js
export { UserService } from './user.service.js';
export { OrderService } from './order.service.js';
export { PaymentService } from './payment.service.js';

// utils/index.js
export { validateEmail } from './validation.js';
export { formatCurrency } from './formatting.js';
export { debounce, throttle } from './performance.js';

// Clean imports
import { UserService, OrderService } from './services';
import { validateEmail, formatCurrency } from './utils';
```

## 6. ESLint + Prettier Setup 🛠️

### Package.json dependencies

```json
{
  "devDependencies": {
    "eslint": "^8.50.0",
    "@eslint/js": "^9.0.0",
    "eslint-config-prettier": "^9.0.0",
    "eslint-plugin-import": "^2.28.1",
    "eslint-plugin-jsdoc": "^46.8.2",
    "prettier": "^3.0.3"
  },
  "scripts": {
    "lint": "eslint src/",
    "lint:fix": "eslint src/ --fix",
    "format": "prettier --write src/",
    "check-format": "prettier --check src/"
  }
}
```

### ESLint Config (eslint.config.js)

```javascript
import js from '@eslint/js';
import prettier from 'eslint-config-prettier';
import importPlugin from 'eslint-plugin-import';
import jsdoc from 'eslint-plugin-jsdoc';

export default [
    js.configs.recommended,
    prettier,
    {
        plugins: {
            import: importPlugin,
            jsdoc: jsdoc
        },
        rules: {
            // Code Quality
            'no-console': 'warn',
            'no-debugger': 'error',
            'no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
            'no-var': 'error',
            'prefer-const': 'error',
            
            // Functions
            'max-params': ['error', 3],
            'max-lines-per-function': ['warn', { max: 50 }],
            'arrow-body-style': ['error', 'as-needed'],
            
            // Naming
            'camelcase': ['error', { properties: 'never' }],
            
            // Import/Export
            'import/order': ['error', {
                groups: [
                    'builtin',
                    'external', 
                    'internal',
                    'parent',
                    'sibling',
                    'index'
                ],
                'newlines-between': 'always'
            }],
            'import/no-default-export': 'warn',
            
            // JSDoc
            'jsdoc/require-jsdoc': ['warn', {
                require: {
                    FunctionDeclaration: true,
                    ClassDeclaration: true
                }
            }],
            'jsdoc/require-param': 'warn',
            'jsdoc/require-returns': 'warn'
        }
    }
];
```

### Prettier Config (.prettierrc.json)

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "arrowParens": "avoid",
  "endOfLine": "lf"
}
```

### VS Code Settings (settings.json)

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },
  "eslint.validate": ["javascript", "typescript"],
  "files.associations": {
    "*.js": "javascript"
  }
}
```

### Git Hooks với Husky + lint-staged

```json
// package.json
{
  "devDependencies": {
    "husky": "^8.0.3",
    "lint-staged": "^14.0.1"
  },
  "lint-staged": {
    "*.{js,jsx}": [
      "eslint --fix",
      "prettier --write",
      "git add"
    ]
  }
}
```

```bash
# Setup husky
npm install --save-dev husky lint-staged
npx husky install
npx husky add .husky/pre-commit "npx lint-staged"
```

## 7. Testing Clean Code 🧪

```javascript
// ✅ Clean test structure
describe('UserService', () => {
    let userService;
    let mockApiClient;
    let mockCache;
    
    beforeEach(() => {
        mockApiClient = createMockApiClient();
        mockCache = createMockCache();
        userService = new UserService(mockApiClient, mockCache);
    });
    
    describe('getById', () => {
        it('should return cached user when available', async () => {
            // Arrange
            const userId = '123';
            const cachedUser = { id: userId, name: 'John' };
            mockCache.get.mockReturnValue(cachedUser);
            
            // Act
            const result = await userService.getById(userId);
            
            // Assert
            expect(result).toEqual(cachedUser);
            expect(mockApiClient.get).not.toHaveBeenCalled();
        });
        
        it('should fetch from API when not cached', async () => {
            // Arrange
            const userId = '123';
            const apiUser = { id: userId, name: 'John' };
            mockCache.get.mockReturnValue(null);
            mockApiClient.get.mockResolvedValue(apiUser);
            
            // Act
            const result = await userService.getById(userId);
            
            // Assert
            expect(mockApiClient.get).toHaveBeenCalledWith(`/users/${userId}`);
            expect(mockCache.set).toHaveBeenCalledWith(`user:${userId}`, apiUser);
            expect(result).toEqual(apiUser);
        });
    });
});
```

## Performance Tips for Clean Code ⚡

### 1. Object Destructuring for Readability

```javascript
// ✅ Clean and performant
function processUserData({ name, email, preferences: { theme, language } }) {
    return {
        displayName: name.toUpperCase(),
        contactEmail: email.toLowerCase(),
        userTheme: theme,
        userLanguage: language
    };
}
```

### 2. Early Returns vs Ternary Hell

```javascript
// ❌ Ternary hell
const getStatus = user => 
    user.isActive 
        ? user.isPremium 
            ? user.hasAccess 
                ? 'premium-active' 
                : 'premium-inactive'
            : 'basic-active'
        : 'inactive';

// ✅ Clean early returns
function getUserStatus(user) {
    if (!user.isActive) return 'inactive';
    if (!user.isPremium) return 'basic-active';
    if (!user.hasAccess) return 'premium-inactive';
    return 'premium-active';
}
```

## Clean Code Checklist ✅

### Before Committing:

- [ ] **Names** are descriptive and searchable
- [ ] **Functions** do one thing and are < 20 lines
- [ ] **No magic numbers** - use named constants  
- [ ] **Error handling** is explicit and helpful
- [ ] **Comments** explain WHY, not WHAT
- [ ] **ESLint** passes with no warnings
- [ ] **Prettier** formatted consistently
- [ ] **Tests** are readable and cover edge cases

### Code Review Questions:

- [ ] Can I understand this code without asking the author?
- [ ] Are the variable/function names clear?
- [ ] Is the function doing too many things?
- [ ] Are error cases handled gracefully?
- [ ] Is this code testable?
- [ ] Would a junior dev understand this?

## Kết luận 📋

**Clean Code không phải luxury, mà là necessity!**

### Benefits:
- 🚀 **Faster development** - Ít thời gian hiểu code
- 🐛 **Fewer bugs** - Logic rõ ràng, dễ spot issues  
- 👥 **Better teamwork** - Code consistent, dễ handover
- 🔧 **Easier maintenance** - Thay đổi features không sợ
- 📈 **Scalable** - Thêm tính năng không mess up

### Tools Summary:
- **ESLint** - Catch issues automatically
- **Prettier** - Consistent formatting
- **Husky** - Prevent bad code from committing
- **JSDoc** - Document APIs properly

### Remember:
> "Code is read much more often than it is written" - Robert C. Martin

Đầu tư thời gian viết clean code sẽ tiết kiệm hàng giờ debugging sau này! 🎉

**Start today**: Setup ESLint + Prettier, refactor 1 function mỗi ngày! 🚀