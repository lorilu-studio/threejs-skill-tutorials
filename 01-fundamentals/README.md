# Three.js 入门教程 - 第一部分：基础概念与场景搭建

> 本教程专为Three.js初学者设计，从零开始学习3D网页开发。我们将用通俗易懂的语言和丰富的实例，带你逐步掌握Three.js的核心概念和实践技能。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：Three.js基础概念](#第一章threejs基础概念)
- [第二章：场景、相机、渲染器](#第二章场景相机渲染器)
- [第三章：坐标系与空间概念](#第三章坐标系与空间概念)
- [第四章：几何体与材质](#第四章几何体与材质)
- [第五章：动画基础](#第五章动画基础)
- [第六章：响应式设计](#第六章响应式设计)
- [第七章：综合实战](#第七章综合实战)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Three.js的核心概念和架构
- ✅ 创建并配置基本的3D场景
- ✅ 使用不同类型的几何体和材质
- ✅ 理解3D坐标系和空间变换
- ✅ 实现基本的动画效果
- ✅ 创建响应式的3D网页应用
- ✅ 掌握调试和优化技巧

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **HTML5**: 网页的基本结构
- **CSS3**: 基本的样式设置
- **JavaScript (ES6+)**: 变量、函数、箭头函数、类等现代语法

### 推荐知识（非必需）
- **Canvas API**: 了解基本的2D绘图概念
- **数学基础**: 基本的三角函数（sin、cos、tan）

### 开发环境准备

#### 方式一：直接在浏览器中运行（推荐初学者）
- 任意现代浏览器（Chrome、Firefox、Edge等）
- 文本编辑器（VS Code、Sublime Text等）

#### 方式二：本地开发服务器（推荐进阶学习）
```bash
# 安装Node.js后，使用以下命令启动本地服务器
npx serve
```

---

## 第一章：Three.js基础概念

### 1.1 什么是Three.js？

**Three.js** 是一个基于WebGL的JavaScript 3D图形库，它让开发者能够在网页上轻松创建和展示3D内容。

#### 为什么选择Three.js？

| 特性 | 说明 |
|------|------|
| **简单易用** | 封装了复杂的WebGL API，提供简洁的接口 |
| **功能强大** | 支持几何体、材质、光照、动画、物理等 |
| **跨平台** | 支持所有现代浏览器，无需插件 |
| **性能优秀** | 利用GPU加速，流畅渲染复杂场景 |
| **社区活跃** | 丰富的文档、示例和第三方库 |

#### Three.js的应用场景

- 🎮 **3D游戏**: 网页游戏、互动体验
- 🏠 **建筑可视化**: 房屋展示、室内设计
- 🛍️ **电商展示**: 产品3D展示、虚拟试衣
- 📊 **数据可视化**: 3D图表、信息展示
- 🎨 **创意艺术**: 交互艺术、数字展览
- 🎬 **影视特效**: 网页动画、视觉特效

### 1.2 Three.js的核心架构

Three.js采用**场景图（Scene Graph）**架构，类似于现实世界的摄影棚：

```
现实世界摄影棚          Three.js 3D场景
┌─────────────┐         ┌─────────────┐
│   摄影棚    │    →    │   Scene     │  场景（容器）
│  (舞台)     │         │  (容器)     │
├─────────────┤         ├─────────────┤
│   摄像机    │    →    │   Camera    │  相机（观察者）
│  (观察者)   │         │  (观察者)   │
├─────────────┤         ├─────────────┤
│   灯光      │    →    │   Light     │  光源（照明）
│  (照明)     │         │  (照明)     │
├─────────────┤         ├─────────────┤
│   道具      │    →    │   Mesh      │  网格（物体）
│  (物体)     │         │  (物体)     │
├─────────────┤         ├─────────────┤
│   胶片      │    →    │  Renderer   │  渲染器（绘制）
│  (记录)     │         │  (绘制)     │
└─────────────┘         └─────────────┘
```

### 1.3 Three.js的三大核心组件

#### 组件一：场景（Scene）

**场景**是所有3D对象的容器，相当于一个虚拟的舞台。

```javascript
const scene = new THREE.Scene();
```

**场景的常用属性：**
- `background`: 背景颜色或纹理
- `fog`: 雾化效果
- `children`: 场景中的所有子对象

**类比理解：**
- 场景就像一个**空房间**，你可以在里面放置家具、灯光等
- 所有要显示的3D对象都必须添加到场景中

#### 组件二：相机（Camera）

**相机**决定了我们能看到什么，相当于我们的眼睛。

```javascript
const camera = new THREE.PerspectiveCamera(
    75,                                     // 视野角度
    window.innerWidth / window.innerHeight, // 宽高比
    0.1,                                    // 近裁剪面
    1000                                    // 远裁剪面
);
```

**相机的类型：**
- `PerspectiveCamera`: 透视相机（最常用，模拟人眼）
- `OrthographicCamera`: 正交相机（无透视，适合2D/等轴测）

**相机的关键属性：**
- `position`: 相机位置
- `lookAt()`: 观察目标
- `fov`: 视野角度

**类比理解：**
- 相机就像**摄像机**，你可以移动它、旋转它
- 不同的相机位置会看到不同的画面

#### 组件三：渲染器（Renderer）

**渲染器**负责将3D场景绘制到2D屏幕上。

```javascript
const renderer = new THREE.WebGLRenderer({
    antialias: true  // 开启抗锯齿
});
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);
```

**渲染器的功能：**
- 创建Canvas元素
- 渲染场景到Canvas
- 处理阴影、抗锯齿等效果

**类比理解：**
- 渲染器就像**画笔**，将3D场景"画"到屏幕上
- 每次调用`render()`方法，就会绘制一帧画面

### 1.4 Three.js的工作流程

```
1. 创建场景（Scene）
   ↓
2. 创建相机（Camera）
   ↓
3. 创建渲染器（Renderer）
   ↓
4. 添加物体到场景
   ↓
5. 添加光源
   ↓
6. 启动动画循环
   ↓
7. 渲染场景
```

---

## 第二章：场景、相机、渲染器

### 2.1 创建第一个3D场景

让我们从最简单的示例开始，创建一个包含旋转立方体的3D场景。

#### 完整代码示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>我的第一个Three.js场景</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { overflow: hidden; background-color: #000; }
    </style>
</head>
<body>
    <script type="importmap">
        {
            "imports": {
                "three": "https://unpkg.com/three@0.160.0/build/three.module.js"
            }
        }
    </script>
    <script type="module">
        import * as THREE from 'three';

        // 步骤1：创建场景
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x1a1a2e);

        // 步骤2：创建相机
        const camera = new THREE.PerspectiveCamera(
            75,
            window.innerWidth / window.innerHeight,
            0.1,
            1000
        );
        camera.position.z = 5;

        // 步骤3：创建渲染器
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        document.body.appendChild(renderer.domElement);

        // 步骤4：创建立方体
        const geometry = new THREE.BoxGeometry(1, 1, 1);
        const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
        const cube = new THREE.Mesh(geometry, material);
        scene.add(cube);

        // 步骤5：动画循环
        function animate() {
            requestAnimationFrame(animate);
            cube.rotation.x += 0.01;
            cube.rotation.y += 0.01;
            renderer.render(scene, camera);
        }
        animate();
    </script>
</body>
</html>
```

### 2.2 深入理解场景（Scene）

#### 场景的创建与配置

```javascript
// 创建场景
const scene = new THREE.Scene();

// 设置背景颜色
scene.background = new THREE.Color(0x1a1a2e);

// 设置背景纹理
const textureLoader = new THREE.TextureLoader();
scene.background = textureLoader.load('skybox.jpg');

// 添加雾化效果
scene.fog = new THREE.Fog(0x1a1a2e, 10, 50);  // 颜色、近距离、远距离
```

#### 雾化效果详解

**雾化效果**让远处的物体逐渐淡出，增加场景的深度感。

```javascript
// 线性雾（Linear Fog）
scene.fog = new THREE.Fog(0x1a1a2e, 10, 50);
// 参数说明：
// - 0x1a1a2e: 雾的颜色
// - 10: 雾开始出现的距离
// - 50: 雾完全遮挡的距离

// 指数雾（Exponential Fog）
scene.fog = new THREE.FogExp2(0x1a1a2e, 0.02);
// 参数说明：
// - 0x1a1a2e: 雾的颜色
// - 0.02: 雾的密度（值越大，雾越浓）
```

### 2.3 深入理解相机（Camera）

#### 透视相机（PerspectiveCamera）

透视相机模拟人眼的视觉效果，近大远小。

```javascript
const camera = new THREE.PerspectiveCamera(
    fov,      // 视野角度（Field of View）
    aspect,   // 宽高比（Aspect Ratio）
    near,     // 近裁剪面（Near Clipping Plane）
    far       // 远裁剪面（Far Clipping Plane）
);
```

**参数详解：**

| 参数 | 说明 | 推荐值 |
|------|------|--------|
| `fov` | 视野角度（度数） | 45-75 |
| `aspect` | 宽高比（宽度/高度） | window.innerWidth / window.innerHeight |
| `near` | 最近可见距离 | 0.1 |
| `far` | 最远可见距离 | 100-1000 |

**视野角度（FOV）对比：**

```
FOV = 30°          FOV = 75°          FOV = 120°
  ▲                  ▲                   ▲
 / \                / \                 / \
/   \              /   \               /   \
-----            -----               -----
(窄视野)         (标准视野)           (宽视野)
```

#### 相机位置与朝向

```javascript
// 设置相机位置
camera.position.set(x, y, z);
camera.position.x = 5;
camera.position.y = 2;
camera.position.z = 10;

// 让相机看向某个点
camera.lookAt(0, 0, 0);

// 创建目标向量
const target = new THREE.Vector3(1, 2, 3);
camera.lookAt(target);
```

**相机移动示例：**

```javascript
// 围绕原点旋转
const radius = 10;
const angle = Date.now() * 0.001;
camera.position.x = Math.cos(angle) * radius;
camera.position.z = Math.sin(angle) * radius;
camera.lookAt(0, 0, 0);
```

#### 正交相机（OrthographicCamera）

正交相机没有透视效果，适合2D游戏或等轴测视图。

```javascript
const aspect = window.innerWidth / window.innerHeight;
const frustumSize = 10;
const camera = new THREE.OrthographicCamera(
    (frustumSize * aspect) / -2,  // 左边界
    (frustumSize * aspect) / 2,   // 右边界
    frustumSize / 2,               // 上边界
    frustumSize / -2,              // 下边界
    0.1,                           // 近裁剪面
    1000                           // 远裁剪面
);
```

**透视相机 vs 正交相机：**

```
透视相机（近大远小）        正交相机（无透视）
     ▲                          ▲
    / \                        | |
   /   \                       | |
  /_____\                      | |
  (远小近大)                   (大小一致)
```

### 2.4 深入理解渲染器（Renderer）

#### 渲染器的创建与配置

```javascript
const renderer = new THREE.WebGLRenderer({
    canvas: document.querySelector('#canvas'),  // 指定现有canvas
    antialias: true,                             // 开启抗锯齿
    alpha: true,                                 // 透明背景
    powerPreference: 'high-performance',         // GPU性能偏好
    preserveDrawingBuffer: true,                 // 保留绘制缓冲（用于截图）
    stencil: true,                               // 启用模板缓冲
    depth: true                                  // 启用深度缓冲
});
```

**常用配置选项：**

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `antialias` | 抗锯齿（平滑边缘） | false |
| `alpha` | 透明背景 | false |
| `powerPreference` | GPU性能偏好 | 'default' |
| `preserveDrawingBuffer` | 保留缓冲（截图用） | false |

#### 渲染器尺寸与像素比

```javascript
// 设置渲染器尺寸
renderer.setSize(window.innerWidth, window.innerHeight);

// 设置像素比（优化高清屏）
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
```

**像素比说明：**
- `devicePixelRatio`: 物理像素与CSS像素的比例
- 高清屏（Retina）的像素比通常为2或3
- 限制最大值为2可以避免性能问题

#### 阴影配置

```javascript
// 开启阴影
renderer.shadowMap.enabled = true;

// 阴影类型
renderer.shadowMap.type = THREE.PCFSoftShadowMap;  // 软阴影
// 可选值：
// - THREE.BasicShadowMap: 基础阴影（性能最好）
// - THREE.PCFShadowMap: PCF阴影
// - THREE.PCFSoftShadowMap: 软阴影（效果最好）

// 阴影贴图分辨率
renderer.shadowMap.width = 2048;
renderer.shadowMap.height = 2048;
```

#### 色调映射

```javascript
// 色调映射类型
renderer.toneMapping = THREE.ACESFilmicToneMapping;
// 可选值：
// - THREE.LinearToneMapping: 线性
// - THREE.ReinhardToneMapping: Reinhard
// - THREE.CineonToneMapping: Cineon
// - THREE.ACESFilmicToneMapping: ACES电影级（推荐）

// 曝光度
renderer.toneMappingExposure = 1.0;
```

#### 输出色彩空间

```javascript
// 设置输出色彩空间（Three.js r152+）
renderer.outputColorSpace = THREE.SRGBColorSpace;
// 可选值：
// - THREE.SRGBColorSpace: sRGB色彩空间（推荐）
// - THREE.LinearSRGBColorSpace: 线性sRGB
```

### 2.5 完整的场景设置示例

```javascript
// 创建场景
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x1a1a2e);
scene.fog = new THREE.Fog(0x1a1a2e, 10, 50);

// 创建相机
const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);
camera.position.set(0, 5, 10);
camera.lookAt(0, 0, 0);

// 创建渲染器
const renderer = new THREE.WebGLRenderer({
    antialias: true,
    alpha: true
});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.0;
renderer.outputColorSpace = THREE.SRGBColorSpace;
document.body.appendChild(renderer.domElement);
```

---

## 第三章：坐标系与空间概念

### 3.1 Three.js的坐标系

Three.js使用**右手坐标系**，这是3D图形学中最常用的坐标系。

#### 坐标轴方向

```
        Y轴（绿色）
         ↑
         |
         |
         |_______→ X轴（红色）
        /
       /
      /
     ↓
   Z轴（蓝色，指向屏幕外）
```

**坐标轴颜色约定：**
- **红色（Red）**: X轴 - 向右
- **绿色（Green）**: Y轴 - 向上
- **蓝色（Blue）**: Z轴 - 向外（指向观察者）

#### 右手坐标系规则

伸出你的右手，按照以下方式：

```
拇指指向 = X轴正方向（向右）
食指指向 = Y轴正方向（向上）
中指指向 = Z轴正方向（向外）
```

### 3.2 使用坐标轴辅助线

Three.js提供了辅助工具来可视化坐标系。

#### AxesHelper（坐标轴辅助线）

```javascript
// 创建坐标轴辅助线
const axesHelper = new THREE.AxesHelper(size);
scene.add(axesHelper);

// 示例：长度为5的坐标轴
const axesHelper = new THREE.AxesHelper(5);
scene.add(axesHelper);
```

**效果说明：**
- 红色线：X轴
- 绿色线：Y轴
- 蓝色线：Z轴
- 线的长度由参数决定

#### GridHelper（网格辅助线）

```javascript
// 创建网格辅助线
const gridHelper = new THREE.GridHelper(size, divisions);
scene.add(gridHelper);

// 示例：10x10的网格，分成10份
const gridHelper = new THREE.GridHelper(10, 10);
scene.add(gridHelper);
```

**参数说明：**
- `size`: 网格的总大小
- `divisions`: 网格的分段数

**自定义网格颜色：**

```javascript
// 自定义中心线和网格线颜色
const gridHelper = new THREE.GridHelper(
    10,           // 大小
    10,           // 分段数
    0x444444,     // 中心线颜色
    0x222222      // 网格线颜色
);
scene.add(gridHelper);
```

### 3.3 物体的位置、旋转和缩放

#### 位置（Position）

```javascript
// 设置位置
mesh.position.set(x, y, z);
mesh.position.x = 5;
mesh.position.y = 2;
mesh.position.z = 10;

// 获取位置
const x = mesh.position.x;
const y = mesh.position.y;
const z = mesh.position.z;

// 复制位置
mesh.position.copy(otherMesh.position);

// 移动物体
mesh.position.x += 1;  // 向右移动1个单位
mesh.position.y -= 1;  // 向下移动1个单位
```

#### 旋转（Rotation）

Three.js使用欧拉角（Euler angles）表示旋转。

```javascript
// 设置旋转（弧度制）
mesh.rotation.set(x, y, z);
mesh.rotation.x = Math.PI / 2;  // 绕X轴旋转90度
mesh.rotation.y = Math.PI / 4;  // 绕Y轴旋转45度
mesh.rotation.z = Math.PI / 6;  // 绕Z轴旋转30度

// 旋转顺序（默认：XYZ）
mesh.rotation.order = 'YXZ';  // 先绕Y轴，再绕X轴，最后绕Z轴

// 持续旋转
mesh.rotation.x += 0.01;  // 每帧绕X轴旋转0.01弧度
mesh.rotation.y += 0.01;  // 每帧绕Y轴旋转0.01弧度
```

**角度与弧度转换：**

```javascript
// 角度转弧度
const radians = THREE.MathUtils.degToRad(90);  // 90度 → 1.5708弧度

// 弧度转角度
const degrees = THREE.MathUtils.radToDeg(Math.PI / 2);  // 1.5708弧度 → 90度
```

#### 缩放（Scale）

```javascript
// 设置缩放
mesh.scale.set(x, y, z);
mesh.scale.x = 2;  // X轴方向放大2倍
mesh.scale.y = 0.5;  // Y轴方向缩小到一半
mesh.scale.z = 1;  // Z轴方向保持原样

// 均匀缩放
mesh.scale.set(2, 2, 2);  // 所有方向放大2倍

// 获取缩放
const scaleX = mesh.scale.x;
const scaleY = mesh.scale.y;
const scaleZ = mesh.scale.z;
```

### 3.4 空间变换示例

```javascript
// 创建一个立方体
const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
const cube = new THREE.Mesh(geometry, material);

// 设置位置
cube.position.set(2, 1, 0);

// 设置旋转
cube.rotation.set(Math.PI / 4, Math.PI / 4, 0);

// 设置缩放
cube.scale.set(1.5, 1.5, 1.5);

scene.add(cube);
```

### 3.5 坐标系可视化示例

```javascript
// 创建场景
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x1a1a2e);

// 创建相机
const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);
camera.position.set(5, 5, 5);
camera.lookAt(0, 0, 0);

// 创建渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// 添加坐标轴辅助线
const axesHelper = new THREE.AxesHelper(5);
scene.add(axesHelper);

// 添加网格辅助线
const gridHelper = new THREE.GridHelper(10, 10);
scene.add(gridHelper);

// 在X轴上放置红色立方体
const boxX = new THREE.Mesh(
    new THREE.BoxGeometry(0.5, 0.5, 0.5),
    new THREE.MeshBasicMaterial({ color: 0xff0000 })
);
boxX.position.set(2, 0, 0);
scene.add(boxX);

// 在Y轴上放置绿色立方体
const boxY = new THREE.Mesh(
    new THREE.BoxGeometry(0.5, 0.5, 0.5),
    new THREE.MeshBasicMaterial({ color: 0x00ff00 })
);
boxY.position.set(0, 2, 0);
scene.add(boxY);

// 在Z轴上放置蓝色立方体
const boxZ = new THREE.Mesh(
    new THREE.BoxGeometry(0.5, 0.5, 0.5),
    new THREE.MeshBasicMaterial({ color: 0x0000ff })
);
boxZ.position.set(0, 0, 2);
scene.add(boxZ);

// 在原点放置白色球体
const sphere = new THREE.Mesh(
    new THREE.SphereGeometry(0.3, 32, 32),
    new THREE.MeshBasicMaterial({ color: 0xffffff })
);
sphere.position.set(0, 0, 0);
scene.add(sphere);

// 动画循环
function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
}
animate();
```

---

## 第四章：几何体与材质

### 4.1 几何体（Geometry）

几何体定义了物体的形状，相当于物体的"骨架"。

#### 基础几何体类型

| 几何体 | 说明 | 常用参数 |
|--------|------|----------|
| `BoxGeometry` | 立方体 | 宽、高、深 |
| `SphereGeometry` | 球体 | 半径、水平分段、垂直分段 |
| `ConeGeometry` | 圆锥体 | 底面半径、高度、分段数 |
| `CylinderGeometry` | 圆柱体 | 顶部半径、底部半径、高度、分段数 |
| `TorusGeometry` | 圆环 | 主半径、管半径、径向分段、管分段 |
| `PlaneGeometry` | 平面 | 宽、高、宽度分段、高度分段 |
| `CircleGeometry` | 圆形 | 半径、分段数 |
| `RingGeometry` | 圆环 | 内半径、外半径、径向分段、分段数 |

#### 立方体（BoxGeometry）

```javascript
// 创建立方体
const geometry = new THREE.BoxGeometry(
    width,   // 宽度
    height,  // 高度
    depth    // 深度
);

// 示例：创建一个2x3x4的立方体
const geometry = new THREE.BoxGeometry(2, 3, 4);
```

#### 球体（SphereGeometry）

```javascript
// 创建球体
const geometry = new THREE.SphereGeometry(
    radius,        // 半径
    widthSegments, // 水平分段数（影响平滑度）
    heightSegments // 垂直分段数（影响平滑度）
);

// 示例：创建一个半径为1的球体
const geometry = new THREE.SphereGeometry(1, 32, 32);

// 低分段数（多边形效果）
const lowPolySphere = new THREE.SphereGeometry(1, 8, 8);

// 高分段数（平滑效果）
const smoothSphere = new THREE.SphereGeometry(1, 64, 64);
```

**分段数对比：**

```
分段数 = 8          分段数 = 32          分段数 = 64
   ○                    ●                    ●
  / \                  / \                  / \
 /   \                /   \                /   \
○-----○              ●-----●              ●-----●
(多边形明显)         (较平滑)              (非常平滑)
```

#### 圆锥体（ConeGeometry）

```javascript
// 创建圆锥体
const geometry = new THREE.ConeGeometry(
    radius,    // 底面半径
    height,    // 高度
    radialSegments // 分段数
);

// 示例：创建一个底面半径为0.8，高度为2的圆锥
const geometry = new THREE.ConeGeometry(0.8, 2, 32);
```

#### 圆环（TorusGeometry）

```javascript
// 创建圆环
const geometry = new THREE.TorusGeometry(
    radius,      // 主半径（圆环中心到管中心的距离）
    tube,        // 管半径（圆环的粗细）
    radialSegments, // 径向分段数
    tubularSegments // 管分段数
);

// 示例：创建一个圆环
const geometry = new THREE.TorusGeometry(1, 0.3, 16, 100);
```

#### 平面（PlaneGeometry）

```javascript
// 创建平面
const geometry = new THREE.PlaneGeometry(
    width,   // 宽度
    height,  // 高度
    widthSegments,  // 宽度分段数
    heightSegments  // 高度分段数
);

// 示例：创建一个10x10的平面
const geometry = new THREE.PlaneGeometry(10, 10);

// 注意：平面默认面向Z轴正方向
// 如果需要平面水平放置，需要旋转
plane.rotation.x = -Math.PI / 2;
```

### 4.2 材质（Material）

材质定义了物体的外观，相当于物体的"皮肤"。

#### 材质类型对比

| 材质类型 | 说明 | 是否需要光照 | 性能 |
|----------|------|--------------|------|
| `MeshBasicMaterial` | 基础材质（不受光照影响） | 否 | 最高 |
| `MeshStandardMaterial` | 标准材质（PBR） | 是 | 中等 |
| `MeshPhongMaterial` | Phong材质（高光） | 是 | 中等 |
| `MeshLambertMaterial` | Lambert材质（漫反射） | 是 | 较高 |
| `MeshToonMaterial` | 卡通材质 | 是 | 中等 |
| `MeshNormalMaterial` | 法线材质（显示法线方向） | 否 | 最高 |

#### MeshBasicMaterial（基础材质）

```javascript
const material = new THREE.MeshBasicMaterial({
    color: 0xff0000,        // 颜色
    wireframe: false,       // 是否显示线框
    transparent: false,     // 是否透明
    opacity: 1.0,           // 透明度（0-1）
    side: THREE.FrontSide   // 渲染面（前/后/双面）
});

// 示例：红色线框立方体
const material = new THREE.MeshBasicMaterial({
    color: 0xff0000,
    wireframe: true
});
```

**side属性说明：**
- `THREE.FrontSide`: 只渲染正面（性能最好）
- `THREE.BackSide`: 只渲染背面
- `THREE.DoubleSide`: 渲染双面（性能较差）

#### MeshStandardMaterial（标准材质）

```javascript
const material = new THREE.MeshStandardMaterial({
    color: 0x00ff00,        // 基础颜色
    roughness: 0.5,         // 粗糙度（0-1，越小越光滑）
    metalness: 0.5,         // 金属度（0-1，越大越像金属）
    emissive: 0x000000,     // 自发光颜色
    emissiveIntensity: 1.0, // 自发光强度
    transparent: false,     // 是否透明
    opacity: 1.0,           // 透明度
    side: THREE.FrontSide   // 渲染面
});
```

**粗糙度（Roughness）对比：**

```
roughness = 0.1    roughness = 0.5    roughness = 0.9
     ●                  ●                  ●
   (非常光滑)         (中等粗糙)         (非常粗糙)
   (强反射)           (弱反射)           (无反射)
```

**金属度（Metalness）对比：**

```
metalness = 0.0     metalness = 0.5     metalness = 1.0
     ●                  ●                  ●
   (非金属)           (半金属)           (全金属)
   (塑料感)           (混合感)           (金属感)
```

#### MeshPhongMaterial（Phong材质）

```javascript
const material = new THREE.MeshPhongMaterial({
    color: 0x0000ff,        // 基础颜色
    specular: 0xffffff,     // 高光颜色
    shininess: 30,          // 高光亮度（0-100）
    flatShading: false,     // 是否使用平面着色
    transparent: false,     // 是否透明
    opacity: 1.0,           // 透明度
    side: THREE.FrontSide   // 渲染面
});
```

#### MeshNormalMaterial（法线材质）

```javascript
// 法线材质根据面的法线方向显示颜色
const material = new THREE.MeshNormalMaterial();

// 常用于调试，可视化物体的法线方向
```

### 4.3 创建网格（Mesh）

网格是几何体和材质的组合，是最终显示的3D对象。

```javascript
// 创建网格
const mesh = new THREE.Mesh(geometry, material);

// 将网格添加到场景
scene.add(mesh);

// 从场景中移除
scene.remove(mesh);

// 销毁网格（释放内存）
mesh.geometry.dispose();
mesh.material.dispose();
```

### 4.4 几何体与材质示例

```javascript
// 创建场景
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x1a1a2e);

