# ImageEncoder.h / ImageEncoder.cpp (imageio-lite)

## 文件概述

轻量级图像编码库，属于 `imageio-lite` 命名空间。仅支持 TIFF 格式编码，将 `LinearImage` 编码为 8 位 sRGB RGBA 的无压缩 TIFF 文件。

## 核心类/结构体/函数

- **`imageio_lite::ImageEncoder`** - 轻量编码器
  - `encode(stream, format, image, compression, destName)` - 编码并写入流
  - `chooseFormat(name, forceLinear)` - 根据文件名选择格式（仅 TIFF）
  - `chooseExtension(format)` - 返回 ".tif"
  - `Format` 枚举 - 仅 TIFF
- **`TIFFEncoder`** - TIFF 编码器（内部类）
  - `encode(image)` - 将 LinearImage 编码为 TIFF

## 关键实现逻辑

- 输出格式：小端 TIFF，8 位/通道，4 通道 RGBA，无压缩，sRGB 色彩空间
- 像素转换：线性浮点通过 `fromLinearTosRGB` 转为 8 位 sRGB，不足 4 通道时填充 alpha=255
- Strip 机制：按推荐最大 8KB strip 大小分割行数据
- IFD 构建：两阶段构建 -- 先空运行计算大小，再实际写入
  - 第一阶段计算偏移量（bitsPerSample、分辨率、strip 偏移/大小的存放位置）
  - 第二阶段填入实际值并按 tag 排序
- 单通道图像扩展为灰度 RGB+A=255

## 依赖关系

- `image/ColorTransform.h` - 线性到 sRGB 转换
- `math/vec3.h`, `math/vec4.h` - 数学类型
- `utils/CString.h`, `utils/Panic.h`, `utils/compiler.h` - 工具库
