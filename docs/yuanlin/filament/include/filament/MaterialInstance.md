# MaterialInstance.h

## 文件概述

定义材质实例，持有 Material 中定义的参数的具体值。每个 MaterialInstance 关联一个 Material，可独立设置参数。

## 核心类/结构体

- **`MaterialInstance`** - 材质实例，继承自 `FilamentAPI`。存储材质参数的具体值。

## 主要 API

| 方法 | 说明 |
|------|------|
| `duplicate(other, name)` | 从另一个实例复制创建新实例（静态） |
| `getMaterial()` | 获取关联的 Material |
| `setParameter(name, value)` | 设置 uniform 参数（支持多种类型） |
| `setParameter(name, texture, sampler)` | 设置纹理参数 |
| `setParameter(name, RgbType, color)` | 设置颜色参数（自动空间转换） |
| `getParameter<T>(name)` | 获取参数值 |
| `setScissor(left, bottom, w, h)` | 设置裁剪矩形 |
| `setPolygonOffset(scale, constant)` | 设置多边形偏移 |
| `setCullingMode(mode)` | 覆盖面剔除模式 |
| `setDepthWrite(enable)` / `setDepthCulling(enable)` | 覆盖深度写入/测试 |
| `setStencilCompareFunction(func, face)` | 设置模板比较函数 |
| `setDoubleSided(doubleSided)` | 启用/禁用双面光照 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/Color.h`, `filament/Engine.h`, `filament/MaterialEnums.h`
- `backend/DriverEnums.h`, `math/mathfwd.h`
