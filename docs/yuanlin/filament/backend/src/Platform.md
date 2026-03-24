# Platform

## 文件概述

`Platform.cpp` 实现了 `Platform` 基类的默认行为和 `ExternalImageHandle` 的引用计数管理。`Platform` 是 Filament 后端的平台抽象层，定义了各操作系统和图形 API 需要实现的接口。

## 核心类/结构体

### `Platform::ExternalImageHandle`
- 引用计数的智能句柄，管理 `ExternalImage` 对象的生命周期。
- 完整实现了五件套：默认构造、拷贝构造/赋值、移动构造/赋值、析构。
- **incref()** / **decref()**: 原子引用计数操作。`decref` 使用 `memory_order_release`，归零时执行 `acquire` 屏障后删除对象。
- **clear()**: 释放引用并置空。
- **reset()**: 重新绑定到新对象（先增新引用，再减旧引用）。

### `Platform`（默认实现）
- **pumpEvents()**: 默认返回 false（无事件需处理）。
- **isCompositorTimingSupported()** / **queryCompositorTiming()**: 默认不支持合成器定时。
- **setPresentFrameId()** / **queryFrameTimestamps()**: 默认不支持帧时间戳。
- **setBlobFunc()**: 设置二进制 blob 缓存的插入/检索回调。使用 `shared_ptr` 包装回调以支持线程安全的并发读取。
- **insertBlob()** / **retrieveBlob()**: 通过回调函数执行 blob 缓存操作。
- **setDebugUpdateStatFunc()** / **debugUpdateStat()**: 调试统计信息更新接口。

## 关键实现逻辑

- `ExternalImageHandle` 的引用计数使用精确的内存序：增加引用用 `relaxed`（无需同步），减少引用用 `release`（确保之前的写入对其他线程可见），最后一次释放时用 `acquire` 屏障（确保看到所有线程的写入）。
- Blob 缓存回调使用 `shared_ptr` 包装并在短临界区内复制智能指针，然后在锁外调用回调，避免持锁时执行用户代码。
- `mMutex` 保护所有回调函数指针的设置和读取。

## 依赖关系

- `backend/Platform.h` - Platform 基类声明
- `utils::compiler` / `utils::ostream` - 编译器属性和输出流
- `utils::CString` - 调试统计信息的字符串值
