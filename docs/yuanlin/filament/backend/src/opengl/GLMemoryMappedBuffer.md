# GLMemoryMappedBuffer.h / GLMemoryMappedBuffer.cpp

## 文件概述

实现 OpenGL 后端的内存映射缓冲区（Memory Mapped Buffer），允许 CPU 直接读写 GPU 缓冲区数据，是 Filament `HwMemoryMappedBuffer` 的 GL 特化。

## 核心类/结构体

### `GLMemoryMappedBuffer`
- `boh`: 关联的缓冲区对象句柄。
- `access`: 映射访问标志（读/写/无效化等）。
- `gl.vaddr`: 映射后的虚拟地址。
- `gl.size/offset/binding/id`: 映射范围和关联 GL 对象信息。

### 关键方法
- 构造函数: 执行 `glMapBufferRange` 映射缓冲区（ES3+），或直接使用 CPU 内存指针（ES2）。
- `unmap()`: 调用 `glUnmapBuffer` 取消映射。
- `copy()`: 如果有映射地址则用 `memcpy`，否则回退到 `glBufferSubData`（WebGL 场景）。

## 关键实现逻辑

- ES2 模式下直接操作 CPU 端分配的内存，不涉及 GL 映射。
- ES3+ 使用 `GL_MAP_UNSYNCHRONIZED_BIT` 进行非同步映射以提升性能。
- WebGL 不支持 `glMapBufferRange`，自动回退到 `glBufferSubData`。
- 使用引用计数 `mappingCount` 跟踪活跃映射。

## 依赖关系

- `GLBufferObject.h`, `GLUtils.h`, `OpenGLContext.h`, `OpenGLDriver.h`
- `backend/DriverEnums.h`, `backend/Handle.h`
- `private/backend/HandleAllocator.h`
