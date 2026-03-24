# MetalShaderCompiler

## 文件概述

MetalShaderCompiler 负责 Metal 着色器的编译和管理，支持同步和异步两种编译模式。异步模式使用独立线程池在后台编译着色器，避免阻塞渲染线程。支持 MSL 源码和预编译 Metal Library 两种输入格式。

**源文件**: `MetalShaderCompiler.h`, `MetalShaderCompiler.mm`

## 核心类/结构体

- **`MetalShaderCompiler`** - 着色器编译器主类
  - `Mode` 枚举: `SYNCHRONOUS` / `ASYNCHRONOUS`
  - `createProgram()` - 创建着色器程序（同步或异步）
  - `getProgram(token)` - 获取编译结果，必要时阻塞等待
  - `notifyWhenAllProgramsAreReady()` - 所有程序就绪后触发回调
- **`MetalFunctionBundle`** - 编译结果封装，使用 `std::variant` 表示四种状态：
  - `Raster` - 光栅化程序（顶点 + 片段函数）
  - `Compute` - 计算程序（计算函数）
  - `None` - 空（未编译）
  - `Error` - 编译失败（错误消息 + 程序名）
- **`MetalProgramToken`** - 异步编译令牌，内含条件变量用于等待编译完成

## 关键实现逻辑

- **异步编译**: 使用 `CompilerThreadPool`（单线程）后台编译，通过 `program_token_t`（shared_ptr）传递结果。`getProgram()` 时如果结果未就绪，先尝试从队列中取出任务在当前线程执行（work stealing），否则阻塞等待
- **MSL 编译**: 使用 `newLibraryWithSource` + `MTLCompileOptions`（启用 fastMath），通过 `newFunctionWithName:@"main0"` 获取入口函数
- **Metal Library 加载**: 预编译的 metallib 通过 `newLibraryWithData` 直接加载
- **特化常量**: 通过 `MTLFunctionConstantValues` 传递 int/float/bool 类型的特化常量
- **错误处理**: 编译失败时返回 `Error` 变体，`validate()` 方法在绘制时抛出 NSException 提供详细错误信息
- **回调管理**: `CallbackManager` 跟踪所有待编译程序，全部完成后触发通知

## 依赖关系

- `CompilerThreadPool.h` - 编译线程池
- `CallbackManager.h` - 异步回调管理
- `backend/Program.h` - 着色器程序数据
- `MetalDriver.h` - 驱动引用
- `Metal/Metal.h` - MTLLibrary、MTLFunction、MTLDevice
