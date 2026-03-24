# GLTexture.h

## 文件概述

定义 OpenGL 后端的纹理数据结构，是 Filament `HwTexture` 的 GL 特化实现，包含纹理 ID、格式、LOD 信息和纹理视图引用。

## 核心类/结构体

### `GLTextureRef`
纹理视图引用计数对象，跟踪纹理视图的当前状态：
- `count`: 引用计数。
- `baseLevel` / `maxLevel`: 当前视图活跃的 mipmap 范围。
- `swizzle`: 当前通道重映射（swizzle）状态。

### `GLTexture`
继承自 `HwTexture`：
- `gl.id`: GL 纹理或渲染缓冲区 ID。
- `gl.target`: GL 纹理目标类型（GL_TEXTURE_2D 等）。
- `gl.internalFormat`: 内部格式。
- `gl.sidecarRenderBufferMS`: 多重采样附带渲染缓冲区。
- `gl.imported` / `gl.external`: 标识是否为外部导入/外部纹理。
- `gl.baseLevel` / `gl.maxLevel` / `gl.swizzle`: 纹理参数。
- `ref`: 指向 `GLTextureRef` 的句柄，用于视图管理。
- `externalTexture`: 外部纹理平台对象指针。

## 关键实现逻辑

- 将 mipmap 级别和 swizzle 状态分离到 `GLTextureRef` 中，以支持纹理视图的惰性更新。
- 位域压缩 `imported`（1 bit）、`external`（1 bit）和 `sidecarSamples`（3 bit）。

## 依赖关系

- `DriverBase.h` - 基类 `HwTexture`
- `gl_headers.h` - OpenGL 类型
- `backend/Handle.h`, `backend/DriverEnums.h`
- `backend/platforms/OpenGLPlatform.h` - `ExternalTexture` 类型
