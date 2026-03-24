# FrameGraphTexture

## 文件概述

`FrameGraphTexture.h` / `FrameGraphTexture.cpp` 定义了帧图中最常用的资源类型 -- 纹理资源，包括其描述符、子资源描述符和创建/销毁逻辑。

## 核心类/结构体

- **`FrameGraphTexture`**: 帧图纹理资源。
  - `handle`: 后端纹理句柄 (`Handle<HwTexture>`)
  - **`Descriptor`**: 纹理描述符，包含宽度、高度、深度、mip 级数、采样数、采样器类型（2D/3D/Cube等）、格式和 swizzle 通道映射。
  - **`SubResourceDescriptor`**: 子资源描述符，指定 mip level 和 layer/face。
  - **`Usage`**: 等同于 `backend::TextureUsage`，默认读取用法为 `SAMPLEABLE`，默认写入用法为 `COLOR_ATTACHMENT`。

## 关键实现逻辑

- **创建 (`create`)**: 通过 `TextureCacheInterface` 创建纹理，传递描述符中的所有参数和使用标志。纹理缓存可以复用具有相同参数的纹理以减少分配。
- **销毁 (`destroy`)**: 如果句柄有效，通过纹理缓存销毁（实际可能延迟回收）。
- **子资源描述符生成 (`generateSubResourceDescriptor`)**: 根据父描述符和子资源描述符计算子资源的实际尺寸：`width = max(1, width >> level)`，`height` 类似，`levels` 设为 1。

## 依赖关系

- `TextureCache.h` - 纹理缓存接口（`TextureCacheInterface`）
- `utils/StaticString.h` - 静态字符串
- `backend/DriverEnums.h`, `backend/Handle.h` - 后端枚举和句柄类型
