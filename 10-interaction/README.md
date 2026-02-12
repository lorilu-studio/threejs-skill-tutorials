# Three.js 交互教程 - 第十部分：Interaction详解

> 本教程深入讲解Three.js交互的核心概念、Raycasting和用户输入处理。通过丰富的示例和实践，帮助你掌握点击检测、相机控制、对象选择等交互技术。

---

## 📚 目录

- [学习目标](#学习目标)
- [前置知识](#前置知识)
- [第一章：Raycasting基础](#第一章raycasting基础)
- [第二章：相机控制](#第二章相机控制)
- [第三章：对象选择与高亮](#第三章对象选择与高亮)
- [第四章：拖拽与变换](#第四章拖拽与变换)
- [第五章：坐标转换](#第五章坐标转换)
- [第六章：性能优化](#第六章性能优化)
- [常见问题与故障排除](#常见问题与故障排除)
- [最佳实践](#最佳实践)
- [下一步学习](#下一步学习)

---

## 🎯 学习目标

完成本教程后，你将能够：

- ✅ 理解Raycasting的基本原理和使用方法
- ✅ 熟练使用各种相机控制器（OrbitControls、FlyControls等）
- ✅ 实现对象选择、高亮和拖拽功能
- ✅ 处理鼠标、触摸和键盘输入
- ✅ 实现世界坐标和屏幕坐标的转换
- ✅ 优化交互性能
- ✅ 创建完整的交互系统

---

## 📋 前置知识

在开始学习之前，建议你具备以下基础知识：

### 必备知识
- **Three.js基础**: 场景、相机、渲染器
- **事件处理**: JavaScript事件监听
- **向量数学**: Vector3的基本操作

### 推荐知识
- **几何学**: 射线、平面、相交检测
- **用户体验**: 交互设计原则

---

## 第一章：Raycasting基础

### 1.1 Raycaster概述

**Raycaster**用于从相机发射射线，检测与场景中对象的相交。

#### Raycaster工作原理

```
Raycasting流程：
1. 获取鼠标位置（屏幕坐标）
2. 转换为标准化设备坐标（NDC）
3. 从相机发射射线
4. 检测射线与对象的相交
5. 返回相交信息
```

### 1.2 基本Raycasting

```javascript
import * as THREE from 'three';

const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2();

// 更新鼠标位置
function updateMouse(event) {
  mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
  mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
}

// 执行射线检测
function checkIntersection() {
  raycaster.setFromCamera(mouse, camera);
  const intersects = raycaster.intersectObjects(scene.children);

  if (intersects.length > 0) {
    console.log('相交对象:', intersects[0].object);
    console.log('相交点:', intersects[0].point);
    console.log('相交距离:', intersects[0].distance);
  }
}

window.addEventListener('click', (event) => {
  updateMouse(event);
  checkIntersection();
});
```

### 1.3 相交信息

```javascript
const intersects = raycaster.intersectObjects(objects);

// intersects数组包含：
{
  distance: number,          // 从射线原点的距离
  point: Vector3,            // 世界坐标中的相交点
  face: Face3,                // 相交的面
  faceIndex: number,          // 面索引
  object: Object3D,           // 相交的对象
  uv: Vector2,                // 相交点的UV坐标
  uv1: Vector2,               // 第二UV通道
  normal: Vector3,           // 插值面法线
  instanceId: number         // InstancedMesh的实例ID
}
```

### 1.4 触摸支持

```javascript
function onTouchStart(event) {
  event.preventDefault();

  if (event.touches.length === 1) {
    const touch = event.touches[0];
    mouse.x = (touch.clientX / window.innerWidth) * 2 - 1;
    mouse.y = -(touch.clientY / window.innerHeight) * 2 + 1;

    raycaster.setFromCamera(mouse, camera);
    const intersects = raycaster.intersectObjects(clickableObjects);

    if (intersects.length > 0) {
      handleSelection(intersects[0]);
    }
  }
}

renderer.domElement.addEventListener('touchstart', onTouchStart);
```

---

## 第二章：相机控制

### 2.1 OrbitControls

轨道控制器，最常用的相机控制。

```javascript
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

const controls = new OrbitControls(camera, renderer.domElement);

// 阻尼（平滑移动）
controls.enableDamping = true;
controls.dampingFactor = 0.05;

// 旋转限制
controls.minPolarAngle = 0; // 顶部
controls.maxPolarAngle = Math.PI / 2; // 地平线
controls.minAzimuthAngle = -Math.PI / 4; // 左侧
controls.maxAzimuthAngle = Math.PI / 4; // 右侧

// 缩放限制
controls.minDistance = 2;
controls.maxDistance = 50;

// 启用/禁用功能
controls.enableRotate = true;
controls.enableZoom = true;
controls.enablePan = true;

// 自动旋转
controls.autoRotate = true;
controls.autoRotateSpeed = 2.0;

// 目标点（轨道中心）
controls.target.set(0, 1, 0);

// 在动画循环中更新
function animate() {
  requestAnimationFrame(animate);
  controls.update(); // 阻尼和自动旋转必需
  renderer.render(scene, camera);
}
```

### 2.2 FlyControls

飞行控制器，类似飞行模拟器。

```javascript
import { FlyControls } from 'three/addons/controls/FlyControls.js';

const controls = new FlyControls(camera, renderer.domElement);
controls.movementSpeed = 10;
controls.rollSpeed = Math.PI / 24;
controls.dragToLook = true;

function animate() {
  controls.update(clock.getDelta());
  renderer.render(scene, camera);
}
```

### 2.3 FirstPersonControls

第一人称控制器，类似FPS游戏。

```javascript
import { FirstPersonControls } from 'three/addons/controls/FirstPersonControls.js';

const controls = new FirstPersonControls(camera, renderer.domElement);
controls.movementSpeed = 10;
controls.lookSpeed = 0.1;
controls.lookVertical = true;
controls.constrainVertical = true;
controls.verticalMin = Math.PI / 4;
controls.verticalMax = (Math.PI * 3) / 4;

function animate() {
  controls.update(clock.getDelta());
}
```

### 2.4 PointerLockControls

指针锁定控制器，用于第一人称视角。

```javascript
import { PointerLockControls } from 'three/addons/controls/PointerLockControls.js';

const controls = new PointerLockControls(camera, document.body);

// 点击锁定指针
document.addEventListener('click', () => {
  controls.lock();
});

controls.addEventListener('lock', () => {
  console.log('指针已锁定');
});

controls.addEventListener('unlock', () => {
  console.log('指针已解锁');
});

// 移动控制
const velocity = new THREE.Vector3();
const direction = new THREE.Vector3();
let moveForward = false;
let moveBackward = false;

document.addEventListener('keydown', (event) => {
  switch (event.code) {
    case 'KeyW':
      moveForward = true;
      break;
    case 'KeyS':
      moveBackward = true;
      break;
  }
});

document.addEventListener('keyup', (event) => {
  switch (event.code) {
    case 'KeyW':
      moveForward = false;
      break;
    case 'KeyS':
      moveBackward = false;
      break;
  }
});

function animate() {
  if (controls.isLocked) {
    direction.z = Number(moveForward) - Number(moveBackward);
    direction.normalize();

    velocity.z -= direction.z * 0.1;
    velocity.z *= 0.9; // 摩擦力

    controls.moveForward(-velocity.z);
  }
}
```

---

## 第三章：对象选择与高亮

### 3.1 点击选择

```javascript
const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2();
let selectedObject = null;

function onMouseDown(event) {
  mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
  mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

  raycaster.setFromCamera(mouse, camera);
  const intersects = raycaster.intersectObjects(selectableObjects);

  // 取消选择之前的对象
  if (selectedObject) {
    selectedObject.material.emissive.set(0x000000);
  }

  // 选择新对象
  if (intersects.length > 0) {
    selectedObject = intersects[0].object;
    selectedObject.material.emissive.set(0x444444);
  } else {
    selectedObject = null;
  }
}

window.addEventListener('mousedown', onMouseDown);
```

### 3.2 悬停效果

```javascript
const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2();
let hoveredObject = null;

function onMouseMove(event) {
  mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
  mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

  raycaster.setFromCamera(mouse, camera);
  const intersects = raycaster.intersectObjects(hoverableObjects);

  // 重置之前的悬停
  if (hoveredObject) {
    hoveredObject.material.color.set(hoveredObject.userData.originalColor);
    document.body.style.cursor = 'default';
  }

  // 应用新的悬停
  if (intersects.length > 0) {
    hoveredObject = intersects[0].object;
    if (!hoveredObject.userData.originalColor) {
      hoveredObject.userData.originalColor = hoveredObject.material.color.getHex();
    }
    hoveredObject.material.color.set(0xff6600);
    document.body.style.cursor = 'pointer';
  } else {
    hoveredObject = null;
  }
}

window.addEventListener('mousemove', onMouseMove);
```

### 3.3 框选

```javascript
import { SelectionBox } from 'three/addons/interactive/SelectionBox.js';
import { SelectionHelper } from 'three/addons/interactive/SelectionHelper.js';

const selectionBox = new SelectionBox(camera, scene);
const selectionHelper = new SelectionHelper(renderer, 'selectBox');

document.addEventListener('pointerdown', (event) => {
  selectionBox.startPoint.set(
    (event.clientX / window.innerWidth) * 2 - 1,
    -(event.clientY / window.innerHeight) * 2 + 1,
    0.5
  );
});

document.addEventListener('pointermove', (event) => {
  if (selectionHelper.isDown) {
    selectionBox.endPoint.set(
      (event.clientX / window.innerWidth) * 2 - 1,
      -(event.clientY / window.innerHeight) * 2 + 1,
      0.5
    );
  }
});

document.addEventListener('pointerup', (event) => {
  selectionBox.endPoint.set(
    (event.clientX / window.innerWidth) * 2 - 1,
    -(event.clientY / window.innerHeight) * 2 + 1,
    0.5
  );

  const selected = selectionBox.select();
  console.log('选中的对象:', selected);
});
```

---

## 第四章：拖拽与变换

### 4.1 TransformControls

变换控制器，用于移动、旋转、缩放对象。

```javascript
import { TransformControls } from 'three/addons/controls/TransformControls.js';

const transformControls = new TransformControls(camera, renderer.domElement);
scene.add(transformControls);

// 附加到对象
transformControls.attach(selectedMesh);

// 切换模式
transformControls.setMode('translate'); // 'translate', 'rotate', 'scale'

// 切换空间
transformControls.setSpace('local'); // 'local', 'world'

// 大小
transformControls.setSize(1);

// 事件
transformControls.addEventListener('dragging-changed', (event) => {
  // 拖拽时禁用轨道控制器
  orbitControls.enabled = !event.value;
});

transformControls.addEventListener('change', () => {
  renderer.render(scene, camera);
});

// 键盘快捷键
window.addEventListener('keydown', (event) => {
  switch (event.key) {
    case 'g':
      transformControls.setMode('translate');
      break;
    case 'r':
      transformControls.setMode('rotate');
      break;
    case 's':
      transformControls.setMode('scale');
      break;
    case 'Escape':
      transformControls.detach();
      break;
  }
});
```

### 4.2 DragControls

拖拽控制器，直接拖拽对象。

```javascript
import { DragControls } from 'three/addons/controls/DragControls.js';

const draggableObjects = [mesh1, mesh2, mesh3];
const dragControls = new DragControls(
  draggableObjects,
  camera,
  renderer.domElement
);

dragControls.addEventListener('dragstart', (event) => {
  orbitControls.enabled = false;
  event.object.material.emissive.set(0xaaaaaa);
});

dragControls.addEventListener('drag', (event) => {
  // 限制到地面平面
  event.object.position.y = 0;
});

dragControls.addEventListener('dragend', (event) => {
  orbitControls.enabled = true;
  event.object.material.emissive.set(0x000000);
});
```

---

## 第五章：坐标转换

### 5.1 世界坐标到屏幕坐标

```javascript
function worldToScreen(position, camera) {
  const vector = position.clone();
  vector.project(camera);

  return {
    x: ((vector.x + 1) / 2) * window.innerWidth,
    y: (-(vector.y - 1) / 2) * window.innerHeight
  };
}

// 在3D对象上定位HTML元素
const screenPos = worldToScreen(mesh.position, camera);
element.style.left = screenPos.x + 'px';
element.style.top = screenPos.y + 'px';
```

### 5.2 屏幕坐标到世界坐标

```javascript
function screenToWorld(screenX, screenY, camera, targetZ = 0) {
  const vector = new THREE.Vector3(
    (screenX / window.innerWidth) * 2 - 1,
    -(screenY / window.innerHeight) * 2 + 1,
    0.5
  );

  vector.unproject(camera);

  const dir = vector.sub(camera.position).normalize();
  const distance = (targetZ - camera.position.z) / dir.z;

  return camera.position.clone().add(dir.multiplyScalar(distance));
}
```

### 5.3 射线与平面相交

```javascript
function getRayPlaneIntersection(mouse, camera, plane) {
  const raycaster = new THREE.Raycaster();
  raycaster.setFromCamera(mouse, camera);

  const intersection = new THREE.Vector3();
  raycaster.ray.intersectPlane(plane, intersection);

  return intersection;
}

// 地面平面
const groundPlane = new THREE.Plane(new THREE.Vector3(0, 1, 0), 0);
const worldPos = getRayPlaneIntersection(mouse, camera, groundPlane);
```

---

## 第六章：性能优化

### 6.1 优化技巧

```javascript
// 限制射线检测频率
let lastRaycast = 0;
function onMouseMove(event) {
  const now = Date.now();
  if (now - lastRaycast < 50) return; // 最多20fps
  lastRaycast = now;

  // 执行射线检测
}

// 使用图层过滤
mesh1.layers.set(1); // 可点击图层
raycaster.layers.set(1);

// 使用简单碰撞网格
const complexMesh = loadedModel;
const collisionMesh = new THREE.Mesh(
  new THREE.BoxGeometry(1, 1, 1),
  new THREE.MeshBasicMaterial({ visible: false })
);
collisionMesh.userData.target = complexMesh;
clickables.push(collisionMesh);
```

### 6.2 交互管理器

```javascript
class InteractionManager {
  constructor(camera, renderer, scene) {
    this.camera = camera;
    this.renderer = renderer;
    this.scene = scene;
    this.raycaster = new THREE.Raycaster();
    this.mouse = new THREE.Vector2();
    this.clickables = [];

    this.bindEvents();
  }

  bindEvents() {
    const canvas = this.renderer.domElement;

    canvas.addEventListener('click', (e) => this.onClick(e));
    canvas.addEventListener('mousemove', (e) => this.onMouseMove(e));
    canvas.addEventListener('touchstart', (e) => this.onTouchStart(e));
  }

  updateMouse(event) {
    const rect = this.renderer.domElement.getBoundingClientRect();
    this.mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
    this.mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;
  }

  getIntersects() {
    this.raycaster.setFromCamera(this.mouse, this.camera);
    return this.raycaster.intersectObjects(this.clickables, true);
  }

  onClick(event) {
    this.updateMouse(event);
    const intersects = this.getIntersects();

    if (intersects.length > 0) {
      const object = intersects[0].object;
      if (object.userData.onClick) {
        object.userData.onClick(intersects[0]);
      }
    }
  }

  addClickable(object, callback) {
    this.clickables.push(object);
    object.userData.onClick = callback;
  }

  dispose() {
    // 移除事件监听
  }
}

// 使用
const interaction = new InteractionManager(camera, renderer, scene);
interaction.addClickable(mesh, (intersect) => {
  console.log('点击位置:', intersect.point);
});
```

---

## 常见问题与故障排除

### Q1: Raycaster检测不到对象

**原因**: 对象未添加到场景或射线方向错误

**解决方案**:
```javascript
// 确保对象在场景中
scene.add(mesh);

// 检查射线方向
raycaster.setFromCamera(mouse, camera);
```

### Q2: 触摸事件不工作

**原因**: 未正确处理触摸事件

**解决方案**:
```javascript
function onTouchStart(event) {
  event.preventDefault();
  if (event.touches.length === 1) {
    const touch = event.touches[0];
    mouse.x = (touch.clientX / window.innerWidth) * 2 - 1;
    mouse.y = -(touch.clientY / window.innerHeight) * 2 + 1;
  }
}
```

---

## 最佳实践

### 1. 使用OrbitControls

```javascript
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.05;
```

### 2. 限制射线检测

```javascript
// 只检测特定对象
const clickables = [mesh1, mesh2, mesh3];
const intersects = raycaster.intersectObjects(clickables, false);
```

### 3. 使用图层

```javascript
mesh1.layers.set(1);
raycaster.layers.set(1);
```

### 4. 性能优化

```javascript
// 限制射线检测频率
let lastRaycast = 0;
function onMouseMove(event) {
  const now = Date.now();
  if (now - lastRaycast < 50) return;
  lastRaycast = now;
}
```

---

## 下一步学习

完成本教程后，建议继续学习：

1. **Three.js Animation** - 学习交互动画
2. **Three.js Shaders** - 学习视觉反馈效果
3. **Three.js Materials** - 深入学习材质系统

---

## 示例代码

本教程包含以下示例：

- [01-basic-raycasting.html](./01-basic-raycasting.html) - 基本射线检测
- [02-orbit-controls.html](./02-orbit-controls.html) - 轨道控制器
- [03-object-selection.html](./03-object-selection.html) - 对象选择
- [04-transform-controls.html](./04-transform-controls.html) - 变换控制器
- [05-drag-controls.html](./05-drag-controls.html) - 拖拽控制器
- [06-comprehensive.html](./06-comprehensive.html) - 综合示例

运行示例：
```bash
python -m http.server 8000
http://localhost:8000/10-interaction/01-basic-raycasting.html
```

---

## 参考资料

- [Three.js Raycaster Documentation](https://threejs.org/docs/#api/en/core/Raycaster)
- [Three.js Controls Examples](https://threejs.org/examples/#webgl_controls_orbit)
