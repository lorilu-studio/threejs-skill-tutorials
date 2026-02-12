# Three.js 着色器教程 - 第八部分：Shaders详解

> 本教程深入讲解Three.js着色器的核心概念、GLSL语言和自定义效果。通过丰富的示例和实践，帮助你掌握ShaderMaterial、uniforms和高级着色器技术。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：着色器基础](#第一章着色器基础)
- [第二章：Uniforms和Varyings](#第二章uniforms和varyings)
- [第三章：常见着色器效果](#第三章常见着色器效果)
- [第四章：扩展内置材质](#第四章扩展内置材质)
- [第五章：GLSL内置函数](#第五章glsl内置函数)
- [第六章：性能优化](#第六章性能优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解着色器的基本概念和GLSL语言
- ✅ 熟练使用ShaderMaterial创建自定义材质
- ✅ 掌握uniforms和varyings的使用
- ✅ 实现常见着色器效果（Fresnel、Rim Lighting等）
- ✅ 扩展内置材质的着色器
- ✅ 优化着色器性能
- ✅ 调试着色器代码

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器、材质
- **数学基础**: 向量、矩阵、三角函数
- **编程基础**: JavaScript、变量、函数

### 推荐知识
- **图形学基础**: 顶点着色器、片段着色器
- **GLSL语言**: 着色器语言基础

---

## 第一章：着色器基础

### 1.1 着色器概述

**着色器**是在GPU上运行的程序，用于控制3D图形的渲染。

#### 着色器类型

```
着色器分类：
├── 顶点着色器
│   └── 处理每个顶点
│       ├── 位置变换
│       ├── 法线变换
│       └── 传递数据到片段着色器
└── 片段着色器
    └── 处理每个像素
        ├── 计算颜色
        ├── 纹理采样
        └── 应用光照
```

### 1.2 ShaderMaterial

Three.js提供ShaderMaterial用于自定义着色器。

#### 基本使用

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: {
    time: { value: 0 },
    color: { value: new THREE.Color(0xff0000) }
  },
  vertexShader: `
    void main() {
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform vec3 color;
    void main() {
      gl_FragColor = vec4(color, 1.0);
    }
  `
});

// 更新uniforms
material.uniforms.time.value = clock.getElapsedTime();
```

### 1.3 RawShaderMaterial

完全控制着色器，不提供内置uniforms。

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

---

## 第二章：Uniforms和Varyings

### 2.1 Uniforms

Uniforms是从JavaScript传递到着色器的全局变量。

#### Uniform类型

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: {
    // 数字
    floatValue: { value: 1.5 },
    intValue: { value: 1 },

    // 向量
    vec2Value: { value: new THREE.Vector2(1, 2) },
    vec3Value: { value: new THREE.Vector3(1, 2, 3) },
    vec4Value: { value: new THREE.Vector4(1, 2, 3, 4) },

    // 颜色（转换为vec3）
    colorValue: { value: new THREE.Color(0xff0000) },

    // 矩阵
    mat3Value: { value: new THREE.Matrix3() },
    mat4Value: { value: new THREE.Matrix4() },

    // 纹理
    textureValue: { value: texture },
    cubeTextureValue: { value: cubeTexture },

    // 数组
    floatArray: { value: [1.0, 2.0, 3.0] }
  }
});
```

#### GLSL声明

```glsl
// 在着色器中
uniform float floatValue;
uniform int intValue;
uniform vec2 vec2Value;
uniform vec3 vec3Value;
uniform vec3 colorValue;
uniform vec4 vec4Value;
uniform mat3 mat3Value;
uniform mat4 mat4Value;
uniform sampler2D textureValue;
uniform samplerCube cubeTextureValue;
uniform float floatArray[3];
```

### 2.2 Varyings

Varyings用于在顶点着色器和片段着色器之间传递数据。

#### 基本使用

```javascript
const material = new THREE.ShaderMaterial({
  vertexShader: `
    varying vec2 vUv;
    varying vec3 vNormal;
    varying vec3 vPosition;

    void main() {
      vUv = uv;
      vNormal = normalize(normalMatrix * normal);
      vPosition = (modelViewMatrix * vec4(position, 1.0)).xyz;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    varying vec2 vUv;
    varying vec3 vNormal;
    varying vec3 vPosition;

    void main() {
      gl_FragColor = vec4(vNormal * 0.5 + 0.5, 1.0);
    }
  `
});
```

---

## 第三章：常见着色器效果

### 3.1 Fresnel效果

创建边缘发光效果。

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: {
    fresnelColor: { value: new THREE.Color(0x667eea) },
    fresnelPower: { value: 3.0 }
  },
  vertexShader: `
    varying vec3 vNormal;
    varying vec3 vWorldPosition;

    void main() {
      vNormal = normalize(normalMatrix * normal);
      vWorldPosition = (modelMatrix * vec4(position, 1.0)).xyz;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform vec3 fresnelColor;
    uniform float fresnelPower;
    varying vec3 vNormal;
    varying vec3 vWorldPosition;

    void main() {
      vec3 viewDirection = normalize(cameraPosition - vWorldPosition);
      float fresnel = pow(1.0 - dot(viewDirection, vNormal), fresnelPower);
      gl_FragColor = vec4(fresnelColor * fresnel, fresnel);
    }
  `
});
```

### 3.2 Rim Lighting

边缘光照效果。

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: {
    rimColor: { value: new THREE.Color(0xff6600) },
    rimPower: { value: 4.0 }
  },
  vertexShader: `
    varying vec3 vNormal;
    varying vec3 vViewPosition;

    void main() {
      vNormal = normalize(normalMatrix * normal);
      vec4 mvPosition = modelViewMatrix * vec4(position, 1.0);
      vViewPosition = mvPosition.xyz;
      gl_Position = projectionMatrix * mvPosition;
    }
  `,
  fragmentShader: `
    uniform vec3 rimColor;
    uniform float rimPower;
    varying vec3 vNormal;
    varying vec3 vViewPosition;

    void main() {
      vec3 viewDir = normalize(-vViewPosition);
      float rim = 1.0 - max(0.0, dot(viewDir, vNormal));
      rim = pow(rim, rimPower);

      vec3 baseColor = vec3(0.2, 0.2, 0.8);
      gl_FragColor = vec4(baseColor + rimColor * rim, 1.0);
    }
  `
});
```

### 3.3 顶点位移

通过顶点着色器改变几何体形状。

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: {
    time: { value: 0 },
    amplitude: { value: 0.3 },
    frequency: { value: 2.0 }
  },
  vertexShader: `
    uniform float time;
    uniform float amplitude;
    uniform float frequency;

    void main() {
      vec3 pos = position;
      pos.z += sin(pos.x * frequency + time) * amplitude;
      pos.z += sin(pos.y * frequency + time) * amplitude;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
    }
  `,
  fragmentShader: `
    void main() {
      gl_FragColor = vec4(0.5, 0.8, 1.0, 1.0);
    }
  `
});
```

### 3.4 噪声效果

使用噪声函数创建自然效果。

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: {
    time: { value: 0 },
    scale: { value: 5.0 }
  },
  vertexShader: `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform float time;
    uniform float scale;
    varying vec2 vUv;

    float random(vec2 st) {
      return fract(sin(dot(st.xy, vec2(12.9898, 78.233))) * 43758.5453);
    }

    float noise(vec2 st) {
      vec2 i = floor(st);
      vec2 f = fract(st);
      float a = random(i);
      float b = random(i + vec2(1.0, 0.0));
      float c = random(i + vec2(0.0, 1.0));
      float d = random(i + vec2(1.0, 1.0));
      vec2 u = f * f * (3.0 - 2.0 * f);
      return mix(a, b, u.x) + (c - a) * u.y * (1.0 - u.x) + (d - b) * u.x * u.y;
    }

    void main() {
      float n = noise(vUv * scale + time);
      gl_FragColor = vec4(vec3(n), 1.0);
    }
  `
});
```

---

## 第四章：扩展内置材质

### 4.1 onBeforeCompile

修改现有材质的着色器。

```javascript
const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });

material.onBeforeCompile = (shader) => {
  // 添加自定义uniform
  shader.uniforms.time = { value: 0 };

  // 保存引用以便更新
  material.userData.shader = shader;

  // 修改顶点着色器
  shader.vertexShader = shader.vertexShader.replace(
    "#include <begin_vertex>",
    `
    #include <begin_vertex>
    transformed.y += sin(position.x * 10.0 + time) * 0.1;
    `
  );

  // 添加uniform声明
  shader.vertexShader = "uniform float time;\n" + shader.vertexShader;
};

// 在动画循环中更新
if (material.userData.shader) {
  material.userData.shader.uniforms.time.value = clock.getElapsedTime();
}
```

### 4.2 常用注入点

```javascript
// 顶点着色器
"#include <begin_vertex>"      // 位置计算后
"#include <project_vertex>"   // gl_Position设置后
"#include <beginnormal_vertex>" // 法线计算开始

// 片段着色器
"#include <color_fragment>"   // 漫反射颜色后
"#include <output_fragment>"  // 最终输出
"#include <fog_fragment>"     // 雾效应用后
```

---

## 第五章：GLSL内置函数

### 5.1 数学函数

```glsl
// 基本函数
abs(x)           // 绝对值
sign(x)          // 符号
floor(x)         // 向下取整
ceil(x)          // 向上取整
fract(x)         // 小数部分
mod(x, y)        // 取模
min(x, y)        // 最小值
max(x, y)        // 最大值
clamp(x, min, max) // 限制范围
mix(a, b, t)     // 线性插值
step(edge, x)    // 阶跃函数
smoothstep(edge0, edge1, x) // 平滑阶跃

// 三角函数
sin(x), cos(x), tan(x)
asin(x), acos(x), atan(x)
radians(degrees), degrees(radians)

// 指数函数
pow(x, y)        // 幂
exp(x)           // e^x
log(x)           // 自然对数
sqrt(x)          // 平方根
inversesqrt(x)   // 1/sqrt(x)
```

### 5.2 向量函数

```glsl
length(v)        // 向量长度
distance(p0, p1) // 两点距离
dot(x, y)        // 点积
cross(x, y)      // 叉积
normalize(v)     // 归一化
reflect(I, N)    // 反射
refract(I, N, eta) // 折射
```

### 5.3 纹理函数

```glsl
// GLSL 1.0
texture2D(sampler, coord)
texture2D(sampler, coord, bias)
textureCube(sampler, coord)

// GLSL 3.0
texture(sampler, coord)
texture(sampler, coord, bias)
```

---

## 第六章：性能优化

### 6.1 优化技巧

```glsl
// 避免条件语句
// 不推荐
if (value > 0.5) {
  color = colorA;
} else {
  color = colorB;
}

// 推荐
color = mix(colorB, colorA, step(0.5, value));

// 预计算
// 在JavaScript中预计算常量
material.uniforms.constant.value = precalculatedValue;

// 使用纹理查找
// 复杂函数使用纹理作为查找表
```

### 6.2 减少uniforms

```javascript
// 将相关值组合成向量
material.uniforms.params.value = new THREE.Vector4(
  param1, param2, param3, param4
);

// 在着色器中
uniform vec4 params;
float param1 = params.x;
float param2 = params.y;
```

---

## 常见问题与故障排除

### Q1: 着色器编译错误

**原因**: 语法错误或类型不匹配

**解决方案**:
```javascript
// 检查着色器编译错误
material.onBeforeCompile = (shader) => {
  console.log("Vertex Shader:", shader.vertexShader);
  console.log("Fragment Shader:", shader.fragmentShader);
};

// 启用错误检查
renderer.debug.checkShaderErrors = true;
```

### Q2: Uniforms未更新

**原因**: Uniforms引用错误或未正确更新

**解决方案**:
```javascript
// 确保在动画循环中更新
function animate() {
  material.uniforms.time.value = clock.getElapsedTime();
  renderer.render(scene, camera);
}
```

---

## 最佳实践

### 1. 使用ShaderMaterial

```javascript
const material = new THREE.ShaderMaterial({
  uniforms: { /* ... */ },
  vertexShader: "/* ... */",
  fragmentShader: "/* ... */"
});
```

### 2. 避免条件语句

```javascript
color = mix(colorB, colorA, step(0.5, value));
```

### 3. 预计算常量

```javascript
material.uniforms.constant.value = precalculatedValue;
```

### 4. 使用纹理查找

```javascript
// 复杂函数使用纹理作为查找表
```

### 5. 调试着色器

```javascript
// 可视化调试
gl_FragColor = vec4(vUv, 0.0, 1.0);
```

---

## 下一步学习

完成本教程后，建议继续学习：

1. **Three.js Postprocessing** - 学习全屏着色器效果
2. **Three.js Materials** - 深入学习内置材质
3. **GLSL编程** - 学习高级着色器编程

---

## 示例代码

本教程包含以下示例：

- [01-basic-shader.html](./01-basic-shader.html) - 基本着色器
- [02-uniforms-varyings.html](./02-uniforms-varyings.html) - Uniforms和Varyings
- [03-fresnel-effect.html](./03-fresnel-effect.html) - Fresnel效果
- [04-vertex-displacement.html](./04-vertex-displacement.html) - 顶点位移
- [05-extending-materials.html](./05-extending-materials.html) - 扩展材质
- [06-comprehensive.html](./06-comprehensive.html) - 综合示例

运行示例：
```bash
python -m http.server 8000
http://localhost:8000/08-shaders/01-basic-shader.html
```

---

## 参考资料

- [Three.js ShaderMaterial Documentation](https://threejs.org/docs/#api/en/materials/ShaderMaterial)
- [GLSL Specification](https://www.khronos.org/opengl/wiki/OpenGL_Shading_Language)
- [The Book of Shaders](https://thebookofshaders.com/)
