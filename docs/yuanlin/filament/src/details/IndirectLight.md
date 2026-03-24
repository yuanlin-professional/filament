# IndirectLight

## 文件概述

`FIndirectLight` 是 `IndirectLight` 公共 API 的私有实现类，管理基于图像的光照（IBL）数据。包括反射贴图（预过滤的立方体贴图）和辐照度数据（球谐系数或辐照度立方体贴图），用于环境光照渲染。

## 核心类/结构体

### `FIndirectLight`
- 继承自 `IndirectLight`
- 持有反射纹理 `mReflectionsTexture` 和辐照度纹理 `mIrradianceTexture`（均为弱引用）
- 存储 9 个球谐系数 `mIrradianceCoefs`（3 bands = 9 系数，每个为 float3）
- 默认光照强度 `DEFAULT_INTENSITY = 30000.0f` lux（太阳光照度）

## 关键实现逻辑

- **球谐辐射度转辐照度** -- `radiance()` 方法将辐射度 SH 系数预乘以 Lambertian BRDF (1/PI) 和截断余弦的 A[i] 系数，转换为可直接在着色器中使用的辐照度系数
- **光照方向估算** -- `getDirectionEstimate()` 从 SH 系数的线性分量（band 1）估算主光方向
- **颜色估算** -- `getColorEstimate()` 通过 SH 重建在给定方向上的环境光颜色和强度
- **旋转支持** -- 可通过 `setRotation()` 旋转环境光照

## 依赖关系

- `details/Texture.h` -- 纹理管理
- `details/Engine.h` -- 引擎驱动 API
- `math/mat3.h` -- 旋转矩阵

## 与公共 API 的关系

直接对应 `filament::IndirectLight`。通过 `IndirectLight::Builder` 创建，设置到 `Scene` 上以提供环境光照。与 `Skybox` 配合使用。
