[[vue]]



### 学习路线
**▎JavaScript 学习路线**

按依赖关系排列，先学完前面的才能理解后面的：

---

**▎第一阶段：地基（1-2 周）**

**· 1. 变量与数据类型**

- `const` / `let` 的区别和选择
- 7 种基本类型：string, number, boolean, null, undefined, bigint, symbol
- `typeof` 的坑（`typeof null === 'object'`）
- `==` vs `===`（为什么永远用 `===`）

**· 2. 运算符与表达式**

- 算术、比较、逻辑、三元运算符
- 类型转换规则（`'5' + 3` vs `'5' - 3`）
- 短路求值：`&&`、`||`、`??`

**· 3. 字符串（已完成 ✓）**

- 模板字符串、常用方法、正则基础

**· 4. 数组**

- 增删改：`push`、`pop`、`shift`、`unshift`、`splice`
- 遍历：`for...of`、`forEach`
- 三大金刚：`map`、`filter`、`reduce`
- 查找：`find`、`findIndex`、`includes`、`some`、`every`
- 排序：`sort`（注意默认按字符串排序的坑）

**· 5. 对象**

- 创建、访问（`.` 和 `[]`）、属性的增删改
- 遍历：`Object.keys()`、`Object.values()`、`Object.entries()`
- 判断属性存在：`in`、`hasOwnProperty`、可选链 `?.`

---

**▎第二阶段：逻辑与函数（1 周）**

**· 6. 条件与循环**

- `if/else`、`switch`
- `for`、`while`、`for...of`（数组）、`for...in`（对象）
- `break`、`continue`

**· 7. 函数**

- 声明 vs 表达式 vs 箭头函数，三者的 `this` 区别
- 参数：默认值、rest 参数（`...args`）、解构参数
- 高阶函数：函数作为参数传递、函数作为返回值
- 闭包（理解"函数记住外部变量"）

---

**▎第三阶段：高级核心（2 周）**

**· 8. 解构与展开**

- 对象解构、数组解构、重命名、默认值
- 展开运算符（`...`）用于合并、复制
- rest 参数

**· 9. 模块化**

- ES Module：`import` / `export`
- 命名导出 vs 默认导出
- 动态导入 `import()`

**· 10. 异步编程（重点难点）**

- 事件循环（Event Loop）、宏任务/微任务
- Promise：三种状态、`then`/`catch`/`finally`
- `async/await`（现代写法）
- `Promise.all` / `Promise.race` / `Promise.allSettled`
- 错误处理：`try/catch`

---

**▎第四阶段：浏览器环境（1-2 周）**

**· 11. DOM 操作**

- 查询元素：`querySelector` / `getElementById`
- 修改内容、属性、样式
- 创建/插入/删除节点
- 事件监听：`addEventListener`、事件委托、事件冒泡

**· 12. Web API 入门**

- `fetch` 请求（GET / POST）
- `localStorage` / `sessionStorage`
- `setTimeout` / `setInterval`

---

**▎第五阶段：实战与工程化（持续）**

**· 13. 面向对象（了解即可）**

- `class` 语法糖、`constructor`、`extends`
- 原型链概念（面试常考，实际开发少写）

**· 14. 工程化工具**

- ESLint + Prettier（项目已配好）
- npm 基本命令
- Git 基本操作

---

**▎学习路径图**

```
变量/类型 → 运算符 → 字符串 → 数组 → 对象
                                ↓
                          条件/循环 → 函数
                                ↓
                          解构/展开 → 模块化
                                ↓
                          异步编程（最难）
                                ↓
                          DOM → Web API
                                ↓
                           CesiumJS 实战
```
























### 数据类型


#### const / let
1.`const`：声明时赋值后，**不能再指向别的值** `let`：随时可以重新赋值

2.注意
`const` 锁的是**绑定**（变量名指向谁），不是**值的内容**。对象和数组是引用类型，所以：

