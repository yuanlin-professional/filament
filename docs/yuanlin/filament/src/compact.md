# compact.h

## 文件概述

提供一个高效的范围压缩算法模板函数 `compact`。该函数在范围 `[first, last)` 中查找连续的等价元素组（runs），将它们按最大块大小分割并交由处理器处理，最后原地压缩范围，仅保留每个块的代表元素。时间复杂度为 O(N)。

## 核心类/结构体/函数

### `compact<MaxRunSize>(first, last, equivalent, processor)` 模板函数

- **模板参数**：
  - `MaxRunSize` -- 每个处理块的最大大小。
  - `Iterator` -- 迭代器类型。
  - `BinaryPredicate` -- 判断两个相邻元素是否等价的二元谓词。
  - `Processor` -- 处理每个块的回调函数，签名为 `void(Iterator dst, Iterator chunkBegin, Iterator chunkEnd)`。
- **返回值**：压缩后范围的新逻辑末尾迭代器。

## 关键实现逻辑

- **快速路径**：首先使用 `std::adjacent_find` 查找第一个连续重复序列。如果没有找到，直接返回，不做任何额外工作。
- **慢速路径**：发现连续序列后，使用双指针（`src`/`dst`）遍历范围。对于连续等价元素组，按 `MaxRunSize` 分块处理，每块的第一个元素移动到目标位置并调用处理器。
- **平凡可拷贝优化**：对 `trivially_copyable` 类型跳过自移动赋值检查，减少运行时开销。
- **单元素处理**：非连续序列中的单个元素直接移动到目标位置，不调用处理器。

## 依赖关系

- `utils/compiler.h` -- `UTILS_UNLIKELY` 等编译器提示宏
- `<algorithm>`、`<iterator>`、`<cstddef>` -- 标准库

## 被引用关系

被渲染管线中的命令排序和合并步骤使用，用于将具有相同材质或状态的连续渲染命令压缩合并。
