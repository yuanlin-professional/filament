# ImageDiff.h / ImageDiff.cpp

## 文件概述

图像差异比较库，支持浮点 `LinearImage` 和 8 位 `Bitmap` 两种输入格式。提供可配置的多通道阈值比较，支持递归的 AND/OR 逻辑组合，以及可选的遮罩（mask）、差异图像生成和 JSON 序列化。

## 核心类/结构体/函数

- **`Channel`** 枚举 - R/G/B/A/RGB/RGBA 通道选择位掩码
- **`ImageDiffConfig`** - 比较配置（递归树形结构）
  - `Mode` - LEAF（叶节点，执行实际比较）、AND、OR（逻辑组合子节点）
  - `Swizzle` - RGBA / BGRA 像素布局
  - `channelMask` - 要检查的通道
  - `maxAbsDiff` - 允许的最大绝对差异
  - `maxFailingPixelsFraction` - 允许失败的像素比例
  - `children` - AND/OR 模式的子配置
- **`ImageDiffResult`** - 比较结果
  - `Status` - PASSED / SIZE_MISMATCH / PIXEL_DIFFERENCE
  - `failingPixelCount` - 失败像素数
  - `maxDiffFound[4]` - 各通道最大差异
  - `diffImage` / `maskImage` - 可选的差异和遮罩可视化图像
- **`Bitmap`** - 8 位 RGBA 原始缓冲区描述
- **`compare()`** - 两个重载：LinearImage 版本和 Bitmap 版本
- **`parseConfig(json, size, outConfig)`** - 从 JSON 解析配置
- **`serializeResult(result)`** - 将结果序列化为 JSON

## 关键实现逻辑

- 递归像素检查：`checkPixelRecursive()` 根据节点类型（LEAF/AND/OR）递归评估
- 遮罩支持：mask 值乘以差异值，0=忽略，1=严格检查
- 失败判定：失败像素比例超过 `maxFailingPixelsFraction` 才判定为 PIXEL_DIFFERENCE
- JSON 解析使用 JSMN（极简 JSON 解析器），支持递归深度限制（32 层）
- BGRA swizzle 在 8 位 Bitmap 版本中将 B/R 通道交换

## 依赖关系

- `image/LinearImage.h` - 浮点图像
- `utils/CString.h`, `utils/Log.h` - 工具库
- `jsmn.h` - JSON 解析器