```js
const user = { name: '张三' };
user.name = '李四';             // ✅ 可以的！修改的是对象内部，不是绑定
user.age = 25;                  // ✅ 也可以加新属性
// user = { name: '王五' };    // ❌ 不行！这是在改变绑定

const arr = [1, 2, 3];
arr.push(4);                    // ✅ 可以向数组里加东西
arr[0] = 99;                    // ✅ 可以修改已有元素
// arr = [5, 6, 7];            // ❌ 不行！这是在改变绑定
```

**· 一个类比帮你记住**

```
const 是一把锁住的门牌号：你永远住这个地址，但屋里家具可以随便换
let  是一把可换的门牌号：你可以搬到新地址去住
```



#### string

1.创建字符串-三种字符串形式：
```js
const a = 'hello';            // 单引号 — 最常用（项目规范也是单引号）
const b = "hello";            // 双引号 — HTML 属性用得多
const c = `hello`;            // 反引号 — 支持插值和换行
```
2.模版字符串
```js
const name = '张三';
const age = 25;

// 插值：${} 里可以放任何 JS 表达式
const msg = `我叫${name}，明年${age + 1}岁`;
// → "我叫张三，明年26岁"

// 多行：不需要 \n，直接换行
const html = `
  <div>
    <p>多行字符串</p>
  </div>
`;
```

3.方法

|方法|作用|示例|结果|
|---|---|---|---|
|`.length`|获取长度|`'hello'.length`|`5`|
|`.toUpperCase()`|转大写|`'hi'.toUpperCase()`|`'HI'`|
|`.toLowerCase()`|转小写|`'HI'.toLowerCase()`|`'hi'`|
|`.trim()`|去首尾空格|`' a '.trim()`|`'a'`|
|`.includes(sub)`|是否包含子串|`'hello'.includes('ll')`|`true`|
|`.startsWith(sub)`|是否以某串开头|`'hello'.startsWith('he')`|`true`|
|`.endsWith(sub)`|是否以某串结尾|`'hello'.endsWith('lo')`|`true`|
|`.indexOf(sub)`|子串首次出现位置|`'hello'.indexOf('l')`|`2`（找不到返回 -1）|
|`.slice(a, b)`|截取 [a, b)|`'hello'.slice(1, 4)`|`'ell'`|
|`.split(sep)`|按分隔符切割|`'a,b,c'.split(',')`|`['a','b','c']`|
|`.replace(a, b)`|替换**首个**匹配|`'a-a'.replace('-', '')`|`'aa-a'`|
|`.replaceAll(a, b)`|替换**全部**匹配|`'a-a'.replaceAll('-', '')`|`'aa'`|

4.注意


#### 引用和值
原始值：primitive
```js
number string boolean undefined symbol null bigint
```


引用值：共用一个地址
+ ```javascript
object、array、function、Date、Map、Set...
```



