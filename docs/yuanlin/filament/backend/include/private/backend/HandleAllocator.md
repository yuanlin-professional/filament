# HandleAllocator.h

## 文件概述

定义了 `HandleAllocator` 模板类，用于高效分配和管理后端资源的 `Handle<>`。使用三级池分配器（Pool Allocator）处理不同大小的对象，带有 use-after-free 检测和调试标签支持。

## 预定义实例

| 宏 | 池大小 (P0, P1, P2) | 用途 |
|----|---------------------|------|
| `HandleAllocatorGL` | 32, 96, 184 | OpenGL 后端 |
| `HandleAllocatorVK` | 64, 160, 312 | Vulkan 后端 |
| `HandleAllocatorMTL` | 32, 64, 552 | Metal 后端 |
| `HandleAllocatorWGPU` | 64, 160, 552 | WebGPU 后端 |

## 核心类

### HandleAllocator\<P0, P1, P2\>

三级池式句柄分配器，继承自 `DebugTag`。

**句柄编码**:
- 位 31: 堆标志（0=池分配, 1=堆分配）
- 位 27-30: 年龄（用于 use-after-free 检测）
- 位 0-26: 索引

## 主要 API

| 方法 | 说明 |
|------|------|
| `allocateAndConstruct<D>(args...)` | 分配并构造对象，返回 Handle |
| `allocate<D>()` | 仅分配（不构造） |
| `construct<D, B>(handle, args...)` | 在已分配的 Handle 上构造对象 |
| `destroyAndConstruct<D, B>(handle, args...)` | 析构后重新构造 |
| `deallocate<D>(handle)` | 析构并释放 Handle |
| `deallocate(handle, p)` | 从已知指针析构并释放 |
| `handle_cast<Dp>(handle)` | 将 Handle 转为类型安全的指针（含 use-after-free 检查） |
| `is_valid(handle)` | 检查句柄是否有效 |
| `associateTagToHandle(id, tag)` | 关联调试标签 |

### DebugTag

调试标签管理基类。

| 方法 | 说明 |
|------|------|
| `writePoolHandleTag(key, tag)` | 写入池句柄标签 |
| `writeHeapHandleTag(key, tag)` | 写入堆句柄标签 |
| `findHandleTag(key)` | 查找句柄标签 |

## 依赖关系

- `backend/Handle.h` - Handle 基类
- `utils/Allocator.h` - 池分配器
- `utils/CString.h` / `utils/Log.h` / `utils/Panic.h`
- `tsl/robin_map.h` - 高性能哈希表
