# vulkan_video_codec_h265std.h

## 文件概述

`vulkan_video_codec_h265std.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 H.265 (HEVC) 视频编解码标准定义头文件，定义了 H.265 标准中的各种参数集、配置和标志位。

## 核心类/结构体/函数

### 常量定义
- H.265 缩放列表、参考图像列表等尺寸常量

### 枚举类型
- **`StdVideoH265ChromaFormatIdc`**: 色度采样格式
- **`StdVideoH265ProfileIdc`**: H.265 Profile（Main、Main10、Main Still Picture 等）
- **`StdVideoH265LevelIdc`**: H.265 Level
- **`StdVideoH265SliceType`**: Slice 类型

### 结构体
- **`StdVideoH265VideoParameterSet`**: VPS（视频参数集，H.265 新增）
- **`StdVideoH265SequenceParameterSet`**: SPS
- **`StdVideoH265PictureParameterSet`**: PPS
- **`StdVideoH265ScalingLists`**: 量化缩放列表

## 关键实现逻辑

遵循 ITU-T H.265/HEVC 标准，将 H.265 比特流语法元素映射为 C 结构体。H.265 相比 H.264 新增了 VPS 层级，支持更灵活的分层编码。

## 依赖关系

- `<stdint.h>` -- 标准整数类型
