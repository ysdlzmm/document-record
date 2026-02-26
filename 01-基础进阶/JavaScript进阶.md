# JavaScript 进阶

## 一、执行上下文与作用域

### 1.1 执行上下文（Execution Context）

**三种类型**
- 全局上下文
- 函数上下文
- eval 上下文（不推荐使用）

**执行栈**
```javascript
// 执行顺序示例
function first() {
  console.log('First start');
  second();
  console.log('First end');
}

function second() {
  console.log('Second');
}

first();

/*
 * 输出顺序：
 * First start
 * Second
 * First end
 *
 * 执行栈变化：
 * [全局上下文]
 * [全局上下文, first]
 * [全局上下文, first, second]
 * [全局上下文, first]
 * [全局上下文]
 * []
 */
```

### 1.2 变量提升

```javascript
// 函数提升（整个函数提升）
console.log(add(1, 2)); // 3
function add(a, b) {
  return a + b;
}

// 变量提升（仅声明提升，赋值不提升）
console.log(name); // undefined，而非报错
var name = '张三';
console.log(name); // '张三'

// let 和 const 不会提升（暂时性死区）
// console.log(age); // ReferenceError
let age = 25;

// 函数表达式不会提升
// console.log(subtract(5, 3)); // TypeError: subtract is not a function
var subtract = function(a, b) {
  return a - b;
};
```

### 1.3 作用域链

```javascript
// 作用域链示例
var globalVar = 'global';

function outer() {
  var outerVar = 'outer';

  function inner() {
    var innerVar = 'inner';

    // 可以访问所有变量
    console.log(globalVar); // 'global'
    console.log(outerVar);  // 'outer'
    console.log(innerVar);  // 'inner'
  }

  inner();
  // console.log(innerVar); // ReferenceError
}

outer();

// 闭包示例
function createCounter() {
  let count = 0;
  return function() {
    return ++count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

---

## 二、this 与原型链

### 2.1 this 的指向

**四种绑定规则**

```javascript
// 1. 默认绑定（严格模式下为 undefined）
function fn() {
  console.log(this);
}
fn(); // window（非严格模式）/ undefined（严格模式）

// 2. 隐式绑定
const obj = {
  name: '张三',
  sayName() {
    console.log(this.name);
  }
};
obj.sayName(); // '张三'

// 隐式丢失
const fn2 = obj.sayName;
fn2(); // undefined

// 3. 显式绑定（call、apply、bind）
function greet(greeting, punctuation) {
  console.log(`${greeting}, ${this.name}${punctuation}`);
}

const person = { name: '李四' };

greet.call(person, 'Hello', '!');     // 'Hello, 李四!'
greet.apply(person, ['Hi', '.']);     // 'Hi, 李四.'
const boundFn = greet.bind(person, 'Hey');
boundFn('~');                          // 'Hey, 李四~'

// 4. new 绑定
function Person(name) {
  this.name = name;
}
const p = new Person('王五');
console.log(p.name); // '王五'
```

**箭头函数的 this**
```javascript
// 箭头函数没有自己的 this，继承外层
const obj = {
  name: '赵六',
  sayName: () => {
    console.log(this.name); // window.name（外层 this）
  },
  sayNameNormal() {
    const inner = () => {
      console.log(this.name); // '赵六'（继承外层普通函数的 this）
    };
    inner();
  }
};

obj.sayName();
obj.sayNameNormal();
```

### 2.2 原型链

```javascript
// 构造函数创建对象
function Person(name) {
  this.name = name;
}

// 原型上添加方法
Person.prototype.sayName = function() {
  console.log(this.name);
};

// 创建实例
const person1 = new Person('张三');
const person2 = new Person('李四');

person1.sayName(); // '张三'
person2.sayName(); // '李四'
console.log(person1.sayName === person2.sayName); // true（共享方法）

