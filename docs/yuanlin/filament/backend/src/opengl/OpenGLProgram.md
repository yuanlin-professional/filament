# OpenGLProgram.h / OpenGLProgram.cpp

## 文件概述

实现 OpenGL 后端的着色器程序管理，封装 GL 程序对象的创建、初始化、绑定映射以及 ES2 下的 uniform 模拟。

## 核心类/结构体

### `PushConstantBundle`
存储顶点和片段阶段的推送常量（push constant）位置和类型信息。

### `OpenGLProgram`
继承自 `HwProgram`，大小限制在 96 字节以内。

- `mToken`: 编译器服务的程序令牌，用于延迟初始化。
- `mBindingMap`: 描述符绑定映射。
- `gl.program`: GL 程序 ID。
- `mPushConstants`: 推送常量位置和类型列表。
- `mUniformsRecords`: ES2 uniform 模拟记录。

### 关键方法
- `use()`: 延迟初始化模式 -- 首次使用时才从编译器服务获取程序。
- `initialize()`: 从编译器获取 GL 程序 ID，初始化程序状态。
- `initializeProgramState()`: 设置 UBO 绑定（`glUniformBlockBinding`）、采样器纹理单元（`glUniform1i`）、ES2 uniform 位置查询。
- `updateUniforms()`: ES2 专用，将 UBO 数据拆解为独立 `glUniform*` 调用。
- `setRec709ColorSpace()`: ES2 sRGB 颜色空间模拟。

## 关键实现逻辑

- **延迟初始化**: 程序在首次 `use()` 时才执行 `initialize()`，支持异步编译。
- **绑定映射构建**: 遍历描述符集描述，为每个 UBO 分配绑定点，为每个采样器分配纹理单元。
- **ES2 uniform 模拟**: 使用 `UniformsRecord` 缓存每个 uniform 的位置，按 UBO 索引分组，通过 age/offset 跟踪避免重复更新。
- **std140 到 packed mat3 转换**: ES2 下 mat3 uniform 需要从 std140 布局转换为紧凑布局。

## 依赖关系

- `BindingMap.h`, `ShaderCompilerService.h`, `OpenGLContext.h`
- `backend/DriverEnums.h`, `backend/Program.h`
- `utils/FixedCapacityVector.h`, `utils/Slice.h`