#### 数组
1.创建数组
```js
const arr1 = [1, 2, 3];              // 字面量 — 最常用
const arr2 = new Array(3);           // [empty × 3] — 不推荐，容易踩坑
const arr3 = Array.from('abc');      // ['a', 'b', 'c'] — 类数组转数组
const arr4 = [...'abc'];             // ['a', 'b', 'c'] — 展开方式
```
2.增删改
```js
const arr = [2, 3, 4];

// 尾部操作（常用）
arr.push(5);           // [2, 3, 4, 5]    末尾追加
arr.pop();             // [2, 3, 4]       末尾删除，返回被删的 5

// 头部操作（少用，性能差：整个数组要挪位置）
arr.unshift(1);        // [1, 2, 3, 4]    头部插入
arr.shift();           // [2, 3, 4]       头部删除

// 中间操作（splice 是万能刀）
arr.splice(1, 1);      // [2, 4]          从索引1删1个
arr.splice(1, 0, 99);  // [2, 99, 4]      从索引1删0个，插入99
arr.splice(1, 1, 88);  // [2, 88, 4]      从索引1删1个，替换为88
```
3.查找和判断
```js
const arr = [1, 2, 3, 4, 5];

// 判断是否存在
arr.includes(3);               // true
arr.indexOf(3);                // 2（不存在返回 -1）

// 找第一个满足条件的元素
arr.find(n => n > 3);          // 4（只返回元素本身）
arr.findIndex(n => n > 3);     // 3（返回索引）

// 全局判断
arr.some(n => n > 4);          // true  有一个满足就行
arr.every(n => n > 0);         // true  全部满足才行
```
**4.遍历4种写法和选择**
```js 
const arr = ['a', 'b', 'c'];

// for...of — 需要 break/continue 时用
for (const item of arr) {
    if (item === 'b') continue;
    console.log(item);
}

// forEach — 只需执行副作用（打日志、发请求）时用
arr.forEach((item, index) => {
    console.log(`${index}: ${item}`);
});

// 普通 for — 需要精确控制索引或反向遍历时用
for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
}

// for...in — ❌ 数组不要用，它遍历的是键名（字符串），还会带上原型链
```
#### map
映射，一对一
```js
const nums = [1, 2, 3];

nums.map(n => n * 2);              // [2, 4, 6]
nums.map(n => `<li>${n}</li>`);    // ['<li>1</li>', '<li>2</li>', '<li>3</li>']
nums.map((n, i) => `${i}:${n}`);   // ['0:1', '1:2', '2:3']
```
#### filter
```js
const nums = [1, 2, 3, 4, 5, 6];

nums.filter(n => n % 2 === 0);     // [2, 4, 6]
nums.filter(n => n > 3);           // [4, 5, 6]

// 实战：过滤掉无效数据
const raw = [0, '', null, undefined, 'hello', false, 42];
raw.filter(Boolean);               // ['hello', 42]  巧用 Boolean 构造函数去假值
```
#### reduce
```js
const nums = [1, 2, 3, 4, 5];

// 语法：reduce((累积值, 当前值) => 新累积值, 初始值)
nums.reduce((sum, n) => sum + n, 0);       // 15（求和）
nums.reduce((max, n) => n > max ? n : max, -Infinity);  // 5（最大值）

// 更常见：把数组转成对象
const users = [
    { id: 1, name: '张三' },
    { id: 2, name: '李四' },
];
const byId = users.reduce((map, u) => {
    map[u.id] = u;
    return map;
}, {});
// { 1: {id:1, name:'张三'}, 2: {id:2, name:'李四'} }
```

#### 正则表达式


### 对象


#### 遍历对象
1.Object.keys() - 键名数组
2.Object.values()
3.Object.entries() - 


#### 对象和引用


#### 构造函数



```js
class User {
    constructor(name, age) {   // ← 等价于构造函数体
        this.name = name;
        this.age = age;
    }

    sayHi() {                   // ← 自动挂到 User.prototype 上
        console.log(`你好，我是 ${this.name}`);
    }
}

const u = new User('张三', 25);
u.sayHi();  // 你好，我是 张三
```

cesium中都是：
```js
// viewer 就是 new 出来的
const viewer = new Cesium.Viewer('cesiumContainer');

// 坐标点
const position = Cesium.Cartesian3.fromDegrees(116.397, 39.908);
// ↑ fromDegrees 内部帮你 new 了 Cartesian3

// 颜色
const red = new Cesium.Color(1.0, 0.0, 0.0, 1.0);
```

#### 对象继承

1.老写法（不用）Object.create()
```js
const Animal =  {
	
	};
const dog = Object.create(Animal);
```

2.构造函数+ prototype -> ES5写法
```js
// 1. 构造函数（首字母大写只是约定）
function Animal(name) {
    this.name = name;
    this.type = '动物';
}

// 2. 在 prototype 上放共享方法
Animal.prototype.breathe = true;
Animal.prototype.describe = function() {
    return `我是${this.type}，需要呼吸：${this.breathe}`;
};

// 3. new 一个实例
const dog = new Animal('旺财');
dog.name;         // '旺财'   ← 自身属性
dog.breathe;      // true     ← 来自 prototype
dog.describe();   // '我是动物，需要呼吸：true'  ← 来自 prototype

```

