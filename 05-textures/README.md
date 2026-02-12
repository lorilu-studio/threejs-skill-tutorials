# Three.js 纹理教程 - 第五部分：Textures详解

> 本教程深入讲解Three.js纹理（Textures）的核心概念、API使用和高级技巧。通过丰富的示例和实践，帮助你掌握3D纹理的创建、配置和优化。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：纹理基础概念](#第一章纹理基础概念)
- [第二章：纹理加载](#第二章纹理加载)
- [第三章：纹理配置](#第三章纹理配置)
- [第四章：纹理类型](#第四章纹理类型)
- [第五章：立方体纹理](#第五章立方体纹理)
- [第六章：HDR纹理](#第六章hdr纹理)
- [第七章：UV映射](#第七章uv映射)
- [第八章：纹理优化](#第八章纹理优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Three.js纹理的核心概念和作用
- ✅ 熟练使用TextureLoader加载各种纹理
- ✅ 掌握纹理的配置选项（重复、偏移、旋转、过滤）
- ✅ 理解不同纹理类型（普通纹理、数据纹理、画布纹理、视频纹理）
- ✅ 使用立方体纹理创建环境贴图
- ✅ 加载和使用HDR纹理实现真实光照
- ✅ 理解UV映射原理和操作
- ✅ 掌握纹理的性能优化技巧

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器的基本概念
- **材质基础**: 理解材质如何使用纹理
- **JavaScript (ES6+)**: 异步编程、Promise、async/await

### 推荐知识
- **计算机图形学基础**: 纹理映射、UV坐标
- **图像处理基础**: 颜色空间、图像格式

---

## 第一章：纹理基础概念

### 1.1 什么是纹理（Texture）？

**纹理**是应用到3D物体表面的2D图像，用于定义物体的颜色、粗糙度、法线等表面属性。

#### 纹理的作用

```
几何体（Geometry）     +     材质（Material）     +     纹理（Texture）     =     真实的3D对象
    ↓                              ↓                              ↓                      ↓
  形状定义                      材质属性                      表面细节                  完整的视觉效果
  - 顶点                      - 光照模型                    - 颜色                   - 真实感
  - 面                        - 透明度                      - 粗糙度                 - 细节丰富
  - 法线                      - 发光                        - 法线                   - 材质感
  - UV坐标                    - 反射率                      - 金属度
```

#### 类比理解

- **几何体**就像建筑的**结构框架**
- **材质**就像建筑的**装修材料**（油漆、壁纸等）
- **纹理**就像装修材料的**图案和质感**（木纹、大理石纹、布料纹理等）

### 1.2 UV坐标系统

UV坐标是纹理映射的核心概念，用于将2D纹理映射到3D物体表面。

#### UV坐标范围

```
纹理空间（0-1范围）
┌─────────────────┐
│ (0,1)    (1,1)  │
│                 │
│                 │
│ (0,0)    (1,0)  │
└─────────────────┘
   U轴 →
   ↓ V轴
```

- **U坐标**: 水平方向，从左到右（0到1）
- **V坐标**: 垂直方向，从下到上（0到1）
- **原点(0,0)**: 纹理左下角

#### UV映射过程

```
3D物体表面顶点          UV坐标              2D纹理
    ↓                    ↓                   ↓
  顶点位置          (u, v)坐标          像素颜色
  (x, y, z)        (0.5, 0.3)         RGB(128, 64, 32)
    ↓                    ↓                   ↓
  映射到纹理空间      采样纹理           获取颜色
    └─────────────────────────────────────┘
              纹理映射
```

### 1.3 纹理类型概述

Three.js支持多种纹理类型，每种都有特定的用途：

| 纹理类型 | 用途 | 性能 | 示例场景 |
|----------|------|------|----------|
| **Texture** | 普通图像纹理 | 高 | 物体颜色贴图 |
| **DataTexture** | 程序化数据纹理 | 高 | 噪声、梯度 |
| **CanvasTexture** | Canvas绘制纹理 | 中 | 动态文字、图表 |
| **VideoTexture** | 视频纹理 | 中 | 视频播放、监控 |
| **CubeTexture** | 立方体环境贴图 | 中 | 天空盒、反射 |
| **CompressedTexture** | 压缩纹理 | 高 | 移动端优化 |
| **DepthTexture** | 深度纹理 | 高 | 深度效果、后处理 |

---

## 第二章：纹理加载

### 2.1 TextureLoader基础

TextureLoader是加载纹理的主要工具，支持同步和异步加载方式。

#### 基本加载方式

```javascript
const loader = new THREE.TextureLoader();

// 方式1：同步加载（内部异步）
const texture = loader.load('texture.jpg');
material.map = texture;

// 方式2：带回调的异步加载
loader.load(
  'texture.jpg',           // 纹理URL
  (texture) => {           // 加载成功回调
    console.log('纹理加载完成');
    material.map = texture;
  },
  (xhr) => {               // 加载进度回调
    console.log((xhr.loaded / xhr.total * 100) + '% 已加载');
  },
  (error) => {             // 加载错误回调
    console.error('纹理加载失败', error);
  }
);
```

#### 异步加载示例

```javascript
async function loadTexture(url) {
  return new Promise((resolve, reject) => {
    new THREE.TextureLoader().load(url, resolve, undefined, reject);
  });
}

// 使用示例
async function init() {
  try {
    const texture = await loadTexture('texture.jpg');
    material.map = texture;
    material.needsUpdate = true;
  } catch (error) {
    console.error('加载失败:', error);
  }
}
```

#### 批量加载纹理

```javascript
async function loadTextures(urls) {
  const loader = new THREE.TextureLoader();
  const promises = urls.map(url => {
    return new Promise((resolve, reject) => {
      loader.load(url, resolve, undefined, reject);
    });
  });

  return Promise.all(promises);
}

// 使用示例
const textures = await loadTextures([
  'color.jpg',
  'normal.jpg',
  'roughness.jpg'
]);

material.map = textures[0];
material.normalMap = textures[1];
material.roughnessMap = textures[2];
```

### 2.2 加载管理器（LoadingManager）

LoadingManager可以管理多个资源的加载进度。

```javascript
const manager = new THREE.LoadingManager();

// 全局加载完成
manager.onLoad = function() {
  console.log('所有资源加载完成');
};

// 加载进度
manager.onProgress = function(url, itemsLoaded, itemsTotal) {
  const progress = (itemsLoaded / itemsTotal * 100).toFixed(1);
  console.log(`加载进度: ${progress}%`);
};

// 加载错误
manager.onError = function(url) {
  console.error('加载错误:', url);
};

// 创建使用管理器的加载器
const textureLoader = new THREE.TextureLoader(manager);

// 加载多个纹理
const texture1 = textureLoader.load('texture1.jpg');
const texture2 = textureLoader.load('texture2.jpg');
const texture3 = textureLoader.load('texture3.jpg');
```

### 2.3 纹理加载最佳实践

#### 错误处理

```javascript
function loadTextureWithFallback(url, fallbackUrl) {
  return new Promise((resolve) => {
    new THREE.TextureLoader().load(
      url,
      (texture) => resolve(texture),
      undefined,
      () => {
        console.warn(`加载${url}失败，使用备用纹理${fallbackUrl}`);
        new THREE.TextureLoader().load(fallbackUrl, resolve);
      }
    );
  });
}
```

#### 预加载纹理

```javascript
class TextureCache {
  constructor() {
    this.cache = new Map();
    this.loader = new THREE.TextureLoader();
  }

  async get(url) {
    if (this.cache.has(url)) {
      return this.cache.get(url);
    }

    const texture = await new Promise((resolve, reject) => {
      this.loader.load(url, resolve, undefined, reject);
    });

    this.cache.set(url, texture);
    return texture;
  }

  dispose(url) {
    const texture = this.cache.get(url);
    if (texture) {
      texture.dispose();
      this.cache.delete(url);
    }
  }

  disposeAll() {
    this.cache.forEach(texture => texture.dispose());
    this.cache.clear();
  }
}

const textureCache = new TextureCache();
const texture = await textureCache.get('texture.jpg');
```

---

## 第三章：纹理配置

### 3.1 颜色空间（Color Space）

颜色空间设置对纹理的正确显示至关重要。

#### 颜色空间类型

```javascript
// 颜色/反照率纹理 - 使用sRGB
colorTexture.colorSpace = THREE.SRGBColorSpace;

// 数据纹理（法线、粗糙度、金属度、AO）- 保持默认（NoColorSpace）
// 不要为数据纹理设置colorSpace

// 线性颜色空间（用于特殊效果）
texture.colorSpace = THREE.LinearSRGBColorSpace;
```

#### 颜色空间示例

```javascript
// PBR材质纹理设置
const material = new THREE.MeshStandardMaterial({
  // 颜色贴图 - sRGB
  map: colorTexture,
  map: { value: colorTexture, colorSpace: THREE.SRGBColorSpace },

  // 法线贴图 - Linear（数据纹理）
  normalMap: normalTexture,

  // 粗糙度贴图 - Linear（数据纹理）
  roughnessMap: roughnessTexture,

  // 金属度贴图 - Linear（数据纹理）
  metalnessMap: metalnessTexture,

  // AO贴图 - Linear（数据纹理）
  aoMap: aoTexture,

  // 自发光贴图 - sRGB
  emissiveMap: emissiveTexture,
  emissiveMap: { value: emissiveTexture, colorSpace: THREE.SRGBColorSpace },
});
```

### 3.2 包裹模式（Wrapping）

包裹模式决定纹理超出UV范围时的行为。

#### 包裹模式类型

```javascript
// 水平方向包裹
texture.wrapS = THREE.ClampToEdgeWrapping;  // 拉伸边缘像素（默认）
texture.wrapS = THREE.RepeatWrapping;        // 平铺重复
texture.wrapS = THREE.MirroredRepeatWrapping; // 镜像重复

// 垂直方向包裹
texture.wrapT = THREE.ClampToEdgeWrapping;
texture.wrapT = THREE.RepeatWrapping;
texture.wrapT = THREE.MirroredRepeatWrapping;
```

#### 包裹模式效果

```
ClampToEdgeWrapping:    RepeatWrapping:        MirroredRepeatWrapping:
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│████████████████│     │████████████████│     │████████████████│
│████████████████│     │████████████████│     │████████████████│
│████████████████│     │████████████████│     │████████████████│
└─────────────────┘     └─────────────────┘     └─────────────────┘
边缘拉伸                 平铺重复                 镜像重复
```

#### 平铺纹理示例

```javascript
// 创建平铺纹理
const texture = loader.load('tile.jpg');
texture.wrapS = THREE.RepeatWrapping;
texture.wrapT = THREE.RepeatWrapping;
texture.repeat.set(4, 4); // 水平4次，垂直4次

const material = new THREE.MeshStandardMaterial({
  map: texture
});
```

### 3.3 重复、偏移、旋转

这些属性控制纹理在物体表面的显示方式。

#### 重复（Repeat）

```javascript
// 设置重复次数
texture.repeat.set(2, 3); // 水平2次，垂直3次

// 需要配合RepeatWrapping使用
texture.wrapS = THREE.RepeatWrapping;
texture.wrapT = THREE.RepeatWrapping;

// 单独设置
texture.repeat.x = 2;  // 水平重复
texture.repeat.y = 3;  // 垂直重复
```

#### 偏移（Offset）

```javascript
// 设置偏移量（0-1范围）
texture.offset.set(0.5, 0.25); // 向右偏移50%，向上偏移25%

// 单独设置
texture.offset.x = 0.5;  // 水平偏移
texture.offset.y = 0.25; // 垂直偏移
```

#### 旋转（Rotation）

```javascript
// 设置旋转角度（弧度）
texture.rotation = Math.PI / 4; // 旋转45度

// 设置旋转中心（默认为0,0）
texture.center.set(0.5, 0.5); // 围绕纹理中心旋转

// 旋转动画
function animate() {
  texture.rotation += 0.01;
}
```

#### 综合示例

```javascript
const texture = loader.load('pattern.jpg');

// 配置纹理变换
texture.wrapS = THREE.RepeatWrapping;
texture.wrapT = THREE.RepeatWrapping;
texture.repeat.set(3, 3);
texture.offset.set(0.1, 0.1);
texture.rotation = Math.PI / 6;
texture.center.set(0.5, 0.5);

// 应用到材质
const material = new THREE.MeshStandardMaterial({
  map: texture
});
```

### 3.4 过滤模式（Filtering）

过滤模式决定纹理缩放时的采样方式。

#### 缩小过滤（Minification Filter）

纹理比屏幕像素大时使用。

```javascript
// 平滑过滤（默认）
texture.minFilter = THREE.LinearMipmapLinearFilter;

// 像素化过滤
texture.minFilter = THREE.NearestFilter;

// 线性过滤（无mipmap）
texture.minFilter = THREE.LinearFilter;
```

#### 放大过滤（Magnification Filter）

纹理比屏幕像素小时使用。

```javascript
// 平滑过滤（默认）
texture.magFilter = THREE.LinearFilter;

// 像素化过滤（复古游戏风格）
texture.magFilter = THREE.NearestFilter;
```

#### 过滤模式对比

```
LinearFilter:           NearestFilter:
┌─────────────────┐     ┌─────────────────┐
│ ╱╲╱╲╱╲╱╲╱╲╱╲  │     │████████████████│
│╱╲╱╲╱╲╱╲╱╲╱╲╱╲│     │████████████████│
│ ╱╲╱╲╱╲╱╲╱╲╱╲  │     │████████████████│
└─────────────────┘     └─────────────────┘
平滑                    像素化
```

#### 各向异性过滤（Anisotropy）

提高倾斜视角下的纹理清晰度。

```javascript
// 设置最大各向异性级别
texture.anisotropy = renderer.capabilities.getMaxAnisotropy();

// 手动设置级别（1-16）
texture.anisotropy = 8;
```

### 3.5 Mipmap生成

Mipmap是多级纹理金字塔，用于优化远距离渲染。

#### Mipmap控制

```javascript
// 启用Mipmap（默认）
texture.generateMipmaps = true;

// 禁用Mipmap（非2的幂次纹理或数据纹理）
texture.generateMipmaps = false;
texture.minFilter = THREE.LinearFilter;

// 手动设置Mipmap级别
texture.minFilter = THREE.LinearMipMapLinearFilter;
```

#### Mipmap结构

```
原始纹理 (512x512)
    ↓
Mipmap Level 0 (512x512)
    ↓
Mipmap Level 1 (256x256)
    ↓
Mipmap Level 2 (128x128)
    ↓
Mipmap Level 3 (64x64)
    ↓
...
```

---

## 第四章：纹理类型

### 4.1 普通纹理（Texture）

最常用的纹理类型，从图像文件加载。

```javascript
const loader = new THREE.TextureLoader();
const texture = loader.load('image.jpg');

// 配置纹理
texture.colorSpace = THREE.SRGBColorSpace;
texture.wrapS = THREE.RepeatWrapping;
texture.wrapT = THREE.RepeatWrapping;
texture.repeat.set(2, 2);

// 应用到材质
const material = new THREE.MeshStandardMaterial({
  map: texture
});
```

### 4.2 数据纹理（DataTexture）

从原始数据创建纹理，用于程序化生成。

#### 创建数据纹理

```javascript
function createNoiseTexture(size = 256) {
  const data = new Uint8Array(size * size * 4);

  for (let i = 0; i < size * size; i++) {
    const value = Math.random() * 255;
    data[i * 4] = value;     // R
    data[i * 4 + 1] = value; // G
    data[i * 4 + 2] = value; // B
    data[i * 4 + 3] = 255;   // A
  }

  const texture = new THREE.DataTexture(data, size, size);
  texture.needsUpdate = true;
  return texture;
}

const noiseTexture = createNoiseTexture(512);
material.roughnessMap = noiseTexture;
```

#### 创建渐变纹理

```javascript
function createGradientTexture(color1, color2, size = 256) {
  const data = new Uint8Array(size * size * 4);

  const c1 = new THREE.Color(color1);
  const c2 = new THREE.Color(color2);

  for (let y = 0; y < size; y++) {
    for (let x = 0; x < size; x++) {
      const t = x / size;
      const index = (y * size + x) * 4;

      data[index] = THREE.MathUtils.lerp(c1.r, c2.r, t) * 255;
      data[index + 1] = THREE.MathUtils.lerp(c1.g, c2.g, t) * 255;
      data[index + 2] = THREE.MathUtils.lerp(c1.b, c2.b, t) * 255;
      data[index + 3] = 255;
    }
  }

  const texture = new THREE.DataTexture(data, size, size);
  texture.needsUpdate = true;
  return texture;
}

const gradientTexture = createGradientTexture('#ff0000', '#0000ff');
material.map = gradientTexture;
```

### 4.3 画布纹理（CanvasTexture）

从HTML Canvas元素创建纹理，支持动态更新。

#### 创建画布纹理

```javascript
function createTextTexture(text, size = 256) {
  const canvas = document.createElement('canvas');
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext('2d');

  // 绘制背景
  ctx.fillStyle = '#ffffff';
  ctx.fillRect(0, 0, size, size);

  // 绘制文字
  ctx.fillStyle = '#000000';
  ctx.font = 'bold 48px Arial';
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.fillText(text, size / 2, size / 2);

  const texture = new THREE.CanvasTexture(canvas);
  return texture;
}

const textTexture = createTextTexture('Hello Three.js');
material.map = textTexture;
```

#### 动态更新画布纹理

```javascript
const canvas = document.createElement('canvas');
canvas.width = 256;
canvas.height = 256;
const ctx = canvas.getContext('2d');

const texture = new THREE.CanvasTexture(canvas);
const material = new THREE.MeshStandardMaterial({ map: texture });

let counter = 0;

function animate() {
  counter++;

  // 清除画布
  ctx.fillStyle = '#ffffff';
  ctx.fillRect(0, 0, 256, 256);

  // 绘制动态内容
  ctx.fillStyle = '#ff0000';
  ctx.font = '48px Arial';
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.fillText(`Count: ${counter}`, 128, 128);

  // 标记纹理需要更新
  texture.needsUpdate = true;

  requestAnimationFrame(animate);
}

animate();
```

### 4.4 视频纹理（VideoTexture）

从视频元素创建纹理，用于视频播放。

#### 创建视频纹理

```javascript
const video = document.createElement('video');
video.src = 'video.mp4';
video.loop = true;
video.muted = true;
video.playsInline = true;
video.play();

const texture = new THREE.VideoTexture(video);
texture.colorSpace = THREE.SRGBColorSpace;

const material = new THREE.MeshStandardMaterial({
  map: texture
});
```

#### 视频控制

```javascript
const video = document.createElement('video');
video.src = 'video.mp4';
video.loop = true;
video.muted = true;
video.playsInline = true;

const texture = new THREE.VideoTexture(video);

// 播放控制
function playVideo() {
  video.play();
}

function pauseVideo() {
  video.pause();
}

function setVolume(volume) {
  video.volume = volume;
}

// 视频事件
video.addEventListener('loadeddata', () => {
  console.log('视频加载完成');
});

video.addEventListener('ended', () => {
  console.log('视频播放结束');
});
```

### 4.5 压缩纹理（CompressedTexture）

使用压缩格式优化纹理加载和内存使用。

#### KTX2纹理加载

```javascript
import { KTX2Loader } from 'three/examples/jsm/loaders/KTX2Loader.js';

const ktx2Loader = new KTX2Loader();
ktx2Loader.setTranscoderPath('path/to/basis/');
ktx2Loader.detectSupport(renderer);

ktx2Loader.load('texture.ktx2', (texture) => {
  material.map = texture;
});
```

---

## 第五章：立方体纹理

### 5.1 立方体纹理概念

立方体纹理由6个面组成，用于环境贴图和天空盒。

#### 立方体纹理结构

```
        +Y (py)
        ┌───┐
        │   │
+X (px) │   │ -X (nx)
┌───┐    │   │    ┌───┐
│   │    └───┘    │   │
│   │             │   │
└───┘             └───┘
        +Z (pz)
        ┌───┐
        │   │
-Z (nz) │   │
        │   │
        └───┘
```

### 5.2 CubeTextureLoader

加载6个面的立方体纹理。

```javascript
const loader = new THREE.CubeTextureLoader();

// 加载立方体纹理
const cubeTexture = loader.load([
  'px.jpg', // +X (右)
  'nx.jpg', // -X (左)
  'py.jpg', // +Y (上)
  'ny.jpg', // -Y (下)
  'pz.jpg', // +Z (前)
  'nz.jpg'  // -Z (后)
]);

// 作为背景
scene.background = cubeTexture;

// 作为环境贴图
scene.environment = cubeTexture;
material.envMap = cubeTexture;
```

### 5.3 天空盒（Skybox）

使用立方体纹理创建天空盒。

```javascript
// 加载天空盒纹理
const loader = new THREE.CubeTextureLoader();
const skyboxTexture = loader.load([
  'skybox_right.jpg',
  'skybox_left.jpg',
  'skybox_top.jpg',
  'skybox_bottom.jpg',
  'skybox_front.jpg',
  'skybox_back.jpg'
]);

// 设置场景背景
scene.background = skyboxTexture;

// 或者使用大立方体
const skyboxGeometry = new THREE.BoxGeometry(1000, 1000, 1000);
const skyboxMaterial = new THREE.MeshBasicMaterial({
  map: skyboxTexture,
  side: THREE.BackSide // 渲染立方体内侧
});
const skybox = new THREE.Mesh(skyboxGeometry, skyboxMaterial);
scene.add(skybox);
```

### 5.4 环境反射

使用立方体纹理实现反射效果。

```javascript
// 加载环境贴图
const loader = new THREE.CubeTextureLoader();
const envMap = loader.load([
  'env_right.jpg',
  'env_left.jpg',
  'env_top.jpg',
  'env_bottom.jpg',
  'env_front.jpg',
  'env_back.jpg'
]);

// 应用到材质
const material = new THREE.MeshStandardMaterial({
  color: 0xffffff,
  metalness: 1.0,
  roughness: 0.1,
  envMap: envMap,
  envMapIntensity: 1.0
});

// 设置场景环境
scene.environment = envMap;
```

---

## 第六章：HDR纹理

### 6.1 HDR纹理概念

HDR（High Dynamic Range）纹理包含高动态范围的颜色信息，用于真实的环境光照。

#### HDR vs LDR

```
LDR (0-255):           HDR (浮点):
┌─────────────────┐     ┌─────────────────┐
│   亮度范围有限   │     │   亮度范围宽广   │
│   容易过曝       │     │   保留高光细节   │
│   适合显示       │     │   适合光照计算   │
└─────────────────┘     └─────────────────┘
```

### 6.2 RGBELoader

加载HDR格式的纹理。

```javascript
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader.js';

const loader = new RGBELoader();
loader.load('environment.hdr', (texture) => {
  texture.mapping = THREE.EquirectangularReflectionMapping;

  // 作为环境贴图
  scene.environment = texture;

  // 作为背景
  scene.background = texture;
});
```

### 6.3 PMREMGenerator

将HDR纹理转换为高质量的环境贴图。

```javascript
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader.js';

const pmremGenerator = new THREE.PMREMGenerator(renderer);
pmremGenerator.compileEquirectangularShader();

new RGBELoader().load('environment.hdr', (texture) => {
  const envMap = pmremGenerator.fromEquirectangular(texture).texture;

  scene.environment = envMap;
  scene.background = envMap;

  texture.dispose();
  pmremGenerator.dispose();
});
```

### 6.4 背景配置

配置背景的模糊度和强度。

```javascript
// 加载HDR纹理
const loader = new RGBELoader();
loader.load('environment.hdr', (texture) => {
  texture.mapping = THREE.EquirectangularReflectionMapping;
  scene.background = texture;

  // 背景模糊度（0-1）
  scene.backgroundBlurriness = 0.5;

  // 背景强度
  scene.backgroundIntensity = 1.5;

  // 背景旋转
  scene.backgroundRotation.y = Math.PI;
});
```

---

## 第七章：UV映射

### 7.1 访问UV坐标

直接操作几何体的UV属性。

```javascript
const geometry = new THREE.BoxGeometry(1, 1, 1);
const uvs = geometry.attributes.uv;

// 读取UV坐标
const u = uvs.getX(vertexIndex);
const v = uvs.getY(vertexIndex);

// 修改UV坐标
uvs.setXY(vertexIndex, newU, newV);
uvs.needsUpdate = true;
```

### 7.2 第二UV通道（UV2）

用于AO贴图等需要独立UV的纹理。

```javascript
// 复制UV到UV2
geometry.setAttribute('uv2', geometry.attributes.uv);

// 或创建自定义UV2
const uv2 = new Float32Array(vertexCount * 2);
// 填充uv2数据
geometry.setAttribute('uv2', new THREE.BufferAttribute(uv2, 2));

// 使用UV2
const material = new THREE.MeshStandardMaterial({
  aoMap: aoTexture,
  aoMapIntensity: 1.0
});
```

### 7.3 UV变换动画

在着色器中实现UV变换。

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: {
    map: { value: texture },
    uvOffset: { value: new THREE.Vector2(0, 0) },
    uvScale: { value: new THREE.Vector2(1, 1) }
  },
  vertexShader: `
    varying vec2 vUv;
    uniform vec2 uvOffset;
    uniform vec2 uvScale;

    void main() {
      vUv = uv * uvScale + uvOffset;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    varying vec2 vUv;
    uniform sampler2D map;

    void main() {
      gl_FragColor = texture2D(map, vUv);
    }
  `
});

// 动画更新
function animate() {
  material.uniforms.uvOffset.value.x += 0.001;
}
```

---

## 第八章：纹理优化

### 8.1 纹理尺寸优化

使用合适的纹理尺寸平衡质量和性能。

```javascript
// 检测设备类型
const isMobile = /iPhone|iPad|Android/i.test(navigator.userAgent);

// 根据设备选择纹理尺寸
const textureSize = isMobile ? 1024 : 2048;

// 加载纹理
const texture = loader.load('texture.jpg');
texture.image = resizeTexture(texture.image, textureSize);
```

### 8.2 纹理压缩

使用压缩格式减少内存占用。

```javascript
import { KTX2Loader } from 'three/examples/jsm/loaders/KTX2Loader.js';

const ktx2Loader = new KTX2Loader();
ktx2Loader.setTranscoderPath('path/to/basis/');
ktx2Loader.detectSupport(renderer);

ktx2Loader.load('texture.ktx2', (texture) => {
  material.map = texture;
});
```

### 8.3 纹理池

重用纹理减少内存占用。

```javascript
class TexturePool {
  constructor() {
    this.textures = new Map();
    this.loader = new THREE.TextureLoader();
  }

  async get(url) {
    if (this.textures.has(url)) {
      return this.textures.get(url);
    }

    const texture = await new Promise((resolve, reject) => {
      this.loader.load(url, resolve, undefined, reject);
    });

    this.textures.set(url, texture);
    return texture;
  }

  dispose(url) {
    const texture = this.textures.get(url);
    if (texture) {
      texture.dispose();
      this.textures.delete(url);
    }
  }

  disposeAll() {
    this.textures.forEach(t => t.dispose());
    this.textures.clear();
  }
}

const texturePool = new TexturePool();
const texture = await texturePool.get('texture.jpg');
```

### 8.4 纹理内存管理

正确释放纹理资源。

```javascript
// 释放单个纹理
texture.dispose();

// 释放材质的所有纹理
function disposeMaterialTextures(material) {
  const maps = [
    'map', 'normalMap', 'roughnessMap', 'metalnessMap',
    'aoMap', 'emissiveMap', 'displacementMap', 'alphaMap',
    'envMap', 'lightMap', 'bumpMap', 'specularMap'
  ];

  maps.forEach(mapName => {
    if (material[mapName]) {
      material[mapName].dispose();
    }
  });
}

// 检查纹理内存使用
console.log(renderer.info.memory.textures);
```

---

## 常见问题与故障排除

### Q1: 纹理显示为黑色或白色

**原因**: 纹理加载失败或路径错误

**解决方案**:
```javascript
// 添加错误处理
loader.load('texture.jpg',
  (texture) => { /* 成功 */ },
  undefined,
  (error) => {
    console.error('纹理加载失败:', error);
    // 使用备用纹理
    material.map = createFallbackTexture();
  }
);
```

### Q2: 纹理颜色不正确

**原因**: 颜色空间设置错误

**解决方案**:
```javascript
// 颜色贴图使用sRGB
colorTexture.colorSpace = THREE.SRGBColorSpace;

// 数据纹理不设置颜色空间
normalTexture.colorSpace = THREE.NoColorSpace;
```

### Q3: 纹理模糊或像素化

**原因**: 过滤模式或尺寸不合适

**解决方案**:
```javascript
// 提高纹理质量
texture.minFilter = THREE.LinearMipmapLinearFilter;
texture.magFilter = THREE.LinearFilter;
texture.anisotropy = renderer.capabilities.getMaxAnisotropy();
```

### Q4: 纹理重复显示不正确

**原因**: 包裹模式设置错误

**解决方案**:
```javascript
// 设置重复包裹
texture.wrapS = THREE.RepeatWrapping;
texture.wrapT = THREE.RepeatWrapping;
texture.repeat.set(2, 2);
```

### Q5: 纹理占用内存过大

**原因**: 纹理尺寸过大或未压缩

**解决方案**:
```javascript
// 使用压缩纹理
import { KTX2Loader } from 'three/examples/jsm/loaders/KTX2Loader.js';

// 或降低纹理尺寸
texture.image = resizeTexture(texture.image, 1024);
```

---

## 最佳实践

### 1. 使用合适的纹理尺寸

```javascript
// 推荐：2的幂次尺寸
const sizes = [256, 512, 1024, 2048, 4096];

// 移动端使用较小尺寸
const size = isMobile ? 1024 : 2048;
```

### 2. 正确设置颜色空间

```javascript
// 颜色贴图
colorTexture.colorSpace = THREE.SRGBColorSpace;

// 数据纹理
dataTexture.colorSpace = THREE.NoColorSpace;
```

### 3. 启用各向异性过滤

```javascript
texture.anisotropy = renderer.capabilities.getMaxAnisotropy();
```

### 4. 使用纹理池管理资源

```javascript
const texturePool = new TexturePool();
const texture = await texturePool.get('texture.jpg');
```

### 5. 及时释放纹理资源

```javascript
// 切换场景时释放旧纹理
oldMaterial.map.dispose();
oldMaterial.normalMap.dispose();
```

### 6. 使用压缩纹理优化加载

```javascript
// KTX2格式
ktx2Loader.load('texture.ktx2', (texture) => {
  material.map = texture;
});
```

### 7. 批量加载纹理

```javascript
const textures = await Promise.all([
  loadTexture('color.jpg'),
  loadTexture('normal.jpg'),
  loadTexture('roughness.jpg')
]);
```

---

## 下一步学习

完成本教程后，建议继续学习：

1. **Three.js Animation** - 学习如何为纹理和材质添加动画效果
2. **Three.js Shaders** - 深入学习自定义着色器和纹理采样
3. **Three.js Postprocessing** - 学习使用纹理实现后处理效果

---

## 示例代码

本教程包含以下示例：

- [01-basic-textures.html](./01-basic-textures.html) - 基础纹理加载和应用
- [02-texture-settings.html](./02-texture-settings.html) - 纹理设置（重复、偏移、旋转）
- [03-cube-textures.html](./03-cube-textures.html) - 立方体纹理和环境贴图
- [04-hdr-textures.html](./04-hdr-textures.html) - HDR纹理和环境光照
- [05-procedural-textures.html](./05-procedural-textures.html) - 程序化纹理生成
- [06-comprehensive.html](./06-comprehensive.html) - 综合示例

运行示例：
```bash
# 使用本地服务器
python -m http.server 8000
# 或
npx serve

# 在浏览器中打开
http://localhost:8000/05-textures/01-basic-textures.html
```

---

## 参考资料

- [Three.js Texture Documentation](https://threejs.org/docs/#api/en/textures/Texture)
- [Three.js TextureLoader Documentation](https://threejs.org/docs/#api/en/loaders/TextureLoader)
- [MDN: Texture mapping](https://developer.mozilla.org/en-US/docs/Games/Techniques/3D_on_the_web/Texture_mapping)
