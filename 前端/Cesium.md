
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

> 说明：以下所有代码示例都需要先创建 `viewer`（完整 HTML 模板见文末）。所有示例都在模板的 `<script>` 里运行。


### Viewer

```js
// 前提：HTML 里有一个 <div id="cesiumContainer">（见文末完整模板）
const viewer = new Cesium.Viewer('cesiumContainer', {
    animation: false,        // 关掉底部时间轴控件（用不到就关，省性能）
    timeline: false,
    baseLayerPicker: false,  // 关掉右上角图层选择器
    // terrain: Cesium.Terrain.fromWorldTerrain(),  // 可选：全球地形
});

// viewer 是"总管家"，下面所有概念的入口都挂在它身上：
// viewer.scene / viewer.entities / viewer.imageryLayers / viewer.camera / viewer.dataSources ...
```


### Scene

- 一个地球（Globe）
- 一个相机（Camera）
- 一堆光
- 一堆 Primitives（真正被画出来的东西）

```js
const scene = viewer.scene;           // 3D 世界本身
scene.globe;                          // → 地球（Globe）
scene.camera;                         // → 相机（Camera）
scene.primitives;                     // → 底层绘制的东西（Primitive）
scene.light;                          // → 光（太阳/月亮）
scene.skyBox; scene.skyAtmosphere;    // 天空盒 / 大气
scene.globe.enableLighting = true;    // 地形光照开关
```

#### Globe

Globe = 球体形状(Ellipsoid) + 表面贴图(Imagery) + 地形起伏(Terrain)。


- **Ellipsoid**：地球是个椭球体（WGS84 标准），不是正圆。所有坐标计算都基于这个椭球
- **Imagery**：贴在椭球上的卫星图/地图
- **Terrain**：地形数据，告诉某个经纬度的地方海拔多高，有了它才有山脉起伏

```js
const globe = viewer.scene.globe;

// ① Ellipsoid：WGS84 椭球，所有坐标计算的基准
globe.ellipsoid;   // Ellipsoid.WGS84

// ② Imagery：表面贴图（贴一张 OpenStreetMap 底图）
globe.imageryLayers.addImageryProvider(
    new Cesium.UrlTemplateImageryProvider({
        url: 'https://tile.openstreetmap.org/{z}/{x}/{y}.png',
    })
);

// ③ Terrain：地形起伏（填自己的地形服务地址，如 http://localhost:8080/terrain）
globe.terrainProvider = new Cesium.CesiumTerrainProvider({
    url: '...'   // 量化网格地形服务地址
});
```

#### Camera

```
Camera 的属性 = 位置(position) + 朝向(heading/pitch/roll)
```

- `position`：相机在世界坐标中的位置（Cartesian3）
- `heading`：水平旋转角（北=0，东=90°，南=180°）
- `pitch`：俯仰角（平视=0，俯视=-90°，仰视=90°）
- `roll`：滚转（基本永远是 0，除非你模拟飞机侧翻）

`camera.flyTo()` 就是自动计算起点到终点的路径，平滑过渡过去

```js
const camera = viewer.camera;

// 位置 + 朝向一起设
camera.setView({
    destination: Cesium.Cartesian3.fromDegrees(116.4, 39.9, 5000), // 看北京，5000m 高
    orientation: {
        heading: Cesium.Math.toRadians(0),    // 镜头朝北
        pitch:   Cesium.Math.toRadians(-90),  // 垂直俯视
        roll:    0,                           // 不翻滚
    },
});

// 平滑飞过去（自动插值出起点到终点的路径）
camera.flyTo({
    destination: Cesium.Cartesian3.fromDegrees(120.2, 30.3, 2000),
    duration: 3,   // 3 秒飞完
});

// 直接飞向某个 Entity（自动算它的包围盒）
viewer.flyTo(entity);
```

### Entity

