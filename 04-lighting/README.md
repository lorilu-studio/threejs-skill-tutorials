# Three.js 光照教程 - 第四部分：Lighting详解

> 本教程深入讲解Three.js光照系统的核心概念、API使用和实际应用。通过丰富的示例和实践，帮助你掌握3D场景中光照的配置、优化和高级技巧。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：光照基础](#第一章光照基础)
- [第二章：基础光源类型](#第二章基础光源类型)
- [第三章：阴影系统](#第三章阴影系统)
- [第四章：环境光照](#第四章环境光照)
- [第五章：高级光照技巧](#第五章高级光照技巧)
- [第六章：性能优化](#第六章性能优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Three.js光照系统的工作原理
- ✅ 熟练使用各种光源类型
- ✅ 配置和优化阴影效果
- ✅ 实现环境光照（IBL）
- ✅ 掌握光照性能优化技巧
- ✅ 创建真实感的光照场景

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器的基本概念
- **材质基础**: 理解材质如何响应光照
- **JavaScript (ES6+)**: 对象、类、模块等现代语法

### 推荐知识
- **计算机图形学基础**: 光照模型、阴影原理
- **物理渲染理论**: PBR、能量守恒

---

## 第一章：光照基础

### 1.1 为什么需要光照？

光照是3D场景中最重要的元素之一，它决定了场景的视觉效果和真实感。

#### 光照的作用

```
无光照场景              有光照场景
    ●                    ●
   ╱ ╲                 ╱ ╲
  ╱   ╲               ╱   ╲
 ●─────●             ●─────●
(平面无深度）         (有立体感）
```

#### 光照提供的信息

1. **形状信息**: 通过高光和阴影表现物体形状
2. **材质信息**: 不同材质对光照的响应不同
3. **空间关系**: 光照和阴影表现物体间的位置关系
4. **氛围营造**: 光照颜色和强度影响场景情绪

### 1.2 光照类型概览

Three.js提供了多种光源类型，每种都有特定的用途和性能特点。

| 光源类型 | 描述 | 阴影支持 | 性能消耗 | 适用场景 |
|----------|------|----------|----------|----------|
| **AmbientLight** | 全局均匀光照 | 否 | 极低 | 基础环境光 |
| **HemisphereLight** | 天空到地面的渐变 | 否 | 极低 | 户外场景 |
| **DirectionalLight** | 平行光线（太阳） | 是 | 低 | 日光、月光 |
| **PointLight** | 四周发光（灯泡） | 是 | 中 | 灯泡、蜡烛 |
| **SpotLight** | 锥形光束（手电筒） | 是 | 中 | 聚光灯、舞台灯 |
| **RectAreaLight** | 面光源（窗户） | 否* | 高 | 柔和光照 |

*RectAreaLight需要自定义解决方案才能投射阴影

### 1.3 光照与材质的交互

不同材质类型对光照的响应不同：

```javascript
// MeshBasicMaterial - 不受光照影响
const basicMaterial = new THREE.MeshBasicMaterial({
    color: 0xff0000
});

// MeshStandardMaterial - 完整光照响应
const standardMaterial = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.5
});
```

#### 材质光照响应对比

```
MeshBasicMaterial          MeshStandardMaterial
    ●                        ●
   ╱ ╲                     ╱ ╲
  ╱   ╲                   ╱   ╲
 ●─────●                 ●─────●
(无光照变化）            (有高光和阴影）
```

---

## 第二章：基础光源类型

### 2.1 AmbientLight（环境光）

环境光为整个场景提供均匀的基础光照。

#### 特点
- ✅ 性能最高
- ✅ 无方向性
- ✅ 无阴影
- ❌ 缺乏真实感

#### 基本用法

```javascript
// AmbientLight(color, intensity)
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

// 修改光照属性
ambientLight.color.set(0xffffcc);  // 颜色
ambientLight.intensity = 0.3;      // 强度
```

#### 参数说明

| 参数 | 类型 | 范围 | 说明 |
|------|------|--------|------|
| color | Color | - | 光照颜色 |
| intensity | Number | 0-∞ | 光照强度 |

#### 使用场景

| 场景 | 说明 |
|------|------|
| 基础环境光 | 提供最低限度的可见性 |
| 室内场景 | 模拟漫反射环境光 |
| 调试 | 快速查看几何体 |

#### 注意事项

⚠️ **不要过度使用环境光**

```javascript
// 错误示例：环境光过强
const ambientLight = new THREE.AmbientLight(0xffffff, 2.0);
// 结果：场景扁平，无立体感

// 正确示例：适度环境光
const ambientLight = new THREE.AmbientLight(0xffffff, 0.3);
// 结果：保留立体感，提供基础可见性
```

### 2.2 HemisphereLight（半球光）

半球光模拟天空到地面的渐变光照。

#### 特点
- ✅ 适合户外场景
- ✅ 模拟自然光照
- ✅ 性能高
- ❌ 无阴影

#### 基本用法

```javascript
// HemisphereLight(skyColor, groundColor, intensity)
const hemisphereLight = new THREE.HemisphereLight(
    0x87ceeb,  // 天空颜色（天蓝）
    0x8b4513,  // 地面颜色（棕色）
    0.6         // 强度
);
hemisphereLight.position.set(0, 50, 0);
scene.add(hemisphereLight);

// 修改光照属性
hemisphereLight.color.set(0x87ceeb);        // 天空颜色
hemisphereLight.groundColor.set(0x8b4513);  // 地面颜色
hemisphereLight.intensity = 0.6;              // 强度
```

#### 参数说明

| 参数 | 类型 | 范围 | 说明 |
|------|------|--------|------|
| skyColor | Color | - | 天空颜色 |
| groundColor | Color | - | 地面颜色 |
| intensity | Number | 0-∞ | 光照强度 |

#### 使用场景

| 场景 | 说明 |
|------|------|
| 户外白天 | 模拟天空和地面的自然光照 |
| 户外夜晚 | 深色天空，深色地面 |
| 游戏场景 | 简单的环境光照 |

#### 效果对比

```
AmbientLight              HemisphereLight
    ●                        ●
   ╱ ╲                     ╱ ╲
  ╱   ╲                   ╱   ╲
 ●─────●                 ●─────●
(均匀颜色）              (天空到地面渐变）
```

### 2.3 DirectionalLight（平行光）

平行光模拟来自远处的光源，如太阳。

#### 特点
- ✅ 适合模拟太阳光
- ✅ 支持阴影
- ✅ 性能好
- ❌ 光线平行，不自然

#### 基本用法

```javascript
// DirectionalLight(color, intensity)
const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 10, 5);
scene.add(directionalLight);

// 设置光照目标
directionalLight.target.position.set(0, 0, 0);
scene.add(directionalLight.target);
```

#### 参数说明

| 参数 | 类型 | 范围 | 说明 |
|------|------|--------|------|
| color | Color | - | 光照颜色 |
| intensity | Number | 0-∞ | 光照强度 |

#### 属性说明

```javascript
directionalLight.position.set(5, 10, 5);  // 光源位置
directionalLight.target.position.set(0, 0, 0);  // 光照目标
directionalLight.castShadow = true;  // 投射阴影
directionalLight.shadow.mapSize.width = 2048;  // 阴影贴图宽度
directionalLight.shadow.mapSize.height = 2048;  // 阴影贴图高度
```

#### 使用场景

| 场景 | 说明 |
|------|------|
| 日光 | 模拟太阳光 |
| 月光 | 低强度白色光 |
| 室内定向光 | 模拟窗户光 |

#### 光照方向

```
光源位置                光照方向
    ●                    ↓
   ╱ ╲                 ↓
  ╱   ╲               ↓
 ●─────●             ●─────●
(平行光线）            (所有光线平行）
```

### 2.4 PointLight（点光源）

点光源从一点向四周发射光线，如灯泡。

#### 特点
- ✅ 适合模拟灯泡
- ✅ 支持阴影
- ✅ 光线自然衰减
- ❌ 性能消耗中等

#### 基本用法

```javascript
// PointLight(color, intensity, distance, decay)
const pointLight = new THREE.PointLight(
    0xffffff,  // 颜色
    1,         // 强度
    100,       // 最大距离（0 = 无限）
    2          // 衰减系数（物理正确 = 2）
);
pointLight.position.set(0, 5, 0);
pointLight.castShadow = true;
scene.add(pointLight);
```

#### 参数说明

| 参数 | 类型 | 范围 | 说明 |
|------|------|--------|------|
| color | Color | - | 光照颜色 |
| intensity | Number | 0-∞ | 光照强度 |
| distance | Number | 0-∞ | 最大照射距离（0 = 无限） |
| decay | Number | 0-∞ | 衰减系数（2 = 物理正确） |

#### 光照衰减

```javascript
// 距离衰减公式
intensity = baseIntensity / (1 + decay * distance * distance)

// 示例
pointLight.intensity = 1;
pointLight.distance = 20;
pointLight.decay = 2;

// 距离光源5米处
intensity = 1 / (1 + 2 * 5 * 5) = 1 / 51 ≈ 0.02
```

#### 使用场景

| 场景 | 说明 |
|------|------|
| 灯泡 | 室内照明 |
| 蜡烛 | 低强度暖色光 |
| 萤火虫 | 移动的点光源 |
| 爆炸效果 | 瞬时高强度光 |

#### 光照效果

```
PointLight光照
        ●
       ╱╲
      ╱  ╲
     ╱    ╲
    ╱      ╲
   ╱        ╲
  ●──────────●
(向四周扩散）
```

### 2.5 SpotLight（聚光灯）

聚光灯发射锥形光束，如手电筒或舞台灯。

#### 特点
- ✅ 适合聚光效果
- ✅ 支持阴影
- ✅ 可调节光束角度
- ❌ 性能消耗中等

#### 基本用法

```javascript
// SpotLight(color, intensity, distance, angle, penumbra, decay)
const spotLight = new THREE.SpotLight(
    0xffffff,        // 颜色
    1,              // 强度
    100,            // 最大距离
    Math.PI / 6,    // 光束角度（弧度）
    0.5,            // 半影（0-1，边缘柔和度）
    2               // 衰减系数
);
spotLight.position.set(0, 10, 0);
spotLight.castShadow = true;
scene.add(spotLight);

// 设置光照目标
spotLight.target.position.set(0, 0, 0);
scene.add(spotLight.target);
```

#### 参数说明

| 参数 | 类型 | 范围 | 说明 |
|------|------|--------|------|
| color | Color | - | 光照颜色 |
| intensity | Number | 0-∞ | 光照强度 |
| distance | Number | 0-∞ | 最大照射距离 |
| angle | Number | 0-PI/2 | 光束角度（弧度） |
| penumbra | Number | 0-1 | 半影（边缘柔和度） |
| decay | Number | 0-∞ | 衰减系数 |

#### 光束角度

```javascript
// 角度转换为弧度
const angleInRadians = (angleInDegrees * Math.PI) / 180;

// 常用角度
spotLight.angle = Math.PI / 6;   // 30度
spotLight.angle = Math.PI / 4;   // 45度
spotLight.angle = Math.PI / 3;   // 60度
```

#### 半影效果

```
penumbra = 0              penumbra = 0.5            penumbra = 1
    ●                        ●                        ●
   ╱╲                      ╱╲                      ╱╲
  ╱  ╲                    ╱  ╲                    ╱  ╲
 ●────●                  ●────●                  ●────●
(硬边缘）              (柔和边缘）              (最柔和）
```

#### 使用场景

| 场景 | 说明 |
|------|------|
| 手电筒 | 窄角度，高半影 |
| 舞台灯 | 可调节角度和颜色 |
| 车灯 | 前方锥形光束 |
| 探照灯 | 大角度，远距离 |

---

## 第三章：阴影系统

### 3.1 阴影概述

阴影是3D场景中表现空间关系的重要元素。

#### 阴影的作用

```
无阴影场景              有阴影场景
    ●                    ●
   ╱ ╲                 ╱ ╲
  ╱   ╲               ╱   ╲
 ●─────●             ●─────●
(无深度感）            (有空间关系）
```

### 3.2 启用阴影

Three.js阴影系统需要三个步骤：

#### 步骤1：启用渲染器阴影

```javascript
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

// 阴影贴图类型
// THREE.BasicShadowMap - 最快，低质量
// THREE.PCFShadowMap - 默认，过滤
// THREE.PCFSoftShadowMap - 柔和边缘
// THREE.VSMShadowMap - 方差阴影贴图
```

#### 步骤2：启用光源阴影

```javascript
directionalLight.castShadow = true;
pointLight.castShadow = true;
spotLight.castShadow = true;
```

#### 步骤3：启用物体阴影

```javascript
// 投射阴影的物体
mesh.castShadow = true;

// 接收阴影的物体
floor.receiveShadow = true;

// 地面通常不投射阴影
floor.castShadow = false;
```

#### 完整示例

```javascript
// 1. 启用渲染器阴影
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

// 2. 创建光源并启用阴影
const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 10, 5);
directionalLight.castShadow = true;
directionalLight.shadow.mapSize.width = 2048;
directionalLight.shadow.mapSize.height = 2048;
scene.add(directionalLight);

// 3. 创建物体并启用阴影
const sphere = new THREE.Mesh(geometry, material);
sphere.castShadow = true;
sphere.receiveShadow = true;
scene.add(sphere);

// 4. 创建地面并启用阴影
const floor = new THREE.Mesh(floorGeometry, floorMaterial);
floor.receiveShadow = true;
scene.add(floor);
```

### 3.3 阴影贴图配置

#### 阴影贴图大小

```javascript
directionalLight.shadow.mapSize.width = 2048;
directionalLight.shadow.mapSize.height = 2048;

// 大小与质量关系
// 512 - 低质量
// 1024 - 中等质量
// 2048 - 高质量
// 4096 - 很高质量（性能消耗大）
```

#### 阴影相机配置

```javascript
// DirectionalLight阴影相机（正交相机）
directionalLight.shadow.camera.near = 0.5;
directionalLight.shadow.camera.far = 50;
directionalLight.shadow.camera.left = -10;
directionalLight.shadow.camera.right = 10;
directionalLight.shadow.camera.top = 10;
directionalLight.shadow.camera.bottom = -10;

// PointLight阴影相机（透视相机）
pointLight.shadow.camera.near = 0.5;
pointLight.shadow.camera.far = 50;

// SpotLight阴影相机（透视相机）
spotLight.shadow.camera.near = 0.5;
spotLight.shadow.camera.far = 50;
spotLight.shadow.camera.fov = 30;
```

#### 阴影偏移

```javascript
// 深度偏移（修复阴影痤疮）
directionalLight.shadow.bias = -0.0001;

// 法线偏移（沿法线方向偏移）
directionalLight.shadow.normalBias = 0.02;

// 阴影半径（PCFSoftShadowMap）
directionalLight.shadow.radius = 4;
```

### 3.4 阴影问题与解决

#### 阴影痤疮（Shadow Acne）

```
问题表现：
    ●
   ╱╲
  ╱  ╲
 ●────●
 ▓▓▓▓▓  ← 表面出现条纹状伪影
```

**解决方案**：

```javascript
// 方法1：深度偏移
directionalLight.shadow.bias = -0.0001;

// 方法2：法线偏移
directionalLight.shadow.normalBias = 0.02;

// 方法3：增加阴影贴图大小
directionalLight.shadow.mapSize.width = 4096;
directionalLight.shadow.mapSize.height = 4096;
```

#### 阴影锯齿

```
问题表现：
    ●
   ╱╲
  ╱  ╲
 ●────●
 ╱│╱│  ← 阴影边缘锯齿明显
```

**解决方案**：

```javascript
// 方法1：使用PCFSoftShadowMap
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

// 方法2：增加阴影半径
directionalLight.shadow.radius = 4;

// 方法3：增加阴影贴图大小
directionalLight.shadow.mapSize.width = 2048;
directionalLight.shadow.mapSize.height = 2048;
```

#### 阴影缺失

```
问题表现：
    ●
   ╱╲
  ╱  ╲
 ●────●
       ← 阴影缺失
```

**检查清单**：

```javascript
// 1. 渲染器阴影是否启用
renderer.shadowMap.enabled = true;

// 2. 光源阴影是否启用
directionalLight.castShadow = true;

// 3. 物体阴影是否启用
mesh.castShadow = true;
floor.receiveShadow = true;

// 4. 阴影相机范围是否覆盖物体
directionalLight.shadow.camera.left = -10;
directionalLight.shadow.camera.right = 10;
directionalLight.shadow.camera.top = 10;
directionalLight.shadow.camera.bottom = -10;
```

### 3.5 阴影辅助工具

#### 阴影相机辅助线

```javascript
import { CameraHelper } from 'three/addons/helpers/CameraHelper.js';

// DirectionalLight阴影相机辅助
const shadowHelper = new THREE.CameraHelper(directionalLight.shadow.camera);
scene.add(shadowHelper);

// 更新辅助线
shadowHelper.update();
```

#### 阴影贴图可视化

```javascript
// 创建阴影贴图预览
const shadowMaterial = new THREE.MeshBasicMaterial({
    map: directionalLight.shadow.map
});

const shadowPlane = new THREE.Mesh(
    new THREE.PlaneGeometry(5, 5),
    shadowMaterial
);
scene.add(shadowPlane);
```

---

## 第四章：环境光照

### 4.1 IBL（基于图像的光照）

IBL使用环境贴图为场景提供真实的光照和反射。

#### IBL的优势

```
传统光照              IBL光照
    ●                    ●
   ╱ ╲                 ╱ ╲
  ╱   ╲               ╱   ╲
 ●─────●             ●─────●
(单一光源）            (环境反射）
```

### 4.2 HDR环境贴图

HDR（高动态范围）贴图提供更真实的光照和反射。

#### 加载HDR环境

```javascript
import { RGBELoader } from 'three/addons/loaders/RGBELoader.js';

const rgbeLoader = new RGBELoader();

rgbeLoader.load('environment.hdr', (texture) => {
    texture.mapping = THREE.EquirectangularReflectionMapping;

    // 设置为场景环境（影响所有PBR材质）
    scene.environment = texture;

    // 可选：同时用作背景
    scene.background = texture;
    scene.backgroundBlurriness = 0;  // 背景模糊度（0-1）
    scene.backgroundIntensity = 1;   // 背景强度
});
```

#### PMREM预过滤

```javascript
import { PMREMGenerator } from 'three/addons/pmrem/PMREMGenerator.js';

// 创建PMREM生成器
const pmremGenerator = new THREE.PMREMGenerator(renderer);
pmremGenerator.compileEquirectangularShader();

// 加载HDR并预过滤
rgbeLoader.load('environment.hdr', (texture) => {
    const envMap = pmremGenerator.fromEquirectangular(texture).texture;
    scene.environment = envMap;
    scene.background = envMap;

    // 清理资源
    texture.dispose();
    pmremGenerator.dispose();
});
```

### 4.3 立方体贴图

立方体贴图是另一种环境贴图格式。

#### 加载立方体贴图

```javascript
import { CubeTextureLoader } from 'three/addons/loaders/CubeTextureLoader.js';

const cubeLoader = new THREE.CubeTextureLoader();

const envMap = cubeLoader.load([
    'px.jpg',  // 右
    'nx.jpg',  // 左
    'py.jpg',  // 上
    'ny.jpg',  // 下
    'pz.jpg',  // 前
    'nz.jpg'   // 后
]);

scene.environment = envMap;
scene.background = envMap;
```

#### 立方体贴图结构

```
      py (上)
      ↑
      |
nx ← ┼ → pz (前)
      |
      ↓
      ny (下)

px (右) ← ┼ → nz (后)
```

### 4.4 环境光照参数

#### 环境强度

```javascript
// 环境光照强度
scene.environmentIntensity = 1.0;

// 背景强度
scene.backgroundIntensity = 1.0;

// 背景模糊度
scene.backgroundBlurriness = 0;
```

#### 色调映射

```javascript
// 色调映射类型
renderer.toneMapping = THREE.ACESFilmicToneMapping;

// 色调映射曝光
renderer.toneMappingExposure = 1.0;

// 可选色调映射
// THREE.LinearToneMapping - 线性
// THREE.ReinhardToneMapping - Reinhard
// THREE.CineonToneMapping - Cineon
// THREE.ACESFilmicToneMapping - ACES电影级（推荐）
```

### 4.5 光照探针（LightProbe）

光照探针捕获空间中某点的光照信息。

#### 生成光照探针

```javascript
import { LightProbeGenerator } from 'three/addons/lights/LightProbeGenerator.js';

// 从立方体贴图生成
const lightProbe = new THREE.LightProbe();
scene.add(lightProbe);

lightProbe.copy(LightProbeGenerator.fromCubeTexture(cubeTexture));

// 从渲染目标生成
const cubeCamera = new THREE.CubeCamera(
    0.1,
    100,
    new THREE.WebGLCubeRenderTarget(256)
);
cubeCamera.update(renderer, scene);
lightProbe.copy(
    LightProbeGenerator.fromCubeRenderTarget(renderer, cubeCamera.renderTarget)
);
```

#### 使用光照探针

```javascript
// 光照探针提供环境光照
scene.add(lightProbe);

// 可以与IBL结合使用
scene.environment = envMap;
scene.add(lightProbe);
```

---

## 第五章：高级光照技巧

### 5.1 三点光照系统

经典的光照布置，用于摄影和3D渲染。

#### 三点光照组成

```
        主光
         ↓
    ●─────────●
   ╱           ╲
  ╱             ╲
 ●               ●
背光           补光
```

#### 实现代码

```javascript
// 主光（Key Light）- 主要光源
const keyLight = new THREE.DirectionalLight(0xffffff, 1);
keyLight.position.set(5, 5, 5);
keyLight.castShadow = true;
scene.add(keyLight);

// 补光（Fill Light）- 柔和阴影
const fillLight = new THREE.DirectionalLight(0xffffff, 0.5);
fillLight.position.set(-5, 3, 5);
scene.add(fillLight);

// 背光（Back Light）- 轮廓光
const backLight = new THREE.DirectionalLight(0xffffff, 0.3);
backLight.position.set(0, 5, -5);
scene.add(backLight);

// 环境光
const ambient = new THREE.AmbientLight(0x404040, 0.3);
scene.add(ambient);
```

#### 参数调整

```javascript
// 主光：强度1.0，位置(5, 5, 5)
// 补光：强度0.5，位置(-5, 3, 5)
// 背光：强度0.3，位置(0, 5, -5)

// 比例：主光 : 补光 : 背光 = 1 : 0.5 : 0.3
```

### 5.2 光照动画

#### 旋转光源

```javascript
const clock = new THREE.Clock();

function animate() {
    const time = clock.getElapsedTime();

    // 光源围绕场景旋转
    light.position.x = Math.cos(time) * 5;
    light.position.z = Math.sin(time) * 5;

    // 更新辅助线
    lightHelper.update();
}
```

#### 脉冲光照

```javascript
function animate() {
    const time = clock.getElapsedTime();

    // 光源强度脉冲
    light.intensity = 1 + Math.sin(time * 2) * 0.5;
}
```

#### 颜色循环

```javascript
function animate() {
    const time = clock.getElapsedTime();

    // 光源颜色循环
    light.color.setHSL((time * 0.1) % 1, 1, 0.5);
}
```

### 5.3 光照层

光照层允许选择性应用光照到特定物体。

#### 设置光照层

```javascript
// 设置光源层
light.layers.set(1);

// 设置物体层
mesh.layers.enable(1);
otherMesh.layers.disable(1);

// 结果：光源只影响层1的物体
```

#### 多层光照

```javascript
// 光源1 - 层1
const light1 = new THREE.PointLight(0xff0000, 1);
light1.layers.set(1);
scene.add(light1);

// 光源2 - 层2
const light2 = new THREE.PointLight(0x00ff00, 1);
light2.layers.set(2);
scene.add(light2);

// 物体1 - 层1
mesh1.layers.enable(1);
scene.add(mesh1);

// 物体2 - 层2
mesh2.layers.enable(2);
scene.add(mesh2);
```

### 5.4 接触阴影（Contact Shadows）

接触阴影是一种快速、伪阴影效果。

#### 使用接触阴影

```javascript
import { ContactShadows } from 'three/addons/objects/ContactShadows.js';

const contactShadows = new ContactShadows({
    resolution: 512,
    blur: 2,
    opacity: 0.5,
    scale: 10,
    position: [0, 0, 0]
});
scene.add(contactShadows);
```

#### 参数说明

| 参数 | 类型 | 说明 |
|------|------|------|
| resolution | Number | 阴影分辨率 |
| blur | Number | 模糊程度 |
| opacity | Number | 不透明度 |
| scale | Number | 阴影缩放 |
| position | Array | 阴影位置 |

---

## 第六章：性能优化

### 6.1 光照数量限制

#### 光源数量建议

```javascript
// 移动设备：最多2-3个光源
// 桌面设备：最多4-6个光源
// 高性能设备：最多8-10个光源

// 性能影响：每个光源都会增加着色器复杂度
```

#### 使用烘焙光照

```javascript
// 对于静态场景，烘焙光照到纹理
const lightmapTexture = textureLoader.load('lightmap.jpg');

const material = new THREE.MeshStandardMaterial({
    map: diffuseTexture,
    lightMap: lightmapTexture,
    lightMapIntensity: 1.0
});
```

### 6.2 阴影优化

#### 减小阴影贴图

```javascript
// 使用合适的阴影贴图大小
directionalLight.shadow.mapSize.width = 1024;  // 而不是4096
directionalLight.shadow.mapSize.height = 1024;

// 性能提升：4倍
```

#### 紧凑阴影相机

```javascript
// 只覆盖需要的区域
const d = 10;
directionalLight.shadow.camera.left = -d;
directionalLight.shadow.camera.right = d;
directionalLight.shadow.camera.top = d;
directionalLight.shadow.camera.bottom = -d;
directionalLight.shadow.camera.near = 0.5;
directionalLight.shadow.camera.far = 30;
```

#### 禁用不必要的阴影

```javascript
// 小物体通常不需要投射阴影
smallObject.castShadow = false;

// 装饰性物体不需要接收阴影
decoration.receiveShadow = false;
```

### 6.3 光照辅助线管理

#### 动态切换辅助线

```javascript
let showHelpers = true;

function toggleHelpers() {
    showHelpers = !showHelpers;
    helpers.forEach(helper => {
        helper.visible = showHelpers;
    });
}

// 在生产环境中禁用辅助线
if (isProduction) {
    showHelpers = false;
}
```

### 6.4 材质优化

#### 使用合适的材质

```javascript
// 性能优先：MeshBasicMaterial
const basicMaterial = new THREE.MeshBasicMaterial({
    color: 0xff0000
});

// 质量优先：MeshStandardMaterial
const standardMaterial = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.5
});
```

#### 复用材质

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
```

---

## 常见问题与故障排除

### 问题1：场景太暗

**可能原因**：
1. 没有添加光源
2. 光照强度太低
3. 材质不响应光照

**解决方案**：

```javascript
// 1. 添加光源
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// 2. 增加光照强度
ambientLight.intensity = 0.5;
directionalLight.intensity = 1.0;

// 3. 使用响应光照的材质
const material = new THREE.MeshStandardMaterial({
    color: 0xff0000,
    roughness: 0.5,
    metalness: 0.5
});
```

### 问题2：阴影不显示

**可能原因**：
1. 渲染器阴影未启用
2. 光源阴影未启用
3. 物体阴影未启用
4. 阴影相机范围不对

**解决方案**：

```javascript
// 1. 启用渲染器阴影
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

// 2. 启用光源阴影
light.castShadow = true;

// 3. 启用物体阴影
mesh.castShadow = true;
floor.receiveShadow = true;

// 4. 调整阴影相机范围
light.shadow.camera.left = -10;
light.shadow.camera.right = 10;
light.shadow.camera.top = 10;
light.shadow.camera.bottom = -10;
```

### 问题3：阴影质量差

**可能原因**：
1. 阴影贴图太小
2. 阴影偏移不对
3. 阴影类型不合适

**解决方案**：

```javascript
// 1. 增加阴影贴图大小
light.shadow.mapSize.width = 2048;
light.shadow.mapSize.height = 2048;

// 2. 调整阴影偏移
light.shadow.bias = -0.0001;
light.shadow.normalBias = 0.02;

// 3. 使用合适的阴影类型
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

### 问题4：光照不自然

**可能原因**：
1. 只使用环境光
2. 光照颜色不合适
3. 缺少环境贴图

**解决方案**：

```javascript
// 1. 使用多种光源
const ambientLight = new THREE.AmbientLight(0x404040, 0.3);
const directionalLight = new THREE.DirectionalLight(0xffffcc, 1);
scene.add(ambientLight);
scene.add(directionalLight);

// 2. 调整光照颜色
directionalLight.color.set(0xffffcc);  // 暖白色

// 3. 添加环境贴图
scene.environment = envMap;
```

### 问题5：性能问题

**可能原因**：
1. 光源数量太多
2. 阴影贴图太大
3. 光照计算太复杂

**解决方案**：

```javascript
// 1. 减少光源数量
// 移除不必要的光源
scene.remove(unusedLight);

// 2. 减小阴影贴图
light.shadow.mapSize.width = 1024;
light.shadow.mapSize.height = 1024;

// 3. 使用烘焙光照
material.lightMap = lightmapTexture;
```

---

## 最佳实践

### 1. 光照布置原则

| 原则 | 说明 |
|--------|------|
| **主次分明** | 一个主光源，多个辅助光源 |
| **颜色协调** | 光照颜色与场景氛围一致 |
| **强度平衡** | 主光最强，补光次之，背光最弱 |
| **方向自然** | 光照方向符合物理规律 |

### 2. 阴影配置建议

```javascript
// 阴影贴图大小
// 移动：512-1024
// 桌面：1024-2048
// 高性能：2048-4096

// 阴影类型
// 性能优先：PCFShadowMap
// 质量优先：PCFSoftShadowMap

// 阴影偏移
// 通常：bias = -0.0001, normalBias = 0.02
// 根据场景调整
```

### 3. 环境光照建议

```javascript
// 使用HDR环境贴图
const envMap = pmremGenerator.fromEquirectangular(hdrTexture).texture;
scene.environment = envMap;

// 使用色调映射
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.0;

// 调整环境强度
scene.environmentIntensity = 1.0;
```

### 4. 性能优化建议

```javascript
// 限制光源数量
const maxLights = isMobile ? 3 : 6;

// 使用合适的阴影贴图大小
const shadowMapSize = isMobile ? 512 : 1024;

// 紧凑阴影相机
const frustumSize = 10;
light.shadow.camera.left = -frustumSize;
light.shadow.camera.right = frustumSize;
light.shadow.camera.top = frustumSize;
light.shadow.camera.bottom = -frustumSize;
```

### 5. 调试技巧

```javascript
// 使用光源辅助线
const lightHelper = new THREE.DirectionalLightHelper(light, 5);
scene.add(lightHelper);

// 使用阴影相机辅助线
const shadowHelper = new THREE.CameraHelper(light.shadow.camera);
scene.add(shadowHelper);

// 可视化法线
const normalMaterial = new THREE.MeshNormalMaterial();
mesh.material = normalMaterial;
```

---

## 下一步学习

恭喜你完成了Three.js光照教程！接下来你可以学习：

### 推荐学习路径

1. **第五部分：动画系统**
   - 关键帧动画
   - 骨骼动画
   - 程序化动画

2. **第六部分：纹理系统**
   - 高级纹理技术
   - 环境贴图
   - HDR纹理

3. **第七部分：后处理**
   - 泛光效果
   - 景深效果
   - 色彩校正

### 推荐资源

#### 官方资源
- [Three.js官方文档 - Lighting](https://threejs.org/docs/#api/en/lights/Light)
- [Three.js官方示例 - Lighting](https://threejs.org/examples/#webgl_lights)

#### 学习资源
- [Three.js Journey](https://threejs-journey.com/)
- [Bruno Simon的YouTube频道](https://www.youtube.com/c/brunosimon)

### 实践项目建议

1. **初级项目**
   - 创建一个日光场景
   - 实现一个室内照明
   - 制作一个阴影效果

2. **中级项目**
   - 创建一个动态光照系统
   - 实现一个环境光照场景
   - 制作一个光照编辑器

3. **高级项目**
   - 创建一个全局光照系统
   - 实现一个实时阴影系统
   - 制作一个光照预览工具

---

## 总结

本教程涵盖了Three.js光照系统的核心知识，包括：

✅ 光照的基础概念和工作原理
✅ 各种光源类型的使用方法
✅ 阴影系统的配置和优化
✅ 环境光照（IBL）的实现
✅ 高级光照技巧和动画
✅ 光照性能优化方法

通过学习本教程，你应该能够：
- 选择合适的光源类型
- 配置真实的阴影效果
- 实现环境光照
- 优化光照性能
- 创建高质量的光照场景

**继续加油！Three.js的光照系统非常强大，期待你创造出令人惊叹的3D作品！** 🚀

---

## 附录

### A. 光源类型速查

| 光源类型 | 阴影 | 性能 | 主要用途 |
|----------|--------|--------|----------|
| AmbientLight | 否 | 极高 | 基础环境光 |
| HemisphereLight | 否 | 极高 | 户外场景 |
| DirectionalLight | 是 | 低 | 日光、月光 |
| PointLight | 是 | 中 | 灯泡、蜡烛 |
| SpotLight | 是 | 中 | 聚光灯、舞台灯 |
| RectAreaLight | 否* | 高 | 柔和光照 |

### B. 阴影类型速查

| 阴影类型 | 质量 | 性能 | 说明 |
|----------|--------|--------|------|
| BasicShadowMap | 低 | 最高 | 最快，质量最低 |
| PCFShadowMap | 中 | 高 | 默认，过滤 |
| PCFSoftShadowMap | 高 | 中 | 柔和边缘 |
| VSMShadowMap | 高 | 中 | 方差阴影贴图 |

### C. 光照参数速查

| 参数 | 范围 | 说明 |
|------|--------|------|
| intensity | 0-∞ | 光照强度 |
| distance | 0-∞ | 最大照射距离 |
| decay | 0-∞ | 衰减系数（2 = 物理正确） |
| angle | 0-PI/2 | 聚光灯光束角度 |
| penumbra | 0-1 | 聚光灯半影 |
| bias | -∞-∞ | 阴影深度偏移 |
| normalBias | 0-∞ | 阴影法线偏移 |
| radius | 0-∞ | 阴影模糊半径 |

### D. 性能优化清单

- [ ] 限制光源数量（移动<3，桌面<6）
- [ ] 使用合适的阴影贴图大小（512-2048）
- [ ] 紧凑阴影相机范围
- [ ] 禁用不必要的阴影
- [ ] 使用光照层
- [ ] 使用烘焙光照
- [ ] 使用PMREM预过滤
- [ ] 及时更新辅助线
- [ ] 在生产环境禁用辅助线
- [ ] 使用合适的材质类型

---

**教程版本**: 1.0

**Three.js版本**: r160

---

**祝你学习愉快！如有问题，欢迎查阅官方文档或参与社区讨论。** 🎉
