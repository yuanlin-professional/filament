# RenderTarget

## 文件概述

`FRenderTarget` 是 `RenderTarget` 公共 API 的私有实现类，封装了离屏渲染目标（FBO）。支持多个颜色附件和一个深度附件，每个附件可指定纹理、mip 级别、立方体贴图面和图层，支持多视图渲染（Multiview）。

## 核心类/结构体

### `FRenderTarget`
- 继承自 `RenderTarget`
- 持有后端渲染目标句柄 `HwHandle`
- `Attachment` 结构体描述每个附件：纹理指针、mip 级别、立方体贴图面、图层、多视图图层数
- `ATTACHMENT_COUNT = MAX_SUPPORTED_COLOR_ATTACHMENTS_COUNT + 1`（颜色 + 深度）

## 关键实现逻辑

- **附件掩码** -- `mAttachmentMask` 记录已配置的附件位，`mSampleableAttachmentsMask` 记录可采样的附件
- **深度采样** -- `hasSampleableDepth()` 检查深度附件是否可在着色器中采样
- **readPixels 支持** -- `mSupportsReadPixels` 标志指示该渲染目标是否支持像素回读

## 依赖关系

- `details/Texture.h` -- 纹理对象引用
- `details/Engine.h` -- 引擎驱动 API
- `backend/Handle.h` -- 后端句柄

## 与公共 API 的关系

直接对应 `filament::RenderTarget`。通过 `RenderTarget::Builder` 创建并设置附件，绑定到 `View` 上实现离屏渲染。
