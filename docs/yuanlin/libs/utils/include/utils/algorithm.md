# algorithm.h

## 文件概述
底层位运算和算法工具集，提供 clz/ctz/popcount 等位操作以及优化的分区点查找。

## 核心类/结构体/函数
- **clz(x)**: 计算前导零个数 (Count Leading Zeros)，支持多种整数类型
- **ctz(x)**: 计算尾随零个数 (Count Trailing Zeros)
- **popcount(x)**: 计算置位位数 (Population Count)
- **log2i(x)**: 计算无符号整数的以 2 为底的对数
- **partition_point()**: 优化的分区点查找，对 2 的幂大小数组进行手动循环展开
- **bit_cast<Dest, Source>()**: 类型安全的位转换，优先使用 `__builtin_bit_cast`

## 关键实现逻辑
- 优先使用编译器内建函数 (`__builtin_clz` 等)，不可用时回退到纯 C++ 实现
- details 命名空间中的 popcount 使用经典的分治位并行计算
- partition_point 对非 2 的幂大小做预处理，然后手动展开两次无分支二分查找

## 依赖关系
- `utils/compiler.h`