3.class - **vue中基本不用class** 
```js
class Animal {
    // 构造函数（new 的时候自动调用）
    constructor(name) {
        this.name = name;
        this.type = '动物';
    }

    // 方法自动放到 Animal.prototype 上
    breathe = true;
    describe() {
        return `我是${this.type}，名字叫${this.name}`;
    }
}

// extends 实现父子继承
class Dog extends Animal {
    constructor(name, breed) {
        super(name);               // 必须先调用父类 constructor
        this.breed = breed;        // 然后才可以用 this
    }

    bark() {
        return `${this.name}说：汪！`;
    }

    // 重写父类方法
    describe() {
        return `${super.describe()}，品种是${this.breed}`;
        //       ↑ 调用父类的 describe
    }
}

const dahuang = new Dog('大黄', '中华田园犬');
dahuang.name;        // '大黄'        ← 父类 constructor 设置的
dahuang.breed;       // '中华田园犬'   ← 自身 constructor 设置的
dahuang.bark();      // '大黄说：汪！'  ← 自身方法
dahuang.describe();  // '我是动物，名字叫大黄，品种是中华田园犬'
dahuang.breathe;     // true          ← 来自父类

```

4.vue中写法
```js
// Vue 2 Options API（还是主流，对象字面量，不是 class）
export default {
    data() {
        return { name: '', list: [] };
    },
    methods: {
        handleClick() { /* ... */ },
    },
    mounted() {
        this.fetchData();
    },
};

// Vue 3 Composition API（新项目的主流写法）
<script setup>
import { ref, onMounted } from 'vue';

const name = ref('');
const list = ref([]);

function handleClick() {
    name.value = '点击了';
}

onMounted(() => {
    fetchData();
});
</script>

```



### 函数
1.创建函数
```js
// 1. 函数声明（会"提升"到作用域顶部）
function add(a, b) {
    return a + b;
}

// 2. 函数表达式（必须先定义再调用）
const add = function(a, b) {
    return a + b;
};

// 3. 箭头函数（最简洁，但有 this 区别）
const add = (a, b) => a + b;
```
参数
```js
// 默认值 — 不传或传 undefined 时生效
function greet(name = '世界') {
    return `你好，${name}！`;
}
greet();          // '你好，世界！'
greet('张三');    // '你好，张三！'
greet(undefined); // '你好，世界！'
greet(null);      // '你好，null！'  ← null 不会触发默认值

// rest 参数 — 收集剩余参数为数组
function sum(...nums) {
    return nums.reduce((s, n) => s + n, 0);
}
sum(1, 2, 3, 4);  // 10
sum(10, 20);      // 30

// 解构参数 — 直接拆对象
function printUser({ name, age, city = '未知' }) {
    return `${name}，${age}岁，来自${city}`;
}
printUser({ name: '张三', age: 25 });
// → '张三，25岁，来自未知'

```

#### 高阶函数
```js
// ① 函数作为参数（回调）
function process(items, handler) {
    return items.map(handler);
}
const doubled = process([1, 2, 3], n => n * 2);
// [2, 4, 6]

// ② 函数作为返回值（工厂函数）
function createGreeter(greeting) {
    return function(name) {
        return `${greeting}，${name}！`;
    };
}
const sayHi = createGreeter('你好');
const sayHello = createGreeter('Hello');

sayHi('张三');      // '你好，张三！'
sayHello('John');   // 'Hello，John！'

// ③ 箭头函数版工厂（更简洁）
const createGreeter = greeting => name => `${greeting}，${name}！`;
// greeting → (name → 拼接字符串)
```
**闭包**

