# AndroidNativeWindow

## 文件概述

`AndroidNativeWindow.h` / `AndroidNativeWindow.cpp` 封装了 Android 原生窗口（`ANativeWindow`）的底层操作接口。通过私有 API 和 `dlsym` 动态加载的方式，提供帧时间戳查询、合成器定时信息获取以及生产者节流控制等功能。

## 核心类/结构体

### `NativeWindow`
- 不可实例化的结构体，通过将 `ANativeWindow*` 强制转换为 `NativeWindow*` 来访问内部函数指针。
- 定义了 `query` 和 `perform` 函数指针（对应 ANativeWindow 的内部布局）。
- **isValid()**: 验证 ANativeWindow 是否有效。Android 28+ 使用公开 API `ANativeWindow_getBuffersDataSpace`，低版本回退到私有 API。
- **getNextFrameId()**: 获取下一帧的系统帧 ID。
- **enableFrameTimestamps()**: 启用/禁用帧时间戳记录。
- **getCompositorTiming()**: 获取合成器的截止时间、间隔和延迟信息。
- **getFrameTimestamps()**: 获取特定帧的完整时间戳数据（请求展示时间、获取时间、锁存时间等）。

### `AndroidProducerThrottling`
- 通过 `dlsym` 动态加载 `libnativewindow.so` 中的节流控制 API。
- **setProducerThrottlingEnabled()**: 设置生产者节流是否启用。
- **isProducerThrottlingEnabled()**: 查询当前节流状态。
- **isSupported()**: 检查运行时是否支持节流 API。

## 关键实现逻辑

- 使用平台特定的内存布局 padding（32/64 位不同）来定位 `ANativeWindow` 内部的 `query` 和 `perform` 函数指针。
- `AndroidProducerThrottling` 构造时通过 `dlopen`/`dlsym` 动态解析符号，避免编译时依赖高版本 NDK API。

## 依赖关系

- `<android/native_window.h>` / `<android/api-level.h>` - Android NDK 原生窗口 API
- `<dlfcn.h>` - 动态库加载
- `utils::compiler` / `utils::Logger` - 编译器工具和日志
