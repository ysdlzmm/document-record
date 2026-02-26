# CSS 进阶

## 一、CSS 预处理器

### 1.1 为什么需要预处理器？

**核心优势**
- **变量**：统一的颜色、间距管理
- **嵌套**：层级结构清晰
- **混入（Mixin）**：代码复用
- **函数与运算**：动态计算
- **模块化**：代码组织

### 1.2 Sass/SCSS 实战

```scss
// ===== 变量定义 =====
$primary-color: #3b82f6;
$font-size-base: 14px;
$spacing-unit: 8px;
$breakpoint-mobile: 768px;

// ===== 混入（Mixin） =====
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

@mixin respond-to($breakpoint) {
  @if $breakpoint == mobile {
    @media (max-width: $breakpoint-mobile) {
      @content;
    }
  }
}

// ===== 使用变量和混入 =====
.card {
  padding: $spacing-unit * 2;
  background: $primary-color;

  @include flex-center;

  @include respond-to(mobile) {
    padding: $spacing-unit;
  }
}

// ===== 函数与循环 =====
@for $i from 1 through 5 {
  .mt-#{$i} {
    margin-top: $spacing-unit * $i;
  }
}

// ===== 继承 =====
.button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
}

.button-primary {
  @extend .button;
  background: $primary-color;
}
```

### 1.3 Less 对比

```less
// Less 变量（用 @）
@primary-color: #3b82f6;

// Less 嵌套 & 代表父选择器
.card {
  &__title {
    font-size: 18px;
  }

  &:hover {
    transform: scale(1.05);
  }
}
```

---

## 二、CSS 架构方法论

### 2.1 BEM（Block Element Modifier）

**命名规范**
```css
/* Block */
.card {}

/* Element */
.card__title {}
.card__content {}

/* Modifier */
.card--highlighted {}
.card__title--large {}
```

**实战示例**
```html
<!-- BEM 结构 -->
<nav class="navbar">
  <div class="navbar__logo">Logo</div>
  <ul class="navbar__menu">
    <li class="navbar__item navbar__item--active">首页</li>
    <li class="navbar__item">关于</li>
  </ul>
</nav>
```

```css
.navbar {
  display: flex;
  justify-content: space-between;

  &__logo {
    font-weight: bold;
  }

  &__menu {
    display: flex;
    gap: 16px;
  }

  &__item {
    cursor: pointer;

    &--active {
      color: #3b82f6;
    }
  }
}
```

### 2.2 CSS Modules（模块化）

```css
/* Card.module.css */
.card {
  background: white;
  padding: 16px;
  border-radius: 8px;
}

.title {
  font-size: 18px;
}
```

```jsx
// React 中使用
import styles from './Card.module.css';

function Card() {
  return (
    <div className={styles.card}>
      <h3 className={styles.title}>标题</h3>
    </div>
  );
}
```

### 2.3 CSS-in-JS（styled-components）

```jsx
import styled from 'styled-components';

const StyledCard = styled.div`
  background: ${props => props.theme.background};
  padding: 16px;
  border-radius: 8px;

  &:hover {
    transform: scale(1.05);
  }

  @media (max-width: 768px) {
    padding: 8px;
  }
`;

function Card({ title }) {
  return <StyledCard>{title}</StyledCard>;
}
```

---

## 三、CSS 布局进阶

### 3.1 Flexbox 完整指南

```css
/* 容器属性 */
.flex-container {
  display: flex;

  /* 方向：row | row-reverse | column | column-reverse */
  flex-direction: row;

  /* 换行：nowrap | wrap | wrap-reverse */
  flex-wrap: wrap;

  /* 主轴对齐：flex-start | flex-end | center | space-between | space-around */
  justify-content: center;

  /* 交叉轴对齐：flex-start | flex-end | center | baseline | stretch */
  align-items: center;

  /* 多行对齐：flex-start | flex-end | center | space-between | stretch */
  align-content: center;

  /* 简写 */
  flex-flow: row wrap;
}

/* 项目属性 */
.flex-item {
  /* 放大比例：默认 0 */
  flex-grow: 1;

  /* 缩小比例：默认 1 */
  flex-shrink: 0;

  /* 初始大小 */
  flex-basis: auto;

  /* 简写：flex: grow shrink basis */
  flex: 1 0 auto;

  /* 单独对齐 */
  align-self: flex-start;
}
```