#### 箭头函数 / 普通函数 this区别
普通函数：this 由"调用者"决定
```js
const user = {
    name: '张三',
    sayName: function() {
        console.log(this.name);
    },
};
user.sayName();    // '张三'  ← this = user（user 调用的）
const fn = user.sayName;
fn();              // undefined ← this = 全局/window（没有调用者）
```
箭头函数：this 由定义位置决定，固定不变。
```js
// 箭头函数：this 由"定义位置"决定，固定不变
const user2 = {
    name: '李四',
    sayName: () => {
        console.log(this.name);
    },
};
user2.sayName();   // undefined ← this 不在 user2，而是定义时的外层

// 箭头函数最实用的场景：回调中 this 不丢失
const user3 = {
    name: '王五',
    hobbies: ['打球', '看书'],
    printHobbies: function() {
        // 普通回调函数的 this 会丢失
        this.hobbies.forEach(function(h) {
            // console.log(this.name);  // ❌ 这里的 this 丢失了
        });
        // 箭头函数自动取外层 this
        this.hobbies.forEach(h => {
            console.log(`${this.name} 喜欢 ${h}`);  // ✅ this 就是 user3
        });
    },
};
user3.printHobbies();
// 王五 喜欢 打球
// 王五 喜欢 看书
```

验证：
```js
console.log(this);             // Window 对象

const obj = {
    arrow: () => {
        console.log(this);     // 还是 Window
    },
    normal: function() {
        console.log(this);     // obj 自己
    },
};

obj.arrow();   // Window    ← 箭头函数，this 穿透到全局
obj.normal();  // obj       ← 普通函数，this 是点号左边的 obj
```

### 闭包

```js
function createCounter() {
    let count = 0;                    // ① 内部变量
    return function() {               // ② 返回一个函数
        count++;                      // ③ 内部函数访问外部变量
        return count;
    };
}

const counter = createCounter();
counter();  // 1
counter();  // 2
counter();  // 3
```







#### 深拷贝/浅拷贝


```js
// 1. 直接赋值 — 同一个人
const a = { name: '张三', addr: { city: '北京' } };
const b = a;
b.addr.city = '上海';
console.log(a.addr.city);   // '上海' — 没法独立

// 2. 浅拷贝 — 只复印第一页
const c = { name: '张三', addr: { city: '北京' } };
const d = { ...c };
d.addr.city = '上海';
console.log(c.addr.city);   // '上海' — addr 还是共享的

// 3. 深拷贝 — 全部复印
const e = { name: '张三', addr: { city: '北京' } };
const f = structuredClone(e);
f.addr.city = '上海';
console.log(e.addr.city);   // '北京' — 完全独立了

```


### 异步

#### 事件循环
反复检查"调用栈空了没？空了就去任务队列取一个任务来执行"。
```
┌─────────────────┐
│  调用栈          │  ← JS 正在执行的代码（一次只能执行一个函数）
│  (Call Stack)   │
└────────┬────────┘
         │ 遇到异步操作时，外包出去
         ↓
┌─────────────────┐
│  Web APIs       │  ← 浏览器提供的计时/网络/事件监听线程
│  (浏览器提供)     │     setTimeout、fetch、addEventListener 都在这里倒计时
└────────┬────────┘
         │ 倒计时/请求完成，回调塞进队列
         ↓
┌─────────────────────────────┐
│  任务队列                     │
│  ┌───────────┐ ┌──────────┐ │
│  │ 微任务队列  │ │ 宏任务队列 │ │
│  │ Promise   │ │ setTimeout│ │
│  │ .then     │ │ 事件回调   │ │
│  │ await后面  │ │ fetch回调 │ │
│  └───────────┘ └──────────┘ │
└─────────────────────────────┘
         ↑
         事件循环不停地把队列里的任务搬到调用栈里去执行

```

**执行规则：宏任务 → 清空微任务 → 渲染 → 取下一个宏任务**

每一轮循环的顺序是固定的：

```
① 取一个宏任务（整体 script、setTimeout 回调、事件回调...）
② 执行这个宏任务里的所有同步代码
③ 同步代码跑完，清空所有微任务（包括微任务里新产生的微任务）
④ 浏览器可能在这一刻渲染
⑤ 取下一个宏任务，回到 ①
```

