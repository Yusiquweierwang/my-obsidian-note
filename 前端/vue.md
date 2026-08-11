



### 基本概念








### 与原生JS对比
|原生 JS 概念|Vue 对应|作用|
|---|---|---|
|`getElementById`|`ref()` + 模板绑定|拿到 DOM 并更新|
|`addEventListener`|`@click` / `@input`|监听事件|
|`JSON.stringify` 拼接 HTML|`v-for`|列表渲染|
|`element.classList.add('hidden')`|`v-if` / `v-show`|条件显示隐藏|
|`element.setAttribute('src', url)`|`:src="url"`|属性绑定|
|`inputElement.value`|`v-model`|表单双向绑定|






### 模版语法
模板语法就是直接在 HTML 里写 `{{ }}` 和 `v-xxx` 来绑定 JS 数据——Vue 帮你把数据翻译成 DOM，你不用再写 `getElementById`。

#### 插值表达式{{}}
```vue
<span>{{count + 1}}</span>
<span>{{message}}<span>

```



#### 属性绑定

**最常见的绑定场景**

|属性|示例|说明|
|---|---|---|
|`:class`|`:class="{ active: isActive }"`|动态 class|
|`:style`|`:style="{ color: textColor }"`|动态行内样式|
|`:src`|`:src="imageUrl"`|图片路径|
|`:href`|`:href="'/user/' + userId"`|链接地址|
|`:disabled`|`:disabled="!isValid"`|表单禁用状态|
|`:title`|`:title="tooltipText"`|鼠标悬停提示|

**· class 绑定（三种写法）**

```html
<!-- 1. 对象写法：key 是类名，value 是布尔值（控制要不要这个类） -->
<div :class="{ active: isActive, 'text-danger': hasError }"></div>

<!-- 2. 数组写法：每个元素是一个类名变量 -->
<div :class="[baseClass, modifierClass]"></div>

<!-- 3. 混合写法 -->
<div :class="['static-class', { active: isActive }]"></div>
```


1.静态属性

```vue
<img src="/images/logo.png" />
<button class="btn-primary">确定</button>
```


2.动态属性
值由JS变量决定
```js
<img :src="currentImage" />
<button :class="themeClass">确定</button>

```

**判断标准：有没有 `:` 前缀**

```html
<!-- 静态：引号里就是最终值 -->
<div title="这是提示文字"></div>

<!-- 动态：引号里是 JS 表达式，Vue 会先算出来再赋给属性 -->
<div :title="tooltipMessage"></div>
```

没有 `:` — 引号里是什么，属性就是什么。 有 `:` — 引号里是 JS 代码，Vue 执行它，把结果赋给属性。

#### 事件绑定
```vue
<button @click="handleClick">点击</button>
<form @submit.prevent="onSubmit">           <!-- 阻止默认提交 -->
<input @keyup.enter="search" />             <!-- 按回车触发 -->
<button @click="count++">                   <!-- 简单逻辑直接写表达式 -->

```

事件修饰符


#### 条件渲染 v-if / v-show
```vue
<div v-if="type === 'A'">A 内容</div>
<div v-else-if="type === 'B'">B 内容</div>
<div v-else>其他</div>

<div v-show="isVisible">频繁切换用这个</div>

```

#### 列表渲染
```vue
<li v-for="item in items" :key="item.id">{{ item.name }}</li>
<li v-for="(item, index) in items" :key="item.id">{{ index }}. {{ item.name }}</li>

```




#### 双向绑定
本质是`:value`+`@input`的语法糖。

```vue
<input v-model="username" />         <!-- 输入框内容，username 实时同步 -->
<input v-model.number="age" />       <!-- 自动转数字 -->
<input v-model.trim="keyword" />     <!-- 去首尾空格 -->
<textarea v-model="content"></textarea>
<select v-model="selected"></select>
<input type="checkbox" v-model="checked" />

```


#### 与JS对比

|你想做的事|原生 JS|Vue 模板|
|---|---|---|
|把变量显示到页面|`el.textContent = msg`|`{{ msg }}`|
|动态改属性|`img.src = url`|`:src="url"`|
|点击执行函数|`el.addEventListener('click', fn)`|`@click="fn"`|
|条件显示|`el.style.display = cond ? '' : 'none'`|`v-if` / `v-show`|
|循环渲染列表|`arr.forEach(...); el.appendChild(li)`|`v-for`|
|输入框取值|`el.value` + `addEventListener('input')`|`v-model`|