// 创建相机
const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);
camera.position.set(0, 2, 8);
camera.lookAt(0, 0, 0);

// 创建渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// 添加光源
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// 创建立方体
const boxGeometry = new THREE.BoxGeometry(1.5, 1.5, 1.5);
const boxMaterial = new THREE.MeshStandardMaterial({
    color: 0xff6b6b,
    roughness: 0.5,
    metalness: 0.1
});
const box = new THREE.Mesh(boxGeometry, boxMaterial);
box.position.set(-2.5, 0, 0);
scene.add(box);

// 创建球体
const sphereGeometry = new THREE.SphereGeometry(1, 32, 32);
const sphereMaterial = new THREE.MeshStandardMaterial({
    color: 0x4ecdc4,
    roughness: 0.3,
    metalness: 0.2
});
const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
sphere.position.set(0, 0, 0);
scene.add(sphere);

// 创建圆锥体
const coneGeometry = new THREE.ConeGeometry(0.8, 2, 32);
const coneMaterial = new THREE.MeshStandardMaterial({
    color: 0xffe66d,
    roughness: 0.4,
    metalness: 0.3
});
const cone = new THREE.Mesh(coneGeometry, coneMaterial);
cone.position.set(2.5, 0, 0);
scene.add(cone);

// 动画循环
function animate() {
    requestAnimationFrame(animate);
    box.rotation.x += 0.01;
    box.rotation.y += 0.01;
    sphere.rotation.x += 0.015;
    sphere.rotation.z += 0.01;
    cone.rotation.y += 0.02;
    renderer.render(scene, camera);
}
animate();
```

---

## 第五章：动画基础

### 5.1 动画循环（Animation Loop）

动画循环是Three.js动画的核心，它让场景持续更新和渲染。

#### requestAnimationFrame

`requestAnimationFrame`是浏览器提供的API，用于创建平滑的动画。

```javascript
function animate() {
    // 请求下一帧动画
    requestAnimationFrame(animate);

    // 更新物体状态
    mesh.rotation.x += 0.01;
    mesh.rotation.y += 0.01;

    // 渲染场景
    renderer.render(scene, camera);
}

