# vulkan_video_codecs_common.h

## 文件概述

`vulkan_video_codecs_common.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的视频编解码公共定义头文件，提供了所有 Vulkan 视频编解码扩展共用的基础宏和类型。

## 核心类/结构体/函数

### 宏定义
- **`VK_MAKE_VIDEO_STD_VERSION(major, minor, patch)`**: 将主版本号、次版本号和补丁号编码为 32 位整数。编码方式为：major 占高 10 位，minor 占中间 10 位，patch 占低 12 位。

### 符号
- **`vulkan_video_codecs_common`**: 定义为 1，标识此头文件已被包含。

## 关键实现逻辑

此文件非常精简，仅定义了版本号编码宏，供 H.264 和 H.265 编解码标准头文件使用，统一视频标准版本号的表示格式。

## 依赖关系

- 无外部头文件依赖
