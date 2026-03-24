# Hash.h

## 文件概述
哈希函数工具集，提供 CRC32、MurmurHash3 等常用哈希算法。

## 核心类/结构体/函数
- **crc32GenerateTable()**: 生成 CRC-32 查找表
- **crc32Update()**: 增量式 CRC-32 计算
- **murmur3()**: MurmurHash3 哈希函数（字对齐输入）
- **murmurSlow()**: MurmurHash3 哈希函数（字节对齐输入）
- **MurmurHashFn<T>**: 用于容器的 Murmur 哈希函数对象
- **combine()**: 组合两个哈希值
- **combine_fast()**: 快速但质量稍低的哈希组合

## 关键实现逻辑
- CRC-32 使用标准多项式 0xEDB88320，基于查找表加速
- MurmurHash3 针对 32 位字对齐数据优化
- combine 使用黄金比例常数 0x9e3779b9 做哈希混合

## 依赖关系
- 无外部 utils 依赖
