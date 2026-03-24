# GLBufferObject.h

## 文件概述

定义了 OpenGL 后端的缓冲区对象（Buffer Object）数据结构，是 Filament 硬件缓冲区资源 `HwBufferObject` 的 GL 特化实现。

## 核心类/结构体

### `GLBufferObject`
继承自 `HwBufferObject`，表示一个 OpenGL 缓冲区对象。

- `gl.id`: GL 缓冲区名称（GLuint）。
- `gl.binding` / `gl.buffer`: 联合体，分别用于 ES3+（binding target）和 ES2（CPU 端内存指针）。
- `usage`: 缓冲区使用模式（STATIC/DYNAMIC 等），4 位位域。
- `bindingType`: 绑定类型（VERTEX/UNIFORM/SHADER_STORAGE），4 位位域。
- `mappingCount`: 当前活跃的内存映射计数。
- `age`: 版本号，用于追踪缓冲区数据更新（ES2 uniform 模拟中使用）。

## 关键实现逻辑

- 使用位域压缩 `usage` 和 `bindingType` 以节省内存。
- `gl.binding` 和 `gl.buffer` 的联合体设计使 ES2 和 ES3 可复用同一结构。

## 依赖关系

- `DriverBase.h` - 基类 `HwBufferObject`
- `gl_headers.h` - OpenGL 类型
- `backend/DriverEnums.h` - 枚举定义