#### 回调函数 - 原始异步
```js
console.log('开始');

setTimeout(()=>{
	console.log('2秒后执行');
	},
	2000});
	
cosole.log('结束');
// 实际输出顺序：
// ① 开始
// ③ 结束
// ② 2秒后执行        ← 虽然写在前面，但最后才输出

```
`setTimeout` 不阻塞代码。它把回调函数交给浏览器计时，自己立刻返回，继续执行后面的 `console.log('③ 结束')`。

#### Promise
1.Promise 是一个容器，包裹一个"将来才会有结果"的操作。它有三种状态：

```
pending     进行中（还没有结果）
fulfilled   已成功（resolve 被调用）
rejected    已失败（reject 被调用）
```

```js
// Promise 构造函数接收一个函数，这个函数接收 resolve 和 reject 两个参数
const promise = new Promise((resolve, reject) => {
    // 耗时操作
    setTimeout(() => {
        const success = true;        // 模拟：成功或失败
        if (success) {
            resolve('数据获取成功');   // 调用 resolve → 状态变成 fulfilled
        } else {
            reject('网络错误');        // 调用 reject → 状态变成 rejected
        }
    }, 2000);
});

// 使用方
promise
    .then((data) => {
        console.log('✅', data);      // resolve 传过来的值
    })
    .catch((error) => {
        console.log('❌', error);     // reject 传过来的值
    })
    .finally(() => {
        console.log('无论成败都执行');
    });

```

2.resolve和reject原理
```js
new Promise((resolve, reject) => {
    //          ↑         ↑
    //     这两个参数是函数，JS 引擎帮你创建好，自动传进来
});
```
写的是回调函数 `(resolve, reject) => { ... }`。当 `new Promise(...)` 执行时，JS 引擎内部做了两件事：
```js
// JS 引擎内部（简化版示意，不是你写的代码）
function Promise(executor) {
    const resolve = function(value) {   // 引擎创建 resolve 函数
        // 把 Promise 状态改成 fulfilled
        // 把 value 存起来，等 .then 来取
    };

    const reject = function(reason) {   // 引擎创建 reject 函数
        // 把 Promise 状态改成 rejected
        // 把 reason 存起来，等 .catch 来取
    };

    executor(resolve, reject);          // 把你的回调叫过来，把两个函数传进去
}
```

`then（）`可以链式调用，把嵌套拉平：
```js
// 改写回调地狱——每一步返回一个新的 Promise
getUser(1)
    .then(user => getOrders(user.id))
    .then(orders => getOrderDetail(orders[0].id))
    .then(detail => getProduct(detail.productId))
    .then(product => console.log(product))
    .catch(error => console.log('某一步出错了', error));
// 从 > 形状变成了 | 形状，任何一步出错都会被最后的 catch 抓住
```

#### async / await 
`async` 标记一个函数是异步的，`await`等待一个Promise结果。
```js
// 用 Promise 链
function getProductInfo() {
    return getUser(1)
        .then(user => getOrders(user.id))
        .then(orders => getOrderDetail(orders[0].id));
}

// 用 async/await — 逻辑流一目了然
async function getProductInfo() {
    const user = await getUser(1);
    const orders = await getOrders(user.id);
    const detail = await getOrderDetail(orders[0].id);
    return detail;
}
```
`await` 的意思是：**在这里暂停，等 Promise 出结果，把结果赋给左边的变量，再继续往下执行**。它不阻塞主线程，只暂停当前 async 函数内部。

**async 函数的返回值自动包装成 Promise：**
```js
async function greet() {
    return '你好';
    // 等价于 return Promise.resolve('你好');
}

greet().then(msg => console.log(msg));  // '你好'
// 所以调用方也可以用 await：const msg = await greet();

```

命名只是个约定
```js
// 这两个参数只是形参名，叫什么都可以
new Promise((ok, fail) => {
    ok('成功');
});

new Promise((done, error) => {
    done('完成');
});

new Promise((y, n) => {
    y('yes');
});

// 行为完全一样，但约定俗成用 resolve / reject
```

#### 并行请求 - Promise.all





