[[JS]]


```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8" />
    <title>页面标题</title>
</head>
<body>
    <h1>你好，世界</h1>
    <p>这是我的第一个网页。</p>
</body>
</html>

```

|部分|作用|
|---|---|
|`<!DOCTYPE html>`|声明文档类型，告诉浏览器"这是 HTML5"|
|`<html>`|根元素，包裹整个页面，`lang` 属性告诉搜索引擎/屏幕阅读器这是什么语言|
|`<head>`|元数据区：字符编码、标题、样式、SEO 信息——用户看不到|
|`<meta charset="UTF-8" />`|字符编码，不写中文会乱码|
|`<title>`|浏览器标签页上的文字，也是搜索引擎显示的标题|
|`<body>`|可视内容区：所有用户能看到的东西都放这里|

**▎常用标签分类**

+ 文本标签

```html
<h1>一级标题</h1>  <!-- h1~h6，层级递减，一个页面最好只有一个 h1 -->
<h2>二级标题</h2>
<p>段落文本</p>
<span>行内文本片段（不给它单独占一行）</span>
<strong>加粗 + 语义强调</strong>
<em>斜体 + 语义强调</em>
<br />  <!-- 换行，自闭合标签 -->
```

+ 布局标签
```html
<div>块级容器，占一整行，用于分组</div>
<header>页头</header>
<nav>导航</nav>
<main>主体内容</main>
<section>章节/区域</section>
<article>独立内容（如一篇文章）</article>
<aside>侧边栏</aside>
<footer>页脚</footer>
```

```html
<!-- 无序列表 -->
<ul>
    <li>项目一</li>
    <li>项目二</li>
</ul>

<!-- 有序列表 -->
<ol>
    <li>第一步</li>
    <li>第二步</li>
</ol>

```

```html
<a href="https://example.com">文本链接</a>
<a href="/page.html" target="_blank">新标签页打开</a>

<img src="photo.jpg" alt="一张照片" width="400" />
<!-- alt: 图片加载失败时显示的文字，也是无障碍必须的 -->

```


表单
```html
<form action="/submit" method="POST">
    <input type="text" placeholder="请输入姓名" />
    <input type="password" placeholder="密码" />
    <input type="email" placeholder="邮箱" />
    <input type="number" min="0" max="100" />

    <label>
        <input type="radio" name="gender" value="male" /> 男
    </label>
    <label>
        <input type="checkbox" /> 同意协议
    </label>

    <select>
        <option>选项 A</option>
        <option>选项 B</option>
    </select>

    <textarea rows="4" placeholder="请输入内容"></textarea>

    <button type="submit">提交</button>
    <button type="reset">重置</button>
</form>

```

```html
<table>
    <thead>
        <tr>
            <th>姓名</th>
            <th>年龄</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>张三</td>
            <td>25</td>
        </tr>
    </tbody>
</table>

```

























