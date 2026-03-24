# Handle.h

## 文件概述

定义了 Filament 后端资源的类型安全句柄系统。`Handle<T>` 是一个轻量级的标识符，用于引用后端管理的 GPU 资源（如纹理、缓冲区、着色器程序等），避免直接使用裸指针。

## 核心类

### HandleBase

所有句柄的基类，内部存储一个 `uint32_t` 类型的 ID。

- `HandleId` = `uint32_t`，`nullid` = `UINT32_MAX`
- 支持显式布尔转换（判断是否已初始化）
- 移动语义：移动后源句柄被清空

### Handle\<T\>

类型安全的句柄模板类，继承自 `HandleBase`。

- 支持相同类型间的比较操作（==, !=, <, >, <=, >=）
- 支持基类到派生类的隐式转换（通过 `std::is_base_of` 约束）

## 预定义句柄类型别名

| 别名 | 对应硬件资源 |
|------|-------------|
| `BufferObjectHandle` | `HwBufferObject` |
| `TextureHandle` | `HwTexture` |
| `ProgramHandle` | `HwProgram` |
| `RenderTargetHandle` | `HwRenderTarget` |
| `SwapChainHandle` | `HwSwapChain` |
| `VertexBufferHandle` | `HwVertexBuffer` |
| `IndexBufferHandle` | `HwIndexBuffer` |
| `DescriptorSetHandle` | `HwDescriptorSet` |
| `DescriptorSetLayoutHandle` | `HwDescriptorSetLayout` |
| `FenceHandle` / `SyncHandle` / `TimerQueryHandle` | 同步/查询资源 |

## 依赖关系

- `utils/debug.h` - 断言宏
