[[js]]









### 接口 - interface

#### 与class
interface只进行约束，class能实例化和包含实现逻辑。

|interface|class|
|---|---|
|能 new 吗|❌ 不能|✅ `new Foo()`|
|有方法实现吗|❌ 只有签名|✅ 有实际逻辑|
|编译后存在吗|❌ 被删掉|✅ 变成 JS 代码|
|能 implements 吗|✅ 被 class 实现|✅ 实现 interface|
|能 extends 吗|✅ 继承另一个 interface|✅ 继承另一个 class|
```js
// interface —— 只是"形状说明书"
interface Person {
    name: string;
    age: number;
    greet(): void;  // 只说有这个方法的签名，不写具体逻辑
}

// class —— 是"工厂"，真能生产对象
class Employee implements Person {
    name: string;
    age: number;
    department: string;

    constructor(name: string, age: number, dept: string) {
        this.name = name;
        this.age = age;
        this.department = dept;
    }

    greet() {
        console.log(`我是${this.name}，来自${this.department}`);  // 有具体逻辑
    }
}

const zhangsan = new Employee('张三', 25, '研发部');
zhangsan.greet();  // "我是张三，来自研发部"

```



### 泛型
把类型当参数传

```js
// 不用泛型 —— 写死类型，每个类型要单独写一个函数
function getFirstNumber(arr: number[]): number {
    return arr[0];
}
function getFirstString(arr: string[]): string {
    return arr[0];
}
// 来一个新类型就要复制粘贴一个函数，蠢

// 用泛型 —— 类型当参数传
function getFirst<T>(arr: T[]): T {
    return arr[0];
}

getFirst<number>([1, 2, 3]);     // T = number，返回 number
getFirst<string>(['a', 'b']);    // T = string，返回 string
getFirst<User>([user1, user2]);  // T = User，   返回 User

```

#### 泛型约束
```js
// 不加约束：T 可以是任何东西
function logLength<T>(item: T): void {
    console.log(item.length);  // ❌ 报错！T 不一定有 .length
}

// 加约束：T 必须有 length 属性
function logLength<T extends { length: number }>(item: T): void {
    console.log(item.length);  // ✅ 安全
}

logLength('hello');     // ✅ string 有 length
logLength([1, 2, 3]);   // ✅ 数组有 length
logLength(123);          // ❌ number 没有 length，编译报错

```

#### 什么时候显式写泛型参数？
```js
// 1. 初始值是 null/undefined，之后才赋值 —— 得告诉它最终类型
const user = ref<User | null>(null);   // 不能不写，不然 TS 以为永远 null
const list = ref<Item[]>([]);          // 不写的话 TS 推断为 never[]

// 2. 联合类型 —— 初始值只是其中一种可能
const status = ref<'idle' | 'loading' | 'done'>('idle');

// 3. 泛型约束 —— 限定传入对象的形状
const form = ref<LoginForm>({ username: '', password: '' });

```

#### 泛型接口





### 联合类型


```js
// 1. 函数参数多态 —— 你传字符串也行，传对象也行
function getUser(id: number | string) {
    // ...
}
getUser(1);         // ✅
getUser('abc123');  // ✅

// 2. 可空类型 —— 最常见的泛型约束替代
const user = ref<User | null>(null);  // 初始 null，后面赋值 User

// 3. 字面量联合 —— 限制取值
type Status = 'idle' | 'loading' | 'success' | 'error';
let status: Status = 'idle';  // 只能是这四个之一

```

联合后需要收窄：
```js
function printId(id: number | string) {
    console.log(id.toUpperCase());  // ❌ number 没有 toUpperCase
}

// 必须收窄
function printId(id: number | string) {
    if (typeof id === 'string') {
        console.log(id.toUpperCase());  // ✅ 这个分支里 id 确定是 string
    } else {
        console.log(id.toFixed(2));     // ✅ 这个分支里 id 确定是 number
    }
}

```









































































































