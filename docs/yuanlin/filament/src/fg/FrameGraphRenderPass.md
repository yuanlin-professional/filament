# FrameGraphRenderPass

## 文件概述

`FrameGraphRenderPass.h` 定义了帧图中渲染通道的描述结构，用于描述渲染目标的附件（颜色、深度、模板）及其属性。

## 核心类/结构体

- **`FrameGraphRenderPass`**: 渲染通道描述。
  - **`Attachments`**: 附件集合，包含最多 `MAX_SUPPORTED_RENDER_TARGET_COUNT` 个颜色附件，以及深度和模板附件。提供 `operator[]` 索引访问。
  - **`Descriptor`**: 渲染通道描述符，包含附件列表、视口、清除颜色、采样数、图层数（用于多视图渲染）、清除标志和丢弃标志。
  - **`ImportDescriptor`**: 导入渲染目标的描述符，包含附件标志、视口、清除颜色、采样数、清除标志和保持覆盖标志（`keepOverrideStart`/`keepOverrideEnd`）。
  - `ATTACHMENT_COUNT = MRT::MAX_SUPPORTED_RENDER_TARGET_COUNT + 2`（颜色 + 深度 + 模板）

## 关键实现逻辑

- `Attachments` 结构通过下标运算符统一访问颜色、深度和模板附件。
- `layerCount > 1` 时启用多视图渲染（multiview）。
- `samples = 0` 表示使用默认采样数。

## 依赖关系

- `fg/FrameGraphTexture.h` - `FrameGraphId<FrameGraphTexture>` 类型
- `backend/DriverEnums.h` - `TargetBufferFlags` 等枚举
- `backend/TargetBufferInfo.h` - MRT 常量定义
- `filament/Viewport.h` - 视口定义
