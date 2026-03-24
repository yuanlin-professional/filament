# OpenGLDriver.h / OpenGLDriver.cpp

## 文件概述

OpenGLDriver 是 Filament OpenGL 后端的主驱动类，实现了完整的 Driver API 接口。负责 GL 资源的创建/销毁、渲染命令的分发、渲染目标管理、纹理上传、流式纹理等核心功能。

## 核心类/结构体

### `OpenGLDriver`
继承自 `OpenGLDriverBase`，标记为 `final`。

**内部 GL 资源类型**:
- `GLSwapChain`: 交换链，包含帧调度回调。
- `GLVertexBufferInfo` / `GLVertexBuffer`: 顶点缓冲区及其属性信息。
- `GLIndexBuffer`: 索引缓冲区。
- `GLRenderPrimitive`: 渲染图元（包含 VAO 状态）。
- `GLRenderTarget`: 渲染目标（FBO、颜色/深度/模板附件、多重采样解析）。
- `GLStream`: 流式纹理（用于视频帧等外部图像源）。
- `GLFence` / `GLSyncFence`: 同步围栏。

**核心成员**:
- `mPlatform`: 平台抽象引用。
- `mContext`: OpenGLContext 实例（GL 状态管理）。
- `mShaderCompilerService`: 着色器编译服务。
- `mHandleAllocator`: 句柄分配器。

### 关键方法
- `create()`: 静态工厂方法创建驱动实例。
- 通过 `DriverAPI.inc` 宏展开所有 Driver API 方法（资源创建/销毁/更新、渲染通道管理、绘制调用等）。
- `resolvePass()`: 多重采样解析。
- `clearWithRasterPipe()`: 光栅化管线清除。

## 关键实现逻辑

- 使用句柄分配器（HandleAllocator）管理所有 GL 资源的生命周期。
- 渲染通道（render pass）状态追踪：记录当前目标、参数和写入掩码。
- 描述符集绑定状态使用位图追踪无效集，按需更新。
- ES2 特有的 uniform 缓冲区模拟和 sRGB 颜色空间支持。
- 流式纹理管理：维护附加了流的纹理列表和待获取图像的流列表。

## 依赖关系

- `OpenGLContext.h`, `OpenGLDriverBase.h`, `ShaderCompilerService.h`
- `GLBufferObject.h`, `GLDescriptorSet.h`, `GLTexture.h`, `GLMemoryMappedBuffer.h`
- `backend/DriverEnums.h`, `backend/Program.h`, `backend/PipelineState.h`
- `private/backend/Driver.h`, `private/backend/HandleAllocator.h`
- `JobQueue.h` - 异步任务队列
