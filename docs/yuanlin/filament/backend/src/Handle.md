# Handle

## 文件概述

`Handle.cpp` 为 Filament 后端的类型安全句柄 `Handle<T>` 提供调试模式下的流输出操作符实现。仅在 `NDEBUG` 未定义（即调试构建）时编译，用于日志和诊断输出。

## 核心类/结构体

### `Handle<T>`（实例化）
- 头文件位于 `backend/Handle.h`（公共头文件）。
- `operator<<` 模板函数：将句柄输出为 "类型名 @ ID" 格式，其中类型名会移除 `filament::backend::` 前缀以提高可读性。

## 关键实现逻辑

- 使用 `CallStack::typeName<Handle<T>>()` 获取编译器生成的类型名。
- `removeAll()` 辅助函数移除类型名中的命名空间前缀 `filament::backend::`。
- 显式实例化所有硬件资源句柄类型的 `operator<<`，确保它们在动态库中可用：
  - HwVertexBuffer、HwIndexBuffer、HwRenderPrimitive、HwProgram
  - HwTexture、HwRenderTarget、HwFence、HwSwapChain
  - HwStream、HwTimerQuery、HwBufferObject
  - HwDescriptorSet、HwDescriptorSetLayout、HwVertexBufferInfo

## 依赖关系

- `backend/Handle.h` - Handle 模板类定义
- `utils::CallStack` - RTTI 类型名获取
- `utils::CString` - 字符串替换操作
- `utils::ostream` - 输出流
