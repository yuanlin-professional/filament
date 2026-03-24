# ShaderCompilerService.h / ShaderCompilerService.cpp

## 文件概述

着色器编译服务，支持同步、异步（线程池或 `KHR_parallel_shader_compile` 扩展）三种模式的着色器编译和链接，并集成程序二进制缓存。

## 核心类/结构体

### `ShaderCompilerService`
核心编译服务类。

**三种编译模式 `Mode`**:
- `SYNCHRONOUS`: 同步编译，可按帧分摊（amortize）编译数量。
- `THREAD_POOL`: 使用共享 GL 上下文的后台线程池编译（最常用）。
- `ASYNCHRONOUS`: 使用 `KHR_parallel_shader_compile` 扩展。

### `OpenGLProgramToken`
内部令牌结构，跟踪编译任务的生命周期：
- 编译状态（`WAITING` / `LOADING` / `COMPLETED` / `CANCELED`）。
- GL 着色器和程序 ID。
- 条件变量和互斥锁（线程池模式下的同步机制）。
- 缓存键（BlobCacheKey）和缓存来源标记。

### 关键方法
- `init()`: 初始化编译模式和线程池。
- `createProgram()`: 提交编译任务。
- `getProgram()`: 获取编译完成的 GL 程序 ID，必要时阻塞等待。
- `tick()`: 每帧调用，推进异步编译任务。
- `terminate(token)`: 取消编译任务。

## 关键实现逻辑

- **线程池模式**: 线程池中的工作线程拥有共享 GL 上下文，可以直接调用 GL API 编译着色器。使用条件变量通知主线程任务完成。
- **同步分摊**: 每帧限制编译数量（默认 1 个），超出的排队等待，每 16 帧编译一次。
- **着色器源码处理**: 编译前对源码进行多项预处理：
  - 移除 `GOOGLE_cpp_style_line_directive`
  - 替换 multiview 的 `num_views` 值
  - 注入 `ARB_shading_language_packing` 和 `bitCount` 的 polyfill
  - 注入特化常量（specialization constants）的 `#define`
- **程序缓存集成**: 编译前先尝试从 BlobCache 检索，编译后存入缓存。
- **取消机制**: THREAD_POOL 模式使用原子状态机，支持在编译前或编译中取消任务。

## 依赖关系

- `OpenGLBlobCache.h`, `CompilerThreadPool.h`, `CallbackManager.h`
- `OpenGLDriver.h`, `OpenGLContext.h`
- `backend/Program.h`, `backend/DriverEnums.h`
- `utils/CString.h`, `utils/JobSystem.h`
