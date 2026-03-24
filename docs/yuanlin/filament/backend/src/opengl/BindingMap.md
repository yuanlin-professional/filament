# BindingMap.h

## 文件概述

BindingMap 用于管理 OpenGL 后端中描述符集（Descriptor Set）的绑定映射关系。它将 `{set, binding}` 对映射到实际的 GL 绑定点，区分采样器（sampler）和缓冲区（buffer）类型。

## 核心类/结构体

### `BindingMap`
- **CompressedBinding**: 内部压缩结构，用 7 位存储 binding 值，1 位标记是否为采样器。
- **Binding**: 公共结构体，包含 `GLuint binding` 和 `DescriptorType type`。
- **mStorage**: 二维数组 `[MAX_DESCRIPTOR_SET_COUNT][MAX_DESCRIPTOR_COUNT]`，存储压缩后的绑定信息。
- **mActiveDescriptors**: 每个描述符集的活跃描述符位图（bitset64）。

### 关键方法
- `insert(set, binding, entry)`: 插入一条绑定映射。
- `get(set, binding)`: 获取某个描述符的 GL 绑定点。
- `getActiveDescriptors(set)`: 获取某个集合中所有活跃描述符的位集合。

## 关键实现逻辑

- 使用紧凑的位域（7+1 bit）存储绑定信息以减少内存占用。
- 不可复制、不可移动，通过 `new` 分配堆内存存储。
- Debug 模式下将存储初始化为 0xFF 以便检测未初始化的访问。

## 依赖关系

- `backend/DriverEnums.h` - 描述符类型和常量定义
- `gl_headers.h` - OpenGL 类型定义
- `utils/bitset.h` - 位集合工具
- `utils/debug.h` - 断言工具
