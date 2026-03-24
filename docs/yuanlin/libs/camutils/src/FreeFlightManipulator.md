# FreeFlightManipulator.h - 自由飞行相机操控器

## 文件概述

实现 `Manipulator` 的 FREE_FLIGHT 模式子类。提供类似 FPS 游戏的自由飞行相机控制：鼠标控制视角旋转，WASD 键盘控制移动，滚轮调节速度。支持移动阻尼（惯性）。

## 核心类

### `FreeFlightManipulator<FLOAT>`
继承自 `Manipulator<FLOAT>`

**初始化**：
- 从 `flightStartPosition` 设置初始位置
- 从 `flightStartPitch/Yaw` 设置初始朝向
- 默认参数：panSpeed=(0.01, 0.01), maxSpeed=10, speedSteps=80

**视角控制**（grabBegin/Update/End）：
- 记录鼠标初始位置和欧拉角
- 根据鼠标偏移量更新 pitch（限制在 [-PI/2, PI/2]）和 yaw（mod 2*PI）
- 通过 `mat3::eulerZYX` 将欧拉角转换为注视方向

**移动控制**：
- `keyDown/keyUp` -- 记录按键状态
- `update(deltaTime)` -- 根据按键状态计算移动方向

**速度控制**（scroll）：
- 滚轮调节 `mScrollWheel`，归一化到 [-1, 1]
- 移动速度 = `pow(maxSpeed, normalizedScroll)`，实现指数级速度调节

**阻尼系统**（update 中 dampingFactor > 0 时）：
- 模拟运动学阻尼：`v = v * (1 - k*dt)` + 加速项
- 使用 16 步子迭代提高数值稳定性
- 无阻尼时直接将方向力作为速度

**书签**：
- `getCurrentBookmark` -- 记录当前位置和欧拉角
- `getHomeBookmark` -- 返回初始配置
- `jumpToBookmark` -- 恢复位置和朝向

## 关键实现逻辑

移动方向先在局部坐标系中确定（WASD 对应 +-X/+-Z），再通过 `lookAt` 矩阵转换到世界坐标系。UP/DOWN 直接在世界 Y 轴方向操作，不受相机朝向影响。

## 依赖关系

- `<camutils/Manipulator.h>`, `<camutils/Bookmark.h>`
- `<math/scalar.h>`, `<math/mat3.h>`, `<math/mat4.h>`, `<math/quat.h>`