### 组件
1.组件概念




2.为何拆组件？
复用 + 隔离

|收益|说明|
|---|---|
|复用|同一个按钮、弹窗到处用，不用复制粘贴 HTML|
|隔离|每个组件有自己的数据和样式，互不污染|
|可维护|页面出 bug，你能定位到具体哪个组件，而不是在一千行代码里找|
|可测试|单个组件可以独立测试|

3.单文件组件

```vue
<!-- MyButton.vue -->
<script setup>
// JS 逻辑：数据、函数
import { ref } from 'vue';
const count = ref(0);
function increment() {
    count.value++;
}
</script>

<template>
    <!-- HTML 模板：结构 -->
    <button @click="increment">点击了 {{ count }} 次</button>
</template>

<style scoped>
/* CSS：样式，scoped 表示只影响这个组件 */
button {
    background: #4a90d9;
    color: white;
    border: none;
    padding: 8px 16px;
    border-radius: 4px;
}
</style>

```

`<script setup>` 是 Vue 3 推荐写法，比旧版 `export default` 简洁很多。`scoped` 是 Vue 自动给这个组件的元素加唯一属性，保证样式不会漏出去影响别的组件。

4.组件树

```
App.vue（根组件）
├── Header.vue（顶部导航）
├── Sidebar.vue（侧边栏）
│   ├── LayerList.vue（图层列表）
│   ├── LayerItem.vue（单个图层，复用多次）
│   └── LegendPanel.vue（图例）
├── MapContainer.vue（地图容器）
│   ├── ToolBar.vue（地图工具栏）
│   └── Popup.vue（地图弹窗）
└── Footer.vue（底部信息栏）

```




#### 组件生命周期
```
组件创建 → 挂载到页面 → 数据更新 → 卸载销毁
   │           │           │           │
   │     onMounted      onUpdated   onUnmounted
   │     （发请求、      （数据变了    （清理定时器、
   │      初始化地图）    之后做啥）    移除事件监听）

```




#### Props
父组件给子组件传数据。

+ props是父传子，子组件只读不改
+ 字符串可以不写`:`，其他类型必须写`:`
+ 对象/数组默认值用`() => ({...})
+ 类型校验写清楚，传错了vue控制台会报警

Props即组件的参数列表。同函数参数一个概念。
```js
// 函数：调用时传参
function greet(name, age) { ... }
greet('张三', 25);

// 组件：使用时传 props
<ChildComp :name="userName" :age="25" />

```

子组件：定义接收哪些props
```vue
<!-- UserCard.vue -->
<script setup>
const props = defineProps({
    name: String,        // 字符串类型
    age: Number,         // 数字类型
    isAdmin: Boolean,    // 布尔类型
    // 复杂类型用大写开头
    hobbies: Array,      // 数组
    config: Object,      // 对象
});
</script>

<template>
    <div class="card">
        <h3>{{ props.name }}</h3>          <!-- 模板里用 props.xxx -->
        <p>年龄：{{ props.age }}</p>
        <p v-if="props.isAdmin">管理员</p>
    </div>
</template>

```

父组件：传值
```vue
<!-- 父组件 -->
<UserCard
    name="张三"
    :age="25"
    :is-admin="true"
    :hobbies="['摄影', '骑行']"
    :config="{ theme: 'dark' }"
/>

```

注意：字符串可以不写 `:`（`name="张三"`），但数字、布尔、数组、对象、变量**必须写 `:`**，否则会被当成字符串。

```html
<!-- ❌ age 是字符串 "25"，不是数字 25 -->
<UserCard age="25" />

<!-- ✅ 冒号告诉 Vue：引号里是 JS 表达式，算出数字 25 -->
<UserCard :age="25" />
```

3.可以带默认值
```vue
<script setup>
const props = defineProps({
    name: {
        type: String,
        required: true,       // 必传，不传 Vue 会在控制台报警
    },
    age: {
        type: Number,
        default: 0,           // 不传就是 0
    },
    role: {
        type: String,
        default: '普通用户',   // 默认字符串
    },
    config: {
        type: Object,
        default: () => ({ theme: 'light' }),  // ⚠️ 对象/数组默认值必须用函数返回
    },
});
</script>

