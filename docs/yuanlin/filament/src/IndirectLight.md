# IndirectLight.cpp

## 文件概述

`IndirectLight` 类的公共 API 实现文件。间接光照代表图像基照明（IBL），用于为场景提供环境反射和漫反射光照。提供强度、旋转、纹理访问以及光源方向和颜色估算功能。

## 核心类/结构体/函数

### `IndirectLight` 类方法

- **`setIntensity(float)` / `getIntensity()`** -- 设置/获取 IBL 强度。
- **`setRotation(mat3f)` / `getRotation()`** -- 设置/获取 IBL 旋转矩阵。
- **`getReflectionsTexture()`** -- 获取反射纹理（Cubemap）。
- **`getIrradianceTexture()`** -- 获取辐照度纹理。
- **`getDirectionEstimate()`** -- 从球谐系数估算主光源方向。
- **`getColorEstimate(float3 direction)`** -- 从球谐系数估算给定方向的光照颜色。
- **`getDirectionEstimate(const float3* sh)`** -- 静态版本，从外部球谐系数估算方向。
- **`getColorEstimate(const float3* sh, float3 direction)`** -- 静态版本。

## 关键实现逻辑

通过 `downcast` 委托给 `FIndirectLight` 内部实现。光源方向和颜色估算的静态版本直接调用 `FIndirectLight` 的静态方法，不需要实例。

## 依赖关系

- `details/IndirectLight.h` -- 内部实现类
- `details/Texture.h` -- 纹理内部实现

## 被引用关系

被场景（Scene）设置间接光照时使用。在渲染管线中为 PBR 材质提供环境光照信息（反射探针和辐照度）。
