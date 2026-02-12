# Three.js 几何体教程 - 第二部分：Geometry详解

> 本教程深入讲解Three.js几何体（Geometry）的核心概念、API使用和高级技巧。通过丰富的示例和实践，帮助你掌握3D形状的创建、自定义和优化。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：几何体基础概念](#第一章几何体基础概念)
- [第二章：基础几何体](#第二章基础几何体)
- [第三章：高级几何体](#第三章高级几何体)
- [第四章：BufferGeometry详解](#第四章buffergeometry详解)
- [第五章：点云与线段](#第五章点云与线段)
- [第六章：InstancedMesh实例化](#第六章instancedmesh实例化)
- [第七章：几何体优化](#第七章几何体优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Three.js几何体的核心概念和架构
- ✅ 熟练使用各种内置几何体
- ✅ 掌握BufferGeometry的自定义创建
- ✅ 理解顶点、索引、法线、UV等核心概念
- ✅ 使用点云和线段创建特殊效果
- ✅ 运用InstancedMesh优化大量相同物体的渲染
- ✅ 掌握几何体的性能优化技巧

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器的基本概念
- **JavaScript (ES6+)**: 数组、对象、类等现代语法
- **3D数学基础**: 向量、矩阵、坐标变换（了解即可）

### 推荐知识
- **计算机图形学基础**: 顶点、三角形、渲染管线
- **线性代数**: 矩阵运算、向量运算

---

## 第一章：几何体基础概念

### 1.1 什么是几何体（Geometry）？

**几何体**是3D物体的"骨架"，定义了物体的形状、大小和结构。它不包含颜色、纹理等外观信息，这些由材质（Material）负责。

#### 几何体的作用

```
几何体（Geometry）     +     材质（Material）     =     网格（Mesh）
    ↓                              ↓                          ↓
  形状定义                      外观定义                  可渲染的3D对象
  - 顶点                      - 颜色                   - 位置
  - 面                        - 纹理                   - 旋转
  - 法线                      - 光照属性                 - 缩放
  - UV坐标                    - 透明度
```

#### 类比理解

- **几何体**就像建筑的**蓝图**，定义了房屋的结构
- **材质**就像建筑的**装修材料**，决定了房屋的外观
- **网格**就像**建好的房屋**，是最终呈现的结果

### 1.2 Three.js几何体类型

Three.js提供了两种主要的几何体类型：

| 类型 | 说明 | 性能 | 适用场景 |
|------|------|--------|----------|
| **内置几何体** | 预定义的常见形状 | 高 | 日常开发，快速原型 |
| **BufferGeometry** | 自定义几何体，直接操作顶点数据 | 最高 | 复杂形状，性能优化 |

#### 内置几何体

内置几何体是Three.js预先定义好的常见形状，使用简单方便。

```javascript
// 内置几何体示例
const box = new THREE.BoxGeometry(1, 1, 1);
const sphere = new THREE.SphereGeometry(1, 32, 32);
const cone = new THREE.ConeGeometry(1, 2, 32);
```

#### BufferGeometry

BufferGeometry是所有几何体的基类，允许你直接操作顶点数据。

```javascript
// 自定义BufferGeometry示例
const geometry = new THREE.BufferGeometry();
const vertices = new Float32Array([
    -1, -1, 0,  // 顶点0
     1, -1, 0,  // 顶点1
     0,  1, 0   // 顶点2
]);
geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));
```

### 1.3 几何体的核心属性

#### 顶点（Vertex）

顶点是3D空间中的点，是构成几何体的基本单位。

```javascript
// 获取顶点位置
const positions = geometry.attributes.position;
const x = positions.getX(0);  // 获取第0个顶点的x坐标
const y = positions.getY(0);  // 获取第0个顶点的y坐标
const z = positions.getZ(0);  // 获取第0个顶点的z坐标

// 设置顶点位置
positions.setXYZ(0, 1, 2, 3);  // 设置第0个顶点的位置为(1, 2, 3)
positions.needsUpdate = true;  // 标记需要更新
```

#### 面（Face）

面是由顶点组成的三角形，是渲染的基本单位。

```javascript
// Three.js使用三角形面
// 一个立方体有6个面，每个面由2个三角形组成（共12个三角形）
```

#### 法线（Normal）

法线是垂直于面的向量，用于计算光照反射。

```javascript
// 计算法线
geometry.computeVertexNormals();

// 获取法线
const normals = geometry.attributes.normal;
const nx = normals.getX(0);  // 获取第0个顶点的法线x分量
```

#### UV坐标（UV Coordinate）

UV坐标用于将纹理映射到几何体表面，范围从0到1。

```javascript
// UV坐标示例
const uvs = new Float32Array([
    0, 0,  // 左下角
    1, 0,  // 右下角
    1, 1,  // 右上角
    0, 1   // 左上角
]);
geometry.setAttribute('uv', new THREE.BufferAttribute(uvs, 2));
```

**UV坐标图示：**

```
V轴
 ↑
1├───────┐
 │       │
 │   纹理  │
 │       │
0├───────┘
 0       1  U轴
```

#### 索引（Index）

索引用于重用顶点，减少数据量和提高性能。

```javascript
// 使用索引的三角形
const indices = new Uint16Array([
    0, 1, 2,  // 三角形1：使用顶点0, 1, 2
    0, 2, 3   // 三角形2：重用顶点0和2，使用顶点3
]);
geometry.setIndex(new THREE.BufferAttribute(indices, 1));
```

**索引的优势：**

```
不使用索引（6个顶点）：
    0────1
    │╲  ╱│
    │ ╲╱ │
    3────2
    │╱  ╲│
    │ ╱╲ │
    4────5

使用索引（4个顶点）：
    0────1
    │╲  ╱│
    │ ╲╱ │
    3────2
    (重用0和2)
```

### 1.4 BufferAttribute详解

BufferAttribute是存储顶点数据的容器，使用类型化数组提高性能。

#### 创建BufferAttribute

```javascript
// 创建位置属性
const positions = new Float32Array(vertexCount * 3);
const positionAttribute = new THREE.BufferAttribute(positions, 3);
geometry.setAttribute('position', positionAttribute);

// 创建UV属性
const uvs = new Float32Array(vertexCount * 2);
const uvAttribute = new THREE.BufferAttribute(uvs, 2);
geometry.setAttribute('uv', uvAttribute);

// 创建颜色属性
const colors = new Float32Array(vertexCount * 3);
const colorAttribute = new THREE.BufferAttribute(colors, 3);
geometry.setAttribute('color', colorAttribute);
```

#### BufferAttribute类型

| 属性名称 | 数据类型 | 分量数 | 说明 |
|-----------|----------|---------|------|
| `position` | Float32Array | 3 | 顶点位置（x, y, z） |
| `normal` | Float32Array | 3 | 法线向量（nx, ny, nz） |
| `uv` | Float32Array | 2 | UV坐标（u, v） |
| `color` | Float32Array | 3 | 顶点颜色（r, g, b） |
| `index` | Uint16Array/Uint32Array | 1 | 索引 |

#### 类型化数组选择

```javascript
// Float32Array - 用于位置、法线、UV、颜色
const positions = new Float32Array(count * 3);

// Uint16Array - 用于索引（最多65535个顶点）
const indices = new Uint16Array(count);

// Uint32Array - 用于索引（超过65535个顶点）
const indices = new Uint32Array(count);

// Uint8Array - 用于颜色（0-255范围）
const colors = new Uint8Array(count * 3);
```

---

## 第二章：基础几何体

### 2.1 BoxGeometry（立方体）

立方体是最常用的基础几何体之一。

#### 构造函数

```javascript
new THREE.BoxGeometry(
    width,           // 宽度
    height,          // 高度
    depth,           // 深度
    widthSegments,   // 宽度分段数（默认1）
    heightSegments,  // 高度分段数（默认1）
    depthSegments    // 深度分段数（默认1）
);
```

#### 使用示例

```javascript
// 创建一个2x3x4的立方体
const geometry = new THREE.BoxGeometry(2, 3, 4);

// 创建分段立方体（用于变形或纹理映射）
const geometry = new THREE.BoxGeometry(2, 3, 4, 2, 3, 2);

// 创建材质和网格
const material = new THREE.MeshStandardMaterial({ color: 0xff0000 });
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);
```

#### 分段数的影响

```
分段数 = 1           分段数 = 2           分段数 = 4
   ┌─────┐              ┌───┬───┐            ┌─┬─┬─┬─┐
   │     │              │   │   │            │ │ │ │ │
   │     │              ├───┼───┤            ├─┼─┼─┼─┤
   │     │              │   │   │            │ │ │ │ │
   └─────┘              └───┴───┘            └─┴─┴─┴─┘
  (1个面）             (4个面）            (16个面）
```

### 2.2 SphereGeometry（球体）

球体是创建圆形物体的基础。

#### 构造函数

```javascript
new THREE.SphereGeometry(
    radius,          // 半径
    widthSegments,   // 水平分段数（默认32）
    heightSegments,  // 垂直分段数（默认16）
    phiStart,       // 水平起始角度（默认0）
    phiLength,      // 水平角度长度（默认2π）
    thetaStart,     // 垂直起始角度（默认0）
    thetaLength     // 垂直角度长度（默认π）
);
```

#### 使用示例

```javascript
// 创建完整球体
const geometry = new THREE.SphereGeometry(1, 32, 32);

// 创建半球体
const geometry = new THREE.SphereGeometry(1, 32, 32, 0, Math.PI * 2, 0, Math.PI / 2);

// 创建四分之一球体
const geometry = new THREE.SphereGeometry(1, 32, 32, 0, Math.PI / 2, 0, Math.PI / 2);
```

#### 分段数对比

```
分段数 = 8           分段数 = 16          分段数 = 32
   ○                    ●                    ●
  / \                  / \                  / \
 /   \                /   \                /     \
○-----○              ●-----●              ●-------●
(多边形明显）         (较平滑）              (非常平滑）
```

### 2.3 PlaneGeometry（平面）

平面是创建2D表面或地面的基础。

#### 构造函数

```javascript
new THREE.PlaneGeometry(
    width,          // 宽度
    height,         // 高度
    widthSegments,  // 宽度分段数（默认1）
    heightSegments  // 高度分段数（默认1）
);
```

#### 使用示例

```javascript
// 创建一个10x10的平面
const geometry = new THREE.PlaneGeometry(10, 10);

// 创建分段平面（用于地形或变形）
const geometry = new THREE.PlaneGeometry(10, 10, 20, 20);

// 注意：平面默认面向Z轴正方向
// 如果需要平面水平放置，需要旋转
const plane = new THREE.Mesh(geometry, material);
plane.rotation.x = -Math.PI / 2;  // 旋转-90度，使其水平
scene.add(plane);
```

### 2.4 CircleGeometry（圆形）

圆形是创建圆盘或扇形的基础。

#### 构造函数

```javascript
new THREE.CircleGeometry(
    radius,      // 半径
    segments,    // 分段数（默认32）
    thetaStart,  // 起始角度（默认0）
    thetaLength  // 角度长度（默认2π）
);
```

#### 使用示例

```javascript
// 创建完整圆形
const geometry = new THREE.CircleGeometry(1, 32);

// 创建半圆
const geometry = new THREE.CircleGeometry(1, 32, 0, Math.PI);

// 创建四分之一圆
const geometry = new THREE.CircleGeometry(1, 32, 0, Math.PI / 2);
```

### 2.5 CylinderGeometry（圆柱体）

圆柱体可以创建圆柱、圆锥等形状。

#### 构造函数

```javascript
new THREE.CylinderGeometry(
    radiusTop,      // 顶部半径
    radiusBottom,   // 底部半径
    height,         // 高度
    radialSegments,  // 径向分段数（默认32）
    heightSegments, // 高度分段数（默认1）
    openEnded      // 是否开放两端（默认false）
);
```

#### 使用示例

```javascript
// 创建圆柱体
const geometry = new THREE.CylinderGeometry(1, 1, 2, 32);

// 创建圆锥体（顶部半径为0）
const geometry = new THREE.CylinderGeometry(0, 1, 2, 32);

// 创建截头圆锥体
const geometry = new THREE.CylinderGeometry(0.5, 1, 2, 32);

// 创建六棱柱
const geometry = new THREE.CylinderGeometry(1, 1, 2, 6);
```

#### 形状对比

```
圆柱体                圆锥体                截头圆锥体
   ┌───┐                ▲                    ┌───┐
   │   │               / \                   │   │
   │   │              /   \                  │   │
   │   │             /_____\                 │   │
   └───┘                                    └───┘
radiusTop=radiusBottom  radiusTop=0         radiusTop<radiusBottom
```

---

## 第三章：高级几何体

### 3.1 ConeGeometry（圆锥体）

圆锥体是创建锥形物体的专用几何体。

#### 构造函数

```javascript
new THREE.ConeGeometry(
    radius,         // 底面半径
    height,         // 高度
    radialSegments, // 径向分段数（默认32）
    heightSegments, // 高度分段数（默认1）
    openEnded      // 是否开放底面（默认false）
);
```

#### 使用示例

```javascript
// 创建标准圆锥体
const geometry = new THREE.ConeGeometry(1, 2, 32);

// 创建低多边形圆锥体
const geometry = new THREE.ConeGeometry(1, 2, 6);

// 创建开放圆锥体（无底面）
const geometry = new THREE.ConeGeometry(1, 2, 32, 1, true);
```

### 3.2 TorusGeometry（圆环）

圆环是创建环形物体的基础。

#### 构造函数

```javascript
new THREE.TorusGeometry(
    radius,          // 主半径（圆环中心到管中心的距离）
    tube,            // 管半径（圆环的粗细）
    radialSegments,   // 径向分段数（默认16）
    tubularSegments, // 管分段数（默认100）
    arc              // 圆环弧度（默认2π）
);
```

#### 使用示例

```javascript
// 创建完整圆环
const geometry = new THREE.TorusGeometry(1, 0.3, 16, 100);

// 创建半圆环
const geometry = new THREE.TorusGeometry(1, 0.3, 16, 100, Math.PI);

// 创建细管圆环
const geometry = new THREE.TorusGeometry(1, 0.1, 16, 100);

// 创建粗管圆环
const geometry = new THREE.TorusGeometry(1, 0.5, 16, 100);
```

#### 参数图示

```
        tube（管半径）
            ↓
         ┌───┐
        ╱     ╲
       ╱  radius ╲  ← 主半径
      ╱   (主半径）╲
     ╱             ╲
    └───────────────┘
```

### 3.3 TorusKnotGeometry（环形结）

环形结是创建复杂结状物体的几何体。

#### 构造函数

```javascript
new THREE.TorusKnotGeometry(
    radius,          // 主半径
    tube,            // 管半径
    tubularSegments, // 管分段数（默认100）
    radialSegments,   // 径向分段数（默认16）
    p,               // p参数（默认2）
    q                // q参数（默认3）
);
```

#### 使用示例

```javascript
// 创建标准环形结
const geometry = new THREE.TorusKnotGeometry(1, 0.4, 100, 16, 2, 3);

// 创建简单结（p=2, q=3）
const geometry = new THREE.TorusKnotGeometry(1, 0.4, 100, 16, 2, 3);

// 创建复杂结（p=3, q=5）
const geometry = new THREE.TorusKnotGeometry(1, 0.4, 100, 16, 3, 5);

// 创建Trefoil结（p=2, q=3）
const geometry = new THREE.TorusKnotGeometry(1, 0.4, 100, 16, 2, 3);
```

#### p和q参数的影响

```
p=2, q=3              p=3, q=5              p=4, q=7
   ○                    ○                    ○
  ╱ ╲                ╱ ╲                  ╱ ╲
 ╱   ╲              ╱   ╲                ╱   ╲
╱     ╲            ╱     ╲              ╱     ╲
╲     ╱            ╲     ╱              ╲     ╱
 ╲   ╱              ╲   ╱                ╲   ╱
  ╲ ╱                ╲ ╱                  ╲ ╱
   ○                    ○                    ○
(简单结）             (复杂结）             (更复杂结）
```

### 3.4 RingGeometry（圆环）

RingGeometry是创建平面圆环的几何体。

#### 构造函数

```javascript
new THREE.RingGeometry(
    innerRadius,  // 内半径
    outerRadius,  // 外半径
    thetaSegments, // 分段数（默认32）
    phiSegments   // 径向分段数（默认1）
);
```

#### 使用示例

```javascript
// 创建标准圆环
const geometry = new THREE.RingGeometry(0.5, 1, 32);

// 创建细环
const geometry = new THREE.RingGeometry(0.8, 1, 32);

// 创建厚环
const geometry = new THREE.RingGeometry(0.2, 1, 32);

// 创建分段环（用于纹理映射）
const geometry = new THREE.RingGeometry(0.5, 1, 32, 4);
```

### 3.5 CapsuleGeometry（胶囊体）

胶囊体是创建胶囊形状的几何体。

#### 构造函数

```javascript
new THREE.CapsuleGeometry(
    radius,         // 半径
    length,         // 长度（不包括两端的半球）
    capSegments,    // 帽分段数（默认4）
    radialSegments  // 径向分段数（默认8）
);
```

#### 使用示例

```javascript
// 创建标准胶囊体
const geometry = new THREE.CapsuleGeometry(0.5, 1, 4, 8);

// 创建长胶囊体
const geometry = new THREE.CapsuleGeometry(0.5, 2, 4, 8);

// 创建粗胶囊体
const geometry = new THREE.CapsuleGeometry(1, 1, 4, 8);
```

#### 胶囊体结构

```
     ┌───┐  ← 上半球
    ╱     ╲
   │       │  ← 圆柱部分
   │       │
    ╲     ╱
     └───┘  ← 下半球
    length  ← 长度参数
```

---

## 第四章：BufferGeometry详解

### 4.1 BufferGeometry概述

BufferGeometry是Three.js中最灵活的几何体类型，允许你直接操作顶点数据。

#### 为什么使用BufferGeometry？

| 特性 | 内置几何体 | BufferGeometry |
|------|-----------|---------------|
| 易用性 | 高 | 低 |
| 灵活性 | 低 | 高 |
| 性能 | 高 | 最高 |
| 适用场景 | 常见形状 | 自定义形状、性能优化 |

### 4.2 创建自定义BufferGeometry

#### 基本步骤

```javascript
// 步骤1：创建BufferGeometry对象
const geometry = new THREE.BufferGeometry();

// 步骤2：定义顶点位置
const vertices = new Float32Array([
    -1, -1, 0,  // 顶点0
     1, -1, 0,  // 顶点1
     0,  1, 0   // 顶点2
]);

// 步骤3：添加位置属性
geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));

// 步骤4：定义索引（可选）
const indices = new Uint16Array([0, 1, 2]);
geometry.setIndex(new THREE.BufferAttribute(indices, 1));

// 步骤5：计算其他属性
geometry.computeVertexNormals();  // 计算法线
geometry.computeBoundingBox();    // 计算包围盒
geometry.computeBoundingSphere(); // 计算包围球
```

#### 完整示例：创建金字塔

```javascript
const geometry = new THREE.BufferGeometry();

// 定义顶点（5个顶点：4个底面顶点 + 1个顶点）
const vertices = new Float32Array([
    // 底面（4个顶点）
    -1, 0, -1,  // 顶点0
     1, 0, -1,  // 顶点1
     1, 0,  1,  // 顶点2
    -1, 0,  1,  // 顶点3
    // 顶点（1个顶点）
     0, 2,  0   // 顶点4
]);

geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));

// 定义索引（6个三角形）
const indices = new Uint16Array([
    // 底面（2个三角形）
    0, 1, 2,  // 三角形1
    0, 2, 3,  // 三角形2
    // 侧面（4个三角形）
    0, 4, 1,  // 前面
    1, 4, 2,  // 右面
    2, 4, 3,  // 后面
    3, 4, 0   // 左面
]);

geometry.setIndex(new THREE.BufferAttribute(indices, 1));

// 计算法线
geometry.computeVertexNormals();

// 创建材质和网格
const material = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    side: THREE.DoubleSide
});
const pyramid = new THREE.Mesh(geometry, material);
scene.add(pyramid);
```

### 4.3 添加UV坐标

UV坐标用于将纹理映射到几何体表面。

```javascript
// 定义UV坐标（每个顶点2个值：u, v）
const uvs = new Float32Array([
    // 底面UV
    0, 0,  // 顶点0 - 左下角
    1, 0,  // 顶点1 - 右下角
    1, 1,  // 顶点2 - 右上角
    0, 1,  // 顶点3 - 左上角
    // 顶点UV
    0.5, 0.5  // 顶点4 - 中心
]);

// 添加UV属性
geometry.setAttribute('uv', new THREE.BufferAttribute(uvs, 2));
```

### 4.4 添加顶点颜色

顶点颜色允许你为每个顶点设置不同的颜色。

```javascript
// 定义顶点颜色（每个顶点3个值：r, g, b）
const colors = new Float32Array([
    // 底面颜色（渐变）
    1, 0, 0,  // 顶点0 - 红色
    0, 1, 0,  // 顶点1 - 绿色
    0, 0, 1,  // 顶点2 - 蓝色
    1, 1, 0,  // 顶点3 - 黄色
    // 顶点颜色
    1, 0, 1   // 顶点4 - 紫色
]);

// 添加颜色属性
geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

// 创建材质（启用顶点颜色）
const material = new THREE.MeshStandardMaterial({
    vertexColors: true,  // 启用顶点颜色
    side: THREE.DoubleSide
});
```

### 4.5 修改BufferGeometry

#### 修改顶点位置

```javascript
// 获取位置属性
const positions = geometry.attributes.position;

// 修改单个顶点
positions.setXYZ(0, 1, 2, 3);  // 设置第0个顶点的位置

// 修改多个顶点
for (let i = 0; i < positions.count; i++) {
    const x = positions.getX(i);
    const y = positions.getY(i);
    const z = positions.getZ(i);
    
    // 对顶点进行变换
    positions.setXYZ(i, x * 2, y * 2, z * 2);
}

// 标记需要更新
positions.needsUpdate = true;

// 重新计算法线
geometry.computeVertexNormals();
```

#### 动态更新几何体

```javascript
// 创建动画循环
function animate() {
    requestAnimationFrame(animate);

    const time = Date.now() * 0.001;
    const positions = geometry.attributes.position;

    // 动态修改顶点位置
    for (let i = 0; i < positions.count; i++) {
        const x = positions.getX(i);
        const y = positions.getY(i);
        const z = positions.getZ(i);

        // 添加波动效果
        positions.setXYZ(i, x, y + Math.sin(time + x) * 0.5, z);
    }

    positions.needsUpdate = true;
    geometry.computeVertexNormals();

    renderer.render(scene, camera);
}

animate();
```

### 4.6 InterleavedBuffer（交错缓冲区）

交错缓冲区将多个属性存储在同一个数组中，提高缓存利用率。

```javascript
// 创建交错缓冲区
const interleavedBuffer = new THREE.InterleavedBuffer(
    new Float32Array([
        // pos.x, pos.y, pos.z, uv.u, uv.v (每个顶点5个值）
        -1, -1, 0, 0, 0,  // 顶点0
         1, -1, 0, 1, 0,  // 顶点1
         1,  1, 0, 1, 1,  // 顶点2
        -1,  1, 0, 0, 1   // 顶点3
    ]),
    5  // stride（每个顶点的浮点数）
);

// 创建几何体
const geometry = new THREE.BufferGeometry();

// 添加位置属性（从偏移0开始，每个顶点3个值）
geometry.setAttribute(
    'position',
    new THREE.InterleavedBufferAttribute(interleavedBuffer, 3, 0)
);

// 添加UV属性（从偏移3开始，每个顶点2个值）
geometry.setAttribute(
    'uv',
    new THREE.InterleavedBufferAttribute(interleavedBuffer, 2, 3)
);
```

#### 交错缓冲区优势

```
标准布局（多个数组）：
positions: [x0, y0, z0, x1, y1, z1, x2, y2, z2, ...]
uvs:       [u0, v0, u1, v1, u2, v2, ...]

交错布局（单个数组）：
buffer: [x0, y0, z0, u0, v0, x1, y1, z1, u1, v1, x2, y2, z2, u2, v2, ...]

优势：
- 更好的缓存局部性
- 减少内存访问次数
- 提高渲染性能
```

---

## 第五章：点云与线段

### 5.1 Points（点云）

点云由大量独立的点组成，常用于粒子效果、数据可视化等。

#### 创建点云

```javascript
// 创建几何体
const geometry = new THREE.BufferGeometry();
const pointCount = 1000;
const positions = new Float32Array(pointCount * 3);

// 生成随机点
for (let i = 0; i < pointCount; i++) {
    positions[i * 3] = (Math.random() - 0.5) * 10;     // x
    positions[i * 3 + 1] = (Math.random() - 0.5) * 10; // y
    positions[i * 3 + 2] = (Math.random() - 0.5) * 10; // z
}

geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));

// 创建材质
const material = new THREE.PointsMaterial({
    size: 0.1,              // 点的大小
    sizeAttenuation: true,    // 点的大小随距离衰减
    color: 0xffffff,          // 点的颜色
    transparent: true,         // 透明
    opacity: 0.8              // 透明度
});

// 创建点云
const points = new THREE.Points(geometry, material);
scene.add(points);
```

#### 点云颜色

```javascript
// 为每个点设置颜色
const colors = new Float32Array(pointCount * 3);

for (let i = 0; i < pointCount; i++) {
    colors[i * 3] = Math.random();     // r
    colors[i * 3 + 1] = Math.random(); // g
    colors[i * 3 + 2] = Math.random(); // b
}

geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

// 启用顶点颜色
const material = new THREE.PointsMaterial({
    size: 0.1,
    vertexColors: true,  // 启用顶点颜色
    sizeAttenuation: true
});
```

#### 点云形状

```javascript
// 使用纹理创建自定义点形状
const texture = new THREE.TextureLoader().load('particle.png');
const material = new THREE.PointsMaterial({
    size: 0.5,
    map: texture,           // 使用纹理
    transparent: true,
    alphaTest: 0.5,        // 透明度测试
    depthWrite: false        // 禁用深度写入
});
```

### 5.2 Line（线段）

Line将点按顺序连接成一条连续的线。

#### 创建Line

```javascript
// 定义点
const points = [
    new THREE.Vector3(-1, 0, 0),
    new THREE.Vector3(0, 1, 0),
    new THREE.Vector3(1, 0, 0)
];

// 创建几何体
const geometry = new THREE.BufferGeometry().setFromPoints(points);

// 创建材质
const material = new THREE.LineBasicMaterial({
    color: 0xff0000,
    linewidth: 2
});

// 创建线
const line = new THREE.Line(geometry, material);
scene.add(line);
```

#### LineLoop（闭合线段）

LineLoop将首尾相连形成闭合形状。

```javascript
// 定义点
const points = [
    new THREE.Vector3(0, 0, 0),
    new THREE.Vector3(1, 1, 0),
    new THREE.Vector3(2, 0, 0),
    new THREE.Vector3(1, -1, 0)
];

// 创建几何体
const geometry = new THREE.BufferGeometry().setFromPoints(points);

// 创建材质
const material = new THREE.LineBasicMaterial({
    color: 0x00ff00,
    linewidth: 2
});

// 创建闭合线
const lineLoop = new THREE.LineLoop(geometry, material);
scene.add(lineLoop);
```

#### LineSegments（独立线段）

LineSegments每两个点组成一条独立的线段。

```javascript
// 创建几何体
const geometry = new THREE.BufferGeometry();
const segmentCount = 100;
const positions = new Float32Array(segmentCount * 6);  // 每条线段2个点，每个点3个坐标

// 生成随机线段
for (let i = 0; i < segmentCount; i++) {
    positions[i * 6] = (Math.random() - 0.5) * 10;     // 线段起点x
    positions[i * 6 + 1] = (Math.random() - 0.5) * 10; // 线段起点y
    positions[i * 6 + 2] = (Math.random() - 0.5) * 10; // 线段起点z
    positions[i * 6 + 3] = (Math.random() - 0.5) * 10; // 线段终点x
    positions[i * 6 + 4] = (Math.random() - 0.5) * 10; // 线段终点y
    positions[i * 6 + 5] = (Math.random() - 0.5) * 10; // 线段终点z
}

geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));

// 创建材质
const material = new THREE.LineBasicMaterial({
    color: 0x0000ff,
    transparent: true,
    opacity: 0.6
});

// 创建线段
const lineSegments = new THREE.LineSegments(geometry, material);
scene.add(lineSegments);
```

#### 线段类型对比

```
Line（连续线）：
    0────1────2────3
    (连接所有点）

LineLoop（闭合线）：
    0────1
    │     │
    3────2
    (首尾相连）

LineSegments（独立线段）：
    0────1    2────3    4────5
    (每两个点一条线）
```

### 5.3 EdgesGeometry与WireframeGeometry

#### EdgesGeometry（边缘线）

EdgesGeometry只显示几何体的边缘（硬边）。

```javascript
// 创建立方体
const boxGeometry = new THREE.BoxGeometry(1, 1, 1);

// 创建边缘几何体
const edgesGeometry = new THREE.EdgesGeometry(boxGeometry, 15);  // 15 = 角度阈值

// 创建材质
const edgesMaterial = new THREE.LineBasicMaterial({
    color: 0xffffff,
    linewidth: 2
});

// 创建边缘线
const edges = new THREE.LineSegments(edgesGeometry, edgesMaterial);
scene.add(edges);
```

#### WireframeGeometry（线框）

WireframeGeometry显示几何体的所有三角形边。

```javascript
// 创建立方体
const boxGeometry = new THREE.BoxGeometry(1, 1, 1);

// 创建线框几何体
const wireframeGeometry = new THREE.WireframeGeometry(boxGeometry);

// 创建材质
const wireframeMaterial = new THREE.LineBasicMaterial({
    color: 0xffffff,
    linewidth: 1
});

// 创建线框
const wireframe = new THREE.LineSegments(wireframeGeometry, wireframeMaterial);
scene.add(wireframe);
```

#### Edges vs Wireframe

```
EdgesGeometry（只显示硬边）：
    ┌─────┐
    │     │
    │     │
    └─────┘
    (只显示外轮廓）

WireframeGeometry（显示所有三角形）：
    ┌─────┐
    │╲   ╱│
    │ ╲ ╱ │
    └─────┘
    (显示所有三角形边）
```

---

## 第六章：InstancedMesh实例化

### 6.1 InstancedMesh概述

InstancedMesh是一种高效渲染大量相同几何体的技术。

#### 为什么使用InstancedMesh？

| 方法 | Draw Calls | 内存使用 | 性能 | 适用场景 |
|------|------------|----------|--------|----------|
| 多个Mesh | 高 | 高 | 低 | 少量物体 |
| InstancedMesh | 1 | 低 | 高 | 大量相同物体 |

#### 性能对比

```
传统方法（1000个Mesh）：
Draw Calls: 1000
内存使用: 高
性能: 差

InstancedMesh（1000个实例）：
Draw Calls: 1
内存使用: 低
性能: 优秀
```

### 6.2 创建InstancedMesh

#### 基本用法

```javascript
// 创建基础几何体
const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });

// 创建InstancedMesh
const count = 1000;
const instancedMesh = new THREE.InstancedMesh(geometry, material, count);
scene.add(instancedMesh);

// 为每个实例设置变换矩阵
const dummy = new THREE.Object3D();

for (let i = 0; i < count; i++) {
    // 设置位置
    dummy.position.set(
        (Math.random() - 0.5) * 20,
        (Math.random() - 0.5) * 20,
        (Math.random() - 0.5) * 20
    );

    // 设置旋转
    dummy.rotation.set(
        Math.random() * Math.PI,
        Math.random() * Math.PI,
        Math.random() * Math.PI
    );

    // 设置缩放
    dummy.scale.setScalar(0.5 + Math.random());

    // 更新矩阵
    dummy.updateMatrix();

    // 设置实例的变换矩阵
    instancedMesh.setMatrixAt(i, dummy.matrix);
}

// 标记矩阵需要更新
instancedMesh.instanceMatrix.needsUpdate = true;
```

#### 设置实例颜色

```javascript
// 为每个实例设置颜色
const color = new THREE.Color();

for (let i = 0; i < count; i++) {
    // 生成随机颜色
    color.setHSL(Math.random(), 0.7, 0.5);

    // 设置实例颜色
    instancedMesh.setColorAt(i, color);
}

// 标记颜色需要更新
instancedMesh.instanceColor.needsUpdate = true;
```

### 6.3 动态更新实例

#### 更新单个实例

```javascript
// 获取实例的矩阵
const matrix = new THREE.Matrix4();
instancedMesh.getMatrixAt(index, matrix);

// 修改矩阵
matrix.setPosition(newX, newY, newZ);

// 设置回实例
instancedMesh.setMatrixAt(index, matrix);

// 标记需要更新
instancedMesh.instanceMatrix.needsUpdate = true;
```

#### 动画更新

```javascript
// 动画循环
function animate() {
    requestAnimationFrame(animate);

    const time = Date.now() * 0.001;
    const dummy = new THREE.Object3D();

    // 更新所有实例
    for (let i = 0; i < count; i++) {
        // 获取当前矩阵
        instancedMesh.getMatrixAt(i, dummy.matrix);
        dummy.matrix.decompose(dummy.position, dummy.quaternion, dummy.scale);

        // 添加动画效果
        dummy.position.y = Math.sin(time + i * 0.1) * 0.5;
        dummy.rotation.y += 0.01;

        // 更新矩阵
        dummy.updateMatrix();
        instancedMesh.setMatrixAt(i, dummy.matrix);
    }

    instancedMesh.instanceMatrix.needsUpdate = true;

    renderer.render(scene, camera);
}

animate();
```

### 6.4 InstancedMesh与射线检测

```javascript
// 创建射线投射器
const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2();

// 监听鼠标移动
window.addEventListener('mousemove', (event) => {
    mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
    mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

    // 投射射线
    raycaster.setFromCamera(mouse, camera);

    // 检测相交
    const intersects = raycaster.intersectObject(instancedMesh);

    if (intersects.length > 0) {
        // 获取实例ID
        const instanceId = intersects[0].instanceId;
        console.log('点击了实例:', instanceId);
    }
});
```

---

## 第七章：几何体优化

### 7.1 减少Draw Calls

Draw Calls是渲染性能的关键指标，越少越好。

#### 合并几何体

```javascript
import { mergeGeometries } from 'three/examples/jsm/utils/BufferGeometryUtils.js';

// 创建多个几何体
const geo1 = new THREE.BoxGeometry(1, 1, 1);
const geo2 = new THREE.SphereGeometry(1, 32, 32);
const geo3 = new THREE.ConeGeometry(1, 2, 32);

// 合并几何体
const mergedGeometry = mergeGeometries([geo1, geo2, geo3]);

// 创建网格
const material = new THREE.MeshStandardMaterial({ color: 0xff0000 });
const mesh = new THREE.Mesh(mergedGeometry, material);
scene.add(mesh);
```

#### 合并的优势

```
未合并（3个Draw Calls）：
Mesh 1 → Draw Call 1
Mesh 2 → Draw Call 2
Mesh 3 → Draw Call 3

合并后（1个Draw Call）：
Merged Mesh → Draw Call 1
```

### 7.2 使用索引

索引可以重用顶点，减少数据量。

```javascript
// 不使用索引（6个顶点）
const vertices = new Float32Array([
    0, 0, 0,  // 顶点0
    1, 0, 0,  // 顶点1
    1, 1, 0,  // 顶点2
    0, 1, 0,  // 顶点3
    0, 0, 0,  // 顶点0（重复）
    1, 1, 0   // 顶点2（重复）
]);

// 使用索引（4个顶点）
const vertices = new Float32Array([
    0, 0, 0,  // 顶点0
    1, 0, 0,  // 顶点1
    1, 1, 0,  // 顶点2
    0, 1, 0   // 顶点3
]);

const indices = new Uint16Array([
    0, 1, 2,  // 三角形1
    0, 2, 3   // 三角形2（重用顶点0和2）
]);
```

### 7.3 选择合适的分段数

分段数影响平滑度和性能，需要平衡。

#### 分段数建议

| 用途 | 推荐分段数 | 说明 |
|------|------------|------|
| 低多边形风格 | 4-8 | 刻意保留多边形感 |
| 性能优先 | 8-16 | 平衡性能和质量 |
| 标准质量 | 32-64 | 大多数场景 |
| 高质量 | 64-128 | 特写镜头 |
| 极高质量 | 128-256 | 电影级质量 |

#### 示例：球体分段数

```javascript
// 低多边形（性能优先）
const lowPolySphere = new THREE.SphereGeometry(1, 8, 8);

// 标准质量（平衡）
const standardSphere = new THREE.SphereGeometry(1, 32, 32);

// 高质量（特写镜头）
const highQualitySphere = new THREE.SphereGeometry(1, 64, 64);
```

### 7.4 内存管理

#### 正确销毁几何体

```javascript
// 销毁几何体
function disposeGeometry(geometry) {
    // 释放所有属性
    for (const name in geometry.attributes) {
        geometry.attributes[name].dispose();
    }

    // 释放索引
    if (geometry.index) {
        geometry.index.dispose();
    }

    // 释放几何体
    geometry.dispose();
}

// 使用
disposeGeometry(mesh.geometry);
```

#### 销毁网格

```javascript
// 销毁网格
function disposeMesh(mesh) {
    // 销毁几何体
    if (mesh.geometry) {
        disposeGeometry(mesh.geometry);
    }

    // 销毁材质
    if (mesh.material) {
        if (Array.isArray(mesh.material)) {
            mesh.material.forEach(m => m.dispose());
        } else {
            mesh.material.dispose();
        }
    }

    // 从场景中移除
    scene.remove(mesh);
}

// 使用
disposeMesh(mesh);
```

### 7.5 使用LOD（Level of Detail）

LOD根据距离自动切换不同细节的几何体。

```javascript
// 创建不同细节的几何体
const highDetailGeometry = new THREE.SphereGeometry(1, 64, 64);
const mediumDetailGeometry = new THREE.SphereGeometry(1, 32, 32);
const lowDetailGeometry = new THREE.SphereGeometry(1, 16, 16);

// 创建不同细节的网格
const highDetailMesh = new THREE.Mesh(highDetailGeometry, material);
const mediumDetailMesh = new THREE.Mesh(mediumDetailGeometry, material);
const lowDetailMesh = new THREE.Mesh(lowDetailGeometry, material);

// 创建LOD对象
const lod = new THREE.LOD();

// 添加不同细节的网格
lod.addLevel(highDetailMesh, 0);      // 距离0-10使用高细节
lod.addLevel(mediumDetailMesh, 10);   // 距离10-20使用中细节
lod.addLevel(lowDetailMesh, 20);      // 距离20+使用低细节

scene.add(lod);
```

#### LOD工作原理

```
相机位置：
    ↑
    │
    │  0-10: 高细节（64分段）
    │  10-20: 中细节（32分段）
    │  20+: 低细节（16分段）
    │
    └─────────────
```

---

## 常见问题与故障排除

### 问题1：几何体显示为黑色

**可能原因：**
1. 没有添加光源
2. 法线计算错误
3. 材质设置错误

**解决方案：**
```javascript
// 添加光源
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// 计算法线
geometry.computeVertexNormals();

// 检查材质
const material = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.5
});
```

### 问题2：几何体背面不可见

**可能原因：**
- 材质的side属性设置不正确

**解决方案：**
```javascript
// 渲染双面
const material = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    side: THREE.DoubleSide  // 渲染正面和背面
});
```

### 问题3：纹理映射不正确

**可能原因：**
- UV坐标设置错误
- 纹理坐标范围不正确

**解决方案：**
```javascript
// 检查UV坐标
const uvs = geometry.attributes.uv;
for (let i = 0; i < uvs.count; i++) {
    const u = uvs.getX(i);
    const v = uvs.getY(i);
    console.log(`顶点${i}的UV坐标: (${u}, ${v})`);
}

// UV坐标应该在0-1范围内
```

### 问题4：性能问题

**可能原因：**
1. 几何体分段数过高
2. Draw Calls过多
3. 没有使用实例化

**解决方案：**
```javascript
// 降低分段数
const geometry = new THREE.SphereGeometry(1, 32, 32);  // 而不是64, 64

// 使用InstancedMesh
const instancedMesh = new THREE.InstancedMesh(geometry, material, 1000);

// 合并静态几何体
const mergedGeometry = mergeGeometries([geo1, geo2, geo3]);
```

### 问题5：几何体变形

**可能原因：**
- 顶点数据错误
- 索引设置错误

**解决方案：**
```javascript
// 使用辅助工具可视化
const wireframe = new THREE.WireframeGeometry(geometry);
const wireframeMesh = new THREE.LineSegments(
    wireframe,
    new THREE.LineBasicMaterial({ color: 0xffffff })
);
scene.add(wireframeMesh);

// 检查顶点数量
console.log('顶点数量:', geometry.attributes.position.count);
console.log('索引数量:', geometry.index ? geometry.index.count : '无索引');
```

---

## 最佳实践

### 1. 选择合适的几何体类型

| 场景 | 推荐几何体 | 原因 |
|--------|------------|------|
| 简单立方体 | BoxGeometry | 内置，性能好 |
| 球形物体 | SphereGeometry | 内置，易于使用 |
| 自定义形状 | BufferGeometry | 灵活，性能高 |
| 大量相同物体 | InstancedMesh | 极高性能 |
| 粒子效果 | Points | 专用优化 |

### 2. 合理设置分段数

```javascript
// 根据物体大小和重要性设置分段数
const getSegmentCount = (size, importance) => {
    const base = importance === 'high' ? 64 : 32;
    return Math.max(8, Math.floor(base * size));
};

// 使用
const geometry = new THREE.SphereGeometry(
    1,
    getSegmentCount(1, 'high'),
    getSegmentCount(1, 'high')
);
```

### 3. 使用索引优化

```javascript
// 总是使用索引（除非有特殊需求）
geometry.setIndex(new THREE.BufferAttribute(indices, 1));

// 优势：
// - 减少内存使用
// - 提高缓存利用率
// - 加速渲染
```

### 4. 及时释放资源

```javascript
// 在对象不再使用时释放资源
function cleanup() {
    // 销毁所有网格
    scene.traverse((object) => {
        if (object.isMesh) {
            disposeMesh(object);
        }
    });

    // 清空场景
    scene.clear();
}
```

### 5. 使用辅助工具调试

```javascript
// 使用线框查看几何体结构
const wireframe = new THREE.WireframeGeometry(geometry);
const wireframeMesh = new THREE.LineSegments(
    wireframe,
    new THREE.LineBasicMaterial({ color: 0xffffff })
);
scene.add(wireframeMesh);

// 使用包围盒查看物体范围
const boxHelper = new THREE.BoxHelper(mesh);
scene.add(boxHelper);
```

---

## 下一步学习

恭喜你完成了Three.js几何体教程！接下来你可以学习：

### 推荐学习路径

1. **第三部分：材质与纹理**
   - 深入学习各种材质类型
   - 使用纹理贴图
   - 环境贴图和反射

2. **第四部分：光照系统**
   - 各种光源类型详解
   - 阴影配置
   - 全局光照技术

3. **第五部分：动画与交互**
   - 使用Tween.js
   - 鼠标交互
   - 键盘控制
   - 触摸事件

### 推荐资源

#### 官方资源
- [Three.js官方文档 - Geometry](https://threejs.org/docs/#api/en/core/BufferGeometry)
- [Three.js官方示例 - Geometries](https://threejs.org/examples/#webgl_geometry)

#### 学习资源
- [Three.js Journey](https://threejs-journey.com/)
- [Bruno Simon的YouTube频道](https://www.youtube.com/c/brunosimon)

### 实践项目建议

1. **初级项目**
   - 创建一个低多边形风格的场景
   - 制作一个粒子星空效果
   - 实现一个简单的3D图表

2. **中级项目**
   - 创建一个程序化生成的城市
   - 制作一个点云可视化
   - 实现一个LOD系统

3. **高级项目**
   - 创建一个自定义地形系统
   - 制作一个大规模粒子系统
   - 实现一个动态几何体变形效果

---

## 总结

本教程涵盖了Three.js几何体的核心知识，包括：

✅ 几何体的基础概念和架构
✅ 各种内置几何体的使用
✅ BufferGeometry的自定义创建
✅ 顶点、索引、法线、UV等核心概念
✅ 点云和线段的应用
✅ InstancedMesh的高效渲染
✅ 几何体的性能优化技巧

通过学习本教程，你应该能够：
- 选择合适的几何体类型
- 创建自定义几何体
- 优化几何体性能
- 实现复杂的几何体效果

**继续加油！Three.js的几何体系统非常强大，期待你创造出令人惊叹的3D作品！** 🚀

---

## 附录

### A. 几何体参数速查

| 几何体 | 主要参数 | 默认值 |
|--------|----------|--------|
| BoxGeometry | width, height, depth | 1, 1, 1 |
| SphereGeometry | radius, widthSegments, heightSegments | 1, 32, 16 |
| PlaneGeometry | width, height | 1, 1 |
| CircleGeometry | radius, segments | 1, 32 |
| CylinderGeometry | radiusTop, radiusBottom, height | 1, 1, 1 |
| ConeGeometry | radius, height | 1, 1 |
| TorusGeometry | radius, tube | 1, 0.4 |
| TorusKnotGeometry | radius, tube, p, q | 1, 0.4, 2, 3 |
| RingGeometry | innerRadius, outerRadius | 0.5, 1 |
| CapsuleGeometry | radius, length | 1, 1 |

### B. BufferAttribute类型速查

| 属性 | 数据类型 | 分量数 | 范围 |
|------|----------|---------|------|
| position | Float32Array | 3 | 任意 |
| normal | Float32Array | 3 | -1到1 |
| uv | Float32Array | 2 | 0到1 |
| color | Float32Array | 3 | 0到1 |
| index | Uint16Array/Uint32Array | 1 | 0到顶点数-1 |

### C. 性能优化清单

- [ ] 使用索引重用顶点
- [ ] 合并静态几何体
- [ ] 使用InstancedMesh渲染大量相同物体
- [ ] 选择合适的分段数
- [ ] 使用LOD系统
- [ ] 及时释放不再使用的资源
- [ ] 使用交错缓冲区提高缓存利用率
- [ ] 避免频繁更新几何体数据

---



**Three.js版本**: r160

---

**祝你学习愉快！如有问题，欢迎查阅官方文档或参与社区讨论。** 🎉
