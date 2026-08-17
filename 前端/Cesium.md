
1.工作原理
浏览器拿到 JS 代码
  → Cesium 调用 WebGL（浏览器内置的显卡接口）
    → 显卡画出一个 3D 地球
      → 每帧更新：地球旋转 + 相机移动 + 上面的要素重绘

2.概念树
```
Viewer（总管家）
├── Scene（3D 世界本身）
│   ├── Globe（地球球体 — 形状 + 贴图 + 地形高低）
│   ├── Camera（眼睛 — 从哪看、看哪、视野多大）
│   ├── Primitives（底层绘制的所有东西）
│   └── 光照/大气/后处理
│
├── Entities（高层 API — 你想画什么，不用管怎么画）
│
├── ImageryLayers（贴在地球表面的图层，像壁纸贴在墙上）
│
├── DataSources（批量数据来源 — GeoJSON/CZML/KML 文件）
│
└── 各种 UI 控件（时间线、缩放按钮、信息框）
```


### Viewer








### Scene

- 一个地球（Globe）
- 一个相机（Camera）
- 一堆光
- 一堆 Primitives（真正被画出来的东西）

#### Globe

Globe = 球体形状(Ellipsoid) + 表面贴图(Imagery) + 地形起伏(Terrain)。


- **Ellipsoid**：地球是个椭球体（WGS84 标准），不是正圆。所有坐标计算都基于这个椭球
- **Imagery**：贴在椭球上的卫星图/地图
- **Terrain**：地形数据，告诉某个经纬度的地方海拔多高，有了它才有山脉起伏

#### Camera

```
Camera 的属性 = 位置(position) + 朝向(heading/pitch/roll)
```

- `position`：相机在世界坐标中的位置（Cartesian3）
- `heading`：水平旋转角（北=0，东=90°，南=180°）
- `pitch`：俯仰角（平视=0，俯视=-90°，仰视=90°）
- `roll`：滚转（基本永远是 0，除非你模拟飞机侧翻）

`camera.flyTo()` 就是自动计算起点到终点的路径，平滑过渡过去

### Entity

```js
viewer.entities.add({
    position: ...,   // 在哪
    point: { ... },  // 画个点
    label: { ... },  // 附带文字
    // 还可以同时有 billboard / cylinder / polygon...
});
```

```
Entity = 一个逻辑对象（比如"北京气象站"）
├── position（位置）
├── point（怎么画：大小、颜色）
├── label（标签文字）
└── 其他图形属性（ellipse / cylinder / billboard ...）
```

### Primitives
去掉Entity的自动管理层，直接给GPU下绘制指令的底层API。

```
Entity（高级，方便）：
  "我想在北京放一个红点"
     ↓ Cesium 内部自动转换
Primitive（底层，高性能）：
  顶点坐标 + 颜色数组 + 索引 → 直接喂给 WebGL
```

为何如此划分？
- Entity 每个是一个独立对象，有开销。5000 个 Entity = 5000 次独立绘制调用。卡。
- Primitive 可以把 5000 个点的数据合并成一个大数组，一次调用全画完。不卡。


2.同Entity类比

| 层级  | UE5 类比                                             | Cesium    |
| --- | -------------------------------------------------- | --------- |
| 高层  | 往场景里拖一个 Actor，它有 Transform、Material、Collision 一堆组件 | Entity    |
| 底层  | 直接构造 FVertexBuffer + FIndexBuffer，调 RHI 提交绘制       | Primitive |
|     |                                                    |           |
Entity 系统给每个 Entity 维护了一套属性更新机制。100 个 Entity = 100 套属性系统在跑。10000 个 = 10000 套。且每个 Entity 是**独立的一次绘制调用**（draw call）。GPU 不怕画三角形，GPU 怕的是被 CPU 频繁打断——"画这个"、"停，画那个"、"停，再画那个"。

Primitive 的思路：**把 10000 个几何体打包成一个数组，一次提交给 GPU。**

```
Entity:
  CPU: "画点1" → GPU画 → "画点2" → GPU画 → ... （10000 次对话）

Primitive:
  CPU: "这些全给你，一起画" → GPU一次性搞定 （1 次对话）
```

#### Primitive组件
| 组件                   | 职责                                         | 关键点                                                                                           |
| -------------------- | ------------------------------------------ | --------------------------------------------------------------------------------------------- |
| **Geometry**         | 定义形状——顶点、三角面索引、法线                          | 只说"是什么形状"，不关心在哪、什么颜色                                                                          |
| **GeometryInstance** | 定义位置和姿态——通过 modelMatrix（4×4 矩阵）放置 Geometry | 同一 Geometry 可被无限个 Instance 复用，省内存                                                             |
| **Appearance**       | 定义着色方式                                     | PerInstanceColorAppearance（每实例不同色）/ MaterialAppearance（共用材质）/ EllipsoidSurfaceAppearance（贴地用） |
#### 常见Geometry类型
`BoxGeometry` `SphereGeometry` `CylinderGeometry` `PlaneGeometry` `RectangleGeometry` `PolygonGeometry` `WallGeometry` `EllipsoidGeometry` `PolylineGeometry`

#### Appearance
| 类型                           | 用途                                  |
| ---------------------------- | ----------------------------------- |
| `PerInstanceColorAppearance` | 每个 Instance 不同颜色（柱状图场景）             |
| `MaterialAppearance`         | 用 Cesium Material 系统，所有 Instance 共用 |
| `EllipsoidSurfaceAppearance` | 贴地几何体专用                             |




#### ImageryLayer
决定地球显示卫星图还是行政图。可以叠多层：

#### Terrain
```js
terrain: Cesium.Terrain.fromWorldTerrain()  // 加载 Cesium 提供的全球地形

```

#### DataSource
```js
// 加载一个 GeoJSON 文件，自动在对应位置创建 Entity
const ds = await Cesium.GeoJsonDataSource.load('省界.geojson');
viewer.dataSources.add(ds);

```

#### CZML 
带时间轴的数据。

GeoJSON 画的是静止不动的边界。CZML 描述的是"一个点在 10:00 飞到这里，10:05 飞到那里"。

```
CZML = "什么东西在什么时间在什么位置长什么样"
用途：轨迹回放、时间动画、动态数据可视化
```







|坐标系|是什么|什么时候用|
|---|---|---|
|经纬度（角度）|(116.4°, 39.9°, 5000m)|人看懂、你填参数|
|`Cartesian3`|(x, y, z) 米，地心为原点|Cesium 内部存储|
|`Cartographic`（弧度）|(lon弧度, lat弧度, height)|中间状态，计算时用|






```js
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8" />
    <title>Cesium 入门</title>
    <script src="https://cesium.com/downloads/cesiumjs/releases/1.119/Build/Cesium/Cesium.js"></script>
    <link href="https://cesium.com/downloads/cesiumjs/releases/1.119/Build/Cesium/Widgets/widgets.css" rel="stylesheet" />
    <style>
        html, body, #cesiumContainer {
            margin: 0; padding: 0;
            width: 100%; height: 100%;
            overflow: hidden;
        }
    </style>
</head>
<body>
    <div id="cesiumContainer"></div>
    <script>
        Cesium.Ion.defaultAccessToken = '你的token';

        const viewer = new Cesium.Viewer('cesiumContainer');
    </script>
</body>
</html>

```





















































































































































