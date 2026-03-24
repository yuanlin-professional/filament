# RenderTarget.cpp

## 文件概述
RenderTarget.cpp 实现了渲染目标的公共 API。RenderTarget 封装了一组纹理附件（颜色、深度等），可作为离屏渲染的输出目标。

## 核心类/结构体/函数
- **RenderTarget** - 渲染目标公共接口
  - `getTexture()` - 获取指定附件点的纹理
  - `getMipLevel()` - 获取附件的 Mip 级别
  - `getFace()` - 获取立方体贴图面
  - `getLayer()` - 获取纹理层
  - `getSupportedColorAttachmentsCount()` - 获取支持的颜色附件数量

## 关键实现逻辑
所有方法通过 `downcast` 委托给内部 `FRenderTarget`，通过 `getAttachment(attachment)` 获取对应附件点的信息。

## 依赖关系
- `details/RenderTarget.h` - 内部实现
- `details/Texture.h` - 纹理类型

## 被引用关系
- 用户层代码通过 RenderTarget 配置离屏渲染
- View 可设置 RenderTarget 实现离屏渲染