```

**对象和数组的默认值为什么必须用函数**：`default: () => ({ ... })` 而不是 `default: { ... }`。因为后者会让所有实例共享同一个对象引用，一个改了全受影响。函数写法每次调用都返回新对象，互不影响。

4.Props 单向数据流

props是只读的，子组件不能改。
```vue
<script setup>
const props = defineProps({ count: Number });

// ❌ 禁止！Vue 会报警
props.count++;

// ✅ 要改就在子组件内部复制一份
import { ref } from 'vue';
const localCount = ref(props.count);  // 自己维护一个本地副本
function increment() {
    localCount.value++;
}
</script>

```


5.cesium项目为例
```
App.vue（父 — 100 行）
├── PageHeader.vue        — 只管标题栏
├── LayerPanel.vue        — 只管图层的展示和切换
│   └── LayerItem.vue     — 单个图层行
├── CesiumMap.vue         — 只管地图的加载和销毁
├── FeaturePanel.vue      — 只管属性面板
├── TimelineBar.vue       — 只管时间轴
└── SearchModal.vue       — 只管搜索弹窗

```

父组件
```vue
<script setup>
import { ref } from 'vue';
import LayerPanel from './LayerPanel.vue';
import CesiumMap from './CesiumMap.vue';
import FeaturePanel from './FeaturePanel.vue';

// 父组件只存"全局共享"的数据
const layers = ref([...]);
const activeLayerId = ref(null);
const selectedFeature = ref(null);
</script>

<template>
    <PageHeader title="智慧城市大屏" />

    <LayerPanel
        :layers="layers"
        :active-id="activeLayerId"
        @select="activeLayerId = $event"
    />

    <CesiumMap
        :layers="layers"
        :selected-feature="selectedFeature"
        @feature-click="selectedFeature = $event"
    />

    <FeaturePanel :feature="selectedFeature" />
</template>

```


6.与函数区别
**唯一的区别**

函数之间通过**参数和返回值**通信：

```js
const result = process(numbers, callback);  // 传参进去，返回值出来
```

组件之间通过 **props 和 emits** 通信：

```html
<ChildComp :data="list" @update="handleUpdate" />
<!--         传参下去        事件返回上来 -->
```


#### Emits
子组件通知父组件
本质是事件回调。

数据流向：

子组件点击删除 → emit('remove', id) → 父组件 removeLayer(id) → layers 更新 → 自动流回子组件

```vue
<!-- 父组件把处理函数"绑定"到子组件的事件上 -->
<ChildComp @delete="handleDelete" />
<!--       ^^^^^^  ^^^^^^^^^^^^ -->
<!--       事件名   回调函数（父组件定义的） -->

//--------------

<!-- 子组件：在合适的时机"触发"这个事件 -->
<script setup>
const emit = defineEmits(['delete']);
// emit 是一个函数，调用它就等于喊了父组件一声
</script>

<template>
    <button @click="emit('delete', 123)">删除</button>
</template>

```


cesium中例

父组件
```vue
<!-- 父组件 App.vue -->
<script setup>
import { ref } from 'vue';
import LayerItem from './LayerItem.vue';

const layers = ref([
    { id: 1, name: '实景三维', visible: true },
    { id: 2, name: '点云', visible: false },
    { id: 3, name: 'BIM', visible: true },
]);

function toggleLayer(id) {
    const layer = layers.value.find((l) => l.id === id);
    if (layer) {
        layer.visible = !layer.visible;
    }
}

function removeLayer(id) {
    layers.value = layers.value.filter((l) => l.id !== id);
}
</script>

<template>
    <LayerItem
        v-for="layer in layers"
        :key="layer.id"
        :layer="layer"
        @toggle="toggleLayer"
        @remove="removeLayer"
    />
</template>
```

子组件
```vue
<!-- 子组件 LayerItem.vue -->
<script setup>
const props = defineProps({
    layer: Object,
});

const emit = defineEmits(['toggle', 'remove']);
// 可以发多个事件，每个事件独立命名
</script>

<template>
    <div class="layer-row">
        <input
            type="checkbox"
            :checked="props.layer.visible"
            @change="emit('toggle', props.layer.id)"
        />
        <span>{{ props.layer.name }}</span>
        <button @click="emit('remove', props.layer.id)">删除</button>
    </div>
</template>

