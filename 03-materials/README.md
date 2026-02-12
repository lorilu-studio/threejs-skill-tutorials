# Three.js 材质教程 - 第三部分：Materials详解

> 本教程深入讲解Three.js材质（Materials）的核心概念、API使用和高级技巧。通过丰富的示例和实践，帮助你掌握3D材质的创建、配置和优化。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：材质基础概念](#第一章材质基础概念)
- [第二章：基础材质类型](#第二章基础材质类型)
- [第三章：PBR材质](#第三章pbr材质)
- [第四章：高级材质](#第四章高级材质)
- [第五章：纹理贴图](#第五章纹理贴图)
- [第六章：着色器材质](#第六章着色器材质)
- [第七章：材质优化](#第七章材质优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Three.js材质的核心概念和架构
- ✅ 熟练使用各种基础材质类型
- ✅ 掌握PBR（物理渲染）材质的配置
- ✅ 理解高级材质的特性（清漆、透射、彩虹等）
- ✅ 使用各种纹理贴图增强材质效果
- ✅ 创建自定义着色器材质
- ✅ 掌握材质的性能优化技巧

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器的基本概念
- **几何体基础**: 理解顶点、面、法线等概念
- **JavaScript (ES6+)**: 对象、类、模块等现代语法

### 推荐知识
- **计算机图形学基础**: 光照模型、着色器
- **物理渲染理论**: PBR、BRDF、能量守恒

---

## 第一章：材质基础概念

### 1.1 什么是材质（Material）？

**材质**定义了3D物体的外观特性，决定了物体如何与光线交互。

#### 材质的作用

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

- **几何体**就像建筑的**结构框架**
- **材质**就像建筑的**装修材料**（油漆、壁纸、玻璃等）
- **网格**就像**装修好的建筑**

### 1.2 Three.js材质类型

Three.js提供了多种材质类型，每种都有特定的用途和性能特点。

| 材质类型 | 光照计算 | 性能 | 适用场景 |
|----------|----------|--------|----------|
| **MeshBasicMaterial** | 无 | 最高 | 线框、调试、UI元素 |
| **MeshLambertMaterial** | 漫反射 | 高 | 哑光表面、性能优先 |
| **MeshPhongMaterial** | 漫反射+高光 | 中 | 塑料、陶瓷等有光泽物体 |
| **MeshStandardMaterial** | PBR | 中 | 真实感渲染（推荐） |
| **MeshPhysicalMaterial** | 高级PBR | 低 | 玻璃、清漆、织物等特殊材质 |
| **MeshToonMaterial** | 卡通着色 | 中 | 卡通风格渲染 |
| **MeshNormalMaterial** | 法线可视化 | 高 | 调试、特殊效果 |
| **MeshDepthMaterial** | 深度可视化 | 高 | 深度效果、阴影 |
| **ShaderMaterial** | 自定义 | 可变 | 自定义效果 |
| **RawShaderMaterial** | 完全自定义 | 可变 | 高级自定义效果 |

### 1.3 材质的通用属性

所有材质都共享以下基础属性：

#### 可见性属性

```javascript
material.visible = true;           // 是否可见
material.transparent = false;       // 是否透明
material.opacity = 1.0;            // 透明度（0-1）
material.alphaTest = 0;             // 透明度测试阈值
```

#### 渲染属性

```javascript
material.side = THREE.FrontSide;   // 渲染面：FrontSide, BackSide, DoubleSide
material.depthTest = true;          // 深度测试
material.depthWrite = true;         // 深度写入
material.colorWrite = true;         // 颜色写入
```

#### 混合模式

```javascript
material.blending = THREE.NormalBlending;
// NormalBlending - 正常混合
// AdditiveBlending - 加法混合
// SubtractiveBlending - 减法混合
// MultiplyBlending - 乘法混合
// CustomBlending - 自定义混合
```

#### 多边形偏移

```javascript
material.polygonOffset = false;          // 启用多边形偏移
material.polygonOffsetFactor = 0;         // 偏移因子
material.polygonOffsetUnits = 0;           // 偏移单位
```

**用途**：解决Z-fighting（深度冲突）问题

```
Z-fighting问题：
    ┌─────┐
    │▓▓▓▓▓│  ← 两个面重叠，产生闪烁
    └─────┘

解决方案（多边形偏移）：
    ┌─────┐
    │     │
    └─────┘
    ┌─────┐  ← 稍微偏移，避免重叠
    │     │
    └─────┘
```

---

## 第二章：基础材质类型

### 2.1 MeshBasicMaterial

最简单的材质，不受光照影响。

#### 特点
- ✅ 性能最高
- ✅ 始终可见
- ❌ 无光照效果
- ❌ 无真实感

#### 使用示例

```javascript
const material = new THREE.MeshBasicMaterial({
    color: 0xff0000,              // 颜色（十六进制）
    transparent: true,              // 透明
    opacity: 0.5,                 // 透明度
    side: THREE.DoubleSide,         // 双面渲染
    wireframe: false,               // 线框模式
    map: texture,                  // 颜色贴图
    alphaMap: alphaTexture,        // 透明度贴图
    envMap: envTexture,            // 环境贴图
    reflectivity: 1,               // 反射强度
    fog: true                      // 受雾影响
});

const mesh = new THREE.Mesh(geometry, material);
scene.add(mesh);
```

#### 使用场景

| 场景 | 说明 |
|------|------|
| 线框显示 | wireframe: true |
| UI元素 | 无需光照的界面元素 |
| 调试 | 快速查看几何体结构 |
| 特殊效果 | 发光、全息等效果 |

### 2.2 MeshLambertMaterial

漫反射材质，性能优于Phong。

#### 特点
- ✅ 性能较高
- ✅ 有漫反射光照
- ❌ 无高光
- ❌ 不适合金属

#### 使用示例

```javascript
const material = new THREE.MeshLambertMaterial({
    color: 0x00ff00,              // 漫反射颜色
    emissive: 0x111111,           // 自发光颜色
    emissiveIntensity: 1,           // 自发光强度
    map: texture,                  // 颜色贴图
    emissiveMap: emissiveTexture,  // 自发光贴图
    envMap: envTexture,            // 环境贴图
    reflectivity: 0.5,             // 反射强度
    side: THREE.DoubleSide
});
```

#### 光照模型

Lambert使用简化的漫反射模型：

```
Lambert光照 = 环境光 + 漫反射光

漫反射 = 光源强度 × 材质颜色 × max(0, 法线·光照方向)
```

### 2.3 MeshPhongMaterial

Phong材质，支持高光反射。

#### 特点
- ✅ 有高光效果
- ✅ 适合塑料、陶瓷
- ❌ 性能低于Lambert
- ❌ 不符合物理规律

#### 使用示例

```javascript
const material = new THREE.MeshPhongMaterial({
    color: 0x0000ff,              // 漫反射颜色
    specular: 0xffffff,            // 高光颜色
    shininess: 100,               // 高光强度（0-1000）
    emissive: 0x000000,           // 自发光颜色
    flatShading: false,            // 平面着色
    map: texture,                 // 颜色贴图
    specularMap: specTexture,      // 高光贴图
    normalMap: normalTexture,       // 法线贴图
    normalScale: new THREE.Vector2(1, 1),  // 法线强度
    bumpMap: bumpTexture,          // 凹凸贴图
    bumpScale: 1,                 // 凹凸强度
    displacementMap: dispTexture,  // 置换贴图
    displacementScale: 1           // 置换强度
});
```

#### 光照模型

Phong使用Blinn-Phong光照模型：

```
Phong光照 = 环境光 + 漫反射 + 高光

漫反射 = 光源强度 × 材质颜色 × max(0, 法线·光照方向)
高光 = 光源强度 × 高光颜色 × (法线·半角向量)^shininess
```

#### 高光效果对比

```
shininess = 10（低光）     shininess = 100（中光）     shininess = 1000（高光）
       ●                           ●                           ●
      ╱ ╲                        ╱ ╲                        ╱ ╲
     ╱   ╲                      ╱   ╲                      ╱   ╲
    ●─────●                    ●─────●                    ●─────●
   (大面积高光）              (中等高光）                (小面积高光）
```

### 2.4 MeshNormalMaterial

法线可视化材质，用于调试。

#### 特点
- ✅ 可视化法线方向
- ✅ 无需光照
- ❌ 不适合最终渲染

#### 使用示例

```javascript
const material = new THREE.MeshNormalMaterial({
    flatShading: false,            // 平面着色
    wireframe: false               // 线框模式
});
```

#### 法线颜色映射

```
法线方向 → 颜色
(1, 0, 0) → 红色   +X
(-1, 0, 0) → 青色   -X
(0, 1, 0) → 绿色   +Y
(0, -1, 0) → 洋红色 -Y
(0, 0, 1) → 蓝色   +Z
(0, 0, -1) → 黄色  -Z
```

#### 使用场景

- 调试法线方向
- 检查模型问题
- 创建特殊视觉效果

### 2.5 MeshDepthMaterial

深度可视化材质。

#### 特点
- ✅ 可视化深度值
- ✅ 用于深度效果
- ❌ 不适合最终渲染

#### 使用示例

```javascript
const material = new THREE.MeshDepthMaterial({
    depthPacking: THREE.RGBADepthPacking,  // 深度打包方式
});
```

#### 使用场景

- 深度效果（DOF）
- 阴影贴图
- 深度缓冲可视化

---

## 第三章：PBR材质

### 3.1 PBR（物理渲染）概述

PBR（Physically Based Rendering）基于物理规律的渲染方法。

#### PBR核心原则

1. **能量守恒**：反射光不能超过入射光
2. **微表面理论**：表面由无数微小镜面组成
3. **菲涅尔效应**：反射率随观察角度变化

#### PBR vs 传统渲染

```
传统渲染（Phong）：
- 不符合物理规律
- 参数不直观
- 需要大量调整

PBR渲染：
- 基于物理规律
- 参数直观（粗糙度、金属度）
- 一致性好
```

### 3.2 MeshStandardMaterial

标准PBR材质，推荐用于大多数场景。

#### 核心属性

```javascript
const material = new THREE.MeshStandardMaterial({
    // 基础属性
    color: 0xffffff,              // 基础颜色
    roughness: 0.5,               // 粗糙度（0-1）
    metalness: 0.0,               // 金属度（0-1）

    // 纹理贴图
    map: colorTexture,             // 基础颜色贴图
    roughnessMap: roughTexture,    // 粗糙度贴图
    metalnessMap: metalTexture,    // 金属度贴图
    normalMap: normalTexture,      // 法线贴图
    normalScale: new THREE.Vector2(1, 1),  // 法线强度
    aoMap: aoTexture,             // 环境光遮蔽贴图
    aoMapIntensity: 1,            // AO强度
    displacementMap: dispTexture,  // 置换贴图
    displacementScale: 0.1,        // 置换强度
    displacementBias: 0,           // 置换偏移

    // 自发光
    emissive: 0x000000,           // 自发光颜色
    emissiveIntensity: 1,          // 自发光强度
    emissiveMap: emissiveTexture,  // 自发光贴图

    // 环境
    envMap: envTexture,           // 环境贴图
    envMapIntensity: 1,           // 环境贴图强度

    // 其他
    flatShading: false,            // 平面着色
    wireframe: false,              // 线框模式
    fog: true                     // 受雾影响
});
```

#### 粗糙度（Roughness）

粗糙度控制表面的光滑程度：

```
roughness = 0（镜面）      roughness = 0.5（中等）      roughness = 1（漫反射）
       ●                          ●                          ●
      ╱ ╲                       ╱ ╲                       ╱ ╲
     ╱   ╲                     ╱   ╲                     ╱   ╲
    ●─────●                   ●─────●                   ●─────●
   (清晰反射）                (模糊反射）                (无反射）
```

#### 金属度（Metalness）

金属度控制材质的金属特性：

```
metalness = 0（非金属）      metalness = 0.5（混合）      metalness = 1（金属）
  塑料、木材                 半金属                   金属、合金
  - 漫反射强                 - 混合特性                 - 高反射
  - 反射弱                   - 中等反射                 - 无漫反射
```

#### 材质组合示例

```javascript
// 金属材质
const metalMaterial = new THREE.MeshStandardMaterial({
    color: 0xffffff,
    roughness: 0.2,
    metalness: 1.0
});

// 塑料材质
const plasticMaterial = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.0
});

// 粗糙金属
const roughMetalMaterial = new THREE.MeshStandardMaterial({
    color: 0x00ff00,
    roughness: 0.8,
    metalness: 1.0
});

// 光滑塑料
const smoothPlasticMaterial = new THREE.MeshStandardMaterial({
    color: 0x0000ff,
    roughness: 0.2,
    metalness: 0.0
});
```

### 3.3 MeshPhysicalMaterial

高级PBR材质，扩展了StandardMaterial。

#### 新增属性

```javascript
const material = new THREE.MeshPhysicalMaterial({
    // 继承MeshStandardMaterial的所有属性

    // 清漆涂层（汽车漆、清漆）
    clearcoat: 1.0,                    // 清漆强度（0-1）
    clearcoatRoughness: 0.1,            // 清漆粗糙度
    clearcoatMap: ccTexture,            // 清漆贴图
    clearcoatRoughnessMap: ccrTexture,  // 清漆粗糙度贴图
    clearcoatNormalMap: ccnTexture,      // 清漆法线贴图
    clearcoatNormalScale: new THREE.Vector2(1, 1),

    // 透射（玻璃、水）
    transmission: 1.0,                  // 透射率（0-1）
    transmissionMap: transTexture,       // 透射贴图
    thickness: 0.5,                    // 厚度
    thicknessMap: thickTexture,          // 厚度贴图
    attenuationDistance: 1,             // 吸收距离
    attenuationColor: new THREE.Color(0xffffff),  // 吸收颜色

    // 折射
    ior: 1.5,                         // 折射率（1-2.333）

    // 绒布（织物、天鹅绒）
    sheen: 1.0,                       // 绒布强度
    sheenRoughness: 0.5,               // 绒布粗糙度
    sheenColor: new THREE.Color(0xffffff),  // 绒布颜色
    sheenColorMap: sheenTexture,       // 绒布颜色贴图
    sheenRoughnessMap: sheenRoughTexture,  // 绒布粗糙度贴图

    // 彩虹（肥皂泡、油膜）
    iridescence: 1.0,                 // 彩虹强度
    iridescenceIOR: 1.3,              // 彩虹折射率
    iridescenceThicknessRange: [100, 400],  // 彩虹厚度范围
    iridescenceMap: iridTexture,       // 彩虹贴图
    iridescenceThicknessMap: iridThickTexture,  // 彩虹厚度贴图

    // 各向异性（拉丝金属）
    anisotropy: 1.0,                  // 各向异性强度
    anisotropyRotation: 0,             // 各向异性旋转
    anisotropyMap: anisoTexture,       // 各向异性贴图

    // 高光
    specularIntensity: 1,              // 高光强度
    specularColor: new THREE.Color(0xffffff),  // 高光颜色
    specularIntensityMap: specIntTexture,  // 高光强度贴图
    specularColorMap: specColorTexture   // 高光颜色贴图
});
```

#### 清漆涂层（Clearcoat）

模拟表面涂层的额外反射层：

```
清漆效果：
    ┌─────────────┐
    │  清漆层     │  ← 额外反射层
    ├─────────────┤
    │  基础材质   │
    └─────────────┘

应用：
- 汽车漆
- 清漆表面
- 抛光木材
```

#### 透射（Transmission）

模拟透明材质的折射效果：

```javascript
// 玻璃材质
const glassMaterial = new THREE.MeshPhysicalMaterial({
    color: 0xffffff,
    roughness: 0,
    metalness: 0,
    transmission: 1.0,    // 完全透明
    thickness: 0.5,        // 玻璃厚度
    ior: 1.5              // 玻璃折射率
});
```

#### 折射率（IOR）参考

| 材质 | IOR值 |
|------|--------|
| 空气 | 1.0 |
| 水 | 1.33 |
| 玻璃 | 1.5 |
| 钻石 | 2.42 |
| 塑料 | 1.4-1.6 |

#### 绒布效果（Sheen）

模拟织物、天鹅绒等材质：

```javascript
// 绒布材质
const velvetMaterial = new THREE.MeshPhysicalMaterial({
    color: 0xff0066,
    roughness: 0.8,
    metalness: 0,
    sheen: 1.0,
    sheenRoughness: 0.5,
    sheenColor: new THREE.Color(0xffffff)
});
```

#### 彩虹效果（Iridescence）

模拟肥皂泡、油膜等彩虹效果：

```javascript
// 彩虹材质
const iridescenceMaterial = new THREE.MeshPhysicalMaterial({
    color: 0x0000ff,
    roughness: 0.2,
    metalness: 0.8,
    iridescence: 1.0,
    iridescenceIOR: 1.3,
    iridescenceThicknessRange: [100, 400]
});
```

#### 各向异性（Anisotropy）

模拟拉丝金属等方向性反射：

```javascript
// 拉丝金属
const brushedMetalMaterial = new THREE.MeshPhysicalMaterial({
    color: 0xffff00,
    roughness: 0.3,
    metalness: 0.9,
    anisotropy: 1.0,
    anisotropyRotation: 0
});
```

---

## 第四章：高级材质

### 4.1 MeshToonMaterial

卡通着色材质，实现Cel-shading效果。

#### 使用示例

```javascript
const material = new THREE.MeshToonMaterial({
    color: 0x00ff00,
    gradientMap: gradientTexture  // 可选：自定义渐变贴图
});
```

#### 创建渐变贴图

```javascript
// 创建阶梯渐变贴图
const colors = new Uint8Array([0, 128, 255]);
const gradientMap = new THREE.DataTexture(colors, 3, 1, THREE.RedFormat);
gradientMap.minFilter = THREE.NearestFilter;
gradientMap.magFilter = THREE.NearestFilter;
gradientMap.needsUpdate = true;

const toonMaterial = new THREE.MeshToonMaterial({
    color: 0x00ff00,
    gradientMap: gradientMap
});
```

#### 效果对比

```
标准着色                  卡通着色
    ●                          ●
   ╱ ╲                       ╱ ╲
  ╱   ╲                     ╱───╲
 ●─────●                   ●─────●
(平滑过渡）                (阶梯过渡）
```

### 4.2 PointsMaterial

点云材质，用于渲染粒子系统。

#### 使用示例

```javascript
const material = new THREE.PointsMaterial({
    color: 0xffffff,
    size: 0.1,                    // 点的大小
    sizeAttenuation: true,          // 大小随距离衰减
    map: pointTexture,             // 点纹理
    alphaMap: alphaTexture,         // 透明度贴图
    transparent: true,             // 透明
    alphaTest: 0.5,               // 透明度测试
    vertexColors: true,            // 使用顶点颜色
    fog: true                     // 受雾影响
});

const points = new THREE.Points(geometry, material);
scene.add(points);
```

#### 点形状

```javascript
// 使用自定义纹理
const texture = new THREE.TextureLoader().load('particle.png');
const material = new THREE.PointsMaterial({
    size: 0.5,
    map: texture,
    transparent: true,
    alphaTest: 0.5,
    depthWrite: false
});
```

### 4.3 LineBasicMaterial & LineDashedMaterial

线段材质。

#### LineBasicMaterial

```javascript
const material = new THREE.LineBasicMaterial({
    color: 0xffffff,
    linewidth: 1,              // 线宽（注意：>1在某些系统上不工作）
    linecap: 'round',          // 线端点样式：'butt', 'round', 'square'
    linejoin: 'round'          // 线连接样式：'round', 'bevel', 'miter'
});
```

#### LineDashedMaterial

```javascript
const material = new THREE.LineDashedMaterial({
    color: 0xffffff,
    dashSize: 0.5,            // 虚线段长度
    gapSize: 0.25,            // 间隙长度
    scale: 1                   // 缩放因子
});

// 虚线必须计算距离
const line = new THREE.Line(geometry, material);
line.computeLineDistances();
```

---

## 第五章：纹理贴图

### 5.1 纹理类型

Three.js支持多种纹理贴图类型：

| 纹理类型 | 用途 | 颜色通道 |
|----------|------|----------|
| **map** | 基础颜色 | RGB |
| **roughnessMap** | 粗糙度 | 单通道 |
| **metalnessMap** | 金属度 | 单通道 |
| **normalMap** | 法线 | RGB |
| **bumpMap** | 凹凸 | 单通道 |
| **displacementMap** | 置换 | 单通道 |
| **aoMap** | 环境光遮蔽 | 单通道 |
| **emissiveMap** | 自发光 | RGB |
| **alphaMap** | 透明度 | 单通道 |

### 5.2 基础颜色贴图（map）

```javascript
const textureLoader = new THREE.TextureLoader();
const colorTexture = textureLoader.load('texture.jpg');

const material = new THREE.MeshStandardMaterial({
    map: colorTexture,
    roughness: 0.5,
    metalness: 0.0
});
```

#### 纹理参数

```javascript
const texture = textureLoader.load('texture.jpg');

// 重复模式
texture.wrapS = THREE.RepeatWrapping;  // 水平重复
texture.wrapT = THREE.RepeatWrapping;  // 垂直重复

// 重复次数
texture.repeat.set(2, 2);  // 水平2次，垂直2次

// 过滤模式
texture.minFilter = THREE.LinearFilter;    // 缩小过滤
texture.magFilter = THREE.LinearFilter;    // 放大过滤

// 翻转
texture.flipY = false;  // 不翻转Y轴

// 偏移
texture.offset.set(0.5, 0.5);  // 偏移50%

// 旋转
texture.rotation = Math.PI / 4;  // 旋转45度
texture.center.set(0.5, 0.5);  // 旋转中心
```

### 5.3 法线贴图（normalMap）

法线贴图用于模拟表面细节。

```javascript
const normalTexture = textureLoader.load('normal.jpg');

const material = new THREE.MeshStandardMaterial({
    map: colorTexture,
    normalMap: normalTexture,
    normalScale: new THREE.Vector2(1, 1)  // 法线强度
});
```

#### 法线贴图原理

```
法线贴图颜色：
- 红色通道（R）：X方向法线
- 绿色通道（G）：Y方向法线
- 蓝色通道（B）：Z方向法线

颜色映射：
RGB(128, 128, 255) → 法线(0, 0, 1)  // 默认法线
RGB(255, 128, 255) → 法线(1, 0, 1)   // 向右偏移
RGB(128, 255, 255) → 法线(0, 1, 1)   // 向上偏移
```

### 5.4 粗糙度贴图（roughnessMap）

```javascript
const roughnessTexture = textureLoader.load('roughness.jpg');

const material = new THREE.MeshStandardMaterial({
    map: colorTexture,
    roughnessMap: roughnessTexture,
    roughness: 0.5  // 基础粗糙度
});
```

#### 粗糙度贴图颜色

```
黑色（0）→ 光滑
白色（1）→ 粗糙

应用：
- 磨损效果
- 污渍效果
- 材质混合
```

### 5.5 金属度贴图（metalnessMap）

```javascript
const metalnessTexture = textureLoader.load('metalness.jpg');

const material = new THREE.MeshStandardMaterial({
    map: colorTexture,
    metalnessMap: metalnessTexture,
    metalness: 0.5  // 基础金属度
});
```

#### 金属度贴图颜色

```
黑色（0）→ 非金属
白色（1）→ 金属

应用：
- 锈蚀效果
- 材质混合
- 表面处理
```

### 5.6 环境光遮蔽贴图（aoMap）

AO贴图模拟环境光遮蔽效果。

```javascript
const aoTexture = textureLoader.load('ao.jpg');

const material = new THREE.MeshStandardMaterial({
    map: colorTexture,
    aoMap: aoTexture,
    aoMapIntensity: 1.0  // AO强度
});

// AO贴图需要第二UV通道
geometry.setAttribute('uv2', geometry.attributes.uv);
```

#### AO效果

```
无AO                    有AO
    ┌─────┐                ┌─────┐
    │     │                │     │
    │     │                │╲   ╱│
    │     │                │ ╲ ╱ │
    └─────┘                └─────┘
  (均匀光照）              (角落变暗）
```

### 5.7 置换贴图（displacementMap）

置换贴图修改顶点位置，创建真实的几何细节。

```javascript
const displacementTexture = textureLoader.load('displacement.jpg');

const material = new THREE.MeshStandardMaterial({
    map: colorTexture,
    displacementMap: displacementTexture,
    displacementScale: 0.1,    // 置换强度
    displacementBias: 0         // 置换偏移
});
```

#### 置换 vs 凹凸

```
凹凸贴图（假效果）：
    ┌─────┐
    │╲   ╱│
    │ ╲ ╱ │
    └─────┘
  (法线偏移，顶点不变）

置换贴图（真效果）：
    ┌─────┐
    │╲   ╱│
    │ ╲ ╱ │
    └─────┘
  (顶点移动，真实几何）
```

---

## 第六章：着色器材质

### 6.1 ShaderMaterial概述

ShaderMaterial允许使用自定义GLSL着色器。

#### 基本结构

```javascript
const material = new THREE.ShaderMaterial({
    uniforms: {
        time: { value: 0 },
        color: { value: new THREE.Color(0xff0000) },
        texture1: { value: texture }
    },
    vertexShader: `
        varying vec2 vUv;
        uniform float time;

        void main() {
            vUv = uv;
            vec3 pos = position;
            pos.z += sin(pos.x * 10.0 + time) * 0.1;
            gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
        }
    `,
    fragmentShader: `
        varying vec2 vUv;
        uniform vec3 color;
        uniform sampler2D texture1;

        void main() {
            vec4 texColor = texture2D(texture1, vUv);
            gl_FragColor = vec4(color * texColor.rgb, 1.0);
        }
    `,
    transparent: true,
    side: THREE.DoubleSide
});
```

### 6.2 Uniforms

Uniforms是从JavaScript传递到着色器的变量。

```javascript
const material = new THREE.ShaderMaterial({
    uniforms: {
        time: { value: 0 },
        color: { value: new THREE.Color(0xff0000) },
        texture1: { value: texture },
        resolution: { value: new THREE.Vector2(window.innerWidth, window.innerHeight) }
    }
});

// 更新uniform
material.uniforms.time.value = clock.getElapsedTime();
```

### 6.3 内置Uniforms

Three.js自动提供以下uniforms：

```glsl
// 顶点着色器
uniform mat4 modelMatrix;         // 模型矩阵
uniform mat4 modelViewMatrix;     // 模型视图矩阵
uniform mat4 projectionMatrix;    // 投影矩阵
uniform mat4 viewMatrix;          // 视图矩阵
uniform mat3 normalMatrix;        // 法线矩阵
uniform vec3 cameraPosition;      // 相机位置

// 属性
attribute vec3 position;          // 顶点位置
attribute vec3 normal;            // 顶点法线
attribute vec2 uv;                // UV坐标
```

### 6.4 Varyings

Varyings在顶点和片段着色器之间传递数据。

```glsl
// 顶点着色器
varying vec2 vUv;
varying vec3 vNormal;

void main() {
    vUv = uv;
    vNormal = normal;
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}

// 片段着色器
varying vec2 vUv;
varying vec3 vNormal;

void main() {
    float intensity = dot(vNormal, vec3(0.0, 0.0, 1.0));
    gl_FragColor = vec4(vec3(intensity), 1.0);
}
```

### 6.5 RawShaderMaterial

RawShaderMaterial提供完全控制，无内置uniforms。

```javascript
const material = new THREE.RawShaderMaterial({
    uniforms: {
        projectionMatrix: { value: camera.projectionMatrix },
        modelViewMatrix: { value: new THREE.Matrix4() }
    },
    vertexShader: `
        precision highp float;
        attribute vec3 position;
        uniform mat4 projectionMatrix;
        uniform mat4 modelViewMatrix;

        void main() {
            gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
        }
    `,
    fragmentShader: `
        precision highp float;

        void main() {
            gl_FragColor = vec4(1.0, 0.0, 0.0, 1.0);
        }
    `
});
```

### 6.6 常见着色器效果

#### 波动效果

```glsl
// 顶点着色器
uniform float time;
varying vec2 vUv;
varying vec3 vNormal;

void main() {
    vUv = uv;
    vNormal = normal;

    vec3 pos = position;
    pos += normal * sin(pos.x * 10.0 + time) * 0.1;
    pos += normal * sin(pos.y * 10.0 + time * 1.5) * 0.1;

    gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
}
```

#### 渐变效果

```glsl
// 片段着色器
varying vec2 vUv;
varying vec3 vPosition;

void main() {
    vec3 color1 = vec3(1.0, 0.0, 0.0);
    vec3 color2 = vec3(0.0, 1.0, 0.0);

    float t = sin(vPosition.y * 2.0 + time) * 0.5 + 0.5;
    vec3 color = mix(color1, color2, t);

    gl_FragColor = vec4(color, 1.0);
}
```

#### 噪声效果

```glsl
// 片段着色器
float random(vec2 st) {
    return fract(sin(dot(st.xy, vec2(12.9898, 78.233))) * 43758.5453123);
}

float noise(vec2 st) {
    vec2 i = floor(st);
    vec2 f = fract(st);

    float a = random(i);
    float b = random(i + vec2(1.0, 0.0));
    float c = random(i + vec2(0.0, 1.0));
    float d = random(i + vec2(1.0, 1.0));

    vec2 u = f * f * (3.0 - 2.0 * f);

    return mix(a, b, u.x) +
           (c - a) * u.y * (1.0 - u.x) +
           (d - b) * u.x * u.y;
}

void main() {
    float n = noise(vPosition.xy * 5.0 + time);
    vec3 color = vec3(n, n * 0.5, n * 0.2);

    gl_FragColor = vec4(color, 1.0);
}
```

#### 菲涅尔效果

```glsl
// 顶点着色器
uniform vec3 viewVector;
varying float vIntensity;

void main() {
    vec3 vNormal = normalize(normalMatrix * normal);
    vec3 vNormel = normalize(normalMatrix * viewVector);
    vIntensity = pow(0.7 - dot(vNormal, vNormel), 2.0);

    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}

// 片段着色器
varying float vIntensity;

void main() {
    vec3 color = vec3(0.0, 1.0, 1.0);
    color *= vIntensity;

    gl_FragColor = vec4(color, vIntensity * 0.8);
}
```

---

## 第七章：材质优化

### 7.1 性能优化技巧

#### 1. 选择合适的材质

```javascript
// 性能优先
const basicMaterial = new THREE.MeshBasicMaterial({ color: 0xff0000 });

// 质量优先
const pbrMaterial = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.0
});
```

#### 2. 复用材质

```javascript
// 创建材质缓存
const materialCache = new Map();

function getMaterial(color) {
    const key = color.toString(16);
    if (!materialCache.has(key)) {
        materialCache.set(key, new THREE.MeshStandardMaterial({ color }));
    }
    return materialCache.get(key);
}

// 使用
const material1 = getMaterial(0xff0000);
const material2 = getMaterial(0xff0000);  // 复用
```

#### 3. 避免透明材质

```javascript
// 使用alphaTest代替透明
const material = new THREE.MeshStandardMaterial({
    map: texture,
    alphaTest: 0.5,  // 丢弃alpha < 0.5的像素
    transparent: false
});
```

#### 4. 优化纹理

```javascript
// 压缩纹理
const texture = textureLoader.load('texture.jpg');
texture.minFilter = THREE.LinearMipmapLinearFilter;
texture.generateMipmaps = true;

// 使用合适的分辨率
const texture = textureLoader.load('texture_512.jpg');  // 而不是4096
```

#### 5. 减少Draw Calls

```javascript
// 使用InstancedMesh
const instancedMesh = new THREE.InstancedMesh(geometry, material, 1000);

// 合并静态几何体
const mergedGeometry = mergeGeometries([geo1, geo2, geo3]);
```

### 7.2 内存管理

#### 释放材质

```javascript
function disposeMaterial(material) {
    // 释放纹理
    if (material.map) material.map.dispose();
    if (material.normalMap) material.normalMap.dispose();
    if (material.roughnessMap) material.roughnessMap.dispose();
    if (material.metalnessMap) material.metalnessMap.dispose();
    if (material.aoMap) material.aoMap.dispose();
    if (material.emissiveMap) material.emissiveMap.dispose();
    if (material.displacementMap) material.displacementMap.dispose();

    // 释放材质
    material.dispose();
}

// 使用
disposeMaterial(mesh.material);
```

#### 释放网格

```javascript
function disposeMesh(mesh) {
    // 释放几何体
    if (mesh.geometry) {
        mesh.geometry.dispose();
    }

    // 释放材质
    if (mesh.material) {
        if (Array.isArray(mesh.material)) {
            mesh.material.forEach(m => disposeMaterial(m));
        } else {
            disposeMaterial(mesh.material);
        }
    }

    // 从场景中移除
    scene.remove(mesh);
}

// 使用
disposeMesh(mesh);
```

### 7.3 LOD（Level of Detail）

根据距离使用不同细节的材质。

```javascript
// 创建不同细节的材质
const lowDetailMaterial = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.8
});

const highDetailMaterial = new THREE.MeshPhysicalMaterial({
    color: 0xff0000,
    roughness: 0.8,
    clearcoat: 1.0,
    clearcoatRoughness: 0.1
});

// 创建LOD对象
const lod = new THREE.LOD();

// 添加不同细节的网格
lod.addLevel(new THREE.Mesh(geometry, highDetailMaterial), 0);
lod.addLevel(new THREE.Mesh(geometry, lowDetailMaterial), 10);

scene.add(lod);
```

---

## 常见问题与故障排除

### 问题1：材质显示为黑色

**可能原因：**
1. 没有添加光源
2. 材质颜色设置错误
3. 环境贴图未设置

**解决方案：**
```javascript
// 添加光源
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// 设置环境贴图
scene.environment = envTexture;

// 检查材质颜色
material.color.set(0xff0000);
```

### 问题2：透明材质不透明

**可能原因：**
- transparent未设置为true
- 深度写入问题

**解决方案：**
```javascript
const material = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    transparent: true,
    opacity: 0.5,
    depthWrite: false  // 透明物体禁用深度写入
});
```

### 问题3：纹理不显示

**可能原因：**
- 纹理路径错误
- UV坐标未设置
- 纹理加载失败

**解决方案：**
```javascript
// 检查纹理路径
const texture = textureLoader.load('textures/texture.jpg');

// 检查UV坐标
if (!geometry.attributes.uv) {
    geometry.setAttribute('uv', new THREE.BufferAttribute(uvs, 2));
}

// 监听加载错误
textureLoader.load('texture.jpg', (texture) => {
    console.log('纹理加载成功');
}, undefined, (error) => {
    console.error('纹理加载失败', error);
});
```

### 问题4：PBR材质效果不真实

**可能原因：**
- 环境贴图未设置
- 光照不足
- 参数设置不当

**解决方案：**
```javascript
// 设置环境贴图
const pmremGenerator = new THREE.PMREMGenerator(renderer);
const envTexture = pmremGenerator.fromScene(sceneEnv).texture;
scene.environment = envTexture;

// 添加足够的光源
const ambientLight = new THREE.AmbientLight(0x404040, 0.3);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 10, 7);
scene.add(directionalLight);

// 调整材质参数
material.roughness = 0.5;
material.metalness = 0.0;
```

### 问题5：着色器材质不工作

**可能原因：**
- GLSL语法错误
- Uniforms未定义
- Varyings不匹配

**解决方案：**
```javascript
// 检查GLSL语法
const vertexShader = `
    varying vec2 vUv;
    void main() {
        vUv = uv;
        gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
`;

// 检查Uniforms
material.uniforms.time.value = clock.getElapsedTime();

// 检查Varyings
// 确保顶点和片段着色器中的varying名称一致
```

---

## 最佳实践

### 1. 选择合适的材质类型

| 场景 | 推荐材质 | 原因 |
|------|----------|------|
| 性能优先 | MeshBasicMaterial | 最快 |
| 哑光表面 | MeshLambertMaterial | 简单漫反射 |
| 塑料/陶瓷 | MeshPhongMaterial | 有高光 |
| 真实渲染 | MeshStandardMaterial | PBR |
| 玻璃/清漆 | MeshPhysicalMaterial | 高级特性 |
| 卡通风格 | MeshToonMaterial | Cel-shading |
| 调试 | MeshNormalMaterial | 法线可视化 |
| 自定义效果 | ShaderMaterial | 完全控制 |

### 2. 合理设置材质参数

```javascript
// 金属材质
const metalMaterial = new THREE.MeshStandardMaterial({
    color: 0xffffff,
    roughness: 0.2,
    metalness: 1.0
});

// 塑料材质
const plasticMaterial = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.0
});

// 玻璃材质
const glassMaterial = new THREE.MeshPhysicalMaterial({
    color: 0xffffff,
    roughness: 0,
    metalness: 0,
    transmission: 1.0,
    thickness: 0.5,
    ior: 1.5
});
```

### 3. 使用环境贴图

```javascript
// 加载环境贴图
const cubeLoader = new THREE.CubeTextureLoader();
const envMap = cubeLoader.load([
    'px.jpg', 'nx.jpg',
    'py.jpg', 'ny.jpg',
    'pz.jpg', 'nz.jpg'
]);

// 应用到材质
material.envMap = envMap;
material.envMapIntensity = 1;

// 或应用到场景
scene.environment = envMap;
```

### 4. 优化纹理使用

```javascript
// 压缩纹理
texture.minFilter = THREE.LinearMipmapLinearFilter;
texture.generateMipmaps = true;

// 使用合适的分辨率
const texture = textureLoader.load('texture_512.jpg');

// 复用纹理
const textureCache = new Map();
function getTexture(url) {
    if (!textureCache.has(url)) {
        textureCache.set(url, textureLoader.load(url));
    }
    return textureCache.get(url);
}
```

### 5. 及时释放资源

```javascript
// 销毁材质
function disposeMaterial(material) {
    if (material.map) material.map.dispose();
    if (material.normalMap) material.normalMap.dispose();
    if (material.roughnessMap) material.roughnessMap.dispose();
    if (material.metalnessMap) material.metalnessMap.dispose();
    material.dispose();
}

// 销毁网格
function disposeMesh(mesh) {
    if (mesh.geometry) mesh.geometry.dispose();
    if (mesh.material) disposeMaterial(mesh.material);
    scene.remove(mesh);
}
```

---

## 下一步学习

恭喜你完成了Three.js材质教程！接下来你可以学习：

### 推荐学习路径

1. **第四部分：光照系统**
   - 各种光源类型详解
   - 阴影配置
   - 全局光照技术

2. **第五部分：动画与交互**
   - 使用Tween.js
   - 鼠标交互
   - 键盘控制

3. **第六部分：纹理系统**
   - 高级纹理技术
   - 环境贴图
   - HDR纹理

### 推荐资源

#### 官方资源
- [Three.js官方文档 - Materials](https://threejs.org/docs/#api/en/materials/Material)
- [Three.js官方示例 - Materials](https://threejs.org/examples/#webgl_materials)

#### 学习资源
- [Three.js Journey](https://threejs-journey.com/)
- [Bruno Simon的YouTube频道](https://www.youtube.com/c/brunosimon)

### 实践项目建议

1. **初级项目**
   - 创建一个材质展示场景
   - 制作一个玻璃效果
   - 实现一个卡通风格场景

2. **中级项目**
   - 创建一个程序化材质系统
   - 制作一个自定义着色器效果
   - 实现一个材质编辑器

3. **高级项目**
   - 创建一个PBR材质库
   - 制作一个高级着色器效果
   - 实现一个实时材质预览系统

---

## 总结

本教程涵盖了Three.js材质的核心知识，包括：

✅ 材质的基础概念和架构
✅ 各种基础材质类型的使用
✅ PBR（物理渲染）材质的配置
✅ 高级材质的特性（清漆、透射、彩虹等）
✅ 各种纹理贴图的应用
✅ 自定义着色器材质的创建
✅ 材质的性能优化技巧

通过学习本教程，你应该能够：
- 选择合适的材质类型
- 配置PBR材质参数
- 使用各种纹理贴图
- 创建自定义着色器
- 优化材质性能

**继续加油！Three.js的材质系统非常强大，期待你创造出令人惊叹的3D作品！** 🚀

---

## 附录

### A. 材质类型速查

| 材质类型 | 光照 | 性能 | 主要用途 |
|----------|------|--------|----------|
| MeshBasicMaterial | 无 | 最高 | 线框、调试 |
| MeshLambertMaterial | 漫反射 | 高 | 哑光表面 |
| MeshPhongMaterial | 漫反射+高光 | 中 | 塑料、陶瓷 |
| MeshStandardMaterial | PBR | 中 | 真实渲染 |
| MeshPhysicalMaterial | 高级PBR | 低 | 玻璃、清漆 |
| MeshToonMaterial | 卡通 | 中 | 卡通风格 |
| MeshNormalMaterial | 法线 | 高 | 调试 |
| MeshDepthMaterial | 深度 | 高 | 深度效果 |
| ShaderMaterial | 自定义 | 可变 | 自定义效果 |
| RawShaderMaterial | 完全自定义 | 可变 | 高级自定义 |

### B. 纹理类型速查

| 纹理类型 | 用途 | 颜色通道 |
|----------|------|----------|
| map | 基础颜色 | RGB |
| roughnessMap | 粗糙度 | 单通道 |
| metalnessMap | 金属度 | 单通道 |
| normalMap | 法线 | RGB |
| bumpMap | 凹凸 | 单通道 |
| displacementMap | 置换 | 单通道 |
| aoMap | 环境光遮蔽 | 单通道 |
| emissiveMap | 自发光 | RGB |
| alphaMap | 透明度 | 单通道 |

### C. PBR参数速查

| 参数 | 范围 | 说明 |
|------|------|------|
| roughness | 0-1 | 粗糙度（0=镜面，1=漫反射） |
| metalness | 0-1 | 金属度（0=非金属，1=金属） |
| clearcoat | 0-1 | 清漆强度 |
| transmission | 0-1 | 透射率（0=不透明，1=完全透明） |
| ior | 1-2.333 | 折射率 |
| sheen | 0-1 | 绒布强度 |
| iridescence | 0-1 | 彩虹强度 |
| anisotropy | 0-1 | 各向异性强度 |

### D. 性能优化清单

- [ ] 选择合适的材质类型
- [ ] 复用材质实例
- [ ] 避免透明材质（使用alphaTest）
- [ ] 优化纹理分辨率
- [ ] 使用纹理压缩
- [ ] 减少Draw Calls
- [ ] 使用InstancedMesh
- [ ] 及时释放资源
- [ ] 使用LOD系统
- [ ] 避免频繁更新材质

---

**教程版本**: 1.0

**Three.js版本**: r160

---

**祝你学习愉快！如有问题，欢迎查阅官方文档或参与社区讨论。** 🎉
