# bitset.h

## 文件概述
自定义位集合实现，支持精确控制存储大小，适用于小型位集合场景。

## 核心类/结构体/函数
- **bitset<T, N>**: 模板位集合类，T 为存储单元类型，N 为单元个数
- 预定义类型: `bitset8`, `bitset32`, `bitset64`, `bitset128`, `bitset256`
- 主要方法: `set`, `unset`, `test`, `flip`, `count`, `any`, `none`, `all`
- `forEachSetBit(exec)`: 遍历所有置位位

## 关键实现逻辑
- 在 ARM64 NEON 平台上对 128 位倍数的 bitset 使用 SIMD 指令加速 count/any/all 操作
- 使用 `utils::ctz` 实现高效的 `forEachSetBit` 遍历
- 支持字典序比较，可用于排序

## 依赖关系
- `utils/algorithm.h`, `utils/compiler.h`