**实战场景**
```css
/* 水平垂直居中 */
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 两端对齐 */
.navbar {
  display: flex;
  justify-content: space-between;
}

/* 等分布局 */
.equal-columns > div {
  flex: 1;
}

/* Sticky Footer（页脚固定在底部） */
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex: 1;
}
```

### 3.2 Grid 网格布局

```css
/* 基础网格 */
.grid-container {
  display: grid;

  /* 列定义：重复 3 列，每列最小 100px 最大 1fr */
  grid-template-columns: repeat(3, minmax(100px, 1fr));

  /* 行定义 */
  grid-template-rows: auto 1fr auto;

  /* 间距 */
  gap: 16px;
  row-gap: 16px;
  column-gap: 16px;

  /* 区域命名 */
  grid-template-areas:
    "header header header"
    "sidebar main main"
    "footer footer footer";
}

/* 项目属性 */
.header {
  grid-area: header;
}

.sidebar {
  grid-area: sidebar;
  /* 网格线定位 */
  grid-column: 1 / 2;
  grid-row: 2 / 3;
}

.main {
  grid-area: main;
}
```

**实战场景**
```css
/* 经典圣杯布局 */
.holy-grail {
  display: grid;
  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "header header header"
    "nav main aside"
    "footer footer footer";
  min-height: 100vh;
}

/* 响应式卡片网格 */
.card-grid {
  display: grid;
  /* 自适应列数，每列最小 250px */
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
}
```

### 3.3 定位体系

```css
/* 相对定位（相对于自身） */
.relative {
  position: relative;
  top: 10px;
  left: 20px;
}

/* 绝对定位（相对于最近的定位祖先） */
.absolute {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}

/* 固定定位（相对于视口） */
.fixed {
  position: fixed;
  top: 20px;
  right: 20px;
}

/* 粘性定位（滚动时切换） */
.sticky {
  position: sticky;
  top: 0;
}
```

---

## 四、CSS 动画与过渡

### 4.1 Transition（过渡）

```css
/* 基础过渡 */
.button {
  background: blue;
  /* property | duration | timing-function | delay */
  transition: background 0.3s ease, transform 0.2s ease;
}

.button:hover {
  background: darkblue;
  transform: scale(1.05);
}

/* 缓动函数 */
.ease-default { transition-timing-function: ease; }
.ease-linear { transition-timing-function: linear; }
.ease-in { transition-timing-function: ease-in; }
.ease-out { transition-timing-function: ease-out; }
.ease-in-out { transition-timing-function: ease-in-out; }
.custom-bezier { transition-timing-function: cubic-bezier(0.68, -0.55, 0.27, 1.55); }
```

### 4.2 Animation（动画）

```css
/* 定义关键帧 */
@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes pulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
}

/* 应用动画 */
.animated {
  /* name | duration | timing-function | delay | iteration-count | direction | fill-mode | play-state */
  animation: slideIn 0.5s ease-out forwards;

  /* 无限循环 */
  animation: pulse 2s ease-in-out infinite;

  /* 交替播放 */
  animation: bounce 1s ease-in-out infinite alternate;
}

/* 动画暂停 */
.paused {
  animation-play-state: paused;
}
```

### 4.3 性能优化技巧

```css
/* ✅ 使用 transform 和 opacity（触发 GPU 加速） */
.gpu-accelerated {
  will-change: transform, opacity;
  transform: translateZ(0);
}

/* ❌ 避免动画 width/height/left/top */
.bad-performance {
  transition: width 0.3s; /* 触发重排 */
}

/* ✅ 替代方案 */
.good-performance {
  transition: transform 0.3s; /* 触发合成 */
}
```

---

## 五、响应式设计

### 5.1 媒体查询

