# BackendUtils

## 文件概述

`BackendUtils.cpp` 提供了后端通用的纹理格式工具函数和像素缓冲区数据重塑功能。这些函数被各图形 API 后端（OpenGL、Vulkan、Metal 等）共享使用，是后端基础设施的核心工具集。

## 核心类/结构体

本文件不定义类，而是在 `filament::backend` 命名空间中提供一组独立函数，以及在 `utils` 命名空间中提供 `to_string` 模板特化。

## 关键实现逻辑

### 纹理格式工具
- **getFormatSize()**: 返回纹理格式每个元素的字节大小，覆盖全部非压缩格式（1~16 字节）和压缩格式（ETC2、DXT、ASTC、BPTC、RGTC）。
- **getTextureType()**: 判断纹理格式类型（FLOAT、UINT、INT、DEPTH、STENCIL、DEPTH_STENCIL）。
- **getFormatComponentCount()**: 返回纹理格式的通道数（1~4），包括压缩格式。
- **getBlockWidth()** / **getBlockHeight()**: 返回压缩纹理的块宽度/高度，ASTC 格式有多种块大小。

### Shader 处理
- **requestsGoogleLineDirectivesExtension()**: 检查 shader 源码是否请求 Google 行指令扩展。
- **removeGoogleLineDirectives()**: 从 shader 源码中移除引号内的文件名（用空格替换）。

### 像素数据重塑
- **reshape()**: 将 3 通道（RGB）像素缓冲区扩展为 4 通道（RGBA），支持 UBYTE、USHORT、UINT、FLOAT 等数据类型。使用 `DataReshaper` 模板类完成实际数据变换。

### 字符串转换
- `to_string<TextureUsage>`: 将纹理使用标志转换为 6 字符缩写字符串。
- `to_string<TargetBufferFlags>`: 将渲染目标缓冲区标志转换为缩写字符串。
- `to_string<TextureFormat>`: 将纹理格式枚举转换为名称字符串。

## 依赖关系

- `private/backend/BackendUtils.h` - 头文件声明
- `DataReshaper.h` - 像素数据通道重塑模板
- `backend/PixelBufferDescriptor.h` / `backend/DriverEnums.h` - 像素缓冲区和枚举定义
- `utils::BitmaskEnum` / `utils::CString` - 位掩码枚举和字符串工具