#### GIS-例
```js
// 真实版本的思路：
function fetch3DTilesUrl(buildingId) {
    return new Promise(resolve => {
        // buildingId 参与构造请求 URL
        fetch(`https://api.example.com/buildings/${buildingId}/tiles`)
        //          使用 buildingId 在这里 ↑
            .then(res => res.json())
            .then(data => {
                resolve(data);   // resolve 的数据是服务器根据 buildingId 返回的
            });
    });
}

// 调用：
fetch3DTilesUrl('building-001');  // 请求 building-001 的模型
fetch3DTilesUrl('building-002');  // 请求 building-002 的模型（不同建筑，不同数据）

```
改造成async/await写法：
```js
async function fetch3DTilesUrl(buildingId) {
    await delay(800);   // 等 800ms
    return { url: `https://api.example.com/buildings/${buildingId}/tileset.json`, version: '1.2' };
    //     ↑ async 函数的 return 自动包装成 Promise
}
```








### Web API
`web api`通过HTTP协议暴露出来的接口。在浏览器发一个网络请求，服务器返回数据。

#### 基本形式
```
https://api.weather.com/v1/current?city=beijing

拆开看：
  https://                    ← 协议（HTTP 的安全版）
  api.weather.com             ← 域名（服务器在哪）
  /v1/current                 ← 路径（要调哪个功能）
  ?city=beijing               ← 参数（给什么条件）

```

#### API
接口。


```
JavaScript 语言本身 → 变量、函数、数组、Promise...（ECMAScript 标准）
Web API              → fetch、DOM、localStorage、定时器...（浏览器提供）
```



#### 常用 - GET / POST / PUT / PATCH / DELETE
| 方法     | 语义           | 类比         |
| ------ | ------------ | ---------- |
| POST   | **新建**一个资源   | 往数据库插一行新记录 |
| PUT    | **整体替换**一个资源 | 把整行记录全部覆盖  |
| PATCH  | **局部修改**一个资源 | 只改其中几个字段   |
| DELETE | **删除**一个资源   | 删掉那一行      |
|        |              |            |
|        |              |            |

应用场景
```
用户管理页面：

┌──────────────────────────────────────┐
│  获取用户列表    GET  /api/users      │  ← 页面加载时
│  查看某个用户    GET  /api/users/3    │  ← 点"详情"
│  创建新用户      POST /api/users      │  ← 点"新增"，填表单，提交
│  编辑用户（全量） PUT  /api/users/3    │  ← 点"编辑"，改表单，提交
│  只改角色        PATCH /api/users/3   │  ← 下拉框切换角色，即时保存
│  删除用户        DELETE /api/users/3  │  ← 点"删除"，确认，消失
└──────────────────────────────────────┘

```

|操作|HTTP 方法|例子|
|---|---|---|
|查数据|GET|`fetch('/api/users/1')` 获取用户信息|
|新建|POST|`fetch('/api/users', {method:'POST', body:...})` 创建新用户|
|更新|PUT / PATCH|`fetch('/api/users/1', {method:'PUT', body:...})` 修改用户信息|
|删除|DELETE|`fetch('/api/users/1', {method:'DELETE'})` 删用户|

2.用户表举例
```
当前数据库里有一条：
{ id: 1, name: "张三", email: "zhang@test.com", role: "前端" }
```


#### 状态码
```
2xx  成功        你要的东西在这
3xx  重定向      你要的东西搬家了
4xx  客户端错误   你写错了 / 你没权限 / 你要的不存在
5xx  服务器错误   服务器自己炸了

```

|码|名称|什么时候出现|
|---|---|---|
|200|OK|正常返回数据，最常见的|
|201|Created|POST 新建成功，服务器返回了新建的东西|
|204|No Content|操作成功但没东西返回（DELETE 后常见，删完了没东西给你看）|
|301|Moved Permanently|这个接口的地址永久换了，浏览器会自动跳|
|304|Not Modified|内容没变过，用你本地缓存的就行|
|400|Bad Request|你发过去的参数不对（格式错、少字段）|
|401|Unauthorized|没登录或 token 过期|
|403|Forbidden|登录了但没权限（你不是管理员不能删这个）|
|404|Not Found|URL 路径写的没这东西|
|405|Method Not Allowed|这是个 GET 接口你用 POST 打|
|422|Unprocessable Entity|参数格式对但内容不合法（邮箱格式不对、年龄负数）|
|429|Too Many Requests|请求太快被限流了|
|500|Internal Server Error|服务器代码崩了|
|502|Bad Gateway|网关/代理转发时上游挂了|
|503|Service Unavailable|服务器在维护/过载|
|504|Gateway Timeout|上游响应太慢，超时了|
```js
const res = await fetch('/api/users/1');