// 原型链查找
console.log(person1.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true
```

**原型继承**
```javascript
// 原型链继承
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} is eating`);
};

function Dog(name, breed) {
  Animal.call(this, name); // 继承属性
  this.breed = breed;
}

// 继承方法
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  console.log('Woof!');
};

const dog = new Dog('Buddy', 'Golden Retriever');
dog.eat();  // 'Buddy is eating'
dog.bark(); // 'Woof!'
```

---

## 三、异步编程

### 3.1 事件循环（Event Loop）

```javascript
// 事件循环执行顺序
console.log('1. 同步代码');

setTimeout(() => {
  console.log('2. 宏任务（setTimeout）');
}, 0);

Promise.resolve().then(() => {
  console.log('3. 微任务（Promise）');
});

console.log('4. 同步代码');

/*
 * 输出顺序：
 * 1. 同步代码
 * 4. 同步代码
 * 3. 微任务（Promise）
 * 2. 宏任务（setTimeout）
 *
 * 执行顺序：
 * 1. 执行同步代码
 * 2. 清空微任务队列
 * 3. 执行一个宏任务
 * 4. 重复 2-3
 */

// 宏任务：setTimeout、setInterval、setImmediate、I/O、UI 渲染
// 微任务：Promise.then、MutationObserver、queueMicrotask
```

### 3.2 Promise

```javascript
// 基础用法
const promise = new Promise((resolve, reject) => {
  // 异步操作
  setTimeout(() => {
    const success = true;
    if (success) {
      resolve('操作成功');
    } else {
      reject('操作失败');
    }
  }, 1000);
});

promise
  .then(result => {
    console.log(result); // '操作成功'
    return '下一步结果';
  })
  .then(result => {
    console.log(result); // '下一步结果'
  })
  .catch(error => {
    console.error(error);
  })
  .finally(() => {
    console.log('无论如何都会执行');
  });

// Promise 静态方法
Promise.all([p1, p2, p3]);            // 全部成功才成功
Promise.race([p1, p2, p3]);           // 第一个完成的结果
Promise.allSettled([p1, p2, p3]);     // 返回所有结果（无论成功失败）
Promise.any([p1, p2, p3]);            // 第一个成功的结果
```

### 3.3 Async/Await

```javascript
// async 函数返回 Promise
async function fetchData() {
  // await 会暂停函数执行，等待 Promise 结果
  const response = await fetch('https://api.example.com/data');
  const data = await response.json();
  return data;
}

// 使用 async/await 代替 Promise 链
async function getUserData() {
  try {
    const user = await fetchUser();
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);
    return { user, posts, comments };
  } catch (error) {
    console.error('获取数据失败:', error);
    throw error;
  }
}

// 并行请求
async function fetchMultiple() {
  const [users, posts] = await Promise.all([
    fetch('https://api.example.com/users'),
    fetch('https://api.example.com/posts')
  ]);

  return {
    users: await users.json(),
    posts: await posts.json()
  };
}

// 顺序执行（错误会被捕获）
async function sequence() {
  const result1 = await step1();
  const result2 = await step2(result1);
  const result3 = await step3(result2);
  return result3;
}
```

---

## 四、函数式编程

### 4.1 高阶函数

```javascript
// 函数作为参数
function map(array, fn) {
  const result = [];
  for (let i = 0; i < array.length; i++) {
    result.push(fn(array[i], i, array));
  }
  return result;
}

const numbers = [1, 2, 3, 4];
const doubled = map(numbers, n => n * 2);
console.log(doubled); // [2, 4, 6, 8]

// 函数作为返回值
function createMultiplier(multiplier) {
  return function(num) {
    return num * multiplier;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

### 4.2 常用数组方法

```javascript
const users = [
  { id: 1, name: '张三', age: 25 },
  { id: 2, name: '李四', age: 30 },
  { id: 3, name: '王五', age: 25 }
];

// map：映射（返回新数组）
const names = users.map(user => user.name);
// ['张三', '李四', '王五']

// filter：过滤（返回符合条件的元素）
const youngUsers = users.filter(user => user.age < 30);
// [{ id: 1, name: '张三', age: 25 }, { id: 3, name: '王五', age: 25 }]