// 启动动画循环
animate();
```

**为什么使用requestAnimationFrame？**

| 特性 | requestAnimationFrame | setInterval |
|------|----------------------|-------------|
| 性能 | 自动优化，在后台暂停 | 持续运行，浪费资源 |
| 平滑度 | 与屏幕刷新率同步（通常60fps） | 可能不流畅 |
| 节能 | 在不可见时停止 | 持续消耗资源 |

### 5.2 时钟（Clock）

Three.js提供了`Clock`类来精确计算时间。

```javascript
// 创建时钟
const clock = new THREE.Clock();

function animate() {
    requestAnimationFrame(animate);

    // 获取两帧之间的时间差（秒）
    const delta = clock.getDelta();

    // 获取总运行时间（秒）
    const elapsed = clock.getElapsedTime();

    // 使用delta实现平滑动画
    mesh.rotation.y += delta * 0.5;

    // 使用elapsed实现周期动画
    mesh.position.y = Math.sin(elapsed) * 0.5;

    renderer.render(scene, camera);
}

animate();
```

**delta vs elapsed：**

```javascript
// delta: 两帧之间的时间差
// 优点：动画速度与帧率无关，始终平滑
mesh.rotation.y += delta * 2;  // 每秒旋转2弧度

// elapsed: 总运行时间
// 优点：适合周期性动画
mesh.position.y = Math.sin(elapsed * 2) * 0.5;  // 周期为π秒
```

### 5.3 常见动画类型

#### 旋转动画

```javascript
// 持续旋转
mesh.rotation.x += 0.01;
mesh.rotation.y += 0.01;

