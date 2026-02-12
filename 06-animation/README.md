# Three.js 动画教程 - 第六部分：Animation详解

> 本教程深入讲解Three.js动画（Animation）的核心概念、API使用和高级技巧。通过丰富的示例和实践，帮助你掌握3D动画的创建、控制和优化。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：动画基础概念](#第一章动画基础概念)
- [第二章：基础动画](#第二章基础动画)
- [第三章：关键帧动画](#第三章关键帧动画)
- [第四章：动画混合器](#第四章动画混合器)
- [第五章：骨骼动画](#第五章骨骼动画)
- [第六章：变形目标](#第六章变形目标)
- [第七章：程序化动画](#第七章程序化动画)
- [第八章：动画优化](#第八章动画优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Three.js动画系统的核心概念
- ✅ 熟练使用Clock和requestAnimationFrame创建基础动画
- ✅ 掌握关键帧动画的创建和控制
- ✅ 理解AnimationMixer和AnimationAction的使用
- ✅ 掌握骨骼动画的原理和操作
- ✅ 使用变形目标实现形状动画
- ✅ 创建程序化动画效果
- ✅ 掌握动画的性能优化技巧

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器的基本概念
- **JavaScript (ES6+)**: 异步编程、类、模块
- **数学基础**: 三角函数、向量、四元数

### 推荐知识
- **动画原理**: 关键帧、插值、缓动函数
- **物理基础**: 运动学、动力学

---

## 第一章：动画基础概念

### 1.1 什么是动画？

**动画**是通过快速连续显示一系列静态图像来产生运动错觉的技术。在3D中，动画通过随时间改变物体的属性来实现。

#### 动画的核心要素

```
时间轴（Timeline）
    ↓
关键帧（Keyframes）
    ↓
插值（Interpolation）
    ↓
渲染（Rendering）
```

#### Three.js动画系统架构

```
AnimationClip（动画剪辑）
    ↓ 包含关键帧数据
AnimationMixer（动画混合器）
    ↓ 管理动画播放
AnimationAction（动画动作）
    ↓ 控制单个动画
渲染循环（Render Loop）
    ↓ 更新动画状态
屏幕显示（Screen Display）
```

### 1.2 Three.js动画类型

Three.js支持多种动画类型：

| 动画类型 | 用途 | 复杂度 | 性能 |
|----------|------|--------|------|
| **程序化动画** | 简单运动、旋转 | 低 | 高 |
| **关键帧动画** | 复杂路径、变换 | 中 | 中 |
| **骨骼动画** | 角色动画、关节运动 | 高 | 中 |
| **变形目标** | 面部表情、形状变化 | 中 | 中 |
| **着色器动画** | 复杂视觉效果 | 高 | 高 |

### 1.3 动画系统组件

Three.js动画系统由三个核心组件组成：

#### AnimationClip

存储关键帧动画数据。

```javascript
const clip = new THREE.AnimationClip(
  'bounce',    // 动画名称
  2,           // 持续时间（秒）
  [track]      // 关键帧轨道数组
);
```

#### AnimationMixer

播放动画并管理混合。

```javascript
const mixer = new THREE.AnimationMixer(rootObject);
mixer.update(deltaTime); // 每帧更新
```

#### AnimationAction

控制单个动画的播放。

```javascript
const action = mixer.clipAction(clip);
action.play();
action.timeScale = 1.5; // 播放速度
```

---

## 第二章：基础动画

### 2.1 Clock（时钟）

Clock用于精确测量时间间隔。

#### 基本使用

```javascript
const clock = new THREE.Clock();

function animate() {
  const delta = clock.getDelta(); // 获取时间增量（秒）
  const elapsed = clock.getElapsedTime(); // 获取总时间（秒）

  mesh.rotation.y += delta; // 每秒旋转1弧度
  mesh.position.y = Math.sin(elapsed) * 0.5; // 上下浮动

  requestAnimationFrame(animate);
  renderer.render(scene, camera);
}
```

#### 时钟方法

```javascript
const clock = new THREE.Clock();

// 获取时间增量（自动重置）
const delta = clock.getDelta();

// 获取总时间（不重置）
const elapsed = clock.getElapsedTime();

// 获取旧时间增量（不重置）
const oldDelta = clock.getDelta();

// 手动控制时间
clock.start(); // 启动时钟
clock.stop(); // 停止时钟
clock.autoStart = false; // 禁用自动启动
```

### 2.2 requestAnimationFrame

浏览器提供的动画循环API。

#### 基本使用

```javascript
function animate() {
  // 更新动画
  mesh.rotation.y += 0.01;

  // 渲染场景
  renderer.render(scene, camera);

  // 请求下一帧
  requestAnimationFrame(animate);
}

// 启动动画循环
animate();
```

#### 性能优化

```javascript
let lastTime = 0;
const targetFPS = 60;
const frameInterval = 1000 / targetFPS;

function animate(currentTime) {
  requestAnimationFrame(animate);

  const deltaTime = currentTime - lastTime;

  // 限制帧率
  if (deltaTime >= frameInterval) {
    lastTime = currentTime - (deltaTime % frameInterval);

    // 更新动画
    const delta = deltaTime / 1000;
    mesh.rotation.y += delta;

    // 渲染场景
    renderer.render(scene, camera);
  }
}

animate(0);
```

### 2.3 程序化动画基础

使用数学函数创建动画。

#### 旋转动画

```javascript
function animate() {
  const delta = clock.getDelta();

  // 恒定旋转
  mesh.rotation.y += delta;

  // 加速旋转
  mesh.rotation.x += delta * delta;

  // 正弦旋转
  mesh.rotation.z = Math.sin(clock.getElapsedTime()) * Math.PI;
}
```

#### 位置动画

```javascript
function animate() {
  const t = clock.getElapsedTime();

  // 圆形运动
  mesh.position.x = Math.cos(t) * 2;
  mesh.position.z = Math.sin(t) * 2;

  // 椭圆运动
  mesh.position.x = Math.cos(t) * 3;
  mesh.position.z = Math.sin(t) * 1.5;

  // 螺旋运动
  mesh.position.x = Math.cos(t) * (1 + t * 0.1);
  mesh.position.z = Math.sin(t) * (1 + t * 0.1);
  mesh.position.y = t * 0.1;
}
```

#### 缩放动画

```javascript
function animate() {
  const t = clock.getElapsedTime();

  // 脉冲效果
  const scale = 1 + Math.sin(t * 2) * 0.2;
  mesh.scale.set(scale, scale, scale);

  // 呼吸效果
  mesh.scale.x = 1 + Math.sin(t) * 0.1;
  mesh.scale.y = 1 + Math.sin(t * 1.5) * 0.1;
  mesh.scale.z = 1 + Math.sin(t * 2) * 0.1;
}
```

---

## 第三章：关键帧动画

### 3.1 AnimationClip创建

创建包含关键帧数据的动画剪辑。

#### NumberKeyframeTrack

```javascript
// 创建关键帧轨道
const times = [0, 1, 2]; // 关键帧时间（秒）
const values = [0, 1, 0]; // 每个关键帧的值

const track = new THREE.NumberKeyframeTrack(
  '.position[y]',  // 属性路径
  times,           // 时间数组
  values           // 值数组
);

// 创建动画剪辑
const clip = new THREE.AnimationClip(
  'bounce',  // 动画名称
  2,         // 持续时间
  [track]    // 轨道数组
);
```

#### VectorKeyframeTrack

```javascript
// 位置动画
const times = [0, 1, 2];
const values = [
  0, 0, 0,   // t=0: (0, 0, 0)
  1, 2, 0,   // t=1: (1, 2, 0)
  0, 0, 0    // t=2: (0, 0, 0)
];

const positionTrack = new THREE.VectorKeyframeTrack(
  '.position',
  times,
  values
);

// 缩放动画
const scaleValues = [
  1, 1, 1,   // t=0
  1.5, 1.5, 1.5, // t=1
  1, 1, 1    // t=2
];

const scaleTrack = new THREE.VectorKeyframeTrack(
  '.scale',
  times,
  scaleValues
);
```

#### QuaternionKeyframeTrack

```javascript
// 旋转动画（使用四元数）
const times = [0, 1];
const q1 = new THREE.Quaternion().setFromEuler(new THREE.Euler(0, 0, 0));
const q2 = new THREE.Quaternion().setFromEuler(new THREE.Euler(0, Math.PI, 0));

const rotationTrack = new THREE.QuaternionKeyframeTrack(
  '.quaternion',
  times,
  [
    q1.x, q1.y, q1.z, q1.w,  // t=0
    q2.x, q2.y, q2.z, q2.w   // t=1
  ]
);
```

#### ColorKeyframeTrack

```javascript
// 颜色动画
const times = [0, 1, 2];
const values = [
  1, 0, 0,   // t=0: 红色
  0, 1, 0,   // t=1: 绿色
  0, 0, 1    // t=2: 蓝色
];

const colorTrack = new THREE.ColorKeyframeTrack(
  '.material.color',
  times,
  values
);
```

### 3.2 插值模式

控制关键帧之间的过渡方式。

#### 插值类型

```javascript
const track = new THREE.VectorKeyframeTrack('.position', times, values);

// 线性插值（默认）
track.setInterpolation(THREE.InterpolateLinear);

// 平滑插值（三次样条）
track.setInterpolation(THREE.InterpolateSmooth);

// 离散插值（阶梯函数）
track.setInterpolation(THREE.InterpolateDiscrete);
```

#### 插值效果对比

```
Linear:                Smooth:                Discrete:
    ●─────●                 ●─────●                 ●     ●
     \   /                   \   /                   |     |
      \ /                     \ /                    |     |
       ●                       ●                      ●     ●
```

### 3.3 完整关键帧动画示例

```javascript
// 创建多个轨道
const times = [0, 1, 2, 3];

// 位置轨道
const positionValues = [
  0, 0, 0,   // 起点
  2, 1, 0,   // 中间点1
  0, 2, 0,   // 中间点2
  0, 0, 0    // 终点
];
const positionTrack = new THREE.VectorKeyframeTrack(
  '.position',
  times,
  positionValues
);

// 旋转轨道
const rotationValues = [
  0, 0, 0, 1,     // 起始旋转
  0, 0.707, 0, 0.707, // 90度
  0, 1, 0, 0,     // 180度
  0, 0, 0, 1      // 360度
];
const rotationTrack = new THREE.QuaternionKeyframeTrack(
  '.quaternion',
  times,
  rotationValues
);

// 缩放轨道
const scaleValues = [
  1, 1, 1,   // 原始大小
  1.5, 1.5, 1.5, // 放大
  0.5, 0.5, 0.5, // 缩小
  1, 1, 1    // 恢复
];
const scaleTrack = new THREE.VectorKeyframeTrack(
  '.scale',
  times,
  scaleValues
);

// 创建动画剪辑
const clip = new THREE.AnimationClip(
  'complexAnimation',
  3,
  [positionTrack, rotationTrack, scaleTrack]
);

// 创建混合器和动作
const mixer = new THREE.AnimationMixer(mesh);
const action = mixer.clipAction(clip);
action.play();

// 动画循环
function animate() {
  const delta = clock.getDelta();
  mixer.update(delta);
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

---

## 第四章：动画混合器

### 4.1 AnimationMixer基础

管理动画的播放和混合。

#### 创建混合器

```javascript
// 为单个对象创建混合器
const mixer = new THREE.AnimationMixer(mesh);

// 为场景创建混合器（管理多个对象）
const mixer = new THREE.AnimationMixer(scene);
```

#### 更新混合器

```javascript
function animate() {
  const delta = clock.getDelta();
  mixer.update(delta); // 必须每帧调用
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

### 4.2 AnimationAction控制

控制单个动画的播放。

#### 基本控制

```javascript
const action = mixer.clipAction(clip);

// 播放
action.play();

// 停止
action.stop();

// 重置
action.reset();

// 暂停
action.paused = true;

// 恢复
action.paused = false;
```

#### 时间控制

```javascript
// 设置当前时间
action.time = 1.5; // 跳转到1.5秒

// 播放速度
action.timeScale = 1; // 正常速度
action.timeScale = 2; // 2倍速
action.timeScale = -1; // 倒放
action.timeScale = 0.5; // 0.5倍速

// 获取状态
console.log(action.isRunning()); // 是否正在运行
console.log(action.isScheduled()); // 是否已调度
```

#### 循环控制

```javascript
// 循环模式
action.loop = THREE.LoopRepeat; // 无限循环（默认）
action.loop = THREE.LoopOnce; // 播放一次
action.loop = THREE.LoopPingPong; // 来回播放

// 循环次数
action.repetitions = 3; // 循环3次
action.repetitions = Infinity; // 无限循环

// 完成时保持
action.clampWhenFinished = true; // 保持在最后一帧
```

#### 权重控制

```javascript
// 设置权重（用于混合）
action.weight = 0.5; // 50%影响
action.setEffectiveWeight(0.5); // 平滑过渡到50%

// 获取权重
console.log(action.getEffectiveWeight());
```

### 4.3 动画混合

混合多个动画创建复杂效果。

#### 基础混合

```javascript
// 创建两个动作
const action1 = mixer.clipAction(clip1);
const action2 = mixer.clipAction(clip2);

// 播放两个动作
action1.play();
action2.play();

// 设置权重
action1.weight = 0.7;
action2.weight = 0.3;
```

#### 淡入淡出

```javascript
// 淡入
action.reset().fadeIn(0.5).play();

// 淡出
action.fadeOut(0.5);

// 交叉淡入淡出
const action1 = mixer.clipAction(clip1);
const action2 = mixer.clipAction(clip2);

action1.play();

// 0.5秒后切换到action2
setTimeout(() => {
  action1.crossFadeTo(action2, 0.5, true);
  action2.play();
}, 5000);
```

#### 混合模式

```javascript
// 正常混合（默认）
action.blendMode = THREE.NormalAnimationBlendMode;

// 叠加混合（用于叠加动画）
action.blendMode = THREE.AdditiveAnimationBlendMode;
```

### 4.4 混合器事件

监听动画事件。

```javascript
// 动画完成
mixer.addEventListener('finished', (event) => {
  console.log('动画完成:', event.action.getClip().name);
});

// 动画循环
mixer.addEventListener('loop', (event) => {
  console.log('动画循环:', event.action.getClip().name);
});

// 动画开始
mixer.addEventListener('start', (event) => {
  console.log('动画开始:', event.action.getClip().name);
});
```

---

## 第五章：骨骼动画

### 5.1 骨骼系统基础

骨骼动画用于角色和关节运动。

#### 骨骼结构

```
根骨骼（Root）
    ↓
    ├── 髋部（Hip）
    │   ├── 左腿（LeftLeg）
    │   │   └── 左脚（LeftFoot）
    │   └── 右腿（RightLeg）
    │       └── 右脚（RightFoot）
    ├── 脊柱（Spine）
    │   ├── 胸部（Chest）
    │   │   ├── 左臂（LeftArm）
    │   │   │   └── 左手（LeftHand）
    │   │   └── 右臂（RightArm）
    │   │       └── 右手（RightHand）
    │   └── 头部（Head）
```

#### 访问骨骼

```javascript
// 获取蒙皮网格
const skinnedMesh = model.getObjectByProperty('type', 'SkinnedMesh');

// 获取骨骼
const skeleton = skinnedMesh.skeleton;

// 遍历所有骨骼
skeleton.bones.forEach((bone) => {
  console.log(bone.name, bone.position, bone.rotation);
});

// 查找特定骨骼
const headBone = skeleton.bones.find(b => b.name === 'Head');
if (headBone) {
  headBone.rotation.y = Math.PI / 4;
}
```

### 5.2 骨骼辅助工具

可视化骨骼结构。

```javascript
// 创建骨骼辅助器
const helper = new THREE.SkeletonHelper(model);
scene.add(helper);

// 更新辅助器
function animate() {
  helper.update();
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

### 5.3 程序化骨骼动画

手动控制骨骼运动。

```javascript
function animate() {
  const time = clock.getElapsedTime();

  // 旋转头部
  const headBone = skeleton.bones.find(b => b.name === 'Head');
  if (headBone) {
    headBone.rotation.y = Math.sin(time) * 0.3;
    headBone.rotation.x = Math.cos(time * 0.5) * 0.1;
  }

  // 摆动手臂
  const leftArmBone = skeleton.bones.find(b => b.name === 'LeftArm');
  if (leftArmBone) {
    leftArmBone.rotation.x = Math.sin(time * 2) * 0.5;
  }

  // 更新混合器
  mixer.update(clock.getDelta());
}
```

### 5.4 骨骼附件

将物体附加到骨骼上。

```javascript
// 创建武器
const weapon = new THREE.Mesh(
  new THREE.BoxGeometry(0.1, 0.5, 0.1),
  new THREE.MeshStandardMaterial({ color: 0x888888 })
);

// 附加到右手骨骼
const rightHandBone = skeleton.bones.find(b => b.name === 'RightHand');
if (rightHandBone) {
  rightHandBone.add(weapon);

  // 设置偏移
  weapon.position.set(0, -0.5, 0.2);
  weapon.rotation.set(0, Math.PI / 2, 0);
}
```

---

## 第六章：变形目标

### 6.1 变形目标基础

在网格的不同形状之间混合。

#### 创建变形目标

```javascript
// 创建基础网格
const geometry = new THREE.BoxGeometry(1, 1, 1, 10, 10, 10);

// 添加变形目标
const positionAttribute = geometry.attributes.position;
const vertexCount = positionAttribute.count;

// 变形目标1：膨胀
const morphTarget1 = new Float32Array(vertexCount * 3);
for (let i = 0; i < vertexCount; i++) {
  const x = positionAttribute.getX(i);
  const y = positionAttribute.getY(i);
  const z = positionAttribute.getZ(i);

  // 向外膨胀
  const normal = new THREE.Vector3(x, y, z).normalize();
  morphTarget1[i * 3] = normal.x * 0.2;
  morphTarget1[i * 3 + 1] = normal.y * 0.2;
  morphTarget1[i * 3 + 2] = normal.z * 0.2;
}

geometry.morphAttributes.position = [morphTarget1];
geometry.morphTargetsRelative = true;

// 创建网格
const mesh = new THREE.Mesh(geometry, material);
scene.add(mesh);
```

#### 访问变形目标

```javascript
// 获取变形目标影响
console.log(mesh.morphTargetInfluences); // [0, 0, ...]

// 获取变形目标字典
console.log(mesh.morphTargetDictionary); // { '膨胀': 0, ... }

// 设置影响
mesh.morphTargetInfluences[0] = 0.5; // 50%影响

// 通过名称设置
const index = mesh.morphTargetDictionary['膨胀'];
mesh.morphTargetInfluences[index] = 1;
```

### 6.2 变形目标动画

使用关键帧动画控制变形目标。

```javascript
// 创建变形目标轨道
const times = [0, 1, 2];
const values = [0, 1, 0]; // 从0到1再回到0

const morphTrack = new THREE.NumberKeyframeTrack(
  '.morphTargetInfluences[0]',
  times,
  values
);

// 创建动画剪辑
const clip = new THREE.AnimationClip(
  'morphAnimation',
  2,
  [morphTrack]
);

// 播放动画
const mixer = new THREE.AnimationMixer(mesh);
const action = mixer.clipAction(clip);
action.play();
```

### 6.3 程序化变形动画

使用数学函数控制变形目标。

```javascript
function animate() {
  const time = clock.getElapsedTime();

  // 正弦波变形
  mesh.morphTargetInfluences[0] = (Math.sin(time) + 1) / 2;

  // 呼吸效果
  mesh.morphTargetInfluences[1] = (Math.sin(time * 0.5) + 1) / 2;

  // 脉冲效果
  mesh.morphTargetInfluences[2] = Math.abs(Math.sin(time * 2));

  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

---

## 第七章：程序化动画

### 7.1 平滑阻尼

实现平滑的跟随效果。

```javascript
class SmoothDamp {
  constructor(smoothTime = 0.3) {
    this.smoothTime = smoothTime;
    this.velocity = new THREE.Vector3();
  }

  update(current, target, deltaTime) {
    const omega = 2 / this.smoothTime;
    const x = omega * deltaTime;
    const exp = 1 / (1 + x + 0.48 * x * x + 0.235 * x * x * x);

    const change = current.clone().sub(target);
    const temp = this.velocity.clone()
      .add(change.clone().multiplyScalar(omega))
      .multiplyScalar(deltaTime);

    this.velocity.sub(temp.clone().multiplyScalar(omega)).multiplyScalar(exp);

    return target.clone().add(change.add(temp).multiplyScalar(exp));
  }
}

// 使用
const smoothDamp = new SmoothDamp(0.3);
const current = new THREE.Vector3();
const target = new THREE.Vector3(2, 0, 0);

function animate() {
  const delta = clock.getDelta();
  current.copy(smoothDamp.update(current, target, delta));
  mesh.position.copy(current);
}
```

### 7.2 弹簧物理

模拟弹簧运动。

```javascript
class Spring {
  constructor(stiffness = 100, damping = 10) {
    this.stiffness = stiffness;
    this.damping = damping;
    this.position = 0;
    this.velocity = 0;
    this.target = 0;
  }

  update(dt) {
    const force = -this.stiffness * (this.position - this.target);
    const dampingForce = -this.damping * this.velocity;

    this.velocity += (force + dampingForce) * dt;
    this.position += this.velocity * dt;

    return this.position;
  }

  setTarget(target) {
    this.target = target;
  }
}

// 使用
const spring = new Spring(100, 10);

function animate() {
  const delta = clock.getDelta();
  mesh.position.y = spring.update(delta);
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}

// 设置目标
spring.setTarget(2);
```

### 7.3 振荡动画

创建各种振荡效果。

```javascript
function animate() {
  const t = clock.getElapsedTime();

  // 正弦波
  mesh.position.y = Math.sin(t * 2) * 0.5;

  // 余弦波
  mesh.position.x = Math.cos(t * 2) * 0.5;

  // 弹跳
  mesh.position.y = Math.abs(Math.sin(t * 3)) * 2;

  // 阻尼振荡
  mesh.position.y = Math.exp(-t * 0.5) * Math.sin(t * 5) * 2;

  // 复合振荡
  mesh.position.y = Math.sin(t) * 0.5 + Math.sin(t * 2.5) * 0.3;

  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

### 7.4 路径动画

沿路径移动对象。

```javascript
// 创建路径
const path = new THREE.CatmullRomCurve3([
  new THREE.Vector3(-5, 0, 5),
  new THREE.Vector3(0, 2, 0),
  new THREE.Vector3(5, 0, -5),
  new THREE.Vector3(0, -2, 0),
  new THREE.Vector3(-5, 0, 5)
]);

// 创建路径可视化
const pathGeometry = new THREE.BufferGeometry().setFromPoints(path.getPoints(100));
const pathMaterial = new THREE.LineBasicMaterial({ color: 0x00ff00 });
const pathLine = new THREE.Line(pathGeometry, pathMaterial);
scene.add(pathLine);

// 路径动画
function animate() {
  const t = (clock.getElapsedTime() * 0.2) % 1;
  const position = path.getPoint(t);
  const tangent = path.getTangent(t);

  mesh.position.copy(position);
  mesh.lookAt(position.clone().add(tangent));

  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

---

## 第八章：动画优化

### 8.1 性能优化技巧

#### 优化动画剪辑

```javascript
// 移除冗余关键帧
clip.optimize();

// 减少关键帧数量
const optimizedClip = THREE.AnimationUtils.subclip(
  clip,
  'optimized',
  0,
  30,
  30 // 采样率
);
```

#### 共享动画剪辑

```javascript
// 多个对象共享同一个剪辑
const clip = createAnimationClip();

const mixer1 = new THREE.AnimationMixer(mesh1);
const mixer2 = new THREE.AnimationMixer(mesh2);

const action1 = mixer1.clipAction(clip);
const action2 = mixer2.clipAction(clip);

action1.play();
action2.play();
```

#### 禁用不可见对象的动画

```javascript
mesh.onBeforeRender = () => {
  action.paused = false;
};

mesh.onAfterRender = () => {
  if (!isInFrustum(mesh)) {
    action.paused = true;
  }
};
```

### 8.2 动画缓存

缓存动画剪辑以提高性能。

```javascript
class AnimationCache {
  constructor() {
    this.cache = new Map();
  }

  get(name) {
    if (!this.cache.has(name)) {
      this.cache.set(name, this.loadAnimation(name));
    }
    return this.cache.get(name);
  }

  loadAnimation(name) {
    // 加载或创建动画
    return createAnimationClip(name);
  }

  dispose(name) {
    const clip = this.cache.get(name);
    if (clip) {
      clip.dispose();
      this.cache.delete(name);
    }
  }

  disposeAll() {
    this.cache.forEach(clip => clip.dispose());
    this.cache.clear();
  }
}

const animationCache = new AnimationCache();
const clip = animationCache.get('walk');
```

### 8.3 LOD动画

根据距离使用不同精度的动画。

```javascript
// 创建LOD
const lod = new THREE.LOD();

// 近距离：高精度动画
const highDetailMesh = createHighDetailMesh();
const highDetailMixer = new THREE.AnimationMixer(highDetailMesh);
lod.addLevel(highDetailMesh, 0);

// 远距离：低精度动画
const lowDetailMesh = createLowDetailMesh();
const lowDetailMixer = new THREE.AnimationMixer(lowDetailMesh);
lod.addLevel(lowDetailMesh, 50);

scene.add(lod);

// 根据距离更新
function animate() {
  const distance = camera.position.distanceTo(lod.position);

  if (distance < 50) {
    highDetailMixer.update(delta);
    lowDetailMixer.update(0);
  } else {
    highDetailMixer.update(0);
    lowDetailMixer.update(delta);
  }

  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

---

## 常见问题与故障排除

### Q1: 动画不播放

**原因**: 未调用mixer.update()或未设置时间增量

**解决方案**:
```javascript
function animate() {
  const delta = clock.getDelta();
  mixer.update(delta); // 确保调用
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

### Q2: 动画速度不正确

**原因**: timeScale设置错误或delta计算错误

**解决方案**:
```javascript
action.timeScale = 1; // 设置正确的播放速度
const delta = clock.getDelta(); // 使用正确的delta
mixer.update(delta);
```

### Q3: 动画混合不自然

**原因**: 权重设置不当或混合模式错误

**解决方案**:
```javascript
// 使用淡入淡出
action1.crossFadeTo(action2, 0.5, true);

// 设置正确的权重
action1.setEffectiveWeight(0.7);
action2.setEffectiveWeight(0.3);
```

### Q4: 性能问题

**原因**: 动画过多或未优化

**解决方案**:
```javascript
// 优化动画剪辑
clip.optimize();

// 禁用不可见动画
if (!isInFrustum(mesh)) {
  action.paused = true;
}

// 使用LOD
lod.addLevel(highDetail, 0);
lod.addLevel(lowDetail, 50);
```

---

## 最佳实践

### 1. 使用Clock精确计时

```javascript
const clock = new THREE.Clock();

function animate() {
  const delta = clock.getDelta();
  mixer.update(delta);
}
```

### 2. 优化动画剪辑

```javascript
clip.optimize();
```

### 3. 使用淡入淡出过渡

```javascript
action1.crossFadeTo(action2, 0.5, true);
```

### 4. 缓存动画剪辑

```javascript
const animationCache = new Map();
animationCache.set('walk', walkClip);
```

### 5. 禁用不可见动画

```javascript
if (!isInFrustum(mesh)) {
  action.paused = true;
}
```

### 6. 使用平滑阻尼

```javascript
const smoothDamp = new SmoothDamp(0.3);
current.copy(smoothDamp.update(current, target, delta));
```

---

## 下一步学习

完成本教程后，建议继续学习：

1. **Three.js Loaders** - 学习如何加载包含动画的GLTF模型
2. **Three.js Shaders** - 深入学习着色器动画
3. **Three.js Interaction** - 学习如何通过用户交互控制动画

---

## 示例代码

本教程包含以下示例：

- [01-basic-animation.html](./01-basic-animation.html) - 基础动画（时钟、requestAnimationFrame）
- [02-keyframe-animation.html](./02-keyframe-animation.html) - 关键帧动画
- [03-procedural-animation.html](./03-procedural-animation.html) - 程序化动画
- [04-morph-targets.html](./04-morph-targets.html) - 变形目标动画
- [05-gltf-animation.html](./05-gltf-animation.html) - GLTF模型动画
- [06-comprehensive.html](./06-comprehensive.html) - 综合示例

运行示例：
```bash
# 使用本地服务器
python -m http.server 8000
# 或
npx serve

# 在浏览器中打开
http://localhost:8000/06-animation/01-basic-animation.html
```

---

## 参考资料

- [Three.js Animation Documentation](https://threejs.org/docs/#api/en/animation/AnimationMixer)
- [Three.js AnimationClip Documentation](https://threejs.org/docs/#api/en/animation/AnimationClip)
- [MDN: Animation basics](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame)
