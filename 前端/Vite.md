- **VS Code** = 你写 UE5 C++ 的 IDE（写代码的地方）
- **Vite** = UE5 编辑器里的 **Play 按钮**（让你跑起来看效果，改代码立刻刷新）

`live-server` 也类似 Play 按钮，但 Vite 更快、更聪明——改一个字不用整个页面重刷，状态也不丢。

和live-server比：

|       | live-server  | Vite                  |
| ----- | ------------ | --------------------- |
| 启动速度  | 快            | 几乎瞬间（毫秒级）             |
| 热更新   | 刷新整个页面       | 只替换改的那个模块，状态不丢        |
| 支持 TS | 需要自己编译       | 直接跑                   |
| 打包生产  | 没有           | `vite build`，输出优化后的文件 |
| 模块导入  | 不支持 `import` | 原生支持 ESM              |


### 搭建步骤
**▎1. 创建项目**

```bash
npm create vite@latest
```

然后跟着提示选：

- Project name: `my-project`
- Framework: 先选 `Vanilla`（纯 JS 练手），后面学 Vue 再选 `Vue`
- Variant: `JavaScript`

**▎2. 进去启动**

```bash
cd my-project
npm install
npm run dev
```

打开 `http://localhost:5173`，效果跟你现在用 `live-server` 一样，但快一截。

**▎3. 你关注的核心区别——改代码**

```
你现在的 live-server：
  改了 app.js → 整个页面刷新 → DOM 状态丢了 → console 输出也清了

Vite（HMR 热模块替换）：
  改了 app.js → 只替换这个模块 → 页面上填的表单还在，console 还在
```

**▎4. 目录结构**

```
my-project/
├── index.html          # 入口（注意在根目录，不是 src 里）
├── src/
│   ├── main.js         # 你的 JS 入口
│   └── style.css       # 你的样式
├── package.json
└── vite.config.js      # Vite 配置（基本不用改）
```

**▎5. `index.html` 里引入 JS 的方式变了**

```html
<!-- live-server 写法 -->
<script src="./app.js"></script>

<!-- Vite 写法，关键是 type="module" -->
<script type="module" src="/src/main.js"></script>
```

加上 `type="module"` 之后，你就可以在 JS 里用 `import` 了：

```javascript
// src/main.js
import './style.css';           // 可以 import CSS
import { helper } from './utils.js';  // 可以 import 别的 JS
```


不用 Vite = 能 import 自己写的 JS 文件，但不能 import 第三方包和 .vue 文件。
用 Vite   = 什么都能 import，而且它还会打包合并，不会发一堆 HTTP 请求。

















