// 使用delta实现平滑旋转
mesh.rotation.y += delta * 0.5;

// 使用elapsed实现周期性旋转
mesh.rotation.y = elapsed * 0.5;
```

#### 位移动画

```javascript
// 简单位移
mesh.position.x += 0.01;

// 往复运动（使用sin函数）
mesh.position.x = Math.sin(elapsed) * 2;

// 圆周运动
mesh.position.x = Math.cos(elapsed) * 3;
mesh.position.z = Math.sin(elapsed) * 3;

// 螺旋运动
mesh.position.x = Math.cos(elapsed) * elapsed * 0.1;
mesh.position.z = Math.sin(elapsed) * elapsed * 0.1;
mesh.position.y = elapsed * 0.05;
```

#### 缩放动画

```javascript
// 呼吸效果（周期性缩放）
const scale = 1 + Math.sin(elapsed * 3) * 0.2;
mesh.scale.set(scale, scale, scale);

// 单轴缩放
mesh.scale.y = 1 + Math.sin(elapsed * 2) * 0.3;
```

#### 复合动画

```javascript
// 组合旋转、位移和缩放
mesh.rotation.y += delta * 0.5;
mesh.position.y = Math.sin(elapsed * 2) * 0.5;
const scale = 1 + Math.sin(elapsed * 3) * 0.1;
mesh.scale.set(scale, scale, scale);
```

### 5.4 动画控制

#### 暂停/继续动画

```javascript
let isAnimating = true;

