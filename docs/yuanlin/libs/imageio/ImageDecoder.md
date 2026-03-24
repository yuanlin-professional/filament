# ImageDecoder.h / ImageDecoder.cpp

## 文件概述

图像解码统一入口，通过文件签名自动识别格式（PNG、HDR、PSD、EXR），调用对应的解码器将图像数据转换为线性浮点 `LinearImage`。

## 核心类/结构体/函数

- **`ImageDecoder`** - 图像解码器统一接口
  - `decode(stream, sourceName, sourceSpace)` - 静态方法，自动检测格式并解码
  - `ColorSpace` 枚举 - LINEAR / SRGB
- **`ImageDecoder::Decoder`** - 解码器基类接口
  - `decode()` - 纯虚方法，返回 `LinearImage`
  - `getColorSpace()` / `setColorSpace()` - 颜色空间设置
- **`PNGDecoder`** - PNG 格式解码器（内部类，在 .cpp 中定义）
  - 支持调色板、灰度、透明度转换，16 位扩展
  - 根据 ColorSpace 设置选择 sRGB 到线性的转换
- **`PSDDecoder`** - Photoshop PSD 格式解码器
  - 仅支持 3 通道 RGB，16 位或 32 位深度，无压缩
- **`EXRDecoder`** - OpenEXR 格式解码器
  - 使用 tinyexr 库，从内存加载 RGBA 数据并提取 RGB

## 关键实现逻辑

- 格式检测：读取前 16 字节，依次尝试 PNG、HDR、PSD、EXR 的签名检查
- PNG 解码使用 libpng，支持 sRGB gamma 处理和 16 位扩展
- PSD 使用大端序存储，通过 `ntohl`/`ntohs` 进行字节序转换
- 所有解码器最终输出线性浮点数据

## 依赖关系

- `image/ColorTransform.h`, `image/ImageOps.h` - 颜色转换
- `imageio/HDRDecoder.h` - HDR 解码器
- `png.h` - libpng 库
- `tinyexr.h` - EXR 解码库