// reduce：归约（返回一个值）
const totalAge = users.reduce((sum, user) => sum + user.age, 0);
// 80

const usersById = users.reduce((acc, user) => {
  acc[user.id] = user;
  return acc;
}, {});
// { 1: { id: 1, name: '张三', age: 25 }, 2: {...}, 3: {...} }

// find：查找第一个符合条件的元素
const user = users.find(u => u.age === 25);
// { id: 1, name: '张三', age: 25 }

// some：是否有元素符合条件
const hasYoungUser = users.some(u => u.age < 20);
// false

// every：是否所有元素符合条件
const allAdults = users.every(u => u.age >= 18);
// true
```

### 4.3 函数柯里化（Currying）

```javascript
// 柯里化：将多参数函数转换为单参数函数序列
function add(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

// 箭头函数写法
const add = a => b => c => a + b + c;

console.log(add(1)(2)(3)); // 6

// 柯里化应用场景
function fetch(url) {
  return function(method) {
    return function(data) {
      // 实际发送请求
      console.log(`Fetching ${url} with ${method}`, data);
    };
  };
}

const getUser = fetch('https://api.example.com/users');
const postUser = getUser('POST');
postUser({ name: '张三' });
```

### 4.4 函数组合（Compose）

```javascript
// 函数组合：将多个函数组合成一个函数
function compose(...fns) {
  return function(value) {
    return fns.reduceRight((acc, fn) => fn(acc), value);
  };
}

// 使用示例
const toUpperCase = str => str.toUpperCase();
const exclaim = str => str + '!';
const reverse = str => str.split('').reverse().join('');

const shout = compose(reverse, exclaim, toUpperCase);
console.log(shout('hello')); // '!OLLEH'

// 管道（Pipe）：从左到右执行
function pipe(...fns) {
  return function(value) {
    return fns.reduce((acc, fn) => fn(acc), value);
  };
}

const processUser = pipe(
  user => ({ ...user, age: user.age + 1 }),
  user => ({ ...user, name: user.name.toUpperCase() }),
  user => JSON.stringify(user, null, 2)
);

console.log(processUser({ name: '张三', age: 25 }));
```

---

## 五、ES6+ 新特性

### 5.1 解构赋值

```javascript
// 数组解构
const numbers = [1, 2, 3, 4, 5];
const [first, second, ...rest] = numbers;
console.log(first, second, rest); // 1 2 [3, 4, 5]

// 交换变量
let a = 1, b = 2;
[a, b] = [b, a];
console.log(a, b); // 2 1

// 对象解构
const user = { name: '张三', age: 25, city: '北京' };
const { name, age, ...others } = user;
console.log(name, age, others); // '张三' 25 { city: '北京' }

// 解构重命名
const { name: userName, age: userAge } = user;
console.log(userName, userAge); // '张三' 25

// 函数参数解构
function greet({ name, age = 18 }) {
  console.log(`${name} 今年 ${age} 岁`);
}
greet({ name: '李四' }); // '李四 今年 18 岁'
```

### 5.2 扩展运算符

```javascript
// 数组扩展
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// 对象扩展（浅拷贝）
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 }; // { a: 1, b: 2, c: 3, d: 4 }

// 覆盖属性
const updated = { ...obj1, b: 3 }; // { a: 1, b: 3 }

// 数组去重
const arr = [1, 2, 2, 3, 3, 4];
const unique = [...new Set(arr)]; // [1, 2, 3, 4]
```

### 5.3 模板字符串

```javascript
const name = '张三';
const age = 25;

// 基本用法
const message = `你好，我是 ${name}，今年 ${age} 岁`;

// 多行字符串
const html = `
  <div class="card">
    <h3>${name}</h3>
    <p>年龄：${age}</p>
  </div>
`;

// 表达式计算
const total = `总价：${100 * 2} 元`;

// 标签模板
function highlight(strings, ...values) {
  return strings.reduce((result, str, i) => {
    const value = values[i] ? `<strong>${values[i]}</strong>` : '';
    return result + str + value;
  }, '');
}

