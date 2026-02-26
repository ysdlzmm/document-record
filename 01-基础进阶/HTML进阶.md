# HTML 进阶

## 一、语义化 HTML

### 1.1 为什么需要语义化？

**核心价值**
- **SEO 优化**：搜索引擎更好地理解页面内容结构
- **可访问性**：屏幕阅读器能准确解析页面
- **代码可维护性**：代码结构清晰，易于团队协作
- **样式与结构分离**：减少对 CSS 类名的依赖

### 1.2 常用语义化标签

```html
<!-- 页面结构 -->
<header>
  <nav>
    <ul>
      <li><a href="#home">首页</a></li>
      <li><a href="#about">关于</a></li>
    </ul>
  </nav>
</header>

<main>
  <article>
    <h1>文章标题</h1>
    <p>发布时间：<time datetime="2024-01-01">2024年1月1日</time></p>
    <section>
      <h2>第一部分</h2>
      <p>内容...</p>
    </section>
  </article>

  <aside>
    <h3>相关推荐</h3>
    <ul>
      <li><a href="#">推荐文章1</a></li>
    </ul>
  </aside>
</main>

<footer>
  <p>&copy; 2024 版权所有</p>
</footer>
```

### 1.3 标签选择指南

| 标签 | 使用场景 | 语义含义 |
|------|----------|----------|
| `<article>` | 独立完整的内容（文章、博客） | 可独立分发的内容单元 |
| `<section>` | 内容的逻辑分组 | 文档或应用的章节 |
| `<aside>` | 侧边栏、相关内容 | 与主要内容间接相关 |
| `<nav>` | 导航链接组 | 页面内主要导航 |
| `<figure>` | 图片、图表、代码块 | 独立的流内容，带标题 |
| `<details>` | 可折叠的详细信息 | 用户可展开查看的详情 |

---

## 二、HTML 元素的分类

### 2.1 替换元素 vs 非替换元素

**替换元素**：内容由外部资源决定
- `<img>`、`<video>`、`<input>`、`<textarea>`、`<iframe>`
- 特点：默认有固有尺寸，部分 CSS 属性表现不同

**非替换元素**：内容由其子内容决定
- `<div>`、`<span>`、`<p>` 等
- 特点：完全受 CSS 盒模型控制

### 2.2 块级元素 vs 行内元素

```css
/* 块级元素 */
div, p, h1-h6, ul, li, table {
  display: block;
  /* 独占一行，可设置宽高、margin、padding */
}

/* 行内元素 */
span, a, strong, em {
  display: inline;
  /* 不独占一行，宽高由内容决定 */
}

/* 行内块元素 */
img, input, button {
  display: inline-block;
  /* 像行内一样排列，但有块级的盒模型特性 */
}
```

---

## 三、HTML 表单增强

### 3.1 新增输入类型（HTML5）

```html
<!-- 邮箱输入（自动验证格式） -->
<input type="email" required />

<!-- 电话输入（移动端显示数字键盘） -->
<input type="tel" pattern="[0-9]{11}" />

<!-- 数字输入（带步进按钮） -->
<input type="number" min="0" max="100" step="5" />

<!-- 日期选择 -->
<input type="date" min="2024-01-01" />

<!-- 颜色选择器 -->
<input type="color" value="#ff0000" />

<!-- 搜索框（带清除按钮） -->
<input type="search" placeholder="搜索..." />
```

### 3.2 表单验证技巧

```html
<form id="loginForm">
  <!-- 必填项 -->
  <input type="text" required />

  <!-- 正则验证 -->
  <input
    type="text"
    pattern="^[a-zA-Z0-9]{6,12}$"
    title="6-12位字母或数字"
  />

  <!-- 自定义验证消息 -->
  <input type="password" id="pwd" minlength="8" />
  <button type="submit">提交</button>
</form>

<script>
  const form = document.getElementById('loginForm');
  form.addEventListener('submit', (e) => {
    if (!form.checkValidity()) {
      e.preventDefault();
      // 自定义错误提示逻辑
    }
  });
</script>
```

---

## 四、HTML 性能优化

### 4.1 资源加载优化

```html
<head>
  <!-- DNS 预解析 -->
  <link rel="dns-prefetch" href="//cdn.example.com" />

  <!-- 预连接 -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />

  <!-- 预加载关键资源 -->
  <link rel="preload" href="critical.css" as="style" />
  <link rel="preload" href="hero-image.jpg" as="image" />

  <!-- 预获取可能用到的资源 -->
  <link rel="prefetch" href="next-page.html" />

  <!-- 延迟加载非关键 CSS -->
  <link href="non-critical.css" rel="stylesheet" media="print" onload="this.media='all'" />
</head>
```

### 4.2 图片优化

```html
<!-- 响应式图片 -->
<picture>
  <source media="(min-width: 768px)" srcset="large.jpg" />
  <source media="(min-width: 480px)" srcset="medium.jpg" />
  <img src="small.jpg" alt="响应式图片" />
</picture>

<!-- 懒加载 -->
<img src="placeholder.jpg" data-src="real-image.jpg" loading="lazy" />

<!-- 现代 WebP 格式 + 降级方案 -->
<picture>
  <source type="image/webp" srcset="image.webp" />
  <img src="image.jpg" alt="图片描述" />
</picture>
```

