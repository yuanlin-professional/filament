# ImageEncoder.h / ImageEncoder.cpp

## 文件概述

图像编码统一接口，将线性浮点 `LinearImage` 编码为多种输出格式：PNG（sRGB/线性/RGBM/RGB_10_11_11）、HDR（Radiance RGBE）、PSD（16/32 位）、EXR（半精度浮点）、DDS（多种位深和格式）。

## 核心类/结构体/函数

- **`ImageEncoder`** - 统一编码接口
  - `encode(stream, format, image, compression, destName)` - 编码并写入流
  - `chooseFormat(name, forceLinear)` - 根据文件名选择格式
  - `chooseExtension(format)` - 获取格式对应的文件扩展名
  - `Format` 枚举 - PNG, PNG_LINEAR, HDR, RGBM, PSD, EXR, DDS, DDS_LINEAR, RGB_10_11_11_REV
- **`PNGEncoder`** - PNG 编码器，支持灰度/RGB/RGBA 和多种像素格式
- **`HDREncoder`** - Radiance HDR 编码器，含 RLE 压缩实现
  - `float2rgbe()` - 浮点到 RGBE 编码
  - `rle()` - 按通道平面进行 RLE 压缩
- **`PSDEncoder`** - Photoshop PSD 编码器（支持 16/32 位）
- **`EXREncoder`** - OpenEXR 编码器（float 转 half，支持多种压缩：PIZ/ZIP/ZIPS/RAW）
- **`DDSEncoder`** - DDS 编码器（DX10 扩展头，支持多种 DXGI 格式）

## 关键实现逻辑

- HDR 编码：8 像素以下或超过 32767 宽度不使用 RLE；RLE 分 4 个通道平面分别压缩
- PSD 编码：32 位模式包含 Photoshop HDR 色调映射数据（undocumented）
- DDS 编码：使用 DX10 扩展头（DDS_FOURCC_DX10），自动选择 DXGI 格式
- sRGB 输出使用 `fromLinearTosRGB` 转换，线性输出使用 `fromLinearToRGB`

## 依赖关系

- `image/ColorTransform.h` - 颜色空间转换
- `png.h` - libpng 库
- `tinyexr.h` - EXR 编码库
- `math/half.h`, `math/vec3.h`, `math/vec4.h` - 数学类型
