# ImageDiffer.h / ImageDiffer.cpp

## 文件概述

图像比较工具，主要用于单元测试中的 "golden image" 比较。提供 UPDATE（生成基准图像）和 COMPARE（与基准比较）两种模式。

## 核心类/结构体/函数

- **`ComparisonMode`** 枚举 - SKIP（跳过）、COMPARE（比较）、UPDATE（更新基准）
- **`updateOrCompare(result, golden, mode, epsilon)`** - 核心函数
  - UPDATE 模式：将结果图像编码为 PNG/RGBM 并写入文件
  - COMPARE 模式：加载基准图像，执行逐像素比较

## 关键实现逻辑

- UPDATE 时根据文件扩展名（.rgbm）选择编码格式
- 单通道图像在处理前先扩展为 3 通道（复制到 RGB）
- COMPARE 时如果基准是 RGBM 格式（4 通道），先转换为线性 RGB
- 使用 `image::compare()` 进行带容差的字典序比较

## 依赖关系

- `image/ColorTransform.h`, `image/ImageOps.h` - 图像转换和操作
- `imageio/ImageDecoder.h`, `imageio/ImageEncoder.h` - 图像编解码
- `utils/Path.h` - 文件路径处理
- `utils/Panic.h` - 断言