if (res.status === 200) {
    const data = await res.json();
    // 正常处理
} else if (res.status === 401) {
    // token 过期，跳登录
    window.location.href = '/login';
} else if (res.status === 403) {
    alert('你没权限操作这个');
} else if (res.status === 404) {
    alert('该用户不存在');
} else if (res.status >= 500) {
    alert('服务器出了点问题，稍后再试');
}

//实际上用 `res.ok` 更快：

// res.ok === true  → 状态码 200-299
// res.ok === false → 其他所有情况

if (!res.ok) {
    console.error('请求失败，状态码:', res.status);
    return;
}
const data = await res.json();
```

#### JSON交互
javascript object notation.
1.本质是把JS对象写成字符串。
JSON是一串文本，没有类型、方法、不能user.name直接拿，必须转回JS对象才能用。

```js
// JS 对象（内存中的数据结构）
const user = {
    name: '张三',
    age: 25,
    skills: ['Vue', 'Cesium', 'UE'],
    address: { city: '北京', district: '海淀' },
    isAdmin: false,
};

// 序列化成 JSON 字符串（在网络上传输用）
'{"name":"张三","age":25,"skills":["Vue","Cesium","UE"],"address":{"city":"北京","district":"海淀"},"isAdmin":false}'

```

2.操作—**发请求 、 收响应**

```js
// 1. 发请求 → JS 对象转 JSON 字符串
const body = JSON.stringify({ name: '张三', age: 25 });
//  → '{"name":"张三","age":25}'

// 2. 收响应 → JSON 字符串转 JS 对象
const data = JSON.parse('{"name":"张三","age":25}');
//  → { name: '张三', age: 25 }
console.log(data.name); // "张三"
```

完整请求链路：
```js
// ====== 发：GET（通常不需要 body） ======
const res = await fetch('/api/users/1');
const user = await res.json();   // ← 相当于 JSON.parse(res.body)

// ====== 发：POST（需要 body） ======
await fetch('/api/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },  // ← 告诉后端：我发的 body 是 JSON
    body: JSON.stringify({          // ← JS 对象 → JSON 字符串
        name: '李四',
        role: '后端',
    }),
});

// ====== 收：res.json() 内部做了 JSON.parse ======
// 等价关系：
// const data = await res.json();
// const data = JSON.parse(await res.text());

```


#### 请求头
**请求头（Headers）**

```js
fetch('/api/data', {
    headers: {
        'Content-Type': 'application/json',    // 告诉服务器我发的是 JSON
        'Authorization': 'Bearer xxx',          // 身份令牌
        'X-Request-Id': 'uuid-xxxx',           // 自定义头：追踪请求链路
    },
});
```

常用请求头：

|头|作用|
|---|---|
|`Content-Type`|告诉服务器 body 里是什么格式|
|`Authorization`|身份认证|
|`Accept`|告诉服务器我能接收什么格式的返回|
|`Cache-Control`|缓存策略|
#### 身份认证
```js
// 1. Bearer Token（最主流：登录后拿到 token，每次请求带上）
fetch('/api/user', {
    headers: { 'Authorization': 'Bearer eyJhbGciOi...' },
});

// 2. API Key（公开 API 常用，比如天气/地图服务）
fetch('https://api.map.com/v1/tiles?key=YOUR_API_KEY');

// 3. Cookie/Session（老式做法，浏览器自动带，不需要前端手动处理）
fetch('/api/user', { credentials: 'include' }); // 跨域时手动开启

```

















