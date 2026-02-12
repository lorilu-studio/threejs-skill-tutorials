# Three.js 后处理教程 - 第九部分：Postprocessing详解

> 本教程深入讲解Three.js后处理的核心概念、EffectComposer和常见效果。通过丰富的示例和实践，帮助你掌握Bloom、DOF、抗锯齿等后处理技术。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：后处理基础](#第一章后处理基础)
- [第二章：常见后处理效果](#第二章常见后处理效果)
- [第三章：自定义着色器通道](#第三章自定义着色器通道)
- [第四章：多通道组合](#第四章多通道组合)
- [第五章：性能优化](#第五章性能优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解后处理的基本概念和工作原理
- ✅ 熟练使用EffectComposer管理后处理通道
- ✅ 掌握常见后处理效果（Bloom、DOF、抗锯齿等）
- ✅ 创建自定义着色器通道
- ✅ 组合多个后处理效果
- ✅ 优化后处理性能
- ✅ 处理窗口大小调整

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器
- **着色器基础**: GLSL语言、ShaderMaterial
- **渲染管线**: 理解渲染流程

### 推荐知识
- **图像处理**: 模糊、锐化、颜色校正
- **性能优化**: GPU性能、渲染优化

---

## 第一章：后处理基础

### 1.1 后处理概述

**后处理**是在场景渲染完成后，对最终图像进行处理的流程。

#### 后处理流程

```
渲染流程：
场景 → 相机 → 渲染器 → 渲染目标 → 后处理通道 → 屏幕
                                    ↓
                            多个后处理效果
                            (Bloom, DOF, FXAA等)
```

### 1.2 EffectComposer

EffectComposer用于管理多个后处理通道。

#### 基本设置

```javascript
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';

const composer = new EffectComposer(renderer);

// 添加渲染通道
const renderPass = new RenderPass(scene, camera);
composer.addPass(renderPass);

// 添加更多通道
composer.addPass(effectPass);

// 在动画循环中使用
function animate() {
  requestAnimationFrame(animate);
  composer.render(); // 使用composer而不是renderer
}
```

### 1.3 RenderPass

RenderPass是第一个通道，用于渲染场景。

```javascript
const renderPass = new RenderPass(scene, camera);
renderPass.clear = true; // 清除颜色缓冲区
renderPass.clearDepth = true; // 清除深度缓冲区
composer.addPass(renderPass);
```

---

## 第二章：常见后处理效果

### 2.1 Bloom（发光效果）

创建发光效果。

#### UnrealBloomPass

```javascript
import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';

const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  1.5, // strength - 发光强度
  0.4, // radius - 发光扩散范围
  0.85 // threshold - 发光阈值
);

composer.addPass(bloomPass);

// 运行时调整
bloomPass.strength = 2.0;
bloomPass.threshold = 0.5;
bloomPass.radius = 0.8;
```

#### 选择性Bloom

只对特定对象应用Bloom。

```javascript
import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';

const BLOOM_LAYER = 1;
const bloomLayer = new THREE.Layers();
bloomLayer.set(BLOOM_LAYER);

// 标记发光对象
glowingMesh.layers.enable(BLOOM_LAYER);

// 创建暗色材质
const darkMaterial = new THREE.MeshBasicMaterial({ color: 0x000000 });
const materials = {};

function darkenNonBloomed(obj) {
  if (obj.isMesh && !bloomLayer.test(obj.layers)) {
    materials[obj.uuid] = obj.material;
    obj.material = darkMaterial;
  }
}

function restoreMaterial(obj) {
  if (materials[obj.uuid]) {
    obj.material = materials[obj.uuid];
    delete materials[obj.uuid];
  }
}

// 自定义渲染循环
function render() {
  scene.traverse(darkenNonBloomed);
  composer.render();
  scene.traverse(restoreMaterial);
  renderer.render(scene, camera);
}
```

### 2.2 抗锯齿

减少锯齿效果。

#### FXAA

```javascript
import { ShaderPass } from 'three/addons/postprocessing/ShaderPass.js';
import { FXAAShader } from 'three/addons/shaders/FXAAShader.js';

const fxaaPass = new ShaderPass(FXAAShader);
fxaaPass.material.uniforms['resolution'].value.set(
  1 / window.innerWidth,
  1 / window.innerHeight
);

composer.addPass(fxaaPass);

// 窗口调整时更新
function onResize() {
  fxaaPass.material.uniforms['resolution'].value.set(
    1 / window.innerWidth,
    1 / window.innerHeight
  );
}
```

#### SMAA

更好的抗锯齿效果。

```javascript
import { SMAAPass } from 'three/addons/postprocessing/SMAAPass.js';

const smaaPass = new SMAAPass(
  window.innerWidth * renderer.getPixelRatio(),
  window.innerHeight * renderer.getPixelRatio()
);

composer.addPass(smaaPass);
```

### 2.3 景深（DOF）

创建焦点模糊效果。

```javascript
import { BokehPass } from 'three/addons/postprocessing/BokehPass.js';

const bokehPass = new BokehPass(scene, camera, {
  focus: 10.0, // 焦距
  aperture: 0.025, // 光圈（越小景深越大）
  maxblur: 0.01 // 最大模糊量
});

composer.addPass(bokehPass);

// 动态更新焦点
bokehPass.uniforms['focus'].value = distanceToTarget;
```

### 2.4 SSAO（环境光遮蔽）

增强场景深度感。

```javascript
import { SSAOPass } from 'three/addons/postprocessing/SSAOPass.js';

const ssaoPass = new SSAOPass(
  scene,
  camera,
  window.innerWidth,
  window.innerHeight
);
ssaoPass.kernelRadius = 16;
ssaoPass.minDistance = 0.005;
ssaoPass.maxDistance = 0.1;

composer.addPass(ssaoPass);

// 输出模式
ssaoPass.output = SSAOPass.OUTPUT.Default;
// SSAOPass.OUTPUT.Default - 最终合成输出
// SSAOPass.OUTPUT.SSAO - 仅AO
// SSAOPass.OUTPUT.Blur - 模糊AO
```

### 2.5 胶片颗粒

添加胶片颗粒效果。

```javascript
import { FilmPass } from 'three/addons/postprocessing/FilmPass.js';

const filmPass = new FilmPass(
  0.35, // 噪声强度
  0.5, // 扫描线强度
  648, // 扫描线数量
  false // 灰度
);

composer.addPass(filmPass);
```

### 2.6 晕影

创建边缘变暗效果。

```javascript
import { ShaderPass } from 'three/addons/postprocessing/ShaderPass.js';
import { VignetteShader } from 'three/addons/shaders/VignetteShader.js';

const vignettePass = new ShaderPass(VignetteShader);
vignettePass.uniforms['offset'].value = 1.0; // 晕影大小
vignettePass.uniforms['darkness'].value = 1.0; // 晕影强度

composer.addPass(vignettePass);
```

### 2.7 颜色校正

调整图像颜色。

```javascript
import { ShaderPass } from 'three/addons/postprocessing/ShaderPass.js';
import { ColorCorrectionShader } from 'three/addons/shaders/ColorCorrectionShader.js';

const colorPass = new ShaderPass(ColorCorrectionShader);
colorPass.uniforms['powRGB'].value = new THREE.Vector3(1.2, 1.2, 1.2);
colorPass.uniforms['mulRGB'].value = new THREE.Vector3(1.0, 1.0, 1.0);

composer.addPass(colorPass);
```

### 2.8 故障效果

创建故障艺术效果。

```javascript
import { GlitchPass } from 'three/addons/postprocessing/GlitchPass.js';

const glitchPass = new GlitchPass();
glitchPass.goWild = false; // 持续故障

composer.addPass(glitchPass);

// 触发故障
glitchPass.triggerGlitch();
```

---

## 第三章：自定义着色器通道

### 3.1 基本自定义通道

创建自定义后处理效果。

```javascript
import { ShaderPass } from 'three/addons/postprocessing/ShaderPass.js';

const CustomShader = {
  uniforms: {
    tDiffuse: { value: null }, // 必需：输入纹理
    time: { value: 0 },
    intensity: { value: 1.0 }
  },
  vertexShader: `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform sampler2D tDiffuse;
    uniform float time;
    uniform float intensity;
    varying vec2 vUv;

    void main() {
      vec2 uv = vUv;
      uv.x += sin(uv.y * 10.0 + time) * 0.01 * intensity;
      vec4 color = texture2D(tDiffuse, uv);
      gl_FragColor = color;
    }
  `
};

const customPass = new ShaderPass(CustomShader);
composer.addPass(customPass);

// 在动画循环中更新
customPass.uniforms.time.value = clock.getElapsedTime();
```

### 3.2 反转颜色

```javascript
const InvertShader = {
  uniforms: {
    tDiffuse: { value: null }
  },
  vertexShader: `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform sampler2D tDiffuse;
    varying vec2 vUv;
    void main() {
      vec4 color = texture2D(tDiffuse, vUv);
      gl_FragColor = vec4(1.0 - color.rgb, color.a);
    }
  `
};
```

### 3.3 色差效果

```javascript
const ChromaticAberrationShader = {
  uniforms: {
    tDiffuse: { value: null },
    amount: { value: 0.005 }
  },
  vertexShader: `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform sampler2D tDiffuse;
    uniform float amount;
    varying vec2 vUv;

    void main() {
      vec2 dir = vUv - 0.5;
      float dist = length(dir);
      float r = texture2D(tDiffuse, vUv - dir * amount * dist).r;
      float g = texture2D(tDiffuse, vUv).g;
      float b = texture2D(tDiffuse, vUv + dir * amount * dist).b;
      gl_FragColor = vec4(r, g, b, 1.0);
    }
  `
};
```

---

## 第四章：多通道组合

### 4.1 组合多个效果

```javascript
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';
import { ShaderPass } from 'three/addons/postprocessing/ShaderPass.js';
import { VignetteShader } from 'three/addons/shaders/VignetteShader.js';
import { GammaCorrectionShader } from 'three/addons/shaders/GammaCorrectionShader.js';

const composer = new EffectComposer(renderer);

// 1. 渲染场景
composer.addPass(new RenderPass(scene, camera));

// 2. Bloom
const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  0.5, 0.4, 0.85
);
composer.addPass(bloomPass);

// 3. 晕影
const vignettePass = new ShaderPass(VignetteShader);
vignettePass.uniforms['offset'].value = 0.95;
vignettePass.uniforms['darkness'].value = 1.0;
composer.addPass(vignettePass);

// 4. 伽马校正
composer.addPass(new ShaderPass(GammaCorrectionShader));
```

### 4.2 通道顺序

通道顺序很重要，通常遵循以下规则：

1. RenderPass（必须第一个）
2. 效果通道（Bloom、DOF等）
3. 颜色校正通道
4. 抗锯齿通道（通常最后）

---

## 第五章：性能优化

### 5.1 优化技巧

```javascript
// 限制通道数量
bloomPass.enabled = false;

// 降低分辨率
const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth / 2, window.innerHeight / 2),
  strength, radius, threshold
);

// 仅在高性能场景应用
const isMobile = /iPhone|iPad|Android/i.test(navigator.userAgent);
if (!isMobile) {
  composer.addPass(expensivePass);
}
```

### 5.2 窗口调整

```javascript
function onWindowResize() {
  const width = window.innerWidth;
  const height = window.innerHeight;
  const pixelRatio = renderer.getPixelRatio();

  camera.aspect = width / height;
  camera.updateProjectionMatrix();

  renderer.setSize(width, height);
  composer.setSize(width, height);

  // 更新通道特定分辨率
  if (fxaaPass) {
    fxaaPass.material.uniforms['resolution'].value.set(
      1 / (width * pixelRatio),
      1 / (height * pixelRatio)
    );
  }

  if (bloomPass) {
    bloomPass.resolution.set(width, height);
  }
}

window.addEventListener('resize', onWindowResize);
```

---

## 常见问题与故障排除

### Q1: 后处理效果不显示

**原因**: 通道顺序错误或未正确添加

**解决方案**:
```javascript
// 确保RenderPass是第一个
composer.addPass(new RenderPass(scene, camera));

// 确保最后一个通道渲染到屏幕
effectPass.renderToScreen = true;
```

### Q2: 性能问题

**原因**: 通道过多或分辨率过高

**解决方案**:
```javascript
// 减少通道数量
bloomPass.enabled = false;

// 降低分辨率
const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth / 2, window.innerHeight / 2),
  strength, radius, threshold
);
```

---

## 最佳实践

### 1. 使用EffectComposer

```javascript
const composer = new EffectComposer(renderer);
composer.addPass(new RenderPass(scene, camera));
composer.addPass(bloomPass);
```

### 2. 通道顺序

```javascript
// RenderPass → 效果通道 → 颜色校正 → 抗锯齿
```

### 3. 性能优化

```javascript
// 限制通道数量
bloomPass.enabled = false;

// 降低分辨率
const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth / 2, window.innerHeight / 2),
  strength, radius, threshold
);
```

### 4. 窗口调整

```javascript
function onWindowResize() {
  composer.setSize(window.innerWidth, window.innerHeight);
}
```

---

## 下一步学习

完成本教程后，建议继续学习：

1. **Three.js Interaction** - 学习交互和用户输入
2. **Three.js Shaders** - 深入学习着色器编程
3. **Three.js Materials** - 深入学习材质系统

---

## 示例代码

本教程包含以下示例：

- [01-basic-postprocessing.html](./01-basic-postprocessing.html) - 基本后处理
- [02-bloom-effect.html](./02-bloom-effect.html) - Bloom效果
- [03-dof-effect.html](./03-dof-effect.html) - 景深效果
- [04-custom-shader.html](./04-custom-shader.html) - 自定义着色器
- [05-multiple-effects.html](./05-multiple-effects.html) - 多效果组合
- [06-comprehensive.html](./06-comprehensive.html) - 综合示例

运行示例：
```bash
python -m http.server 8000
http://localhost:8000/09-postprocessing/01-basic-postprocessing.html
```

---

## 参考资料

- [Three.js Postprocessing Documentation](https://threejs.org/docs/#examples/en/postprocessing/EffectComposer)
- [EffectComposer Examples](https://threejs.org/examples/#webgl_postprocessing)