function animate() {
    if (isAnimating) {
        requestAnimationFrame(animate);
        mesh.rotation.y += 0.01;
        renderer.render(scene, camera);
    }
}

// 暂停动画
isAnimating = false;

// 继续动画
isAnimating = true;
animate();
```

#### 速度控制

```javascript
let animationSpeed = 1.0;

function animate() {
    requestAnimationFrame(animate);
    mesh.rotation.y += 0.01 * animationSpeed;
    renderer.render(scene, camera);
}

// 加速
animationSpeed = 2.0;

// 减速
animationSpeed = 0.5;

// 停止
animationSpeed = 0.0;
```

### 5.5 动画示例

```javascript
// 创建场景
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x1a1a2e);

// 创建相机
const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);
camera.position.z = 5;

// 创建渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// 创建时钟
const clock = new THREE.Clock();

// 创建多个物体
const geometries = [
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.SphereGeometry(0.5, 32, 32),
    new THREE.ConeGeometry(0.5, 1, 32)
];

const colors = [0xff6b6b, 0x4ecdc4, 0xffe66d];
const meshes = [];

for (let i = 0; i < 3; i++) {
    const material = new THREE.MeshStandardMaterial({
        color: colors[i],
        roughness: 0.5,
        metalness: 0.2
    });
    const mesh = new THREE.Mesh(geometries[i], material);
    mesh.position.x = (i - 1) * 2;
    scene.add(mesh);
    meshes.push(mesh);
}

