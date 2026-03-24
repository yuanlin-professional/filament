# vulkan_video_codec_h264std_decode.h

## 文件概述

`vulkan_video_codec_h264std_decode.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 H.264 视频解码专用定义头文件，定义了 Vulkan H.264 解码操作所需的额外参数结构体。

## 核心类/结构体/函数

### 结构体
- **`StdVideoDecodeH264PictureInfo`**: H.264 解码图像信息，包含帧序号、POC（Picture Order Count）等
- **`StdVideoDecodeH264ReferenceInfo`**: 参考帧信息
- **`StdVideoDecodeH264PictureInfoFlags`**: 解码图像标志位（场编码/帧编码等）

## 关键实现逻辑

定义了解码器需要的帧级元数据，作为 `VkVideoDecodeH264PictureInfoKHR` 等 Vulkan 结构体的内嵌数据，驱动硬件解码器正确处理 H.264 比特流。

## 依赖关系

- `vulkan_video_codec_h264std.h` -- H.264 基础定义
- `<stdint.h>` -- 标准整数类型
