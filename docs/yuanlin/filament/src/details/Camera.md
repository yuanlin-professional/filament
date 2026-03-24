# Camera

## 文件概述

`FCamera` 是 `Camera` 公共 API 的私有实现类，负责管理相机的投影矩阵、视图矩阵、曝光参数及立体渲染支持。同时定义了 `CameraInfo` 结构体，用于在渲染管线中传递相机快照信息。

## 核心类/结构体

### `FCamera`
- 继承自 `Camera`
- 持有每眼投影矩阵数组 `mEyeProjection[CONFIG_MAX_STEREOSCOPIC_EYES]`
- 支持透视投影、正交投影和自定义投影矩阵
- 传感器尺寸常量 `SENSOR_SIZE = 0.024f`（35mm 相机的 24mm 传感器）

### `CameraInfo`
- 渲染用的相机信息快照结构体
- 包含投影矩阵、模型/视图矩阵、世界变换、曝光值（EV100）、焦距等
- 通过 union 支持单目/立体渲染两种访问方式

## 关键实现逻辑

- **投影矩阵转换** -- `getProjectionMatrix()` 将用户 GL 约定的裁剪空间转换为 Filament 内部的反转 DX 约定（p33=0），提高深度缓冲精度
- **无限远平面** -- 透视投影默认使用无限远平面（far -> infinity），p[2][2]=-1, p[3][2]=-2*near
- **刚体逆变换** -- `rigidTransformInverse()` 利用旋转矩阵的转置性质高效计算视图矩阵
- **曝光控制** -- 光圈(f/0.5~64)、快门速度(1/25000s~60s)、ISO 感光度(10~204800)
- **有效焦距计算** -- `computeEffectiveFocalLength()` 考虑对焦距离的影响

## 依赖关系

- `components/TransformManager.h` -- 变换组件管理，管理相机实体的模型矩阵
- `filament/Exposure.h` -- 曝光计算
- `filament/Frustum.h` -- 视锥体裁剪

## 与公共 API 的关系

直接对应 `filament::Camera`。通过 `Engine::createCamera()` 创建，绑定到 ECS 实体上。`CameraInfo` 供 `FRenderer` 和 `FView` 在渲染流程中使用。
