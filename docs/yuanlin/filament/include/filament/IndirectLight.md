# IndirectLight.h

## 文件概述

定义间接光照（环境光照），用于模拟来自环境的全局照明，包含辐照度（漫反射）和反射（高光）两个分量。

## 核心类/结构体

- **`IndirectLight`** - 间接光照对象，继承自 `FilamentAPI`。通常通过 cmgen 工具生成数据。
- **`IndirectLight::Builder`** - 构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::reflections(cubemap)` | 设置反射立方体贴图（由 cmgen 生成） |
| `Builder::irradiance(bands, sh)` | 通过球谐系数设置辐照度（1~3 阶） |
| `Builder::radiance(bands, sh)` | 通过辐射度球谐系数设置辐照度 |
| `Builder::intensity(envIntensity)` | 设置环境光强度（默认 30000 lux） |
| `Builder::rotation(mat)` | 设置 IBL 旋转 |
| `setIntensity(intensity)` | 动态更新环境光强度 |
| `getDirectionEstimate(sh)` | 从球谐估计主光方向（静态） |
| `getColorEstimate(sh, dir)` | 从球谐估计主光颜色和强度（静态） |

## 依赖关系

- `filament/FilamentAPI.h`
- `utils/compiler.h`, `math/mathfwd.h`

## 使用示例

```cpp
auto* ibl = IndirectLight::Builder()
    .reflections(cubemapTexture)
    .irradiance(3, shCoeffs)
    .intensity(30000.0f)
    .build(*engine);
scene->setIndirectLight(ibl);
```