```js
// 骨架
viewer.entities.add({
    position: ...,   // 在哪
    point: { ... },  // 画个点
    label: { ... },  // 附带文字
    // 还可以同时有 billboard / cylinder / polygon...
});

// 可运行版：北京气象站 = 一个点 + 一个标签
viewer.entities.add({
    id: 'bj-station',
    name: '北京气象站',
    position: Cesium.Cartesian3.fromDegrees(116.4, 39.9, 100),
    point: {
        pixelSize: 10,
        color: Cesium.Color.RED,
        outlineColor: Cesium.Color.WHITE,
        outlineWidth: 2,
    },
    label: {
        text: '北京气象站',
        font: '14px sans-serif',
        pixelOffset: new Cesium.Cartesian2(0, -20),  // 往上挪 20 像素，别压住点
    },
});

// 多边形 Entity（画一块区域）
viewer.entities.add({
    name: '某区域',
    polygon: {
        hierarchy: Cesium.Cartesian3.fromDegreesArray([
            116.0, 39.5,  118.0, 39.5,  118.0, 41.5,  116.0, 41.5,
        ]),
        material: Cesium.Color.YELLOW.withAlpha(0.4),  // 半透明填充
        outline: true,
        outlineColor: Cesium.Color.ORANGE,
    },
});

// 查 / 删
const e = viewer.entities.getById('bj-station');
viewer.entities.remove(e);
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

```js
// 5000 个点，一次提交（对比：5000 个 Entity 会卡）
const pts = viewer.scene.primitives.add(new Cesium.PointPrimitiveCollection());
for (let i = 0; i < 5000; i++) {
    pts.add({
        position: Cesium.Cartesian3.fromDegrees(
            116 + Math.random() * 2,   // 经度 116~118
            39  + Math.random() * 2,   // 纬度 39~41
        ),
        color: Cesium.Color.RED,
        pixelSize: 5,
    });
}
// 用完清掉
viewer.scene.primitives.remove(pts);
```


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

```js
// 完整三件套：Geometry(形状) + GeometryInstance(摆放) + Appearance(上色)
viewer.scene.primitives.add(new Cesium.Primitive({
    geometryInstances: new Cesium.GeometryInstance({
        // ① Geometry：只描述形状（长宽高各 500/500/800 米的盒子）
        geometry: Cesium.BoxGeometry.fromDimensions({
            vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT,
            dimensions: new Cesium.Cartesian3(500, 500, 800),
        }),
        // ② GeometryInstance：用 4×4 矩阵把盒子放到北京
        modelMatrix: Cesium.Transforms.eastNorthUpToFixedFrame(
            Cesium.Cartesian3.fromDegrees(116.4, 39.9, 0)
        ),
        id: 'box-1',
        // 这个实例的颜色
        attributes: {
            color: Cesium.ColorGeometryInstanceAttribute.fromColor(Cesium.Color.RED),
        },
    }),
    // ③ Appearance：着色方式（每实例不同色）
    appearance: new Cesium.PerInstanceColorAppearance(),
}));

// 同一 Geometry 给多个 Instance 复用（省内存）：
//   const g  = Cesium.BoxGeometry.fromDimensions({...});
//   const i1 = new Cesium.GeometryInstance({ geometry: g, modelMatrix: M1, attributes: { color: 红色 } });
//   const i2 = new Cesium.GeometryInstance({ geometry: g, modelMatrix: M2, attributes: { color: 蓝色 } });
```
#### 常见Geometry类型
`BoxGeometry` `SphereGeometry` `CylinderGeometry` `PlaneGeometry` `RectangleGeometry` `PolygonGeometry` `WallGeometry` `EllipsoidGeometry` `PolylineGeometry`

#### Appearance
| 类型                           | 用途                                  |
| ---------------------------- | ----------------------------------- |
| `PerInstanceColorAppearance` | 每个 Instance 不同颜色（柱状图场景）             |
| `MaterialAppearance`         | 用 Cesium Material 系统，所有 Instance 共用 |
| `EllipsoidSurfaceAppearance` | 贴地几何体专用                             |

```js
// MaterialAppearance：所有实例共用一份材质
appearance: new Cesium.MaterialAppearance({
    material: Cesium.Material.fromType('Color', { color: Cesium.Color.CYAN }),
}),
```




#### ImageryLayer
决定地球显示卫星图还是行政图。可以叠多层：

```js
// 叠加两层：底图卫星图 + 上层半透明行政图
const layer = viewer.imageryLayers.addImageryProvider(
    new Cesium.UrlTemplateImageryProvider({
        url: 'https://tile.openstreetmap.org/{z}/{x}/{y}.png',
    })
);
layer.alpha = 0.5;                  // 调整不透明度
viewer.imageryLayers.raise(layer);  // 提到最上层
viewer.imageryLayers.remove(layer); // 移除
```

#### Terrain
```js
terrain: Cesium.Terrain.fromWorldTerrain()  // 加载 Cesium 提供的全球地形

