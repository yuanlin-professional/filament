# RenderTarget.h

## 文件概述

定义离屏渲染目标，可关联到 View 以将渲染结果输出到纹理而非屏幕，支持多颜色附件和深度附件。

## 核心类/结构体

- **`RenderTarget`** - 离屏渲染目标，继承自 `FilamentAPI`。
- **`RenderTarget::AttachmentPoint`** - 枚举：`COLOR0`~`COLOR7`、`DEPTH`。
- **`RenderTarget::Builder`** - 构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::texture(attachment, texture)` | 设置附件纹理 |
| `Builder::mipLevel(attachment, level)` | 设置 mip 级别 |
| `Builder::face(attachment, face)` | 设置立方体贴图面 |
| `Builder::layer(attachment, layer)` | 设置纹理层 |
| `Builder::multiview(attachment, layerCount, baseLayer)` | 设置多视图 |
| `Builder::samples(n)` | 设置 MSAA 采样数 |
| `Builder::build(engine)` | 创建 RenderTarget |
| `getTexture(attachment)` | 获取附件纹理 |
| `getMipLevel(attachment)` | 获取附件的 mip 级别 |
| `getSupportedColorAttachmentsCount()` | 获取支持的颜色附件数量 |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/DriverEnums.h`, `backend/TargetBufferInfo.h`
