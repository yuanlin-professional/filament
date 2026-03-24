# ashmem.h / ashmem.cpp

## 文件概述
跨平台匿名共享内存创建工具，抽象 Android ashmem / POSIX 临时文件。

## 核心类/结构体/函数
- **ashmem_create_region(name, size)**: 创建指定大小的匿名共享内存区域，返回文件描述符

## 关键实现逻辑
- Android API >= 26: 直接使用 `ASharedMemory_create`
- Android API < 26: 通过 `dlsym` 动态查找，失败则回退到 `/dev/ashmem` ioctl
- Unix/macOS: 使用 `mkstemp` 创建临时文件后 `unlink`，再 `ftruncate` 到目标大小
- Windows: 使用 `_mktemp` + `_open` + `_chsize`

## 依赖关系
- `utils/api_level.h`, `utils/Path.h`