```


### Slots
父组件往子组件里塞html

1.Props 传的是**数据**（字符串、数字、对象），Slots 传的是**内容**（HTML 片段、组件）。

```
子组件写 <slot name="xxx">  →  挖坑
父组件写 <template #xxx>    →  填坑
```



```html
<!-- 子组件 MyCard.vue：留一个"坑" -->
<template>
    <div class="card">
        <div class="card-header">标题</div>
        <div class="card-body">
            <slot />
            <!--    ^^^^ 坑位：父组件塞进来的东西放这里 -->
        </div>
    </div>
</template>
```

```html
<!-- 父组件：往坑里塞东西 -->
<MyCard>
    <p>这是动态内容，想写什么写什么</p>
    <button>按钮也能塞</button>
</MyCard>

<!-- 最终渲染结果 -->
<div class="card">
    <div class="card-header">标题</div>
    <div class="card-body">
        <p>这是动态内容，想写什么写什么</p>
        <button>按钮也能塞</button>
    </div>
</div>
```

**· 跟 Props 的对比**

```vue
Props：  <Child :user-name="name" />     → 传数据（变量、对象）
Slots：  <Child><p>内容</p></Child>      → 传内容（HTML 片段）
```


**▎默认内容**

父组件不传的时候，slot 显示备用的默认内容：

```html
<!-- 子组件 -->
<slot>暂无数据</slot>

<!-- 父组件不传 → 显示"暂无数据" -->
<MyCard />

<!-- 父组件传了 → 默认内容被替换 -->
<MyCard>
    <p>有数据了</p>
</MyCard>
```

#### 具名插槽（多个坑）
1. 有 `name` 的是具名插槽，没 `name` 的是默认插槽（自动叫 `default`）
2. 父组件用 `<template #名字>` 指定目标坑，不加 `#` 的内容自动进默认坑
3. `v-slot:名字` = `#名字`，`#` 是简写，没区别

一个组件里可以开多个不同名字的 slot：

```html
<!-- 子组件 Layout.vue -->
<template>
    <div class="layout">
        <header>
            <slot name="header" />
        </header>
        <main>
            <slot />
            <!--     ^^ 没写 name 默认叫 "default" -->
        </main>
        <footer>
            <slot name="footer" />
        </footer>
    </div>
</template>
```

```html
<!-- 父组件：用 v-slot:名字 或 #名字 指定往哪个坑塞 -->
<Layout>
    <template #header>
        <h1>智慧城市大屏</h1>
    </template>

    <p>主体内容，默认进 default 坑</p>

    <template #footer>
        <span>© 2026 城市信息研究院</span>
    </template>
</Layout>
```

`v-slot:header` 简写为 `#header`，`v-slot:default` 简写为 `#default`。

#### 作用域插槽（子传数据回父）

这是 Slots 里最难但最有用的一块——子组件把自己的数据暴露给父组件，父组件决定怎么渲染：

```html
<!-- 子组件 ListView.vue：有数据，但不知道怎么展示 -->
<script setup>
const items = ref([
    { id: 1, name: '张三', status: '在线' },
    { id: 2, name: '李四', status: '离线' },
]);
</script>

<template>
    <ul>
        <li v-for="item in items" :key="item.id">
            <slot name="row" :item="item" :index="index" />
            <!--           ^^^^^^^^^^^^^^^^^^^^^^^^ -->
            <!--           把 item 和 index 暴露给父组件 -->
        </li>
    </ul>
</template>
```

```html
<!-- 父组件：拿到数据，自己决定每行长什么样 -->
<ListView>
    <template #row="{ item, index }">
        <!-- 解构拿到子组件暴露的数据 -->
        <span>{{ index + 1 }}. {{ item.name }}</span>
        <span :class="item.status === '在线' ? 'green' : 'gray'">
            {{ item.status }}
        </span>
    </template>
</ListView>
```


#### v-model组件帮：defineModel









### 响应式核心

#### ref & reactive
1.ref:基本类型的响应式

**为什么需要 .value**

ref 内部是一个对象 `{ value: 0 }`。JS 的基本类型（数字、字符串）传值不是传引用——如果直接用一个变量，Vue 追踪不到它什么时候被改了。所以 ref 用一个对象把值包起来，通过对象的 getter/setter 拦截读写操作，触发更新。

模板里自动解包是 Vue 的语法糖——它扫描到顶级 ref 时自动掉 `.value`。

