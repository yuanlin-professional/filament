# ParametersProcessor

## 文件概述

`ParametersProcessor` 负责将 JSON 格式的材质属性映射到 `MaterialBuilder` 的 API 调用。它是材质解析器的核心处理器，支持约 50 种材质属性（如 shadingModel、blending、parameters、constants 等）。

**源文件**: `src/ParametersProcessor.h`, `src/ParametersProcessor.cpp`

## 核心类/结构体

### `ParametersProcessor`
- **`process(builder, jsonObject)`**: 遍历 JSON 对象的所有键值对，查找处理回调并执行。先验证值类型是否与预期匹配
- **`process(builder, key, value)`**: 通过键值字符串对设置单个属性（用于命令行参数覆盖）。根据预期类型将字符串转换为 JsonishBool/JsonishNumber/JsonishString

### `ParameterInfo` 结构体
- `callback` -- 回调函数指针 `Status (*)(MaterialBuilder&, const JsonishValue&)`
- `rootAssert` -- 期望的 JSON 值类型

### 支持的属性处理函数（部分列举）
- `processName` -- 材质名称
- `processShading` -- 着色模型（lit/unlit/cloth/subsurface 等）
- `processBlending` -- 混合模式
- `processParameters` -- 统一变量参数数组
- `processConstants` -- 编译时常量数组
- `processBuffers` -- SSBO 缓冲区定义
- `processVariables` -- 自定义变量
- `processRequires` -- 必需的顶点属性
- `processVariantFilter` -- 变体过滤器
- `processOutputs` -- 后处理输出
- `processGroupSize` -- 计算着色器工作组大小

## 关键实现逻辑

**参数处理**: `processParameter` 解析单个参数定义（包含 type/name/precision/format/filterable 等字段），区分 Uniform 类型和 Sampler 类型采用不同的 builder API。

**数组大小提取**: `extractArraySize()` 从类型字符串（如 `"float[4]"`）中提取数组大小并修改类型字符串为基础类型。

**缓冲区处理**: `processBuffer` 构建 `BufferInterfaceBlock`，支持 SSBO 的 qualifiers（coherent/writeonly/readonly 等）和多种字段类型。

## 依赖关系

- `JsonishParser.h` -- JsonishObject、JsonishValue 等 AST 节点
- `filamat/MaterialBuilder.h` -- 材质构建目标
- `filamat/Enums.h` -- 枚举字符串转换
- `backend/DriverEnums.h` -- 后端枚举类型
- `private/filament/BufferInterfaceBlock.h` -- 缓冲区接口块
