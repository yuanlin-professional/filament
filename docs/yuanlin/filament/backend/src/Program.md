# Program

## 文件概述

`Program.cpp` 实现了 `Program` 类的构建器方法。`Program` 是着色器程序的描述对象，采用流式（builder）模式配置着色器源码、描述符绑定、特化常量等参数，最终提交给后端驱动进行编译。

## 核心类/结构体

### `Program`
- 头文件位于 `backend/Program.h`（公共头文件）。
- 所有设置方法返回 `Program&`，支持链式调用。
- **priorityQueue()**: 设置编译优先级队列（CRITICAL / HIGH / LOW）。
- **diagnostics()**: 设置程序名称和诊断日志回调。
- **shader()**: 设置指定阶段（顶点/片元/计算）的着色器二进制数据，内部复制到 `ShaderBlob`。
- **shaderLanguage()**: 设置着色器语言（ESSL3、SPIRV、MSL、WGSL 等）。
- **descriptorBindings()**: 设置指定描述符集的绑定信息。
- **uniforms()**: 添加 uniform 绑定信息（索引、名称、uniform 数组）。
- **attributes()**: 设置顶点属性信息。
- **specializationConstants()**: 设置特化常量。
- **pushConstants()**: 设置指定着色器阶段的推送常量。
- **cacheId()**: 设置缓存 ID，用于 blob 缓存查找。
- **multiview()**: 设置是否启用多视图渲染。

## 关键实现逻辑

- 构造函数、移动构造/赋值、析构均在 .cpp 文件中定义（非内联），因为 Program 对象较大，不值得内联。
- `shader()` 方法会复制传入的数据到 `ShaderBlob`（`std::vector<uint8_t>`），确保 Program 拥有数据的完整副本。
- `operator<<` 通过 `mLogger` 回调输出诊断信息。

## 依赖关系

- `backend/Program.h` - 类声明
- `backend/DriverEnums.h` - 着色器阶段、编译优先级等枚举
- `utils::CString` / `utils::Invocable` - 字符串和回调
- `utils::ostream` - 流输出支持
