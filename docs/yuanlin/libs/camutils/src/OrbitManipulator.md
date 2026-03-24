# OrbitManipulator.h - 轨道模式相机操控器

## 文件概述

实现 `Manipulator` 的 ORBIT 模式子类。提供类似 Sketchfab 的 3D 模型查看器交互：鼠标左键拖拽旋转、右键拖拽平移、滚轮缩放。相机始终围绕一个支点（pivot）运动。

## 核心类

### `OrbitManipulator<FLOAT>`
继承自 `Manipulator<FLOAT>`

**状态枚举**：`GrabState { INACTIVE, ORBITING, PANNING }`

**初始化**：
- 相机位置从 `orbitHomePosition` 开始
- 支点（pivot）设为 `targetPosition`
- 默认 orbitHomePosition=(0,0,1), orbitSpeed=(0.01, 0.01)
- 自动设置地面平面：法线从 home 指向 target

**旋转控制**（ORBITING 模式）：
- 鼠标偏移量乘以 `orbitSpeed` 转换为 theta（方位角）和 phi（俯仰角）增量
- phi 限制在 `[-PI/2 + 0.001, PI/2 - 0.001]` 避免万向锁

**平移控制**（PANNING 模式，strafe=true 时触发）：
- 与 MapManipulator 类似的近远平面比例法
- 同时平移 pivot、eye 和 target

**缩放**（scroll）：
- 沿视线方向移动相机和 target
- 检测是否穿过 pivot 并翻转标志

**书签**：
- `getCurrentBookmark` -- 从 `pivotToEye` 向量提取球坐标 (phi, theta, distance)
- `getHomeBookmark` -- phi=0, theta=0, distance=|target-home|
- `jumpToBookmark` -- 从球坐标重建相机位置：`pivot + (sin(theta)*cos(phi), sin(phi), cos(theta)*cos(phi)) * |distance|`

## 关键实现逻辑

轨道运动使用球坐标系：theta 为 XZ 平面上的方位角（atan2(x, z)），phi 为仰角（asin(y)）。负 distance 表示相机已穿越 pivot 点（翻转状态），此时注视方向取反。

支持同时记录 orbit 和 map 参数到书签中，便于在 ORBIT 和 MAP 模式之间平滑切换。

## 依赖关系

- `<camutils/Manipulator.h>`
- `<math/scalar.h>`
