# DebugRegistry.h

## 文件概述

定义调试属性注册表，允许在运行时查询和修改引擎内部的调试属性，用于性能调优和问题排查。

## 核心类/结构体

- **`DebugRegistry`** - 调试属性注册表，继承自 `FilamentAPI`。
- **`DebugRegistry::DataSource`** - 数据源结构体，包含数据指针和计数。
- **`DebugRegistry::FrameHistory`** - 帧历史信息，包含目标帧时间、实际帧时间、缩放因子和 PID 控制参数。

## 主要 API

| 方法 | 说明 |
|------|------|
| `hasProperty(name)` | 查询属性是否存在 |
| `getPropertyAddress(name)` | 获取属性数据的地址（支持模板） |
| `setProperty(name, value)` | 设置属性值（支持 bool/int/float/float2~4） |
| `getProperty(name, value)` | 获取属性值 |
| `getDataSource(name)` | 获取数据源 |

## 依赖关系

- `filament/FilamentAPI.h`
- `utils/compiler.h`, `math/mathfwd.h`

## 使用示例

```cpp
auto& debug = engine->getDebugRegistry();
if (debug.hasProperty("d.lighting.ibl_intensity")) {
    float value;
    debug.getProperty("d.lighting.ibl_intensity", &value);
    debug.setProperty("d.lighting.ibl_intensity", value * 2.0f);
}
```
