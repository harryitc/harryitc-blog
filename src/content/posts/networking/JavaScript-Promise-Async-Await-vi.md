---
title: Promise, async/await và cách tránh callback hell
published: 2025-10-18
tags:
  - JavaScript
  - Asynchronous
  - Promise
  - Async/Await
  - Web Development
toc: true
lang: vi
abbrlink: javascript-promise-async-await
description: Tìm hiểu cách xử lý bất đồng bộ trong JavaScript - từ callback hell đến Promise và async/await. So sánh 3 cách viết code để hiểu rõ sự tiến hóa.
image:
    url: '/images/async-await-la-gi.webp'
    alt: 'JavaScript Promise and Async/Await'
---

Xử lý code bất đồng bộ là một trong những thách thức lớn nhất khi học JavaScript. Hãy cùng tìm hiểu cách giải quyết vấn đề "callback hell" và viết code async dễ đọc hơn.

## Vấn đề: Callback Hell 🔥

Khi làm việc với nhiều tác vụ bất đồng bộ (gọi API, đọc file...), code callback lồng nhau trở nên khó đọc:

```javascript
// ❌ Callback Hell - Khó đọc, khó maintain
getUserData(userId, (user) => {
    getPostsByUser(user.id, (posts) => {
        getComments(posts[0].id, (comments) => {
            getLikes(comments[0].id, (likes) => {
                console.log('Số lượt like:', likes.length);
                // Còn nhiều cấp nữa...
            });
        });
    });
});
```

## Giải pháp 1: Promise Chain ⛓️

Promise giúp code "phẳng" hơn với chuỗi `.then()`:

```javascript
// ✅ Promise Chain - Dễ đọc hơn
getUserData(userId)
    .then(user => getPostsByUser(user.id))
    .then(posts => getComments(posts[0].id))
    .then(comments => getLikes(comments[0].id))
    .then(likes => {
        console.log('Số lượt like:', likes.length);
    })
    .catch(error => {
        console.error('Có lỗi xảy ra:', error);
    });
```

### Promise cơ bản

```javascript
// Tạo Promise
const myPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
        const success = true;
        if (success) {
            resolve('Thành công!');
        } else {
            reject('Lỗi rồi!');
        }
    }, 1000);
});

// Sử dụng Promise
myPromise
    .then(result => console.log(result))
    .catch(error => console.error(error));
```

## Giải pháp 2: Async/Await ⚡

Async/await làm code bất đồng bộ trông giống code đồng bộ:

```javascript
// ✅ Async/Await - Dễ đọc nhất!
async function fetchAllData(userId) {
    try {
        const user = await getUserData(userId);
        const posts = await getPostsByUser(user.id);
        const comments = await getComments(posts[0].id);
        const likes = await getLikes(comments[0].id);
        
        console.log('Số lượt like:', likes.length);
    } catch (error) {
        console.error('Có lỗi xảy ra:', error);
    }
}

fetchAllData(123);
```

### Async/Await cơ bản

```javascript
// Hàm async luôn trả về Promise
async function greet() {
    return 'Xin chào!';
}

// Tương đương với:
function greet() {
    return Promise.resolve('Xin chào!');
}

// Await chỉ dùng được trong hàm async
async function getData() {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    return data;
}
```

## So sánh 3 phiên bản cùng 1 logic 🔄

Ví dụ: Lấy thông tin user, rồi lấy bài post đầu tiên của user đó.

### Version 1: Callback (😰 Khó đọc)

```javascript
function getFirstPost(userId, callback) {
    getUser(userId, (error, user) => {
        if (error) {
            callback(error, null);
            return;
        }
        getPosts(user.id, (error, posts) => {
            if (error) {
                callback(error, null);
                return;
            }
            callback(null, posts[0]);
        });
    });
}

// Sử dụng
getFirstPost(1, (error, post) => {
    if (error) {
        console.error('Lỗi:', error);
    } else {
        console.log('Bài post:', post);
    }
});
```

### Version 2: Promise (😊 Tốt hơn)

```javascript
function getFirstPost(userId) {
    return getUser(userId)
        .then(user => getPosts(user.id))
        .then(posts => posts[0]);
}

// Sử dụng
getFirstPost(1)
    .then(post => console.log('Bài post:', post))
    .catch(error => console.error('Lỗi:', error));
```

### Version 3: Async/Await (🎉 Tuyệt vời nhất!)

```javascript
async function getFirstPost(userId) {
    const user = await getUser(userId);
    const posts = await getPosts(user.id);
    return posts[0];
}

// Sử dụng
async function main() {
    try {
        const post = await getFirstPost(1);
        console.log('Bài post:', post);
    } catch (error) {
        console.error('Lỗi:', error);
    }
}

main();
```

## Error Handling: Xử lý lỗi đúng cách 🛡️

### Promise - Dùng .catch()

```javascript
fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('Lỗi:', error))
    .finally(() => console.log('Hoàn thành!'));
```

### Async/Await - Dùng try/catch

```javascript
async function fetchData() {
    try {
        const response = await fetch('https://api.example.com/data');
        const data = await response.json();
        console.log(data);
    } catch (error) {
        console.error('Lỗi:', error);
    } finally {
        console.log('Hoàn thành!');
    }
}
```

### Xử lý nhiều Promise cùng lúc

```javascript
// Chạy song song - Nhanh hơn
async function fetchMultiple() {
    try {
        const [users, posts, comments] = await Promise.all([
            fetch('/api/users').then(r => r.json()),
            fetch('/api/posts').then(r => r.json()),
            fetch('/api/comments').then(r => r.json())
        ]);
        
        console.log('Users:', users);
        console.log('Posts:', posts);
        console.log('Comments:', comments);
    } catch (error) {
        console.error('Có API nào đó bị lỗi:', error);
    }
}

// Promise.allSettled - Không dừng khi có lỗi
async function fetchMultipleSafe() {
    const results = await Promise.allSettled([
        fetch('/api/users').then(r => r.json()),
        fetch('/api/posts').then(r => r.json()),
        fetch('/api/comments').then(r => r.json())
    ]);
    
    results.forEach((result, index) => {
        if (result.status === 'fulfilled') {
            console.log(`API ${index} thành công:`, result.value);
        } else {
            console.log(`API ${index} lỗi:`, result.reason);
        }
    });
}
```

## Tips quan trọng 💡

1. **Luôn xử lý lỗi**: Đừng quên `.catch()` hoặc `try/catch`
2. **Async/await dễ đọc hơn Promise chain** cho code phức tạp
3. **Dùng Promise.all()** khi các tác vụ độc lập (chạy song song)
4. **Await tuần tự** khi tác vụ sau phụ thuộc vào tác vụ trước
5. **Top-level await** chỉ dùng được trong ES modules

```javascript
// ❌ Chậm - Chạy tuần tự không cần thiết
const user = await getUser(1);
const settings = await getSettings(); // Không phụ thuộc user

// ✅ Nhanh - Chạy song song
const [user, settings] = await Promise.all([
    getUser(1),
    getSettings()
]);
```

## Kết luận

- **Callback**: Cũ, dễ bị callback hell
- **Promise**: Cải thiện, nhưng vẫn hơi phức tạp với `.then()` chain
- **Async/Await**: Modern, dễ đọc nhất, nên dùng cho mọi code mới

Hãy luôn ưu tiên **async/await** trong code mới và nhớ xử lý lỗi đúng cách! 🚀
