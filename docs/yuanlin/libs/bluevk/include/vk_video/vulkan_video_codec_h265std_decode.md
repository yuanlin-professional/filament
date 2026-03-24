# vulkan_video_codec_h265std_decode.h

## 文件概述

`vulkan_video_codec_h265std_decode.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 H.265 视频解码专用定义头文件，定义了 Vulkan H.265 解码操作所需的额外参数结构体。

## 核心类/结构体/函数

### 结构体
- **`StdVideoDecodeH265PictureInfo`**: H.265 解码图像信息，包含 POC、参考图像集等
- **`StdVideoDecodeH265ReferenceInfo`**: 参考帧信息
- **`StdVideoDecodeH265PictureInfoFlags`**: 解码图像标志位

## 关键实现逻辑

为硬件 H.265 解码器提供帧级元数据。H.265 的参考图像管理比 H.264 更加灵活，采用参考图像集（RPS）机制替代了 H.264 的参考图像列表。

## 依赖关系

- `vulkan_video_codec_h265std.h` -- H.265 基础定义
- `<stdint.h>` -- 标准整数类型