2.reactive:对象的响应式
```vue
<script setup>
import { reactive } from 'vue';

const state = reactive({
    layers: [],
    activeId: null,
    config: { theme: 'dark' },
});

// 不用 .value，直接 .属性
state.activeId = 5;
state.layers.push({ id: 1, name: '实景三维' });
state.config.theme = 'light';  // 深层也响应
</script>

<template>
    <p>{{ state.activeId }}</p>
    <!--            ^^^^^^^^ 要带 state. 前缀 -->
</template>
```

**ref vs reactive 对比**

| |ref|reactive|
|---|---|---|
|包什么|任意类型|只能对象/数组|
|JS 里读|`.value`|直接 `.属性`|
|模板里|顶级 ref 自动解包|必须带前缀 `state.xxx`|
|替换整个对象|`ref.value = {...}` 直接换|替换需要 Object.assign|
|解构|会丢失响应式|用 toRefs|
|推荐度|✅ 优先用|适合一组关联状态|



#### computed
依赖的计算结果，依赖不变就不重新算。

computed 是一个**只读的、自动跟依赖联动的变量**。依赖没变，返回缓存值；依赖变了，自动重新算。
本质就是**声明了一个变量跟其他变量的计算关系**，Vue 帮你维护这个关系。你只声明，不手动维护。

```js
import { ref, computed } from 'vue';

const firstName = ref('张');
const lastName = ref('三');

// fullName 永远 = firstName + lastName，不用手动维护
const fullName = computed(() => {
    return `${firstName.value}${lastName.value}`;  // 张 + 三 → 张三
});

console.log(fullName.value);  // "张三"
// JS 里访问要 .value，模板里直接 {{ fullName }}

```

和直接写函数的区别：
```js
// 方法：每次访问都重新算
const fullName = () => `${firstName.value}${lastName.value}`;

// computed：只在依赖变了才重新算，依赖不变直接返回缓存
const fullName = computed(() => `${firstName.value}${lastName.value}`);

```


#### watch / watchEffect



#### shallowRef
只追踪`.value`的替换，不追踪内部变化。

和普通ref区别
```js
import { ref, shallowRef } from 'vue';

// 普通 ref：深层响应式
const normal = ref({ a: { b: 1 } });
normal.value.a.b = 2;  // ✅ 触发更新 — Vue 递归代理了整个对象树

// shallowRef：浅层响应式
const shallow = shallowRef({ a: { b: 1 } });
shallow.value = { a: { b: 2 } };  // ✅ 替换整个值 → 触发更新
shallow.value.a.b = 3;             // ❌ 改内部属性 → 不触发更新
//    数据确实改了，但 Vue 不知道，模板不会重新渲染

```



普通 ref 是**递归代理**——Vue 把 `{ a: { b: 1 } }` 里每一层都变成响应式。shallowRef 是**只代理第一层**——只有 `.value` 被整体替换时才触发更新。

**· 为什么需要它**

有些对象**太大了**，或者**内部结构不应该被 Vue 动**。

```js
// ❌ 普通 ref 包装 Cesium Viewer → 卡死
const viewer = ref(new Cesium.Viewer('container'));
// Vue 递归遍历 Viewer 内部的所有属性，给每个加 getter/setter
// Viewer 是一个巨大的 WebGL 上下文对象，递归代理直接卡死或者严重卡顿
```

```js
// ✅ shallowRef：Vue 不深入 Viewer 内部
const viewer = shallowRef(null);
viewer.value = new Cesium.Viewer('container');  // 触发更新
// 之后操作 viewer：全部走 Cesium 原生 API，Vue 不插手
viewer.value.camera.flyTo({ ... });  // Cesium 自己管自己的渲染
```

**· 触发了更新但模板没反应怎么办**

```js
const viewer = shallowRef(null);
viewer.value = new Cesium.Viewer('container');

// 改了 Cesium 内部的属性 → Vue 不知道
viewer.value.camera.position = ...;  // 模板不会更新

// 需要强制触发：替换整个 .value
viewer.value = triggerUpdate(viewer.value);
// 或者用一个单独的 ref 当触发器
const trigger = ref(0);
function forceUpdate() {
    trigger.value++;  // 模板里引用了 trigger，变了就重新渲染
}
```

不过实际 Cesium 项目里你几乎不需要模板响应 Viewer 内部的变动——Cesium 自己渲染自己的，不经过 Vue 的模板系统。shallowRef 就是让 Vue **别碰** Cesium 的对象。

**· shallowRef vs ref：选择流程图**