// 添加光源
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// 动画循环
function animate() {
    requestAnimationFrame(animate);

    const delta = clock.getDelta();
    const elapsed = clock.getElapsedTime();

    // 不同的动画效果
    meshes[0].rotation.x += delta * 0.5;  // 持续旋转
    meshes[0].rotation.y += delta * 0.5;

    meshes[1].position.y = Math.sin(elapsed * 2) * 0.5;  // 上下浮动
    meshes[1].rotation.z += delta * 0.3;

    const scale = 1 + Math.sin(elapsed * 3) * 0.2;  // 呼吸效果
    meshes[2].scale.set(scale, scale, scale);
    meshes[2].rotation.y += delta * 0.7;

    renderer.render(scene, camera);
}

animate();
```

---

## 第六章：响应式设计

### 6.1 为什么需要响应式设计？

在Web开发中，用户的屏幕尺寸千差万别：
- 桌面显示器：1920x1080、2560x1440等
- 笔记本：1366x768、1920x1080等
- 平板：768x1024、1024x1366等
- 手机：375x667、414x896等

如果不做响应式处理，3D场景可能会：
- 物体变形（宽高比不正确）
- 画面模糊（像素比不正确）
- 性能下降（渲染不必要的像素）

### 6.2 响应式窗口调整

#### 监听窗口大小变化

```javascript
// 监听resize事件
window.addEventListener('resize', onWindowResize);

function onWindowResize() {
    // 获取新的窗口尺寸
    const width = window.innerWidth;
    const height = window.innerHeight;

    // 更新相机的宽高比
    camera.aspect = width / height;

    // 更新相机的投影矩阵
    camera.updateProjectionMatrix();

    // 更新渲染器尺寸
    renderer.setSize(width, height);

    // 更新像素比
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
}
```

#### 完整的响应式设置

```javascript
// 创建相机
const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);

// 创建渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
document.body.appendChild(renderer.domElement);

// 响应式处理
function onWindowResize() {
    const width = window.innerWidth;
    const height = window.innerHeight;

    // 更新相机
    camera.aspect = width / height;
    camera.updateProjectionMatrix();

    // 更新渲染器
    renderer.setSize(width, height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
}

window.addEventListener('resize', onWindowResize);
```

### 6.3 像素比（Pixel Ratio）

#### 什么是像素比？

像素比 = 物理像素 / CSS像素

| 设备类型 | 像素比 | 示例 |
|----------|--------|------|
| 普通显示器 | 1 | 1920x1080 CSS像素 = 1920x1080 物理像素 |
| Retina显示器 | 2 | 1920x1080 CSS像素 = 3840x2160 物理像素 |
| 高端手机 | 3 | 375x667 CSS像素 = 1125x2001 物理像素 |

#### 为什么限制像素比？

```javascript
// 不限制像素比（可能性能问题）
renderer.setPixelRatio(window.devicePixelRatio);

// 限制最大像素比为2（推荐）
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
```

**限制像素比的好处：**
- 避免在高DPI设备上渲染过多像素
- 提高性能，降低GPU负载
- 视觉质量仍然足够好

### 6.4 响应式设计示例

```javascript
// 创建场景
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x1a1a2e);

// 创建相机
const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);
camera.position.z = 5;

// 创建渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
document.body.appendChild(renderer.domElement);

// 创建多个物体
const geometries = [
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.SphereGeometry(0.5, 32, 32),
    new THREE.ConeGeometry(0.5, 1, 32),
    new THREE.TorusGeometry(0.5, 0.2, 16, 100),
    new THREE.OctahedronGeometry(0.6)
];

const colors = [0xff6b6b, 0x4ecdc4, 0xffe66d, 0x95e1d3, 0xf38181];
const meshes = [];

