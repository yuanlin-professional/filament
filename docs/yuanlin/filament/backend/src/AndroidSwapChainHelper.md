# AndroidSwapChainHelper

## 文件概述

`AndroidSwapChainHelper.h` / `AndroidSwapChainHelper.cpp` 提供了 Android 平台上交换链帧 ID 到系统帧 ID 的映射管理。用于将 Filament 内部的帧 ID 与 `ANativeWindow` 的系统帧 ID 关联，以支持帧时间戳查询等功能。

## 核心类/结构体

### `AndroidSwapChainHelper`
- **setPresentFrameId()**: 在每帧提交时调用，通过 `NativeWindow::getNextFrameId` 获取系统帧 ID，并将 Filament 帧 ID 与之关联存储在环形映射中。
- **getFrameId()**: 根据 Filament 帧 ID 查找对应的系统帧 ID。未找到时返回 `uint64_t::max`。

## 关键实现逻辑

- 使用 `MonotonicRingMap`（单调递增环形映射）存储帧 ID 映射，最大历史记录为 32 条。
- 帧 ID 必须严格单调递增；如果检测到非单调（例如同一 ANativeWindow 被不同 Renderer 使用），会清空整个映射表并重新开始记录。
- 使用互斥锁保护映射表的读写，但由于竞争频率低，性能影响很小。

## 依赖关系

- `AndroidNativeWindow.h` - 提供 `NativeWindow::getNextFrameId` 等接口
- `utils::Mutex` - 低竞争互斥锁
- `utils::MonotonicRingMap` - 单调递增键的环形映射容器
- `<android/native_window.h>` - Android 原生窗口类型定义
