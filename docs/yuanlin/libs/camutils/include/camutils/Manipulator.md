# Manipulator.h + Manipulator.cpp - 相机操控器

## 文件概述

定义相机交互操控器 `Manipulator`，支持三种模式：ORBIT（轨道环绕）、MAP（地图平移缩放）和 FREE_FLIGHT（自由飞行）。类似 Sketchfab 或 Google Maps 的相机交互体验。通过 Builder 模式配置、事件驱动更新。

## 核心类

### `Manipulator<FLOAT>` 模板类

**Config 结构体**（Builder 内部状态）：
- 通用：`viewport`, `targetPosition`, `upVector`, `zoomSpeed`
- ORBIT 模式：`orbitHomePosition`, `orbitSpeed`
- MAP 模式：`fovDirection`, `fovDegrees`, `farPlane`, `mapExtent`, `mapMinDistance`
- FREE_FLIGHT 模式：`flightStartPosition/Pitch/Yaw`, `flightMaxSpeed`, `flightSpeedSteps`, `flightPanSpeed`, `flightMoveDamping`
- 光线投射：`groundPlane`, `raycastCallback`, `raycastUserdata`
- `panning` -- 是否启用平移

**Builder 模式**：
- 链式调用设置各属性
- `build(Mode)` -- 创建对应模式的操控器实例

**核心虚方法**：
- `grabBegin(x, y, strafe)` -- 开始拖拽（MAP: 平移; ORBIT: 旋转/平移; FREE_FLIGHT: 视角旋转）
- `grabUpdate(x, y)` -- 更新拖拽
- `grabEnd()` -- 结束拖拽
- `scroll(x, y, delta)` -- 滚轮事件（缩放/调速）
- `getCurrentBookmark()` / `getHomeBookmark()` -- 获取当前/初始书签
- `jumpToBookmark(bookmark)` -- 跳转到书签位置

**非虚方法**：
- `getLookAt(eye, target, up)` -- 获取当前相机参数（每帧调用）
- `raycast(x, y, result)` -- 视口坐标射线拾取
- `getRay(x, y, origin, dir)` -- 获取拾取射线
- `setViewport(w, h)` -- 更新视口尺寸
- `keyDown/keyUp(Key)` -- 键盘输入（FREE_FLIGHT 的 WASD 移动）
- `update(deltaTime)` -- 每帧更新

**Key 枚举**：`FORWARD`, `LEFT`, `BACKWARD`, `RIGHT`, `UP`, `DOWN`

## 关键实现逻辑

**getRay**：根据视口坐标和 FOV 计算拾取射线。将像素坐标重映射到 [-1, +1]，根据 FOV 方向（垂直/水平）调整光线方向。

**raycast**：先尝试用户提供的回调函数；失败则回退到平面求交 `raycastPlane`（基于平面方程 `dot(n, dir)` 判断是否相交）。

**默认属性**：zoomSpeed=0.01, upVector=(0,1,0), fovDegrees=33, farPlane=5000, mapExtent=512x512。

## 依赖关系

- `<camutils/Bookmark.h>`, `<camutils/compiler.h>`
- `<math/vec2.h>`, `<math/vec3.h>`, `<math/vec4.h>`, `<math/scalar.h>`
- 内部子类：`FreeFlightManipulator.h`, `MapManipulator.h`, `OrbitManipulator.h`