```css
/* 移动优先（Mobile First） */
.container {
  padding: 10px;
}

/* 平板 */
@media (min-width: 768px) {
  .container {
    padding: 20px;
  }
}

/* 桌面 */
@media (min-width: 1024px) {
  .container {
    padding: 30px;
  }
}

/* 桌面优先（Desktop First） */
@media (max-width: 767px) {
  .container {
    padding: 10px;
  }
}

/* 高分辨率屏幕 */
@media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi) {
  .logo {
    background-image: url('logo@2x.png');
  }
}

/* 打印样式 */
@media print {
  .no-print {
    display: none;
  }
}
```

### 5.2 现代响应式单位

```css
/* 视口单位 */
.vw-full { width: 100vw; }  /* 视口宽度 */
.vh-full { height: 100vh; } /* 视口高度 */
.vmin-smaller { font-size: 10vmin; } /* vw 和 vh 中较小的 */
.vmax-larger { font-size: 10vmax; }  /* vw 和 vh 中较大的 */

/* 容器查询（新特性） */
@container (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}
```

### 5.3 图片响应式

```css
/* 响应式背景图 */
.hero {
  background-image: url('small.jpg');
}

@media (min-width: 768px) {
  .hero {
    background-image: url('large.jpg');
  }
}

/* 使用 picture 标签更优（见 HTML 部分） */
```

---

## 六、CSS 函数与计算

### 6.1 常用函数

```css
/* calc() 计算 */
.container {
  width: calc(100% - 20px);
  height: calc(100vh - 60px);
}

/* min() / max() / clamp() */
.responsive-text {
  /* 最小 16px，最大 24px，中间随视口变化 */
  font-size: clamp(16px, 2vw, 24px);
}

.responsive-width {
  /* 不超过 1200px，也不小于 300px */
  width: clamp(300px, 80%, 1200px);
}

.sidebar {
  /* 宽度取 300px 和 20% 中较小的 */
  width: min(300px, 20%);
}

/* var() CSS 变量 */
:root {
  --primary-color: #3b82f6;
  --spacing-unit: 8px;
}

.button {
  background: var(--primary-color);
  padding: var(--spacing-unit);
  /* 带默认值 */
  color: var(--text-color, #333);
}

/* attr() 获取属性值 */
.tooltip::after {
  content: attr(data-tooltip);
  background: #333;
  color: white;
}

/* url() 引用资源 */
.logo {
  background-image: url('./logo.svg');
}
```

### 6.2 颜色函数

```css
/* rgb() / rgba() */
.color {
  color: rgb(255, 0, 0);
  background: rgba(0, 0, 0, 0.5); /* 半透明黑色 */
}

/* hsl() / hsla() */
.color {
  color: hsl(220, 100%, 50%); /* 蓝色 */
  background: hsl(220, 100%, 50%, 0.5);
}

/* 颜色混合（新特性） */
.mixed {
  /* 混合 50% 的蓝色和红色 */
  color: color-mix(in srgb, blue 50%, red);
}
```

---

## 七、CSS 高级技巧

### 7.1 居中方案总结

```css
/* 方案 1：Flexbox（推荐） */
.flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 方案 2：Grid */
.grid-center {
  display: grid;
  place-items: center;
}

/* 方案 3：绝对定位 + transform */
.absolute-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* 方案 4：绝对定位 + margin */
.absolute-center-2 {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
  width: 200px;
  height: 100px;
}
```

### 7.2 多行省略号

```css
/* 单行省略 */
.single-line {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* 多行省略（webkit） */
.multi-line {
  display: -webkit-box;
  -webkit-line-clamp: 3; /* 显示 3 行 */
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* 多行省略（line-clamp 新标准） */
.multi-line-new {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  line-clamp: 3; /* 标准属性 */
}
```

### 7.3 清除浮动

```css
/* 方案 1：clearfix */
.clearfix::after {
  content: "";
  display: table;
  clear: both;
}

/* 方案 2：overflow */
.clear-float {
  overflow: hidden;
}

/* 方案 3：使用 Flexbox/Grid（现代方案，无需清除浮动） */
.modern-container {
  display: flex;
}
```

