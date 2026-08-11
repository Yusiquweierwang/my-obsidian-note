
















#### 解构赋值
1.把对象或数组拆开，一次性取出想要的值，不用一个一个点出来。

```js
const user = { name: '张三', age: 25, role: '前端', city: '北京' };

// ❌ 老写法：一个一个取
const name1 = user.name;
const age1 = user.age;
const role1 = user.role;

// ✅ 解构：一行搞定
const { name, age, role } = user;
console.log(name);  // '张三'
console.log(age);   // 25

//重命名
const user = { name: '张三', role: '前端' };

// : 后面是新变量名
const { name: userName, role: userRole } = user;
console.log(userName);  // '张三' —— 用的是新名字
console.log(userRole);  // '前端'

//常见场景：接口返回的字段名跟代码里的变量名冲突
const res = { data: [...], status: 200 };
const { data: userList, status: code } = res;
// 避免跟当前作用域里已有的 data、status 重名

```

嵌套解构
```js
const project = {
    name: '数字孪生平台',
    owner: {
        name: '张三',
        dept: { id: 3, name: '研发部' },
    },
};

// 一路点到深层
const {
    owner: {
        name: ownerName,
        dept: { name: deptName },
    },
} = project;

console.log(ownerName);  // '张三'
console.log(deptName);   // '研发部'
```

数组解构
```js
const colors = ['red', 'green', 'blue'];

const [first, second, third] = colors;
console.log(first);   // 'red'
console.log(second);  // 'green'

// 跳过不想要的
const [a, , c] = colors;
console.log(a, c);    // 'red' 'blue'

// 默认值
const [x = '备选'] = [];
console.log(x);       // '备选'

// 交换两个变量，不需要中间变量
let a1 = 1, b1 = 2;
[a1, b1] = [b1, a1];
console.log(a1, b1);  // 2, 1

```

3.最常用-函数参数解构
```js
// ❌ 以前：参数不直观，顺序不能错
function createMarker(id, lng, lat, color) { ... }
createMarker(1, 116.397, 39.908, 'red');  // 忘了第三个是 lat 还是 color

// ✅ 解构：参数名明确，顺序无所谓，还能设默认值
function createMarker({ id, lng, lat, color = 'blue' }) {
    console.log(id, lng, lat, color);
}
createMarker({ color: 'red', lng: 116.397, id: 1, lat: 39.908 });
// 顺序完全打乱也正确

```




##### 展开运算符




