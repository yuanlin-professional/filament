# BasisEncoder.h / BasisEncoder.cpp

## 文件概述

Basis Universal 纹理压缩编码器的封装，将 `LinearImage` 浮点图像数据编码为 KTX2 格式的 Basis 压缩纹理。支持 UASTC 和 ETC1S 两种中间格式，可处理立方体贴图、纹理数组和 mipmap。

## 核心类/结构体/函数

- **`BasisEncoder`** - 编码器主类
  - `encode()` - 触发所有 miplevel 的压缩，阻塞等待完成
  - `getKtx2ByteCount()` / `getKtx2Data()` - 获取生成的 KTX2 文件内容
- **`BasisEncoder::Builder`** - 编码器构建器（Builder 模式）
  - `Builder(mipCount, layerCount)` - 指定 mip 和层数
  - `linear(bool)` - 标记为线性（不施加传递函数）
  - `cubemap(bool)` - 立方体贴图模式
  - `intermediateFormat(UASTC/ETC1S)` - 选择中间格式
  - `grayscale(bool)` - 仅使用第一个通道
  - `normals(bool)` - 法线贴图模式（[-1,1] 转 [0,1]）
  - `jobs(count)` - 并行任务数
  - `miplevel(mipIndex, layerIndex, image)` - 提交各级图像数据
  - `build()` - 构建编码器

## 关键实现逻辑

- 浮点图像通过 `fromLinearTosRGB` 或 `fromLinearToRGB` 转换为 8 位整型后交给 BasisU
- sRGB 标记不影响压缩算法，仅作为 KTX2 文件中的注解
- 使用 Zstandard 超压缩（压缩级别 6）
- 法线贴图先通过 `vectorsToColors()` 从 [-1,1] 映射到 [0,1]

## 依赖关系

- `imageio/BasisEncoder.h` - 公共接口
- `image/ColorTransform.h`, `image/ImageOps.h` - 颜色转换和图像操作
- `basisu_comp.h` - Basis Universal 编码器核心库