### 4.3 脚本加载策略

```html
<!-- defer：HTML 解析完成，DOMContentLoaded 前执行（推荐） -->
<script defer src="app.js"></script>

<!-- async：加载完后立即执行，不保证顺序 -->
<script async src="analytics.js"></script>

<!-- 关键 CSS 内联，非关键 CSS 异步加载 -->
<style>
  /* 首屏关键 CSS */
  .hero { background: #000; }
</style>

<!-- type="module" 自动应用 defer -->
<script type="module" src="app.js"></script>
```

---

## 五、可访问性（A11y）最佳实践

### 5.1 ARIA 属性使用

```html
<!-- 语义化按钮 -->
<button aria-label="关闭对话框" onclick="closeModal()">
  &times;
</button>

<!-- 动态内容区域 -->
<div role="status" aria-live="polite">
  操作成功！
</div>

<!-- 导航菜单 -->
<nav aria-label="主导航">
  <ul>
    <li><a aria-current="page" href="#">首页</a></li>
    <li><a href="#">关于</a></li>
  </ul>
</nav>

<!-- 模态框 -->
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="dialog-title"
  aria-describedby="dialog-desc"
>
  <h2 id="dialog-title">确认删除</h2>
  <p id="dialog-desc">此操作无法撤销</p>
</div>
```

### 5.2 键盘导航

```html
<!-- 自定义组件支持键盘 -->
<div
  role="button"
  tabindex="0"
  onclick="handleClick()"
  onkeydown="if(event.key==='Enter') handleClick()"
>
  可聚焦的 div
</div>

<!-- 跳过导航链接（提升体验） -->
<a href="#main-content" class="skip-link">跳到主内容</a>

<!-- 焦点管理 -->
<button
  onclick="openModal()"
  aria-haspopup="dialog"
>
  打开对话框
</button>
```

---

## 六、HTML 元素的交互特性

### 6.1 contenteditable 属性

```html
<!-- 简单的富文本编辑 -->
<div contenteditable="true" id="editor">
  <p>可以直接编辑的内容</p>
</div>

<script>
  const editor = document.getElementById('editor');
  editor.addEventListener('input', (e) => {
    console.log(e.target.innerHTML);
  });
</script>
```

### 6.2 data-* 自定义属性

```html
<div
  data-id="123"
  data-user='{"name": "张三", "age": 25}'
  data-config-theme="dark"
>
  自定义数据
</div>

<script>
  const el = document.querySelector('[data-id]');
  console.log(el.dataset.id);          // "123"
  console.log(el.dataset.user);        // '{"name": "张三", "age": 25}'
  console.log(el.dataset.configTheme); // "dark"
</script>
```

---

## 七、常见问题与避坑指南

### ❌ 问题 1：过度使用 div

```html
<!-- 不推荐 -->
<div class="header">...</div>
<div class="article">...</div>
<div class="footer">...</div>

<!-- ✅ 推荐 -->
<header>...</header>
<main>...</main>
<footer>...</footer>
```

### ❌ 问题 2：忘记 alt 属性

```html
<!-- ❌ 可访问性差 -->
<img src="photo.jpg" />

<!-- ✅ 正确做法 -->
<img src="photo.jpg" alt="产品展示图" />
<!-- 装饰性图片 -->
<img src="bg.jpg" alt="" role="presentation" />
```

### ❌ 问题 3：table 用于布局

```html
<!-- ❌ 不要用 table 布局 -->
<table>
  <tr><td>侧边栏</td><td>主内容</td></tr>
</table>

<!-- ✅ 使用 CSS 布局 -->
<div class="container">
  <aside>侧边栏</aside>
  <main>主内容</main>
</div>
```

### ❌ 问题 4：忽略 meta 标签

```html
<!-- ✅ 必不可少的 meta 标签 -->
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="页面描述，利于 SEO" />
  <meta http-equiv="X-UA-Compatible" content="ie=edge" />
</head>
```

---

## 八、总结与记忆要点

### 🎯 核心记忆口诀

```
语义化标签记清楚
SEO 可读性都兼顾
表单验证用原生
性能优化要预加载
可访问性别忘记
ARIA 属性来辅助
```

### 📚 关键技术点

1. **语义化**：header、nav、main、article、section、aside、footer
2. **表单增强**：email、tel、date、pattern、required
3. **性能优化**：preload、defer/async、lazy、picture
4. **可访问性**：alt、aria-*、role、tabindex
5. **交互特性**：contenteditable、data-*、hidden

---

## 九、实战练习建议

1. 重构一个 div-soup 的老页面，使用语义化标签
2. 为表单添加完整的验证逻辑
3. 实现一个支持键盘导航的自定义下拉组件
4. 优化一个页面的资源加载策略
5. 使用 Lighthouse 检查页面的可访问性评分

---

**下一步学习**：👉 [CSS 进阶](./CSS进阶.md)
