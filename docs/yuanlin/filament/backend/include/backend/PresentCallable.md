# PresentCallable.h

## 文件概述

定义了 `PresentCallable` 类，一个可调用对象，调用时会将帧调度到 SwapChain 上呈现。通常由 Filament 后端负责帧的呈现调度，但某些场景下（如 iOS UIKit 同步）应用需要控制呈现时机。

## 核心类

### PresentCallable

帧呈现调度的可调用对象。

**类型定义**:
- `PresentFn` = `void(*)(bool presentFrame, void* user)`

**使用场景**:
- 在 Metal 后端中，应用**必须**调用收到的每个 `PresentCallable`
- 传入 `false` 可取消帧呈现并释放关联内存
- 其他后端中，`PresentCallable` 是空操作

## 主要 API

| 方法 | 说明 |
|------|------|
| `PresentCallable(fn, user)` | 构造函数 |
| `operator()(presentFrame = true)` | 调用呈现，传 `false` 取消呈现 |

## 使用示例（iOS）

```cpp
void myFrameScheduledCallback(PresentCallable presentCallable, void* user) {
    [CATransaction begin];
    // 更新其他 UI 元素...
    presentCallable();
    [CATransaction commit];
}
```

## 依赖关系

- `utils/compiler.h` - 编译器工具宏