### 7.4 CSS 形状

```css
/* 三角形 */
.triangle {
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-bottom: 100px solid red;
}

/* 圆形 */
.circle {
  border-radius: 50%;
  width: 100px;
  height: 100px;
}

/* 卡片阴影 */
.card-shadow {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

/* 玻璃拟态 */
.glassmorphism {
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}
```

---

## 八、CSS 性能优化

### 8.1 选择器性能

```css
/* ✅ 高效选择器 */
.class-name { }           /* 1. 类选择器 */
#id-name { }              /* 2. ID 选择器 */
tag { }                   /* 3. 标签选择器 */

/* ❌ 低效选择器 */
nav ul li a { }           /* 过深的后代选择器 */
[class*="icon-"] { }      /* 属性通配符 */
div > div > div > span { } /* 过多的子选择器 */
```

### 8.2 渲染优化

```css
/* 避免重排（Reflow） */
.optimized {
  /* ✅ 使用 transform 代替 left/top */
  transform: translateX(100px);

  /* ✅ 使用 opacity 代替 visibility */
  opacity: 0;

  /* ❌ 避免这些 */
  /* left: 100px; */
  /* width: 200px; */
}

/* GPU 加速 */
.gpu-layer {
  transform: translateZ(0);
  will-change: transform;
}

/* 减少重绘（Repaint） */
.no-repaint {
  /* 使用合成层 */
  contain: layout style paint;
}
```

### 8.3 CSS 体积优化

```css
/* 使用 CSS 变量减少重复 */
:root {
  --spacing-1: 4px;
  --spacing-2: 8px;
  --spacing-3: 12px;
}

/* 简写属性 */
.shorthand {
  /* ✅ 简写 */
  margin: 10px 20px;
  padding: 10px 20px 10px 20px;
  border: 1px solid #ccc;
  font: 14px/1.5 Arial;

  /* ❌ 展开写法 */
  /* margin-top: 10px;
     margin-right: 20px; ... */
}
```

---

## 九、常见问题与避坑指南

### ❌ 问题 1：z-index 不生效

```css
/* 原因：未设置定位 */
.element {
  z-index: 10; /* ❌ 无效 */
  position: relative; /* ✅ 需要定位 */
}
```

### ❌ 问题 2：margin 合并

```css
/* 父子 margin 合并 */
.parent {
  overflow: hidden; /* ✅ 方案 1：创建 BFC */
  padding-top: 1px; /* ✅ 方案 2：用 padding 代替 */
  display: flex;    /* ✅ 方案 3：使用 Flexbox */
}
```

### ❌ 问题 3：px vs rem vs em

```css
/* px：固定像素，不响应缩放 */
.fixed { font-size: 16px; }

/* rem：相对根元素，推荐用于响应式 */
.responsive { font-size: 1rem; }

/* em：相对父元素，用于组件内部 */
.component { font-size: 1.2em; }
```

---

## 十、总结与记忆要点

### 🎯 核心记忆口诀

```
预处理器来写样式
变量嵌套混入齐
BEM 命名要规范
Flex Grid 布局强
动画用 GPU 加速
响应式设计多测试
性能优化记心上
选择器从右往左
```

### 📚 关键技术点

1. **预处理器**：Sass（$、@mixin、@extend）
2. **命名规范**：BEM（Block__Element--Modifier）
3. **布局**：Flexbox（一维）、Grid（二维）
4. **动画**：transition（过渡）、animation（关键帧）
5. **响应式**：@media、clamp()、容器查询
6. **优化**：避免重排、GPU 加速、选择器优化

---

## 十一、实战练习建议

1. 使用 Sass 重构一个项目的 CSS
2. 用 BEM 规范重写组件样式
3. 实现 5 种不同的居中方案
4. 创建一个响应式卡片网格布局
5. 实现一个高性能的页面过渡动画

---

**下一步学习**：👉 [JavaScript 进阶](./JavaScript进阶.md)