```
这个数据是 Cesium / Three.js / 第三方大型实例吗？
  ├── 是 → shallowRef
  └── 不是 → 需要在模板里展示深层属性吗？
                ├── 是 → ref
                └── 不需要展示深层属性，只做整体替换 → shallowRef
```

**· 典型使用场景**

```js
// ✅ Cesium 相关，全部 shallowRef
const viewer = shallowRef(null);
const tileset = shallowRef(null);
const dataSource = shallowRef(null);

// ✅ 大数组/大对象，只整体替换
const pointCloud = shallowRef([]);    // 几十万条数据
pointCloud.value = newData;           // 整体替换 → 触发更新

// ❌ 大数组如果用 ref：push 一条数据，Vue 递归代理整条新数据
// 数据量大时性能差距明显
```

**· 和你的项目直接相关**

```js
// cesium-demo 里正确的写法：
import { shallowRef, onMounted, onUnmounted } from 'vue';

const viewer = shallowRef(null);

onMounted(() => {
    viewer.value = new Cesium.Viewer('cesiumContainer', {
        // 配置...
    });
});

onUnmounted(() => {
    if (viewer.value) {
        viewer.value.destroy();
        viewer.value = null;
    }
});
```


### 逻辑复用 Composables
把有状态的逻辑抽出来，多个组件共享。

无状态的复用——纯函数，输入进输出出，不存状态。

vue中大部分逻辑带状态（ref / computed / watch / 生命周期)，跟组件绑定了。逻辑复用的目标就是把它从组件里拆出来。

vue2中：
```js
// ❌ Vue 2 Mixins：来源不清晰、命名冲突
const mouseMixin = {
    data() { return { x: 0, y: 0 }; },
    mounted() { window.addEventListener('mousemove', ...); },
};
// 组件里用 mixins: [mouseMixin]，不知道 x、y 从哪来的

```

vue3中：
一个以`use`开头的函数，里面封装了响应式状态和逻辑，返回组件需要的东西。

```js
// useMouse.js — 一个 composable
import { ref, onMounted, onUnmounted } from 'vue';

export function useMouse() {
    const x = ref(0);
    const y = ref(0);

    function update(event) {
        x.value = event.pageX;
        y.value = event.pageY;
    }

    onMounted(() => window.addEventListener('mousemove', update));
    onUnmounted(() => window.removeEventListener('mousemove', update));

    return { x, y };  // 把响应式状态暴露出去
}

```

本质：把组件的script部分抽出去

```
组件 = 模板（HTML） + 逻辑（JS）+ 样式（CSS）
Composable = 只抽逻辑（JS），模板和样式留在组件里
```


2.典型场景
```js
// useFetch.js — 通用数据请求
export function useFetch(url) {
    const data = ref(null);
    const loading = ref(true);
    const error = ref(null);

    async function execute() {
        loading.value = true;
        try {
            const res = await fetch(url);
            data.value = await res.json();
        } catch (e) {
            error.value = e;
        } finally {
            loading.value = false;
        }
    }

    onMounted(execute);
    return { data, loading, error, refresh: execute };
}


<!-- 组件里直接用 -->
<script setup>
const { data: layers, loading } = useFetch('/api/layers');
</script>

<template>
    <div v-if="loading">加载中...</div>
    <LayerList v-else :layers="layers" />
</template>

```

cesium中最常用的
```js
// useCesiumViewer.js — 封装 Viewer 初始化 + 销毁
export function useCesiumViewer(containerId) {
    const viewer = shallowRef(null);

    onMounted(() => {
        viewer.value = new Cesium.Viewer(containerId, {
            // 配置
        });
    });

    onUnmounted(() => {
        viewer.value?.destroy();
        viewer.value = null;
    });

    return { viewer };
}

```

任何需要地图的页面，一行搞定：

```js
const { viewer } = useCesiumViewer('cesiumContainer');
```

不用每个页面重复写 `onMounted` 创建 + `onUnmounted` 销毁。


### 路由
一个URL对应一个页面，页面切换不刷新浏览器。


#### vue-router
1.装路由 + 路由定义
```js
// router/index.js
import { createRouter, createWebHistory } from 'vue-router';

// 路由表：URL 路径 → 组件 的映射
const routes = [
    { path: '/',           component: Dashboard },    // 首页
    { path: '/layers',     component: LayerManager },  // 图层管理
    { path: '/map/:city',  component: MapView },       // 动态路由，:city 是变量
    { path: '/login',      component: Login },
    { path: '/:pathMatch(.*)*', component: NotFound }, // 404
];

const router = createRouter({
    history: createWebHistory(),  // 干净的 URL，没有 # 号
    routes,
});

export default router;

```

