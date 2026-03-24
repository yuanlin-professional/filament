# ColorTransform.h

## 文件概述

`ColorTransform.h` 是一个纯头文件模板库，提供线性颜色空间与各种编码格式之间的转换功能。主要用于将 `LinearImage`（浮点线性图像）转换为 sRGB、RGBM、RGB_10_11_11_REV、灰度等格式，以及反向转换。

## 核心类/结构体/函数

- **`linearToRGB_10_11_11_REV(linear)`** - 将线性 RGB 颜色转换为 R11G11B10 打包格式（uint32_t）
- **`linearToRGBM(linear)`** - 将线性颜色编码为 RGBM（适用于 HDR 存储）
- **`RGBMtoLinear(rgbm)`** - 将 RGBM 编码恢复为线性颜色
- **`linearTosRGB(linear)`** - 线性到 sRGB 伽马校正（向量和标量两个重载）
- **`sRGBToLinear(sRGB)`** - sRGB 到线性空间转换（float3 和 float4 特化）
- **`fromLinearTosRGB<T,N>(image)`** - 将浮点图像转换为 N 通道 sRGB 整型图像
- **`fromLinearToRGB<T,N>(image)`** - 将浮点图像转换为 N 通道线性 RGB 整型图像
- **`fromLinearToRGBM<T>(image)`** - 将浮点图像转换为 4 通道 RGBM uint8 图像
- **`fromLinearToRGB_10_11_11_REV(image)`** - 将浮点图像转换为 R11G11B10 格式
- **`fromLinearToGrayscale<T>(image)`** - 将单通道浮点图像转换为整型灰度图像
- **`toLinear<T>()`** / **`toLinearWithAlpha<T>()`** - 从原始字节数据构建 3/4 通道 LinearImage
- **`toLinearFromRGBM()`** - 从 RGBM 数据构建 3 通道 LinearImage

## 关键实现逻辑

- sRGB 转换遵循标准分段公式：线性段（<= 0.0031308）使用 12.92 倍乘法，非线性段使用 1.055 * pow(x, 1/2.4) - 0.055
- RGBM 编码先做 sqrt（线性到伽马），再除以 16 映射到 [0,1]，alpha 通道存储最大分量值
- 所有模板函数通过 `proc` 和 `transform` lambda 参数实现灵活的格式适配

## 依赖关系

- `image/LinearImage.h` - 线性图像数据结构
- `utils/compiler.h` - 编译器工具宏
- `math/scalar.h`, `math/vec3.h`, `math/vec4.h`, `math/half.h` - 数学库
