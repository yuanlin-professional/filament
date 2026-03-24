# ColorSpace.h

## 文件概述

定义 RGB 色彩空间的完整表示，包括原色色度、白点和传递函数（EOTF/OETF），并预定义了常用的 Rec.709、sRGB、D65 等色彩空间常量。

## 核心类/结构体

- **`Primaries`** - 色彩空间原色的 xy 色度坐标（红、绿、蓝）。
- **`TransferFunction`** - ICC 参数化曲线类型 4 的传递函数（EOTF），由 a/b/c/d/e/f/g 七个参数定义。
- **`ColorSpace`** - RGB 色彩空间，由原色、传递函数和白点组成。
- **`PartialColorSpace`** - 构建色彩空间的中间辅助类，支持 `Rec709-Linear-D65` 语法。
- **`Gamut`** - 色域定义类，封装原色色度。

## 预定义常量

| 常量 | 说明 |
|------|------|
| `Rec709` | Rec.709 色域（sRGB/DisplayP3 使用） |
| `Linear` | 线性传递函数 |
| `sRGB` | sRGB 传递函数 |
| `D65` | CIE 1931 D65 标准光源白点（6504K） |

## 依赖关系

- `math/vec2.h`

## 使用示例

```cpp
using namespace filament::color;
// 声明 "线性 sRGB" 色彩空间
ColorSpace linearSrgb = Rec709 - Linear - D65;
// 声明标准 sRGB 色彩空间
ColorSpace standardSrgb = Rec709 - sRGB - D65;
```
