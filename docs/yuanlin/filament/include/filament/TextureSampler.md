# TextureSampler.h

## 文件概述

定义纹理采样器参数，控制纹理的过滤方式、环绕模式和各向异性过滤等采样行为。

## 核心类/结构体

- **`TextureSampler`** - 纹理采样器配置类。

## 类型别名

- `WrapMode` - 环绕模式（CLAMP_TO_EDGE 等）
- `MinFilter` / `MagFilter` - 最小/最大化过滤
- `CompareMode` / `CompareFunc` - 比较模式和函数

## 主要 API

| 方法 | 说明 |
|------|------|
| `TextureSampler(minMag, wrap)` | 构造：统一过滤和环绕模式 |
| `TextureSampler(min, mag, wrap)` | 构造：分别指定缩小/放大过滤 |
| `setMinFilter(v)` / `setMagFilter(v)` | 设置缩小/放大过滤 |
| `setWrapModeS(v)` / `T(v)` / `R(v)` | 设置 S/T/R 轴环绕模式 |
| `setAnisotropy(value)` | 设置各向异性过滤（2 的幂，最大 128） |
| `setCompareMode(mode, func)` | 设置比较模式（用于深度纹理） |
| `getMinFilter()` / `getMagFilter()` 等 | 获取当前设置 |

## 依赖关系

- `backend/DriverEnums.h`
- `utils/compiler.h`

## 使用示例

```cpp
TextureSampler sampler(TextureSampler::MinFilter::LINEAR_MIPMAP_LINEAR,
                       TextureSampler::MagFilter::LINEAR);
sampler.setAnisotropy(8.0f);
materialInstance->setParameter("albedo", texture, sampler);
```