2.在入口注册
```js
// main.js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

createApp(App).use(router).mount('#app');

```

3.App.vue里留切换区域
```js
<!-- App.vue -->
<template>
    <nav>
        <router-link to="/">首页</router-link>
        <router-link to="/layers">图层</router-link>
    </nav>

    <!-- 关键：当前匹配到的组件渲染在这里 -->
    <router-view />
    <!-- 比如 URL 是 /layers，这里就渲染 LayerManager 组件 -->
</template>

```

4.页面跳转
```js
<!-- App.vue -->
<template>
    <nav>
        <router-link to="/">首页</router-link>
        <router-link to="/layers">图层</router-link>
    </nav>

    <!-- 关键：当前匹配到的组件渲染在这里 -->
    <router-view />
    <!-- 比如 URL 是 /layers，这里就渲染 LayerManager 组件 -->
</template>

```

#### 路由传参
```js
<!-- 路由定义 -->
{ path: '/map/:city', component: MapView }

<!-- MapView.vue：拿到参数 -->
<script setup>
import { useRoute } from 'vue-router';
const route = useRoute();

console.log(route.params.city);  // '/map/beijing' → 'beijing'
</script>

```
两种传参：

```js
// 1. 路径参数：/map/beijing — 必传，SEO 友好
router.push(`/map/${city}`);

// 2. query 参数：/search?keyword=xxx — 可选，类似 GET 参数
router.push({ path: '/search', query: { keyword: '故宫' } });
// → URL: /search?keyword=故宫

```

#### 路由守卫
在页面跳转的前后自动执行的钩子函数，用来做权限检查、埋点统计、页面标题切换等。

可以理解成门卫：
```
用户点击链接 → 
    → router.beforeEach（进门安检：有没有权限？）
        → 路由真正跳转（页面切换）
            → router.afterEach（离开登记：记录访问日志）
```

常见守卫类型：
```js
const router = createRouter({ ... });

// 1. beforeEach — 跳转之前执行（常用：权限检查）
router.beforeEach((to, from) => {
    // to: 要去哪个页面
    // from: 从哪个页面来
    if (to.meta.requiresAuth && !isLoggedIn()) {
        return '/login';  // 没登录，拦回去
    }
    // 不 return 或 return true → 放行
});

// 2. afterEach — 跳转之后执行（常用：埋点、记录日志）
router.afterEach((to, from) => {
    // 页面已经切换完了，不能拦截，只能做记录
    console.log(`从 ${from.path} 到了 ${to.path}`);
});

// 3. beforeRouteEnter — 在单个组件里定义，进入前
// 4. beforeRouteUpdate — 同一个组件，参数变了（比如 /user/1 → /user/2）
// 5. beforeRouteLeave — 离开当前组件前（比如"有未保存内容，确定离开？"）

```




```js
// 1. 路径参数：/map/beijing — 必传，SEO 友好
router.push(`/map/${city}`);

// 2. query 参数：/search?keyword=xxx — 可选，类似 GET 参数
router.push({ path: '/search', query: { keyword: '故宫' } });
// → URL: /search?keyword=故宫

```

#### 嵌套路由- 页面中还有子页面
大屏项目常见布局：左侧固定菜单，右侧内容区切换：

```js
const routes = [
    {
        path: '/dashboard',
        component: DashboardLayout,   // 外层骨架：侧边栏 + 顶栏
        children: [
            { path: '',        component: Overview },      // /dashboard
            { path: 'traffic', component: TrafficMonitor }, // /dashboard/traffic
            { path: 'water',   component: WaterMonitor },   // /dashboard/water
        ],
    },
];
```

```html
<!-- DashboardLayout.vue -->
<template>
    <Sidebar />
    <main>
        <router-view />
        <!-- /dashboard/traffic → 渲染 TrafficMonitor -->
        <!-- /dashboard/water   → 渲染 WaterMonitor -->
    </main>
</template>
```

**▎和你的 Cesium 项目的关系**

```
/login              → 登录页（纯 Vue）
/dashboard          → 数据驾驶舱（ECharts + 图表）
/dashboard/3d       → 三维场景（Cesium Viewer）
/dashboard/layers   → 图层管理（Vue 组件 + Cesium API）
/settings           → 系统设置（纯 Vue）
```

