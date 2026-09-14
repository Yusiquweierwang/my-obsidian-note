
Element Plus = Vue 3 的 UI 组件库。别人搓好的一箱按钮/面板/表格/下拉框，直接用标签调用。

类比：
- 对照 fluid_sim_v1 的 css/dashboard.css —— 那些 .hudPanel/.tag 是手搓的，Element Plus 是现成的
- 对照 Cesium 的 Entity —— 一行高层声明，内部帮你做完

1.心智模型：四件套（所有组件都一样）
| 概念 | 作用 | 例子 |
|---|---|---|
| 组件 | 一个标签一个功能 | `<el-button>` `<el-table>` |
| 属性 Props | 配置它长什么样 | `<el-button type="primary" size="small">` |
| 事件 Events | 它通知你发生了什么 | `<el-button @click="save">` |
| 插槽 Slots | 往里面塞内容 | `<el-card><template #header>标题</template></el-card>` |

学会这四样 = 学会全部 60+ 组件。


2.最快上手：CDN 版（不用装任何东西）
新建 demo.html 贴进下面内容，双击即可运行。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <title>Element Plus · 对话窗口 demo</title>
  <link rel="stylesheet" href="https://unpkg.com/element-plus/dist/index.css" />
  <style>
    body { margin: 0; background: #F5F6F8; font-family: "Microsoft YaHei", sans-serif; }
    #app { max-width: 720px; margin: 24px auto; }
    .chat-box { height: 380px; overflow-y: auto; padding: 12px; }
    .msg { display: flex; margin-bottom: 12px; }
    .msg.user { flex-direction: row-reverse; }
    .bubble { max-width: 70%; padding: 8px 12px; border-radius: 8px;
              background: #F0F1F3; line-height: 1.6; font-size: 14px; }
    .msg.user .bubble { background: #1B6FE0; color: #fff; }
    .avatar { width: 32px; height: 32px; border-radius: 50%; background: #1B6FE0;
              color: #fff; display: flex; align-items: center; justify-content: center;
              font-size: 12px; flex: none; }
    .msg.user .avatar { background: #16A34A; margin-left: 8px; }
    .msg.ai  .avatar { margin-right: 8px; }
  </style>
</head>
<body>
<div id="app">
  <el-card shadow="never">
    <template #header>
      <div style="display:flex; align-items:center; justify-content:space-between;">
        <span>低空航路网 · 智算助手</span>
        <el-tag type="success" size="small">测试用例</el-tag>
      </div>
    </template>

    <div class="chat-box" ref="box">
      <div v-for="(m, i) in msgs" :key="i" class="msg" :class="m.role">
        <div class="avatar">{{ m.role === 'user' ? '我' : 'AI' }}</div>
        <div class="bubble">{{ m.text }}</div>
      </div>
    </div>

    <div style="display:flex; gap:8px; margin-top:12px;">
      <el-select v-model="scene" style="width:150px;">
        <el-option label="任务择机" value="test1" />
        <el-option label="相向冲突" value="test2" />
        <el-option label="拒止回答" value="test3" />
        <el-option label="同向追及" value="test4" />
      </el-select>
      <el-input v-model="draft" placeholder="输入你的问题…"
                @keyup.enter="send" clearable />
      <el-button type="primary" :loading="loading" @click="send">发送</el-button>
    </div>
  </el-card>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script src="https://unpkg.com/element-plus"></script>
<script>
  const { createApp, ref, nextTick } = Vue;

  createApp({
    setup() {
      const scene   = ref('test1');
      const draft   = ref('');
      const loading = ref(false);
      const box     = ref(null);
      const msgs    = ref([{ role: 'ai', text: '你好，我是低空航路网智算助手。' }]);

      async function send() {
        const q = draft.value.trim();
        if (!q || loading.value) return;
        msgs.value.push({ role: 'user', text: `[${scene.value}] ${q}` });
        draft.value = '';
        loading.value = true;
        await scrollBottom();

        // ↓↓↓ 这里换成调对接方的接口就行 ↓↓↓
        setTimeout(async () => {
          msgs.value.push({ role: 'ai', text: `（模拟返回）已收到 ${q}` });
          loading.value = false;
          await scrollBottom();
        }, 600);
      }

      async function scrollBottom() {
        await nextTick();
        if (box.value) box.value.scrollTop = box.value.scrollHeight;
      }

      return { scene, draft, loading, msgs, box, send };
    },
  }).use(ElementPlus).mount('#app');
</script>
</body>
</html>
```

试着改一处：`type="primary"` 改成 `type="danger"`，刷新看到按钮变红 —— 这就是"属性"的威力。


3.学习路径（分层，别一次学完）
| 层 | 学什么 | 什么时候需要 |
|---|---|---|
| L0 起步 | CDN 用法 + 四件套概念 | 今天就能用 |
| L1 常用 10 个 | Button / Input / Select / Table / Form / Dialog / Card / Tag / Message / Layout | 做界面 80% 够用 |
| L2 布局 | el-container / el-row / el-col | 做「统一首页框架」 |
| L3 表单表格 | el-form 校验 + el-table 列配置 | 数据类系统的主力 |
| L4 主题定制 | 改 CSS 变量换主色 | 见第 4 节 |
| L5 工程化 | Vite + 按需引入 + 图标 | 要打包部署时再学 |

L0~L2 今天就够做出对话窗口，别一上来就啃 Vite 工程化。


4.⭐ 它直接解决「三个系统 UI 不统一」的问题
如果各系统都用 Element Plus，统一配色只需改一个变量：

```css
:root {
  --el-color-primary: #1B6FE0;   /* 改这一行，全局所有按钮/选中态/链接全变 */
}
```

这就是「UI 统一规范」的技术抓手：
不是让三家各自改 CSS（改不齐），而是统一到同一个组件库 + 同一个主色变量，天生一致。

会上话术：「建议三家都统一用 Element Plus，主色定成一个 CSS 变量。
这样以后改配色改一行，三个系统同时生效，不会再各改各的。」

对照 [[Cesium]] 的 Entity 概念：都是「高层声明 → 内部实现」。


5.坑
1. Vue 2 用 Element UI，Vue 3 用 Element Plus —— 两个不通用，别装错
2. 图标要单独引 @element-plus/icons-vue，不跟主包走
3. CDN 版适合学，不适合上生产；正式项目用 npm install element-plus + Vite


6.待办
- [ ] 跑通 CDN demo（双击 demo.html）
- [ ] 等对接方给接口后，把 demo 里的 setTimeout 换成真实 fetch
- [ ] 改 --el-color-primary 试一次主题定制