for (let i = 0; i < 5; i++) {
    const material = new THREE.MeshStandardMaterial({
        color: colors[i],
        roughness: 0.5,
        metalness: 0.2
    });
    const mesh = new THREE.Mesh(geometries[i], material);
    mesh.position.x = (i - 2) * 1.5;
    mesh.position.y = Math.sin(i) * 0.5;
    scene.add(mesh);
    meshes.push(mesh);
}

// 添加光源
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// 添加坐标轴辅助线
const axesHelper = new THREE.AxesHelper(3);
scene.add(axesHelper);

// 添加网格辅助线
const gridHelper = new THREE.GridHelper(10, 10);
scene.add(gridHelper);

// 响应式处理
function onWindowResize() {
    const width = window.innerWidth;
    const height = window.innerHeight;

    camera.aspect = width / height;
    camera.updateProjectionMatrix();

    renderer.setSize(width, height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
}

window.addEventListener('resize', onWindowResize);

// 动画循环
function animate() {
    requestAnimationFrame(animate);

    meshes.forEach((mesh, index) => {
        mesh.rotation.x += 0.01 * (index + 1);
        mesh.rotation.y += 0.015 * (index + 1);
    });

    renderer.render(scene, camera);
}

animate();
```

### 6.5 移动设备优化

#### 触摸事件处理

```javascript
// 简单的触摸旋转
let touchStartX = 0;
let touchStartY = 0;

document.addEventListener('touchstart', (event) => {
    touchStartX = event.touches[0].clientX;
    touchStartY = event.touches[0].clientY;
});

document.addEventListener('touchmove', (event) => {
    const touchX = event.touches[0].clientX;
    const touchY = event.touches[0].clientY;

    const deltaX = touchX - touchStartX;
    const deltaY = touchY - touchStartY;

    mesh.rotation.y += deltaX * 0.01;
    mesh.rotation.x += deltaY * 0.01;

    touchStartX = touchX;
    touchStartY = touchY;
});
```

#### 设备方向检测

```javascript
// 检测移动设备
const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);

if (isMobile) {
    // 移动设备优化
    renderer.setPixelRatio(1);  // 降低像素比以提高性能
    camera.fov = 90;  // 增大视野角度
    camera.updateProjectionMatrix();
}
```

---

## 第七章：综合实战

### 7.1 综合示例概述

我们将创建一个综合性的3D场景，包含：
- 多种几何体
- 多种材质
- 多种光源
- 复杂的动画效果
- 交互控制
- 响应式设计

### 7.2 完整代码

完整的综合示例代码请参考该项目仓库的`05-comprehensive.html`文件。

### 7.3 代码解析

#### 场景设置
- 创建深色背景
- 添加雾化效果，增加深度感

#### 相机设置
- 使用60度视野角度
- 相机位置设置在上方，俯视场景

#### 渲染器设置
- 开启抗锯齿
- 启用阴影
- 优化像素比

#### 光源系统
- 环境光：提供基础照明
- 平行光：主光源，产生阴影
- 点光源：补光和轮廓光

#### 物体创建
- 地面：接收阴影
- 中心立方体：旋转动画
- 环绕球体：轨道运动
- 环绕圆锥：外圈轨道
- 环绕圆环：最外圈轨道

#### 动画效果
- 旋转动画
- 轨道运动
- 上下浮动
- 相机摆动

#### 交互控制
- 空格键：暂停/继续
- R键：重置相机

#### 响应式设计
- 窗口大小变化处理
- 像素比优化

---

## 常见问题与故障排除

### 问题1：屏幕全黑，什么也看不到

**可能原因：**
1. 相机位置不正确
2. 物体在相机视野外
3. 渲染器没有正确添加到页面

**解决方案：**
```javascript
// 检查相机位置
console.log('Camera position:', camera.position);

// 检查物体位置
console.log('Mesh position:', mesh.position);

// 确保渲染器已添加到页面
document.body.appendChild(renderer.domElement);

// 使用坐标轴辅助线调试
const axesHelper = new THREE.AxesHelper(5);
scene.add(axesHelper);
```

### 问题2：物体变形或拉伸

**可能原因：**
- 相机宽高比不正确

**解决方案：**
```javascript
// 确保在窗口大小变化时更新宽高比
function onWindowResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
}
window.addEventListener('resize', onWindowResize);
```

### 问题3：动画卡顿或不流畅

**可能原因：**
1. 几何体分段数过高
2. 像素比设置过高
3. 渲染了太多物体

**解决方案：**
```javascript
// 降低几何体分段数
const geometry = new THREE.SphereGeometry(1, 16, 16);  // 而不是32, 32

// 限制像素比
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

// 使用LOD（Level of Detail）
const lod = new THREE.LOD();
lod.addLevel(highDetailMesh, 0);
lod.addLevel(lowDetailMesh, 50);
scene.add(lod);
```

### 问题4：材质看起来很暗或没有光泽

**可能原因：**
- 没有添加光源
- 使用了不受光照影响的材质

**解决方案：**
```javascript
// 添加光源
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// 使用受光照影响的材质
const material = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.5
});
```

### 问题5：控制台报错

**常见错误及解决方案：**

```javascript
// 错误：THREE is not defined
// 解决：正确导入Three.js
import * as THREE from 'three';

// 错误：Cannot read property 'x' of undefined
// 解决：确保对象已创建
if (mesh) {
    mesh.position.x = 5;
}

// 错误：WebGL not supported
// 解决：检查浏览器是否支持WebGL
if (!window.WebGLRenderingContext) {
    alert('您的浏览器不支持WebGL');
}
```

---

## 最佳实践

### 1. 性能优化

#### 减少Draw Calls
```javascript
// 合并静态几何体
import { mergeGeometries } from 'three/examples/jsm/utils/BufferGeometryUtils.js';
const mergedGeometry = mergeGeometries([geo1, geo2, geo3]);
```

#### 使用实例化渲染
```javascript
// 渲染大量相同物体
const instancedMesh = new THREE.InstancedMesh(
    geometry,
    material,
    count  // 实例数量
);
```

#### 限制像素比
```javascript
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
```

### 2. 代码组织

#### 使用模块化
```javascript
// scene.js
export function createScene() {
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x1a1a2e);
    return scene;
}

