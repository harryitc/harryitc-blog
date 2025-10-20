---
title: Destructuring và Spread/Rest – Viết code JS hiện đại hơn
published: 2025-10-18
tags:
  - JavaScript
  - ES6
  - Modern JavaScript
  - Destructuring
  - Spread Operator
toc: true
lang: vi
abbrlink: javascript-destructuring-spread-rest
description: Làm chủ cú pháp ES6 giúp code JavaScript gọn gàng và dễ đọc hơn. So sánh cách viết cũ và mới với Destructuring, Spread và Rest operators.
image:
    url: '/images/destructuring-object.png'
    alt: 'JavaScript ES6 Destructuring and Spread'
---

ES6 mang đến nhiều cú pháp mới giúp code JavaScript ngắn gọn và dễ đọc hơn. Hãy cùng tìm hiểu 3 tính năng quan trọng nhất: **Destructuring**, **Spread** và **Rest operators**.

## Destructuring: "Mổ xẻ" object và array 📦

### Object Destructuring

**Cách cũ** - Dài dòng:

```javascript
const user = {
    name: 'Harry',
    age: 25,
    email: 'harry@example.com'
};

// ❌ Cách cũ - Lặp lại nhiều lần
const name = user.name;
const age = user.age;
const email = user.email;
```

**Cách mới** - Gọn gàng:

```javascript
// ✅ Destructuring - Một dòng!
const { name, age, email } = user;

console.log(name);  // "Harry"
console.log(age);   // 25
console.log(email); // "harry@example.com"
```

### Đổi tên biến khi destructure

```javascript
const user = { name: 'Harry', age: 25 };

// Đổi tên biến
const { name: userName, age: userAge } = user;

console.log(userName); // "Harry"
console.log(userAge);  // 25
```

### Giá trị mặc định

```javascript
const user = { name: 'Harry' };

// Nếu không có 'age', dùng giá trị mặc định
const { name, age = 18 } = user;

console.log(age); // 18
```

### Nested Destructuring

```javascript
const user = {
    name: 'Harry',
    address: {
        city: 'Hanoi',
        country: 'Vietnam'
    }
};

// Destructure lồng nhau
const { 
    name, 
    address: { city, country } 
} = user;

console.log(city);    // "Hanoi"
console.log(country); // "Vietnam"
```

### Array Destructuring

**Cách cũ**:

```javascript
const colors = ['red', 'green', 'blue'];

// ❌ Cách cũ
const first = colors[0];
const second = colors[1];
const third = colors[2];
```

**Cách mới**:

```javascript
// ✅ Array destructuring
const [first, second, third] = colors;

console.log(first);  // "red"
console.log(second); // "green"
console.log(third);  // "blue"

// Bỏ qua phần tử
const [, , third] = colors;
console.log(third); // "blue"

// Lấy phần còn lại với Rest
const [first, ...others] = colors;
console.log(first);  // "red"
console.log(others); // ["green", "blue"]
```

### Ứng dụng thực tế: Function parameters

**Cách cũ**:

```javascript
// ❌ Khó nhớ thứ tự tham số
function createUser(name, age, email, role) {
    return { name, age, email, role };
}

createUser('Harry', 25, 'harry@ex.com', 'admin');
```

**Cách mới**:

```javascript
// ✅ Dễ đọc, không cần nhớ thứ tự
function createUser({ name, age, email, role = 'user' }) {
    return { name, age, email, role };
}

createUser({ 
    name: 'Harry', 
    email: 'harry@ex.com',
    age: 25
});
```

## Spread Operator (...): "Trải" array/object 🌟

### Spread với Array

**Cách cũ**:

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// ❌ Nối array cách cũ
const combined = arr1.concat(arr2);

// ❌ Copy array cách cũ
const copy = arr1.slice();
```

**Cách mới**:

```javascript
// ✅ Spread - Ngắn gọn hơn
const combined = [...arr1, ...arr2];
// [1, 2, 3, 4, 5, 6]

// ✅ Copy array
const copy = [...arr1];

// ✅ Thêm phần tử
const newArr = [0, ...arr1, 4];
// [0, 1, 2, 3, 4]
```

### Spread với Object

**Cách cũ**:

```javascript
const user = { name: 'Harry', age: 25 };
const extra = { email: 'harry@ex.com' };

// ❌ Copy object cách cũ
const copy = Object.assign({}, user);

// ❌ Merge objects
const merged = Object.assign({}, user, extra);
```

**Cách mới**:

```javascript
// ✅ Spread object - Dễ đọc hơn
const copy = { ...user };

// ✅ Merge objects
const merged = { ...user, ...extra };
// { name: 'Harry', age: 25, email: 'harry@ex.com' }

// ✅ Override properties
const updated = { ...user, age: 26 };
// { name: 'Harry', age: 26 }
```

### Ứng dụng thực tế: Update state (React)

```javascript
// ✅ Update object immutably
const user = {
    name: 'Harry',
    profile: {
        age: 25,
        city: 'Hanoi'
    }
};

