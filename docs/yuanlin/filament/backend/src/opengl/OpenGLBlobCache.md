# OpenGLBlobCache.h / OpenGLBlobCache.cpp

## 文件概述

实现 OpenGL 程序二进制缓存（Program Binary Cache），通过平台提供的 Blob 存储接口实现着色器程序的序列化和反序列化，避免重复编译以加速启动。

## 核心类/结构体

### `OpenGLBlobCache`
- `mCachingSupported`: 根据 `GL_NUM_PROGRAM_BINARY_FORMATS` 判断是否支持程序二进制。

### 关键方法
- `retrieve()`: 从缓存中检索程序二进制，使用 `glProgramBinary` 恢复程序。如果恢复失败（例如驱动更新后），返回 0 以回退到正常编译。
- `insert()`: 使用 `glGetProgramBinary` 获取程序二进制并通过平台接口存储到缓存。

### `Blob`
内部结构体，存储二进制格式（GLenum）和数据（柔性数组成员）。

## 关键实现逻辑

- 检索时先尝试 64KB 的默认缓冲区，不够则按实际大小重新分配。
- 验证 `glProgramBinary` 后的 `GL_LINK_STATUS`，处理驱动更新导致二进制失效的情况。
- 使用 `BlobCacheKey`（程序缓存 ID + 特化常量）作为缓存键。

## 依赖关系

- `OpenGLContext.h` - GL 能力查询
- `BlobCacheKey.h` - 缓存键
- `backend/Platform.h`, `backend/Program.h`
- `utils/FixedCapacityVector.h`
