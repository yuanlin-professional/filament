# Transcoder - 顶点属性数据格式转换器

## 文件概述

提供一个函数对象，能将各种顶点属性数据格式（BYTE、UBYTE、SHORT、USHORT、HALF、FLOAT，可选归一化）转换为紧密排列的 32 位浮点数组。特别适用于 3 分量格式（如 short3），因为某些后端（如 Vulkan）不支持这些格式。

**源文件**: `libs/geometry/include/geometry/Transcoder.h`, `libs/geometry/src/Transcoder.cpp`

## 核心类/结构体

### `ComponentType` 枚举
- `BYTE`: 有符号字节，归一化时映射 [-127, 127] -> [-1, +1]
- `UBYTE`: 无符号字节，归一化时映射 [0, 255] -> [0, +1]
- `SHORT`: 有符号短整型，归一化时映射 [-32767, 32767] -> [-1, +1]
- `USHORT`: 无符号短整型，归一化时映射 [0, 65535] -> [0, +1]
- `HALF`: 半精度浮点（1位符号 + 5位指数 + 5位尾数）
- `FLOAT`: 标准 32 位浮点

### `Transcoder`
- **Config**: 配置结构体，包含 `componentType`、`normalized`（是否归一化）、`componentCount`（分量数）、`inputStrideBytes`（输入步幅，0 表示紧密排列）
- **operator()**: 执行转换。传 null target 时仅返回所需字节数

## 关键实现逻辑

1. **模板化转换函数**: `convert<SOURCE_TYPE, NORMALIZATION_FACTOR>` 和 `convertClamped<>` 使用模板参数化源类型和归一化因子，实现高效转换
2. **固定分量数优化**: 额外提供 `convert<..., NUM_COMPONENTS>` 模板特化版本（2 或 3 分量），让编译器生成更优代码
3. **有符号归一化钳位**: 有符号归一化类型（BYTE、SHORT）使用 `convertClamped` 将结果钳位到 [-1, 1]，遵循 Vulkan/ES3 规范（例如 -128 被钳位为 -1.0）
4. **HALF/FLOAT 直通**: 半精度直接用 `float(half)` 转换，浮点直接复制
5. 归一化语义遵循 Vulkan 和 OpenGL ES 3.0+ 规范

## 依赖关系

- `<math/half.h>` - 半精度浮点支持
- `<utils/compiler.h>` - 编译器工具宏（UTILS_RESTRICT 等）
