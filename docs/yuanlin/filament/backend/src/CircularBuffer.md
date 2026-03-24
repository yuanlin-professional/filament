# CircularBuffer

## 文件概述

`CircularBuffer.cpp` 实现了命令流使用的环形缓冲区。这是 Filament 渲染引擎命令缓冲系统的核心数据结构，支持在固定内存区域内高效地写入和回绕命令数据。

## 核心类/结构体

### `CircularBuffer`
- 头文件位于 `private/backend/CircularBuffer.h`。
- 管理一块连续内存区域，支持写指针自动回绕。
- **alloc()**: 分配底层内存。优先尝试 "硬环形缓冲区"（通过 mmap 将两段虚拟地址映射到同一物理页），失败则回退到 "软环形缓冲区"。
- **dealloc()**: 释放缓冲区内存。
- **getBuffer()**: 返回当前已写入的数据范围 `[tail, head)`，并处理回绕逻辑。

## 关键实现逻辑

### 硬环形缓冲区（mmap）
- 使用 `ashmem_create_region` 创建匿名共享内存。
- 将同一块物理内存映射两次到连续的虚拟地址空间，实现零拷贝回绕。
- 在第二段映射之后添加保护页（`PROT_NONE`），防止越界访问。
- 此模式下数据跨越边界时无需复制，直接通过虚拟地址映射自动访问。

### 软环形缓冲区（fallback）
- 当硬模式不可用时（如地址空间不足），分配 `size * 2 + block_size` 的内存。
- 回绕时需要调整头指针，采用滑动窗口方式处理边界情况。

### Windows 支持
- 使用 `VirtualAlloc` + `VirtualProtect` 替代 mmap，设置保护页。

### Emscripten / 其他
- 回退到简单的 `malloc(2 * size)`。

## 依赖关系

- `utils::architecture` - 获取系统页面大小
- `utils::ashmem` - Android 共享内存创建
- `utils::Panic` / `utils::Logger` - 错误处理和日志
- `<sys/mman.h>` - Unix 内存映射（非 Windows/Emscripten）
