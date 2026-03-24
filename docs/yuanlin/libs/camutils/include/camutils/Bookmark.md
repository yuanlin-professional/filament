# Bookmark.h + Bookmark.cpp - 相机书签（视点记忆）

## 文件概述

定义相机位姿的不透明快照类型 `Bookmark`，用于记录和恢复相机位置，也用于两个视点之间的动画插值。在 MAP 模式下实现了 Van Wijk 平滑缩放插值。

## 核心结构

### `Bookmark<FLOAT>` 模板结构体

**公开接口**：
- `interpolate(Bookmark a, Bookmark b, double t)` -- 在两个书签之间插值（t 范围 [0,1]）
- `duration(Bookmark a, Bookmark b)` -- 推荐的动画持续时间（无量纲乘数）

**私有参数结构体**：
- `MapParams` -- MAP 模式参数：`extent`（视野范围）、`center`（中心点 vec2）
- `OrbitParams` -- ORBIT 模式参数：`phi`（俯仰角）、`theta`（方位角）、`distance`（距离）、`pivot`（旋转中心 vec3）
- `FlightParams` -- FREE_FLIGHT 模式参数：`pitch`、`yaw`、`position`（vec3）

**友元类**：`FreeFlightManipulator`, `OrbitManipulator`, `MapManipulator`

### `Mode` 枚举
- `ORBIT` -- 轨道模式
- `MAP` -- 地图模式
- `FREE_FLIGHT` -- 自由飞行模式

## 关键实现逻辑

**Van Wijk 插值**（MAP 模式）：基于论文 "Smooth and efficient zooming and panning"，通过双曲函数（cosh、sinh、tanh）实现平滑的地图缩放和平移动画，使得缩放倍率和平移距离的变化在视觉上均匀。退化情况（两点重合）回退到简单的指数插值。

**ORBIT 插值**：对 phi、theta、distance、pivot 分别使用线性插值 `lerp`。

**duration 计算**：使用 Van Wijk 方法的路径长度 S 作为动画时长的无量纲参考值。

## 依赖关系

- `<camutils/compiler.h>` -- 可见性宏
- `<math/vec2.h>`, `<math/vec3.h>`, `<math/scalar.h>`
