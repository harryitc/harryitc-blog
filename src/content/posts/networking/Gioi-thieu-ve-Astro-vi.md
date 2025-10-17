---
title: Giới thiệu về Astro - Framework xây dựng website hiện đại
published: 2025-10-17
tags:
  - Web Development
  - Astro
  - JavaScript
  - Guide
toc: true
lang: vi
abbrlink: gioi-thieu-ve-astro
description: Tìm hiểu về Astro - framework mới giúp xây dựng website nhanh chóng với hiệu suất tối ưu và khả năng tích hợp đa dạng.
image:
    url: 'https://docs.astro.build/assets/rose.webp'
    alt: 'The Astro logo on a dark background with a pink glow.'
---

Astro là một framework web hiện đại được thiết kế để xây dựng những website có hiệu suất cao. Trong bài viết này, chúng ta sẽ cùng tìm hiểu về những tính năng độc đáo của Astro và lý do tại sao nó đang trở thành lựa chọn phổ biến của các developer.

## Astro là gì?

Astro là một **static site generator** mới được phát triển với triết lý "Islands Architecture". Điều này có nghĩa là Astro chỉ gửi JavaScript cần thiết đến trình duyệt, giúp website tải nhanh hơn đáng kể so với các framework truyền thống.

### Những điểm nổi bật của Astro

- **Zero JavaScript by default**: Astro mặc định không gửi JavaScript không cần thiết
- **Component Islands**: Chỉ hydrate các component cần thiết
- **Framework agnostic**: Hỗ trợ React, Vue, Svelte, và nhiều framework khác
- **Built-in optimizations**: Tối ưu hóa tự động cho hình ảnh, CSS, và JavaScript

## Kiến trúc Islands

> [!TIP]
> Islands Architecture là một khái niệm mới trong phát triển web, cho phép bạn tạo ra những "đảo" JavaScript độc lập trên trang web tĩnh.

Thay vì tải toàn bộ JavaScript framework, Astro chỉ "hydrate" những component thực sự cần tương tác. Điều này mang lại những lợi ích:

1. **Hiệu suất tải trang nhanh hơn**
2. **SEO tốt hơn** do nội dung được render sẵn
3. **Trải nghiệm người dùng mượt mà hơn**

## Cài đặt và sử dụng Astro

### Tạo project mới

```bash
# Sử dụng npm
npm create astro@latest my-astro-site

# Sử dụng yarn
yarn create astro my-astro-site

# Sử dụng pnpm
pnpm create astro my-astro-site
```

### Cấu trúc thư mục cơ bản

```
my-astro-site/
├── src/
│   ├── components/
│   ├── layouts/
│   ├── pages/
│   └── styles/
├── public/
├── astro.config.mjs
└── package.json
```

## Viết component đầu tiên

Astro sử dụng cú pháp `.astro` độc đáo, kết hợp HTML, CSS và JavaScript:

```astro
---
// Component script (chạy ở build time)
const greeting = "Xin chào";
const name = "Astro";
---

<style>
  .greeting {
    color: #ff6b6b;
    font-size: 2rem;
    font-weight: bold;
  }
</style>

<div class="greeting">
  {greeting} {name}!
</div>
```

### Sử dụng framework khác

> [!IMPORTANT]
> Astro cho phép bạn sử dụng các framework yêu thích như React, Vue, Svelte trong cùng một project.

```astro
---
// Có thể import component từ các framework khác
import ReactCounter from '../components/ReactCounter.jsx';
import VueButton from '../components/VueButton.vue';
---

<div>
  <h1>Trang web hybrid</h1>
  <ReactCounter client:load />
  <VueButton client:visible />
</div>
```

## Directives trong Astro

Astro cung cấp các **client directives** để kiểm soát khi nào component được hydrate:

- `client:load` - Hydrate ngay khi trang tải
- `client:idle` - Hydrate khi trình duyệt rảnh rỗi  
- `client:visible` - Hydrate khi component xuất hiện trên màn hình
- `client:media` - Hydrate khi điều kiện media query được thỏa mãn

```astro
<!-- Component chỉ hydrate khi người dùng cuộn đến -->
<HeavyComponent client:visible />

<!-- Component chỉ hydrate trên mobile -->
<MobileMenu client:media="(max-width: 768px)" />
```

## Tối ưu hóa hình ảnh

:::note
Astro có hệ thống tối ưu hóa hình ảnh tích hợp sẵn, tự động chuyển đổi format và resize theo nhu cầu.
:::

```astro
---
import { Image } from 'astro:assets';
import heroImage from '../assets/hero.jpg';
---

<Image 
  src={heroImage} 
  alt="Hình ảnh hero" 
  width={800} 
  height={400}
  format="webp"
/>
```

## Content Collections

Astro cung cấp **Content Collections** để quản lý nội dung một cách có tổ chức:

```javascript
// src/content.config.ts
import { defineCollection, z } from 'astro:content';

const blogCollection = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    published: z.date(),
    tags: z.array(z.string()),
    lang: z.string().optional(),
  }),
});

export const collections = {
  'blog': blogCollection,
};
```

## So sánh với các framework khác

| Framework | JavaScript Bundle | SSR | Hydration |
|-----------|------------------|-----|-----------|
| **Astro** | Minimal | ✅ | Selective |
| Next.js | Full | ✅ | Full |
| Gatsby | Full | ❌ | Full |
| Nuxt | Full | ✅ | Full |

> [!WARNING]
> Mặc dù Astro rất mạnh mẽ cho static sites, nó có thể không phù hợp cho các ứng dụng cần nhiều tương tác real-time.

## Kết luận

Astro mang đến một cách tiếp cận mới mẻ trong việc xây dựng website, tập trung vào hiệu suất và trải nghiệm người dùng. Với khả năng tích hợp đa framework và tối ưu hóa tự động, Astro là lựa chọn tuyệt vời cho:

- **Blog và trang tin tức**
- **Website công ty**  
- **Portfolio cá nhân**
- **Documentation sites**

Nếu bạn đang tìm kiếm một giải pháp để xây dựng website nhanh, SEO-friendly và có hiệu suất cao, Astro chắc chắn đáng để thử nghiệm!

## Tài liệu tham khảo

- [Astro Documentation](https://docs.astro.build/)
- [Astro GitHub Repository](https://github.com/withastro/astro)
- [Islands Architecture](https://jasonformat.com/islands-architecture/)