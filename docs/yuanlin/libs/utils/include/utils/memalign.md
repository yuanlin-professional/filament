# memalign.h

## 文件概述
跨平台对齐内存分配工具和 STL 兼容的对齐分配器。

## 核心类/结构体/函数
- **aligned_alloc(size, align)**: 跨平台对齐内存分配
- **aligned_free(p)**: 释放对齐分配的内存
- **STLAlignedAllocator<TYPE>**: STL 兼容的对齐分配器

## 关键实现逻辑
- Windows 使用 `_aligned_malloc` / `_aligned_free`
- POSIX 使用 `posix_memalign` / `free`
- 对齐值自动调整为至少 `sizeof(void*)` 且为 2 的幂

## 依赖关系
- 无外部 utils 依赖
