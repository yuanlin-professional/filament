# Fence.h

## 文件概述

定义同步栅栏（Fence），用于应用主线程与 Filament 渲染线程之间的同步。

## 核心类/结构体

- **`Fence`** - 同步栅栏对象，继承自 `FilamentAPI`。
- **`Fence::Mode`** - 枚举：`FLUSH`（等待前刷新命令流）/ `DONT_FLUSH`（不刷新）。

## 主要 API

| 方法 | 说明 |
|------|------|
| `wait(mode, timeout)` | 阻塞当前线程直到栅栏信号，返回 `FenceStatus` |
| `waitAndDestroy(fence, mode)` | 等待栅栏并销毁（静态方法） |

## 常量

- `FENCE_WAIT_FOR_EVER` - 禁用超时的特殊值

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/DriverEnums.h`
- `utils/compiler.h`

## 使用示例

```cpp
Fence* fence = engine->createFence();
// ... 提交渲染命令 ...
auto status = fence->wait(Fence::Mode::FLUSH);
engine->destroy(fence);
```
