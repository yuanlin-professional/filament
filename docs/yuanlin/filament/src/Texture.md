# Texture.cpp

## 文件概述
Texture.cpp 实现了纹理的公共 API。Texture 是 Filament 中最核心的资源类型之一，支持 2D、3D、立方体贴图、数组纹理等多种目标类型。

## 核心类/结构体/函数
- **Texture** - 纹理公共接口
  - `getWidth()` / `getHeight()` / `getDepth()` / `getLevels()` - 尺寸和级别查询
  - `getTarget()` / `getFormat()` - 采样器类型和内部格式查询
  - `setImage()` - 上传纹理数据（支持 2D 子区域和立方体贴图面偏移）
  - `setImageAsync()` - 异步纹理上传，返回 AsyncCallId
  - `setExternalImage()` - 绑定外部图像（平台特定）
  - `setExternalStream()` - 绑定外部视频流
  - `generateMipmaps()` - 生成 Mipmap
  - `isCreationComplete()` - 检查纹理是否创建完成
  - 静态方法：`isTextureFormatSupported()` / `isTextureFormatMipmappable()` / `isTextureFormatCompressed()` / `isProtectedTexturesSupported()` / `isTextureSwizzleSupported()`
  - `computeTextureDataSize()` / `validatePixelFormatAndType()` - 数据大小计算和格式验证
  - `getMaxTextureSize()` / `getMaxArrayTextureLayers()` - 硬件限制查询

## 关键实现逻辑
通过 `downcast` 委托给内部 `FTexture`。支持同步和异步两种纹理上传模式。静态查询方法直接调用 FTexture 的静态方法。

## 依赖关系
- `details/Texture.h` / `details/Engine.h` / `details/Stream.h` - 内部实现

## 被引用关系
- `MaterialInstance` - 绑定纹理到材质参数
- `Skybox` - 获取天空盒纹理
- `RenderTarget` - 获取附件纹理
