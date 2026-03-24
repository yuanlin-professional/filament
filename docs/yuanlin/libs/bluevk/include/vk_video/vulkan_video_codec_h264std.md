# vulkan_video_codec_h264std.h

## 文件概述

`vulkan_video_codec_h264std.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 H.264 (AVC) 视频编解码标准定义头文件。定义了 H.264 标准中的各种参数集、配置和标志位，供 Vulkan 视频解码/编码扩展使用。

## 核心类/结构体/函数

### 常量定义
- `STD_VIDEO_H264_CPB_CNT_LIST_SIZE` (32)
- `STD_VIDEO_H264_SCALING_LIST_4X4_NUM_LISTS` (6)
- `STD_VIDEO_H264_SCALING_LIST_8X8_NUM_LISTS` (6)
- `STD_VIDEO_H264_MAX_NUM_LIST_REF` (32)
- `STD_VIDEO_H264_MAX_CHROMA_PLANES` (2)

### 枚举类型
- **`StdVideoH264ChromaFormatIdc`**: 色度采样格式（Monochrome、420、422、444）
- **`StdVideoH264ProfileIdc`**: H.264 Profile（Baseline、Main、High 等）
- **`StdVideoH264LevelIdc`**: H.264 Level
- **`StdVideoH264SliceType`**: Slice 类型（P、B、I 等）
- **`StdVideoH264PictureType`**: 图像类型

### 结构体
- **`StdVideoH264SequenceParameterSet`**: SPS（序列参数集）
- **`StdVideoH264PictureParameterSet`**: PPS（图像参数集）
- **`StdVideoH264ScalingLists`**: 量化缩放列表

## 关键实现逻辑

严格遵循 ITU-T H.264/AVC 标准定义，将 H.264 比特流中的语法元素映射为 C 结构体，供 Vulkan 硬件视频解码器作为输入参数使用。

## 依赖关系

- `<stdint.h>` -- 标准整数类型
