# ColorGrading.h

## 文件概述

定义颜色分级（Color Grading）系统，用于在 HDR 渲染后对画面进行色彩调整，包括白平衡、对比度、饱和度、色调映射等后处理操作。

## 核心类/结构体

- **`ColorGrading`** - 颜色分级对象，关联到 View 上以应用色彩变换。继承自 `FilamentAPI`。
- **`ColorGrading::Builder`** - 构建器，配置所有颜色分级参数。
- **`QualityLevel`** - 枚举：`LOW`/`MEDIUM`/`HIGH`/`ULTRA`，控制 3D LUT 质量。
- **`LutFormat`** - 枚举：`INTEGER`(10bit) / `FLOAT`(16bit)。

## 主要 API (Builder)

| 方法 | 说明 |
|------|------|
| `quality(level)` | 设置颜色分级质量（影响 LUT 分辨率） |
| `toneMapper(mapper)` | 设置色调映射算子 |
| `exposure(stops)` | 调整曝光（以 EV 为单位） |
| `whiteBalance(temperature, tint)` | 白平衡调整（-1~+1） |
| `channelMixer(outR, outG, outB)` | 通道混合器 |
| `shadowsMidtonesHighlights(...)` | 阴影/中间调/高光调整 |
| `slopeOffsetPower(slope, offset, power)` | ASC CDL 调色 |
| `contrast(v)` / `vibrance(v)` / `saturation(v)` | 对比度/鲜艳度/饱和度 |
| `luminanceScaling(enable)` | 亮度缩放（避免色相偏移） |
| `gamutMapping(enable)` | 色域映射 |
| `build(engine)` | 创建 ColorGrading 对象 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/ToneMapper.h`
- `utils/compiler.h`, `math/mathfwd.h`

## 使用示例

```cpp
auto* cg = ColorGrading::Builder()
    .exposure(1.0f)
    .whiteBalance(0.0f, 0.0f)
    .contrast(1.2f)
    .saturation(1.1f)
    .build(*engine);
view->setColorGrading(cg);
```
