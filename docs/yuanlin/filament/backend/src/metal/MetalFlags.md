# MetalFlags

## 文件概述

MetalFlags 定义了 Metal 后端的编译期特性标志。目前仅包含调试标签控制宏。

**源文件**: `MetalFlags.h`（仅头文件）

## 核心类/结构体

无类定义。

## 关键实现逻辑

- **`FILAMENT_METAL_DEBUG_LABELS`** - 控制是否为 Metal 资源（缓冲区、纹理等）设置调试标签
  - Debug 构建默认启用（值为 1）
  - Release 构建默认禁用（值为 0）
  - 可通过预定义宏覆盖默认值

## 依赖关系

无外部依赖。