// Update nested object
const updated = {
    ...user,
    profile: {
        ...user.profile,
        age: 26
    }
};
```

### Spread trong function call

**Cách cũ**:

```javascript
const numbers = [1, 5, 3, 9, 2];

// ❌ Cách cũ
const max = Math.max.apply(null, numbers);
```

**Cách mới**:

```javascript
// ✅ Spread - Tự nhiên hơn
const max = Math.max(...numbers); // 9
```

## Rest Operator (...): "Thu gom" tham số 📥

Rest operator trông giống Spread nhưng ngược lại - thu gom thành array.

### Rest parameters

**Cách cũ**:

```javascript
// ❌ Dùng arguments (không phải array thật)
function sum() {
    const args = Array.prototype.slice.call(arguments);
    return args.reduce((a, b) => a + b, 0);
}
```

**Cách mới**:

```javascript
// ✅ Rest parameters - Array thật
function sum(...numbers) {
    return numbers.reduce((a, b) => a + b, 0);
}

sum(1, 2, 3, 4, 5); // 15

// Mix với tham số thường
function greet(greeting, ...names) {
    return `${greeting}, ${names.join(' and ')}!`;
}

greet('Hello', 'Harry', 'Tom', 'Jane');
// "Hello, Harry and Tom and Jane!"
```

### Rest trong destructuring

```javascript
const { name, age, ...others } = {
    name: 'Harry',
    age: 25,
    email: 'harry@ex.com',
    role: 'admin'
};

console.log(name);   // "Harry"
console.log(age);    // 25
console.log(others); // { email: 'harry@ex.com', role: 'admin' }

// Array rest
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first);  // 1
console.log(second); // 2
console.log(rest);   // [3, 4, 5]
```

## So sánh tổng hợp: Cũ vs Mới 🔄

### Ví dụ thực tế: Xử lý API response

**Cách cũ**:

```javascript
function handleUserData(response) {
    const user = response.data.user;
    const userName = user.name;
    const userEmail = user.email;
    const permissions = user.permissions;
    
    const settings = user.settings;
    const theme = settings.theme;
    const language = settings.language;
    
    return {
        name: userName,
        email: userEmail,
        theme: theme,
        language: language,
        permissions: permissions
    };
}
```

**Cách mới**:

```javascript
function handleUserData(response) {
    const { 
        data: { 
            user: { 
                name, 
                email, 
                permissions,
                settings: { theme, language }
            } 
        } 
    } = response;
    
    return { name, email, theme, language, permissions };
}

// Hoặc ngắn gọn hơn với rest
function handleUserData({ data: { user } }) {
    const { name, email, settings: { theme, language }, ...rest } = user;
    return { name, email, theme, language, ...rest };
}
```

### Ví dụ: Remove property from object

**Cách cũ**:

```javascript
const user = { name: 'Harry', age: 25, password: '123456' };

// ❌ Xóa property
delete user.password; // Mutate object gốc!
```

**Cách mới**:

```javascript
// ✅ Tạo object mới, không có password
const { password, ...userWithoutPassword } = user;

console.log(userWithoutPassword);
// { name: 'Harry', age: 25 }
```

### Ví dụ: Merge arrays unique

**Cách cũ**:

```javascript
const arr1 = [1, 2, 3];
const arr2 = [3, 4, 5];

// ❌ Cách cũ
const unique = arr1.concat(arr2).filter((item, index, arr) => {
    return arr.indexOf(item) === index;
});
```

**Cách mới**:

```javascript
// ✅ Spread + Set
const unique = [...new Set([...arr1, ...arr2])];
// [1, 2, 3, 4, 5]
```

## Best Practices 💡

1. **Destructuring khi có ≥2 properties**: Nếu chỉ 1 property, không cần destructure
2. **Dùng giá trị mặc định** để tránh `undefined`
3. **Rest operator phải ở cuối** (không thể `...rest, other`)
4. **Shallow copy**: Spread chỉ copy 1 cấp, nested object vẫn reference
5. **Performance**: Spread/Rest tốn performance hơn một chút, nhưng code dễ đọc quan trọng hơn

```javascript
// ❌ Deep nested objects vẫn reference
const user = { profile: { age: 25 } };
const copy = { ...user };
copy.profile.age = 26;
console.log(user.profile.age); // 26 (bị thay đổi!)

// ✅ Deep copy nếu cần
const deepCopy = JSON.parse(JSON.stringify(user));
// Hoặc dùng structuredClone() (modern browsers)
const deepCopy2 = structuredClone(user);
```

## Kết luận

**Destructuring**, **Spread** và **Rest** là 3 tính năng ES6 quan trọng nhất giúp code JavaScript:
- 📝 **Ngắn gọn hơn**: Ít code boilerplate
- 👀 **Dễ đọc hơn**: Ý định rõ ràng
- 🎯 **Immutable**: Tránh thay đổi data gốc
- 🚀 **Modern**: Code theo chuẩn hiện đại

Hãy thử áp dụng ngay vào code của bạn! 🎉