// 方式一：创建 Viewer 时指定（全球地形，需 Ion token）
const viewer = new Cesium.Viewer('cesiumContainer', {
    terrain: Cesium.Terrain.fromWorldTerrain(),
});

// 方式二：运行中切换到自己的地形服务（离线部署就是这种）
viewer.scene.setTerrain(new Cesium.Terrain(
    new Cesium.CesiumTerrainProvider({
        url: 'http://localhost:8080/terrain',   // 自己的瓦片服务
    })
));
```

#### DataSource
```js
// 加载一个 GeoJSON 文件，自动在对应位置创建 Entity
const ds = await Cesium.GeoJsonDataSource.load('省界.geojson', {
    stroke: Cesium.Color.RED,                       // 边线色
    strokeWidth: 2,
    fill: Cesium.Color.YELLOW.withAlpha(0.4),       // 填充色
});
viewer.dataSources.add(ds);

// 自动生成的 Entity 都在这里（GeoJSON 的每个 feature = 一个 Entity）
const entities = ds.entities.values;
viewer.flyTo(ds);   // 飞到数据范围
```

#### CZML 
带时间轴的数据。

GeoJSON 画的是静止不动的边界。CZML 描述的是"一个点在 10:00 飞到这里，10:05 飞到那里"。

```
CZML = "什么东西在什么时间在什么位置长什么样"
用途：轨迹回放、时间动画、动态数据可视化
```

```js
// 加载带时间的轨迹
const czmlDs = new Cesium.CzmlDataSource();
viewer.dataSources.add(czmlDs);
await czmlDs.load('飞机轨迹.czml');

viewer.clock.shouldAnimate = true;   // 自动播放（false = 用底部时间轴手动拖）
viewer.timeline.zoomTo(czmlDs.clock.startTime, czmlDs.clock.stopTime);
```

```json
// 飞机轨迹.czml 的最小内容：一个红点 3 分钟内从北京飞到上海
// cartographicDegrees 的格式：[时间, 经度, 纬度, 高度, 时间, 经度, 纬度, 高度, ...]
[
  {
    "id": "plane",
    "position": {
      "epoch": "2026-09-06T00:00:00Z",
      "cartographicDegrees": [
        0,   116.4, 39.9, 1000,
        60,  118.0, 37.0, 2000,
        120, 121.5, 31.2, 3000
      ]
    },
    "point": { "pixelSize": 10, "color": { "rgba": [255, 0, 0, 255] } }
  }
]
```




|坐标系|是什么|什么时候用|
|---|---|---|
|经纬度（角度）|(116.4°, 39.9°, 5000m)|人看懂、你填参数|
|`Cartesian3`|(x, y, z) 米，地心为原点|Cesium 内部存储|
|`Cartographic`（弧度）|(lon弧度, lat弧度, height)|中间状态，计算时用|

```js
// ① 经纬度（度）→ Cartesian3（Cesium 内部存储）
const p = Cesium.Cartesian3.fromDegrees(116.4, 39.9, 5000);

// ② Cartesian3 → 经纬度（反向）
const carto = Cesium.Cartographic.fromCartesian(p);
const lon = Cesium.Math.toDegrees(carto.longitude);  // 116.4
const lat = Cesium.Math.toDegrees(carto.latitude);   // 39.9
const h   = carto.height;                            // 5000

// ③ 经纬度 → Cartographic（弧度，中间态）
const c = Cesium.Cartographic.fromDegrees(116.4, 39.9, 5000);
// c.longitude / c.latitude 是弧度，用 Cesium.Math.toDegrees() 变回度

// ④ 鼠标点击 → 取地面经纬度（场景拾取，做交互必用）
const handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
handler.setInputAction(function (e) {
    const ray = viewer.camera.getPickRay(e.position);
    const hit = viewer.scene.globe.pick(ray, viewer.scene);  // Cartographic | undefined
    if (hit) {
        console.log(Cesium.Math.toDegrees(hit.longitude), Cesium.Math.toDegrees(hit.latitude));
    }
}, Cesium.ScreenSpaceEventType.LEFT_CLICK);

// ⑤ 鼠标点击 → 拾取对象（Entity / Primitive）
const picked = viewer.scene.pick(e.position);
// picked.id 就是 entities.add({ id: 'bj-station' }) 里的 id
```




```html
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

        // ↑ 本文所有代码示例都放在这里运行
    </script>
</body>
</html>

```
