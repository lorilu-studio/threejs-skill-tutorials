# Three.js 加载器教程 - 第七部分：Loaders详解

> 本教程深入讲解Three.js加载器（Loaders）的核心概念、API使用和高级技巧。通过丰富的示例和实践，帮助你掌握3D资源加载、进度管理和异步模式。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：加载器基础](#第一章加载器基础)
- [第二章：纹理加载](#第二章纹理加载)
- [第三章：GLTF模型加载](#第三章gltf模型加载)
- [第四章：其他格式加载](#第四章其他格式加载)
- [第五章：异步加载](#第五章异步加载)
- [第六章：资源管理](#第六章资源管理)
- [第七章：错误处理](#第七章错误处理)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Three.js加载器的核心概念
- ✅ 熟练使用各种加载器加载不同资源
- ✅ 掌握LoadingManager管理加载进度
- ✅ 理解异步加载模式和Promise
- ✅ 掌握资源缓存和管理
- ✅ 处理加载错误和重试
- ✅ 优化加载性能

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器的基本概念
- **JavaScript (ES6+)**: Promise、async/await、模块
- **网络基础**: HTTP请求、跨域、CDN

### 推荐知识
- **3D格式**: GLTF、OBJ、FBX等格式特点
- **压缩技术**: DRACO、KTX2等压缩格式

---

## 第一章：加载器基础

### 1.1 加载器概述

**加载器**用于从各种来源加载3D资源到Three.js场景中。

#### 加载器类型

```
加载器分类：
├── 纹理加载器
│   ├── TextureLoader - 普通纹理
│   ├── CubeTextureLoader - 立方体纹理
│   ├── RGBELoader - HDR纹理
│   └── EXRLoader - EXR纹理
├── 模型加载器
│   ├── GLTFLoader - GLTF/GLB格式
│   ├── OBJLoader - OBJ格式
│   ├── FBXLoader - FBX格式
│   ├── STLLoader - STL格式
│   └── PLYLoader - PLY格式
└── 其他加载器
    ├── FontLoader - 字体文件
    ├── AudioLoader - 音频文件
    └── DataTextureLoader - 数据纹理
```

### 1.2 LoadingManager

管理多个加载器的进度和状态。

#### 基本使用

```javascript
const manager = new THREE.LoadingManager();

// 加载开始
manager.onStart = (url, loaded, total) => {
  console.log(`开始加载: ${url}`);
  console.log(`进度: ${loaded}/${total}`);
};

// 加载进度
manager.onProgress = (url, loaded, total) => {
  const progress = (loaded / total * 100).toFixed(1);
  console.log(`加载进度: ${progress}%`);
  updateProgressBar(progress);
};

// 全部加载完成
manager.onLoad = () => {
  console.log('所有资源加载完成');
  hideLoadingScreen();
  startApplication();
};

// 加载错误
manager.onError = (url) => {
  console.error(`加载错误: ${url}`);
  showError(url);
};

// 使用管理器创建加载器
const textureLoader = new THREE.TextureLoader(manager);
const gltfLoader = new THREE.GLTFLoader(manager);

// 加载资源
textureLoader.load('texture1.jpg');
textureLoader.load('texture2.jpg');
gltfLoader.load('model.glb');
```

#### URL修改器

```javascript
// 修改所有资源的URL
manager.setURLModifier((url) => {
  // 添加CDN前缀
  return `https://cdn.example.com/${url}`;
});

// 或根据类型修改
manager.setURLModifier((url) => {
  if (url.endsWith('.glb')) {
    return `https://cdn.example.com/models/${url}`;
  } else if (url.endsWith('.jpg')) {
    return `https://cdn.example.com/textures/${url}`;
  }
  return url;
});
```

---

## 第二章：纹理加载

### 2.1 TextureLoader

加载普通纹理图像。

#### 基本加载

```javascript
const loader = new THREE.TextureLoader();

// 回调方式
loader.load(
  'texture.jpg',
  (texture) => {
    // 加载成功
    texture.colorSpace = THREE.SRGBColorSpace;
    material.map = texture;
    material.needsUpdate = true;
  },
  undefined, // onProgress - 图片加载不支持
  (error) => {
    // 加载错误
    console.error('纹理加载失败:', error);
  }
);

// 同步方式（返回纹理，内部异步）
const texture = loader.load('texture.jpg');
material.map = texture;
```

#### Promise封装

```javascript
function loadTexture(url) {
  return new Promise((resolve, reject) => {
    new THREE.TextureLoader().load(url, resolve, undefined, reject);
  });
}

// 使用
async function init() {
  try {
    const texture = await loadTexture('texture.jpg');
    texture.colorSpace = THREE.SRGBColorSpace;
    material.map = texture;
  } catch (error) {
    console.error('加载失败:', error);
  }
}
```

### 2.2 CubeTextureLoader

加载立方体纹理用于环境贴图。

```javascript
const loader = new THREE.CubeTextureLoader();

// 加载6个面
const cubeTexture = loader.load([
  'px.jpg', // +X
  'nx.jpg', // -X
  'py.jpg', // +Y
  'ny.jpg', // -Y
  'pz.jpg', // +Z
  'nz.jpg'  // -Z
]);

// 作为背景
scene.background = cubeTexture;

// 作为环境贴图
scene.environment = cubeTexture;
material.envMap = cubeTexture;
```

### 2.3 HDR纹理加载

加载高动态范围纹理。

#### RGBELoader

```javascript
import { RGBELoader } from 'three/addons/loaders/RGBELoader.js';

const loader = new RGBELoader();
loader.load('environment.hdr', (texture) => {
  texture.mapping = THREE.EquirectangularReflectionMapping;
  scene.environment = texture;
  scene.background = texture;
});
```

#### PMREMGenerator

生成预过滤环境贴图。

```javascript
import { RGBELoader } from 'three/addons/loaders/RGBELoader.js';

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

---

## 第三章：GLTF模型加载

### 3.1 GLTFLoader基础

GLTF是Web 3D的标准格式。

#### 基本加载

```javascript
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

const loader = new GLTFLoader();

loader.load('model.glb', (gltf) => {
  // 添加场景
  const model = gltf.scene;
  scene.add(model);

  // 访问动画
  const animations = gltf.animations;
  console.log('动画数量:', animations.length);

  // 访问相机
  const cameras = gltf.cameras;

  // 访问资源信息
  console.log(gltf.asset);
});
```

### 3.2 处理GLTF内容

#### 遍历和修改

```javascript
loader.load('model.glb', (gltf) => {
  const model = gltf.scene;

  // 启用阴影
  model.traverse((child) => {
    if (child.isMesh) {
      child.castShadow = true;
      child.receiveShadow = true;
    }
  });

  // 查找特定对象
  const head = model.getObjectByName('Head');
  if (head) {
    head.material = new THREE.MeshStandardMaterial({ color: 0xff0000 });
  }

  // 调整材质
  model.traverse((child) => {
    if (child.isMesh && child.material) {
      child.material.envMapIntensity = 0.5;
      child.material.roughness = 0.7;
    }
  });

  scene.add(model);
});
```

#### 居中和缩放

```javascript
loader.load('model.glb', (gltf) => {
  const model = gltf.scene;

  // 计算边界框
  const box = new THREE.Box3().setFromObject(model);
  const center = box.getCenter(new THREE.Vector3());
  const size = box.getSize(new THREE.Vector3());

  // 居中
  model.position.sub(center);

  // 缩放到合适大小
  const maxDim = Math.max(size.x, size.y, size.z);
  model.scale.setScalar(1 / maxDim);

  scene.add(model);
});
```

### 3.3 DRACO压缩

使用DRACO压缩减小模型大小。

```javascript
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { DRACOLoader } from 'three/addons/loaders/DRACOLoader.js';

const dracoLoader = new DRACOLoader();
dracoLoader.setDecoderPath('https://www.gstatic.com/draco/versioned/decoders/1.5.6/');
dracoLoader.preload();

const gltfLoader = new GLTFLoader();
gltfLoader.setDRACOLoader(dracoLoader);

gltfLoader.load('compressed-model.glb', (gltf) => {
  scene.add(gltf.scene);
});
```

### 3.4 KTX2纹理压缩

使用KTX2格式压缩纹理。

```javascript
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { KTX2Loader } from 'three/addons/loaders/KTX2Loader.js';

const ktx2Loader = new KTX2Loader();
ktx2Loader.setTranscoderPath('https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/libs/basis/');
ktx2Loader.detectSupport(renderer);

const gltfLoader = new GLTFLoader();
gltfLoader.setKTX2Loader(ktx2Loader);

gltfLoader.load('model-with-ktx2.glb', (gltf) => {
  scene.add(gltf.scene);
});
```

---

## 第四章：其他格式加载

### 4.1 OBJ + MTL

加载OBJ模型和MTL材质。

```javascript
import { OBJLoader } from 'three/addons/loaders/OBJLoader.js';
import { MTLLoader } from 'three/addons/loaders/MTLLoader.js';

const mtlLoader = new MTLLoader();
mtlLoader.load('model.mtl', (materials) => {
  materials.preload();

  const objLoader = new OBJLoader();
  objLoader.setMaterials(materials);
  objLoader.load('model.obj', (object) => {
    scene.add(object);
  });
});
```

### 4.2 FBX

加载FBX模型。

```javascript
import { FBXLoader } from 'three/addons/loaders/FBXLoader.js';

const loader = new FBXLoader();
loader.load('model.fbx', (object) => {
  // FBX通常需要缩放
  object.scale.setScalar(0.01);

  // 处理动画
  const mixer = new THREE.AnimationMixer(object);
  object.animations.forEach((clip) => {
    mixer.clipAction(clip).play();
  });

  scene.add(object);
});
```

### 4.3 STL

加载STL模型。

```javascript
import { STLLoader } from 'three/addons/loaders/STLLoader.js';

const loader = new STLLoader();
loader.load('model.stl', (geometry) => {
  const material = new THREE.MeshStandardMaterial({
    color: 0x888888,
    roughness: 0.5,
    metalness: 0.5
  });
  const mesh = new THREE.Mesh(geometry, material);
  scene.add(mesh);
});
```

---

## 第五章：异步加载

### 5.1 Promise加载

将加载器封装为Promise。

```javascript
function loadGLTF(url) {
  return new Promise((resolve, reject) => {
    new THREE.GLTFLoader().load(url, resolve, undefined, reject);
  });
}

function loadTexture(url) {
  return new Promise((resolve, reject) => {
    new THREE.TextureLoader().load(url, resolve, undefined, reject);
  });
}

function loadRGBE(url) {
  return new Promise((resolve, reject) => {
    new THREE.RGBELoader().load(url, resolve, undefined, reject);
  });
}
```

### 5.2 批量加载

使用Promise.all并行加载多个资源。

```javascript
async function loadAssets() {
  try {
    const [modelGltf, envTexture, colorTexture] = await Promise.all([
      loadGLTF('model.glb'),
      loadRGBE('environment.hdr'),
      loadTexture('color.jpg')
    ]);

    // 处理模型
    const model = modelGltf.scene;
    scene.add(model);

    // 处理环境
    envTexture.mapping = THREE.EquirectangularReflectionMapping;
    scene.environment = envTexture;

    // 处理纹理
    colorTexture.colorSpace = THREE.SRGBColorSpace;
    material.map = colorTexture;

    console.log('所有资源加载完成');
  } catch (error) {
    console.error('加载失败:', error);
  }
}
```

### 5.3 顺序加载

按顺序加载资源。

```javascript
async function loadSequentially() {
  try {
    // 先加载环境
    const envTexture = await loadRGBE('environment.hdr');
    envTexture.mapping = THREE.EquirectangularReflectionMapping;
    scene.environment = envTexture;

    // 再加载模型
    const modelGltf = await loadGLTF('model.glb');
    scene.add(modelGltf.scene);

    // 最后加载纹理
    const texture = await loadTexture('texture.jpg');
    texture.colorSpace = THREE.SRGBColorSpace;
    material.map = texture;

  } catch (error) {
    console.error('加载失败:', error);
  }
}
```

---

## 第六章：资源管理

### 6.1 内置缓存

Three.js内置缓存系统。

```javascript
// 启用缓存
THREE.Cache.enabled = true;

// 清除缓存
THREE.Cache.clear();

// 手动管理
THREE.Cache.add('key', data);
const data = THREE.Cache.get('key');
THREE.Cache.remove('key');
```

### 6.2 自定义资源管理器

创建资源管理器类。

```javascript
class AssetManager {
  constructor() {
    this.textures = new Map();
    this.models = new Map();
    this.gltfLoader = new THREE.GLTFLoader();
    this.textureLoader = new THREE.TextureLoader();
  }

  async loadTexture(key, url) {
    if (this.textures.has(key)) {
      return this.textures.get(key);
    }

    const texture = await new Promise((resolve, reject) => {
      this.textureLoader.load(url, resolve, undefined, reject);
    });

    this.textures.set(key, texture);
    return texture;
  }

  async loadModel(key, url) {
    if (this.models.has(key)) {
      return this.models.get(key).clone();
    }

    const gltf = await new Promise((resolve, reject) => {
      this.gltfLoader.load(url, resolve, undefined, reject);
    });

    this.models.set(key, gltf.scene);
    return gltf.scene.clone();
  }

  dispose() {
    this.textures.forEach((t) => t.dispose());
    this.textures.clear();
    this.models.clear();
  }
}

// 使用
const assets = new AssetManager();
const texture = await assets.loadTexture('brick', 'brick.jpg');
const model = await assets.loadModel('tree', 'tree.glb');
```

---

## 第七章：错误处理

### 7.1 优雅降级

提供备用资源。

```javascript
async function loadWithFallback(primaryUrl, fallbackUrl) {
  try {
    return await loadModel(primaryUrl);
  } catch (error) {
    console.warn(`主资源加载失败，使用备用资源: ${error}`);
    return await loadModel(fallbackUrl);
  }
}

// 使用
const model = await loadWithFallback('model-high.glb', 'model-low.glb');
```

### 7.2 重试机制

自动重试失败的加载。

```javascript
async function loadWithRetry(url, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await loadModel(url);
    } catch (error) {
      if (i === maxRetries - 1) {
        throw error;
      }
      console.warn(`加载失败，重试 ${i + 1}/${maxRetries}`);
      await new Promise((r) => setTimeout(r, 1000 * (i + 1)));
    }
  }
}
```

### 7.3 超时处理

设置加载超时。

```javascript
async function loadWithTimeout(url, timeout = 30000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);

  try {
    const response = await fetch(url, { signal: controller.signal });
    clearTimeout(timeoutId);
    return response;
  } catch (error) {
    if (error.name === 'AbortError') {
      throw new Error('加载超时');
    }
    throw error;
  }
}
```

---

## 常见问题与故障排除

### Q1: 跨域错误

**原因**: 资源在不同域名下

**解决方案**:
```javascript
// 使用CORS代理
manager.setURLModifier((url) => {
  return `https://cors-anywhere.herokuapp.com/${url}`;
});

// 或配置服务器CORS头
// Access-Control-Allow-Origin: *
```

### Q2: 加载失败

**原因**: 路径错误或资源不存在

**解决方案**:
```javascript
// 检查路径
loader.setPath('assets/models/');

// 使用绝对路径
loader.load('/assets/models/model.glb');

// 添加错误处理
loader.load('model.glb',
  (gltf) => { /* 成功 */ },
  undefined,
  (error) => {
    console.error('加载失败:', error);
    // 使用备用资源
  }
);
```

### Q3: 纹理显示错误

**原因**: 颜色空间或翻转设置错误

**解决方案**:
```javascript
texture.colorSpace = THREE.SRGBColorSpace;
texture.flipY = true;
texture.needsUpdate = true;
```

---

## 最佳实践

### 1. 使用LoadingManager

```javascript
const manager = new THREE.LoadingManager();
manager.onLoad = () => startApplication();
```

### 2. 压缩资源

```javascript
// 使用DRACO压缩模型
gltfLoader.setDRACOLoader(dracoLoader);

// 使用KTX2压缩纹理
gltfLoader.setKTX2Loader(ktx2Loader);
```

### 3. 启用缓存

```javascript
THREE.Cache.enabled = true;
```

### 4. 异步加载

```javascript
const [model, texture] = await Promise.all([
  loadModel('model.glb'),
  loadTexture('texture.jpg')
]);
```

### 5. 错误处理

```javascript
try {
  const model = await loadModel('model.glb');
} catch (error) {
  console.error('加载失败:', error);
  // 使用备用资源
}
```

### 6. 进度显示

```javascript
manager.onProgress = (url, loaded, total) => {
  const progress = (loaded / total * 100).toFixed(1);
  updateProgressBar(progress);
};
```

---

## 下一步学习

完成本教程后，建议继续学习：

1. **Three.js Animation** - 学习如何播放加载的动画
2. **Three.js Materials** - 深入学习材质和纹理
3. **Three.js Shaders** - 学习自定义着色器

---

## 示例代码

本教程包含以下示例：

- [01-texture-loading.html](./01-texture-loading.html) - 纹理加载
- [02-gltf-loading.html](./02-gltf-loading.html) - GLTF模型加载
- [03-multiple-formats.html](./03-multiple-formats.html) - 多格式加载
- [04-async-loading.html](./04-async-loading.html) - 异步加载
- [05-asset-manager.html](./05-asset-manager.html) - 资源管理
- [06-comprehensive.html](./06-comprehensive.html) - 综合示例

运行示例：
```bash
# 使用本地服务器
python -m http.server 8000
# 或
npx serve

# 在浏览器中打开
http://localhost:8000/07-loaders/01-texture-loading.html
```

---

## 参考资料

- [Three.js LoadingManager Documentation](https://threejs.org/docs/#api/en/loaders/LoadingManager)
- [Three.js GLTFLoader Documentation](https://threejs.org/docs/#examples/en/loaders/GLTFLoader)
- [GLTF Format Specification](https://registry.khronos.org/glTF/)