每个路径对应不同的业务场景。三维场景页面里挂 Cesium Viewer，切到设置页时 Viewer 自动销毁——这就接上了之前讲的 `onUnmounted` 销毁 Viewer。

**▎一个核心意识**

SPA（单页应用）路由 = **只是换组件，不是换 HTML 文件**。URL 变了，浏览器的 DOM 被 Vue 自动替换成新的组件树，网络请求只发 API 不重新加载页面。

这就是和传统多页面的区别：

```
传统：点击链接 → 浏览器刷新 → 重新下载整页 HTML+CSS+JS
SPA： 点击链接 → Vue Router 拦截 → 只替换 <router-view> 里的组件
```

### 状态管理

### Pinia
1.Pinia
```js
// stores/useProjectStore.js
import { defineStore } from 'pinia';

export const useProjectStore = defineStore('project', {
    // 1. state — 数据本身（相当于组件的 data / ref）
    state: () => ({
        currentId: null,
        layers: [],
        dataSources: [],
    }),

    // 2. getters — 计算属性（相当于 computed）
    getters: {
        layerCount: (state) => state.layers.length,
        activeDataSources: (state) => state.dataSources.filter((ds) => ds.active),
    },

    // 3. actions — 修改数据的方法（相当于 methods）
    actions: {
        selectProject(id) {
            this.currentId = id;
            this.layers = [];
            this.dataSources = [];
            this.loadLayers(id);
        },
        async loadLayers(projectId) {
            const data = await fetch(`/api/projects/${projectId}/layers`);
            this.layers = await data.json();
        },
        addLayer(layer) {
            this.layers.push(layer);
        },
    },
});

```

在组件中使用：
```js
<script setup>
import { useProjectStore } from '@/stores/useProjectStore';

const projectStore = useProjectStore();  // 获取 store 实例

// 读 state —— 直接访问
console.log(projectStore.currentId);
console.log(projectStore.layerCount);  // getter

// 写 state —— 通过 action
projectStore.selectProject(123);
</script>

<template>
    <p>当前项目：{{ projectStore.currentId }}</p>
    <p>图层数：{{ projectStore.layerCount }}</p>
    <button @click="projectStore.selectProject(456)">切换项目</button>
</template>

```

2.**组合式API写法（setup风格，对标composables）**
```js
export const useProjectStore = defineStore('project', () => {
    // 用 ref/reactive 定义状态
    const currentId = ref(null);
    const layers = ref([]);

    // 用 computed 定义 getters
    const layerCount = computed(() => layers.value.length);

    // 用普通函数定义 actions
    function selectProject(id) {
        currentId.value = id;
        layers.value = [];
    }

    async function loadLayers(projectId) {
        const res = await fetch(`/api/projects/${projectId}/layers`);
        layers.value = await res.json();
    }

    // 返回需要暴露的内容
    return { currentId, layers, layerCount, selectProject, loadLayers };
});

```


3.不适合放Pinia的：
- Cesium Viewer 实例（用 `shallowRef` + composable，因为不是多组件共享的数据，而是一个重量级对象）
- 表单临时输入值（组件自己用 `ref` 就够了）
- 动画状态、过渡效果（组件的局部状态）










2.为何用Pinia

GIS项目例：
```
App
├── Header（需要：用户名、未读数）
├── Sidebar（需要：用户名、权限列表）
├── Main
│   ├── MapView（需要：当前项目ID、图层列表）
│   └── DataPanel（需要：当前项目ID、数据源列表）
└── Footer
```

如果只用Props + Emits：
- 用户名从 App → Header → ... 一层层传（**prop drilling**，逐层透传）
- Header 改用户名 → 层层 emit 上去
- 兄弟组件 DataPanel 和 MapView 共享"当前项目ID"——必须把状态提到 App，然后分别往下传

解决：把共享状态抽到组件外面
```
组件树                      状态（独立于组件树）
───────                    ──────────
App                        store
├── Header ──→ 读取 ←──    ├── user.name
├── Sidebar ──→ 读取 ←──   ├── user.permissions
├── Main                   ├── project.currentId
│   ├── MapView ←── 读写 ── ├── project.layers
│   └── DataPanel ←── 读 ── └── project.dataSources
└── Footer

```



