// camera.js
export function createCamera() {
    const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
    );
    camera.position.z = 5;
    return camera;
}

// main.js
import { createScene } from './scene.js';
import { createCamera } from './camera.js';

const scene = createScene();
const camera = createCamera();
```

### 3. 内存管理

#### 正确销毁对象
```javascript
function disposeMesh(mesh) {
    // 销毁几何体
    mesh.geometry.dispose();

    // 销毁材质
    if (Array.isArray(mesh.material)) {
        mesh.material.forEach(m => m.dispose());
    } else {
        mesh.material.dispose();
    }

    // 从场景中移除
    scene.remove(mesh);
}

// 使用
disposeMesh(mesh);
```

### 4. 调试技巧

#### 使用辅助工具
```javascript
// 坐标轴辅助线
const axesHelper = new THREE.AxesHelper(5);
scene.add(axesHelper);

// 网格辅助线
const gridHelper = new THREE.GridHelper(10, 10);
scene.add(gridHelper);

// 包围盒辅助线
const boxHelper = new THREE.BoxHelper(mesh);
scene.add(boxHelper);
```

#### 使用控制台日志
```javascript
// 输出场景信息
console.log('Scene:', scene);
console.log('Camera:', camera);
console.log('Renderer:', renderer);

// 输出物体信息
console.log('Mesh position:', mesh.position);
console.log('Mesh rotation:', mesh.rotation);
console.log('Mesh scale:', mesh.scale);
```

### 5. 响应式设计

#### 始终处理窗口大小变化
```javascript
function onWindowResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
}

window.addEventListener('resize', onWindowResize);
```

---

## 下一步学习

恭喜你完成了Three.js入门教程的第一部分！接下来你可以学习：

### 推荐学习路径

1. **第二部分：材质与纹理**
   - 深入学习各种材质类型
   - 使用纹理贴图
   - 环境贴图和反射

2. **第三部分：光照系统**
   - 各种光源类型详解
   - 阴影配置
   - 全局光照技术

3. **第四部分：动画与交互**
   - 使用Tween.js
   - 鼠标交互
   - 键盘控制
   - 触摸事件

4. **第五部分：加载外部模型**
   - GLTF/GLB格式
   - OBJ格式
   - FBX格式

5. **第六部分：高级特性**
   - 后处理效果
   - 粒子系统
   - 物理引擎
   - 着色器编程

### 推荐资源

#### 官方资源
- [Three.js官方网站](https://threejs.org/)
- [Three.js官方文档](https://threejs.org/docs/)
- [Three.js官方示例](https://threejs.org/examples/)

#### 学习资源
- [Three.js Journey](https://threejs-journey.com/) - 付费课程，非常全面
- [Bruno Simon的YouTube频道](https://www.youtube.com/c/brunosimon)
- [Three.js Fundamentals](https://threejs.org/manual/#en/fundamentals)

#### 社区资源
- [Three.js GitHub仓库](https://github.com/mrdoob/three.js)
- [Three.js论坛](https://discourse.threejs.org/)
- [Stack Overflow - Three.js标签](https://stackoverflow.com/questions/tagged/three.js)

### 实践项目建议

1. **初级项目**
   - 创建一个旋转的地球仪
   - 制作一个简单的3D相册
   - 实现一个交互式3D产品展示

2. **中级项目**
   - 创建一个3D游戏场景
   - 制作一个数据可视化仪表板
   - 实现一个虚拟展厅

3. **高级项目**
   - 创建一个完整的3D游戏
   - 制作一个VR/AR应用
   - 实现一个3D建模工具

---

## 总结

本教程涵盖了Three.js的基础知识，包括：

✅ Three.js的核心概念和架构
✅ 场景、相机、渲染器的使用
✅ 坐标系和空间变换
✅ 几何体和材质的应用
✅ 动画基础和控制
✅ 响应式设计
✅ 调试和优化技巧

通过学习本教程，你应该能够：
- 创建基本的3D场景
- 使用各种几何体和材质
- 实现简单的动画效果
- 处理响应式设计
- 调试和优化你的3D应用

**继续加油！Three.js的世界非常精彩，期待你创造出令人惊叹的3D作品！** 🚀

---

## 附录

### A. 快速参考

#### 基本场景设置
```javascript
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, aspect, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(width, height);
document.body.appendChild(renderer.domElement);
```

#### 创建网格
```javascript
const mesh = new THREE.Mesh(geometry, material);
scene.add(mesh);
```

#### 动画循环
```javascript
function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
}
animate();
```

#### 响应式处理
```javascript
window.addEventListener('resize', () => {
    camera.aspect = width / height;
    camera.updateProjectionMatrix();
    renderer.setSize(width, height);
});
```

### B. 常用几何体参数速查

| 几何体 | 构造函数 | 参数 |
|--------|----------|------|
| BoxGeometry | `BoxGeometry(w, h, d)` | 宽、高、深 |
| SphereGeometry | `SphereGeometry(r, ws, hs)` | 半径、水平分段、垂直分段 |
| ConeGeometry | `ConeGeometry(r, h, rs)` | 底面半径、高度、分段数 |
| CylinderGeometry | `CylinderGeometry(rt, rb, h, rs)` | 顶部半径、底部半径、高度、分段数 |
| TorusGeometry | `TorusGeometry(r, t, rs, ts)` | 主半径、管半径、径向分段、管分段 |
| PlaneGeometry | `PlaneGeometry(w, h, ws, hs)` | 宽、高、宽度分段、高度分段 |

### C. 常用材质属性速查

| 材质类型 | 主要属性 | 是否需要光照 |
|----------|----------|--------------|
| MeshBasicMaterial | color, wireframe, opacity | 否 |
| MeshStandardMaterial | color, roughness, metalness | 是 |
| MeshPhongMaterial | color, specular, shininess | 是 |
| MeshLambertMaterial | color | 是 |
| MeshNormalMaterial | - | 否 |

### D. Three.js版本兼容性

本教程基于Three.js r160版本编写。不同版本之间可能存在API差异，建议：

1. 始终查看官方文档
2. 关注版本更新日志
3. 使用稳定版本进行生产开发
4. 定期更新以获得新功能和性能改进

---

**教程版本**: 1.0

**Three.js版本**: r160

---

**祝你学习愉快！如有问题，欢迎查阅官方文档或参与社区讨论。** 🎉
