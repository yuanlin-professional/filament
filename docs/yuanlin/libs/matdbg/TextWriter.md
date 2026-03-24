# TextWriter

## 文件概述

`TextWriter` 将材质包信息序列化为格式化的纯文本输出，主要用于 matinfo 命令行工具的控制台显示。与 `JsonWriter` 类似但输出为人类友好的表格格式。

**源文件**: `include/matdbg/TextWriter.h`, `src/TextWriter.cpp`

## 核心类/结构体

### `TextWriter`
- **`writeMaterialInfo(package)`**: 生成材质的完整文本描述
- **`getString()` / `getSize()`**: 获取生成的文本

## 关键实现逻辑

输出分为多个区段，每个区段由对应函数生成：

1. **`printMaterial`**: 输出版本、特性级别、名称、编译参数、着色模型、顶点域、插值方式等
2. **`printParametersInfo`**: 输出 Uniform 参数表（字段名、类型、精度、关联采样器）和 Sampler 参数表（名称、绑定点、类型、格式、精度、可过滤性、多重采样）
3. **`printDescriptorSetLayout`**: 输出描述符集布局（类型、阶段、绑定点、标志）
4. **`printConstantInfo`**: 输出编译时常量（名称、类型）
5. **`printSubpassesInfo`**: 输出 Subpass 参数
6. **`printShaderInfo`**: 输出各后端的着色器变体列表（编号、模型、阶段、变体位掩码和可读字符串）
7. **`printChunks`**: 输出材质包中所有 Chunk 的名称和大小

所有表格使用 `std::setw` 对齐，Chunk 类型名通过 `typeToString` 将 64 位整数解包为 8 字符字符串。

## 依赖关系

- `CommonWriter.h` -- read() 辅助函数和 toString() 枚举转换
- `matdbg/ShaderInfo.h` -- 着色器信息查询
- `filaflat/ChunkContainer.h`, `filaflat/Unflattener.h` -- 数据读取
- `filament/MaterialEnums.h`, `backend/DriverEnums.h` -- 枚举类型
