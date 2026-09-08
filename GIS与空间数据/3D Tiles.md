



3D tiles是OGC开放标准，专门解决“海量三维数据在浏览器里按需流式加载”的问题。对象是立在地球表面之上的东西：
+ 建筑物
+ 倾斜摄影实景三维模型
+ 点云
+ BIM


|你的 Terrarium 地形瓦片|3D Tiles|
|---|---|
|贴在哪|贴在地球**表面**（Globe 的一部分）|立在地表**之上**（独立对象）|
|一块瓦片装什么|高程值（每像素一个高度）|完整几何（顶点/三角面/纹理/属性）|
|解决什么|地形起伏|城市模型、实景三维|
|加载入口|`globe.terrainProvider`|`scene.primitives.add(Cesium3DTileset)`|

#### tileset.json
是3d tiles 数据集的根节点/总目录。


#### 核心-一棵瓦片树
```
tileset.json（入口清单：根瓦片 + 元数据）
└── tile 0（全城最粗糙的一层，geometricError 大）
    ├── tile 1（东城区）
    ├── tile 2（西城区）
    │   ├── tile 2-1（某街区）
    │   └── tile 2-2 ...
    └── ...

```
每个瓦片带两个关键参数：

- **boundingVolume（包围体）**：这一块数据占多大空间，Cesium 用它做"视野裁剪"——看不见的块根本不请求
- **geometricError（几何误差，单位米）**：这个块"有多粗糙"。误差换算成屏幕上的像素误差后，**离得近就往下钻取更细的子级，离得远就用粗的父级**——这就是自动 LOD





#### 文件格式族

|格式|装什么|典型场景|
|---|---|---|
|`.b3dm`|批量模型（一个文件装几百个模型+属性）|倾斜摄影、城市建筑——**最常见**|
|`.i3dm`|实例化模型（同一个模型放 N 个位置）|路灯、行道树|
|`.pnts`|点云|激光扫描|
|`.glb`|单个 glTF 模型|单体精模|
|`.cmpt`|复合（上面几种拼一块）|混合场景|

#### 加载代码
```js
const tileset = await Cesium.Cesium3DTileset.fromUrl(
    'http://localhost:8080/tiles/deqing/tileset.json'
);
viewer.scene.primitives.add(tileset);
viewer.flyTo(tileset);   // 飞到模型范围

```






















































































