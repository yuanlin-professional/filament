# Platform.h

## 文件概述

定义了 `Platform` 抽象基类，是 Filament 后端（Driver）创建的核心接口。后端提供了多种平台实现（如 OpenGL、Vulkan、Metal），默认自动选择。用户也可在创建 Engine 时提供自定义 Platform。

## 核心类

### Platform

平台抽象接口，定义了驱动创建、交换链时序、缓存和调试等功能。

**内部类型**:
- `SwapChain`, `Fence`, `Stream`, `Sync` - 平台资源的空结构体标签
- `ExternalImage` / `ExternalImageHandle` - 外部图像引用计数管理
- `CompositorTiming` - 合成器时序信息
- `FrameTimestamps` - 帧时间戳（请求时间、获取时间、呈现时间等）
- `DriverConfig` - 驱动配置（Arena 大小、并行编译、立体渲染等）

**枚举**:
- `StereoscopicType` - 立体渲染类型（NONE/INSTANCED/MULTIVIEW）
- `DeviceInfoType` - 设备信息查询类型
- `GpuContextPriority` - GPU 上下文优先级
- `AsynchronousMode` - 异步操作模式

## 主要 API

| 方法 | 说明 |
|------|------|
| `createDriver(sharedContext, driverConfig)` | 纯虚函数，创建底层驱动 |
| `getOSVersion()` | 查询操作系统版本 |
| `getDeviceInfo(infoType, driver)` | 查询设备/驱动信息 |
| `pumpEvents()` | 处理平台事件队列 |
| `isCompositorTimingSupported()` | 是否支持合成器时序 |
| `queryCompositorTiming(...)` | 查询合成器时序 |
| `setBlobFunc(insert, retrieve)` | 设置 Blob 缓存回调 |
| `insertBlob(...) / retrieveBlob(...)` | 缓存读写 |
| `setDebugUpdateStatFunc(...)` | 设置调试统计回调 |

## 依赖关系

- `utils/CString.h` / `utils/compiler.h` / `utils/Invocable.h` / `utils/Mutex.h`