const highlighted = highlight`用户 ${name} 的年龄是 ${age}`;
// '用户 <strong>张三</strong> 的年龄是 <strong>25</strong>'
```

### 5.4 Symbol 与 BigInt

```javascript
// Symbol：唯一标识符
const id1 = Symbol('id');
const id2 = Symbol('id');
console.log(id1 === id2); // false

// 作为对象属性
const user = {
  [id1]: 'value1',
  [id2]: 'value2',
  name: '张三'
};

// Symbol 属性不可枚举
console.log(Object.keys(user)); // ['name']
console.log(Object.getOwnPropertySymbols(user)); // [Symbol(id), Symbol(id)]

// BigInt：大整数
const bigNumber = 9007199254740991n;
const anotherBig = BigInt(9007199254740991);
console.log(bigNumber + 1n); // 9007199254740992n

// 不可与普通数字混合运算
// bigNumber + 1 // TypeError
```

### 5.5 Optional Chaining & Nullish Coalescing

```javascript
// 可选链操作符（?.）
const user = {
  name: '张三',
  address: {
    city: '北京'
  }
};

// 安全访问嵌套属性
console.log(user.address?.city); // '北京'
console.log(user.contact?.phone); // undefined

// 可选链调用
console.log(user.getAddress?.()?.city); // undefined

// 空值合并操作符（??）
const name = user.name ?? '匿名'; // '张三'
const nickname = user.nickname ?? '匿名'; // '匿名'

// 与 || 的区别
const count = 0;
console.log(count || 10);  // 10（0 是 falsy）
console.log(count ?? 10);  // 0（0 不是 null 或 undefined）
```

---

## 六、模块化

### 6.1 ES Modules

```javascript
// 导出变量、函数、类
// math.js
export const PI = 3.14159;

export function add(a, b) {
  return a + b;
}

export class Calculator {
  add(a, b) { return a + b; }
}

// 默认导出
export default function multiply(a, b) {
  return a * b;
}

// 导入
// main.js
import multiply, { add, PI, Calculator } from './math.js';

// 导入所有
import * as math from './math.js';

// 动态导入
async function loadModule() {
  const { default: multiply } = await import('./math.js');
  console.log(multiply(2, 3));
}
```

### 6.2 CommonJS（Node.js）

```javascript
// 导出
// math.js
const PI = 3.14159;

function add(a, b) {
  return a + b;
}

module.exports = {
  PI,
  add
};

// 或
exports.multiply = function(a, b) {
  return a * b;
};

// 导入
// main.js
const { PI, add } = require('./math');
const multiply = require('./math').multiply;
```

---

## 七、设计模式

### 7.1 单例模式

```javascript
class Database {
  static instance = null;

  constructor() {
    if (Database.instance) {
      return Database.instance;
    }
    this.connection = 'connected';
    Database.instance = this;
  }
}

const db1 = new Database();
const db2 = new Database();
console.log(db1 === db2); // true
```

### 7.2 观察者模式

```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }

  emit(event, data) {
    const callbacks = this.events[event];
    if (callbacks) {
      callbacks.forEach(callback => callback(data));
    }
  }

  off(event, callback) {
    const callbacks = this.events[event];
    if (callbacks) {
      this.events[event] = callbacks.filter(cb => cb !== callback);
    }
  }
}

// 使用
const emitter = new EventEmitter();
emitter.on('click', data => console.log('Clicked:', data));
emitter.emit('click', { x: 100, y: 200 });
```

### 7.3 工厂模式

```javascript
class Car {
  constructor(model) {
    this.model = model;
  }
}

class Bike {
  constructor(model) {
    this.model = model;
  }
}

class VehicleFactory {
  static create(type, model) {
    switch (type) {
      case 'car':
        return new Car(model);
      case 'bike':
        return new Bike(model);
      default:
        throw new Error('Unknown vehicle type');
    }
  }
}

