# vulkan_video_codec_h264std_encode.h

## 文件概述

`vulkan_video_codec_h264std_encode.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 H.264 视频编码专用定义头文件，定义了 Vulkan H.264 编码操作所需的参数结构体。

## 核心类/结构体/函数

### 结构体
- **`StdVideoEncodeH264SliceHeader`**: 编码 Slice 头信息
- **`StdVideoEncodeH264PictureInfo`**: 编码图像信息
- **`StdVideoEncodeH264ReferenceInfo`**: 编码参考帧信息
- **`StdVideoEncodeH264RefPicMarkingEntry`**: 参考图像标记条目

## 关键实现逻辑

为 H.264 编码器提供帧级和 Slice 级的编码参数定义，这些参数控制编码器的 Slice 分割、参考帧管理和码率控制行为。

## 依赖关系

- `vulkan_video_codec_h264std.h` -- H.264 基础定义
- `<stdint.h>` -- 标准整数类型
