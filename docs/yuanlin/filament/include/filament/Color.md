# Color.h

## 文件概述

定义颜色类型和颜色空间转换工具，支持 sRGB 与线性空间之间的精确/快速转换，以及色温等高级颜色计算。

## 核心类/结构体

- **`Color`** - 颜色转换工具类，提供 sRGB/线性空间转换、色温计算等静态方法。
- **`RgbType`** - 枚举：`sRGB` / `LINEAR`，标识 RGB 颜色空间。
- **`RgbaType`** - 枚举：`sRGB` / `LINEAR` / `PREMULTIPLIED_sRGB` / `PREMULTIPLIED_LINEAR`。
- **`ColorConversion`** - 枚举：`ACCURATE`（精确 sRGB 曲线）/ `FAST`（gamma 2.2 近似）。

## 类型别名

- `LinearColor` = `math::float3` - 线性空间 RGB
- `sRGBColor` = `math::float3` - sRGB 空间 RGB
- `LinearColorA` / `sRGBColorA` = `math::float4` - 带 alpha 通道

## 主要 API

| 方法 | 说明 |
|------|------|
| `Color::toLinear(type, color)` | 将 RGB/RGBA 颜色转换到线性空间 |
| `Color::toSRGB(color)` | 将线性颜色转换到 sRGB 空间 |
| `Color::cct(K)` | 将色温（开尔文）转换为线性 RGB（1000K~15000K） |
| `Color::illuminantD(K)` | 将 CIE D 系列标准光源转换为线性 RGB |
| `Color::absorptionAtDistance(color, distance)` | 计算 Beer-Lambert 吸收系数 |

## 依赖关系

- `utils/compiler.h`
- `math/vec3.h`, `math/vec4.h`

## 使用示例

```cpp
// sRGB 转线性空间
auto linear = Color::toLinear<ACCURATE>(sRGBColor{0.8f, 0.2f, 0.1f});

// 色温转颜色
auto sunColor = Color::cct(6500.0f); // 日光色温
```