const car = VehicleFactory.create('car', 'Tesla');
const bike = VehicleFactory.create('bike', 'Yamaha');
```

---

## 八、性能优化

### 8.1 防抖与节流

```javascript
// 防抖（debounce）：延迟执行，重复触发重置计时器
function debounce(fn, delay) {
  let timer = null;
  return function(...args) {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}

// 使用场景：搜索框输入
const search = debounce(function(keyword) {
  console.log('搜索:', keyword);
}, 300);

// 节流（throttle）：固定时间间隔执行
function throttle(fn, delay) {
  let last = 0;
  return function(...args) {
    const now = Date.now();
    if (now - last >= delay) {
      last = now;
      fn.apply(this, args);
    }
  };
}

// 使用场景：滚动事件
const handleScroll = throttle(function() {
  console.log('滚动位置:', window.scrollY);
}, 100);
```

### 8.2 内存泄漏防范

```javascript
// 1. 意外的全局变量
// ❌ 错误
function fn() {
  data = 'global variable'; // 创建了全局变量
}

// ✅ 正确
function fn() {
  const data = 'local variable';
}

// 2. 未清理的定时器
// ❌ 错误
function startTimer() {
  setInterval(() => {
    console.log('tick');
  }, 1000);
}

// ✅ 正确
function startTimer() {
  const timer = setInterval(() => {
    console.log('tick');
  }, 1000);

  return function cleanup() {
    clearInterval(timer);
  };
}

const cleanup = startTimer();
// 组件卸载时调用 cleanup()

// 3. 闭包引用
function createHandler() {
  const largeData = new Array(1000000).fill('data');

  return function() {
    // 仅在需要时访问 largeData
    console.log(largeData.length);
  };
}
```

---

## 九、常见问题与避坑指南

### ❌ 问题 1：比较运算

```javascript
// ❌ 使用 ==
console.log(0 == false);   // true
console.log('' == 0);      // true
console.log(null == undefined); // true

// ✅ 使用 ===
console.log(0 === false);  // false
console.log('' === 0);     // false
console.log(null === undefined); // false
```

### ❌ 问题 2：变量声明

```javascript
// ❌ 使用 var
var name = '张三';
if (true) {
  var name = '李四'; // 覆盖外层变量
}

// ✅ 使用 let/const
let age = 25;
if (true) {
  let age = 30; // 块级作用域，不冲突
}
```

### ❌ 问题 3：异步循环

```javascript
// ❌ 错误：var 的作用域问题
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 输出：3 3 3
}

// ✅ 方案 1：使用 let
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 输出：0 1 2
}

// ✅ 方案 2：闭包
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(() => console.log(j), 100);
  })(i);
}
```

---

## 十、总结与记忆要点

### 🎯 核心记忆口诀

```
执行上下文分三种
作用域链往上找
this 指向四规则
原型链继承共享
异步编程事件循环
Promise 链式调用
async await 最优雅
函数式编程纯函数
高阶函数复用强
ES6 新特性多记
解构扩展模板串
模块化 import export
设计模式单例观察者
性能优化防抖节流
```

### 📚 关键技术点

1. **执行机制**：执行上下文、变量提升、作用域链、闭包
2. **this 指向**：默认、隐式、显式、new 绑定
3. **原型链**：`__proto__`、`prototype`、继承
4. **异步**：Event Loop、Promise、async/await
5. **函数式**：高阶函数、柯里化、函数组合
6. **ES6+**：解构、扩展、模板字符串、可选链
7. **模块化**：ES Modules、CommonJS
8. **优化**：防抖、节流、内存泄漏防范

---

## 十一、实战练习建议

1. 手写 Promise 的核心方法（then、catch、all、race）
2. 实现一个发布订阅模式的事件总线
3. 用 async/await 重构一个 Promise 链式调用
4. 手写防抖和节流函数
5. 实现深拷贝函数（考虑循环引用）

---

**下一步学习**：👉 [TypeScript 类型系统](../02-TypeScript/类型系统.md)
