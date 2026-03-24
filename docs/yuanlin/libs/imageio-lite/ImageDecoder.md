# ImageDecoder.h / ImageDecoder.cpp (imageio-lite)

## 文件概述

轻量级图像解码库，属于 `imageio-lite` 命名空间。仅支持 TIFF 格式的解码，设计目标是最少依赖，适用于不需要完整 imageio 库的场景。

## 核心类/结构体/函数

- **`imageio_lite::ImageDecoder`** - 轻量解码器
  - `decode(stream, sourceName, sourceSpace)` - 自动检测格式并解码为 `LinearImage`
  - `ColorSpace` 枚举 - LINEAR / SRGB
- **`TIFFDecoder`** - TIFF 格式解码器（内部类）
  - `checkSignature(buf)` - 检查 "II\x2a\x00"（小端）或 "MM\x00\x2a"（大端）签名
  - `decode()` - 完整的 TIFF IFD 解析和像素数据读取

## 关键实现逻辑

- 支持大端和小端 TIFF 文件
- 解析 IFD（Image File Directory）条目：宽高、压缩方式、色彩模型、采样位数、strip 信息等
- 仅支持：无压缩、RGB 色彩模型（3 或 4 采样/像素）、8 位/采样、Chunky 平面配置
- Strip 数据组装：遍历所有 strip 偏移量，按顺序读取并拼装完整图像
- 根据 ColorSpace 设置选择 sRGB 到线性转换或直接输出
- 包含全面的整数溢出和数据一致性检查

## 依赖关系

- `image/ColorTransform.h`, `image/ImageOps.h` - 颜色转换
- `math/vec3.h`, `math/vec4.h` - 数学向量
- `utils/CString.h`, `utils/Panic.h` - 工具库
