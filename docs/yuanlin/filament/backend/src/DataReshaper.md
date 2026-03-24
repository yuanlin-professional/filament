# DataReshaper

## 文件概述

`DataReshaper.h` 是一个纯头文件工具类，提供像素数据的通道数转换、类型转换和图像重塑功能。用于处理不同图形 API 对像素格式的不同要求（例如某些平台只支持 4 通道数据而用户提交的是 3 通道）。

## 核心类/结构体

### `DataReshaper`
- **reshape()**: 模板函数，在多通道交错数据中添加或移除通道。例如将 RGB 数据扩展为 RGBA（第 4 通道填充最大值）。
- **reshapeImage()**: 综合图像重塑函数，支持不同源/目标数据类型组合的转换。根据源和目标的 `PixelDataType` 选择合适的模板实例化。
- **copyImage()**: 优化的逐行复制函数，当行大小相同时使用单次 `memcpy`。

### 辅助模板和函数（匿名命名空间）
- **getMaxValue()**: 返回各数据类型的最大值/归一化值（如 float 返回 1.0，uint8 返回 0xFF）。
- **reshapeImageImpl()**: 核心重塑实现，支持类型转换（如 float->uint8）、通道数变换和 BGR/RGB 交换（swizzle）。
- **grayscaleFill()**: 灰度图扩展为 RGB/RGBA 时复制第一通道到其余通道。
- **UnpackerR11G11B10**: 解包 R11F_G11F_B10F 压缩浮点格式。
- **reshapeImagePacked()**: 处理打包像素格式（如 R11G11B10）到标准格式的转换。

## 关键实现逻辑

- 双重模板化设计：外层按目标类型分派，内层按源类型分派，覆盖 UBYTE/FLOAT/INT/UINT/HALF 的组合。
- 类型转换通过 double 中间值进行归一化缩放和钳制（clamp），确保数值正确映射。
- swizzle 参数控制 RGB <-> BGR 通道交换，通过索引数组 `inds[4]` 实现。
- `copyImage` 作为特殊优化路径，当格式完全匹配时避免逐像素处理。

## 依赖关系

- `backend/PixelBufferDescriptor.h` - 像素缓冲区描述符
- `math/scalar.h` / `math/half.h` - 数学工具和半精度浮点
- `utils::debug` / `utils::Logger` - 断言和日志
