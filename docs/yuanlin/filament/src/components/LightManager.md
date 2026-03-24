# LightManager

## 文件概述

`LightManager.h` / `LightManager.cpp` 实现了 Filament 引擎的光源组件管理器，支持多种光源类型（方向光/太阳光、点光源、聚光灯）及其参数管理，包括阴影、颜色、强度、衰减等。

## 核心类/结构体

- **`FLightManager`**: 继承自公共 `LightManager`，管理所有光源组件。
  - **`LightType`**: 位域结构，包含光源类型（3位）、是否投射阴影（1位）、是否投射光线（1位）。
  - **`SpotParams`**: 聚光灯参数，包括半径、外角平方余弦、正弦倒数、光通量和缩放偏移。
  - **`ShadowParams`**: 阴影参数，封装 `ShadowOptions`。
  - **`IntensityUnit`**: 强度单位枚举，支持流明/勒克斯和坎德拉两种单位。
- **`Sim`**: 内部 SoA（Structure of Arrays）管理器，使用 `Proxy` 联合体提供便捷的字段访问，共 12 个字段，约 120 字节。
- **`BuilderDetails`**: Builder 模式的内部数据，包含光源创建所需的全部默认参数。

## 关键实现逻辑

- **强度转换 (`setIntensity`)**: 根据光源类型和强度单位进行不同的光度学转换。点光源：`li = lp / (4*pi)`，聚焦聚光灯：`li = lp / (2*pi*(1-cos(outer/2)))`。
- **聚光灯锥角 (`setSpotLightCone`)**: 内角/外角范围限制在 0.5 度至 90 度之间，自动计算 `cosOuterSquared`、`sinInverse` 和 `scaleOffset`，并为 `FOCUSED_SPOT` 类型重新计算发光强度。
- **阴影级联分割**: `ShadowCascades` 提供均匀分割、对数分割和实用分割三种阴影级联计算方法。

## 依赖关系

- `filament/LightManager.h` - 公共 API 定义
- `utils/SingleInstanceComponentManager.h` - ECS 组件管理基础
- `math/fast.h`, `math/scalar.h` - 快速三角函数和数学运算
- `backend/DriverApiForward.h` - 后端驱动 API 前向声明
- `downcast.h` - FILAMENT_DOWNCAST 宏，用于安全向下转换
