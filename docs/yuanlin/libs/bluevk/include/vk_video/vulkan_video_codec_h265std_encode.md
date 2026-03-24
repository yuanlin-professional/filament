# vulkan_video_codec_h265std_encode.h

## 文件概述

`vulkan_video_codec_h265std_encode.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 H.265 视频编码专用定义头文件，定义了 Vulkan H.265 编码操作所需的参数结构体。

## 核心类/结构体/函数

### 结构体
- **`StdVideoEncodeH265SliceSegmentHeader`**: 编码 Slice Segment 头信息
- **`StdVideoEncodeH265PictureInfo`**: 编码图像信息
- **`StdVideoEncodeH265ReferenceInfo`**: 编码参考帧信息
- **`StdVideoEncodeH265ReferenceModifications`**: 参考图像列表修改

## 关键实现逻辑

为 H.265 编码器提供帧级和 Slice Segment 级的编码参数。H.265 使用 Slice Segment 替代了 H.264 的 Slice 概念，支持更细粒度的并行编码。

## 依赖关系

- `vulkan_video_codec_h265std.h` -- H.265 基础定义
- `<stdint.h>` -- 标准整数类型
