# Sync

## 文件概述

`FSync` 是 `Sync` 公共 API 的私有实现类，提供 GPU 同步对象的创建和外部句柄获取功能。与 `FFence` 不同，`FSync` 允许导出平台特定的同步句柄（如 EGLSync、MTLSharedEvent），用于跨进程或跨 API 的 GPU 同步。

## 核心类/结构体

### `FSync`
- 继承自 `Sync`
- 持有 `backend::SyncHandle` 后端同步句柄
- 通过 `FILAMENT_DOWNCAST(Sync)` 宏支持类型转换

## 关键实现逻辑

- **构造** -- 调用 `driverApi.createSync()` 创建后端同步对象
- **getExternalHandle()** -- 通过 `driverApi.getPlatformSync()` 异步获取平台特定的同步句柄，结果通过回调返回
- **terminate()** -- 调用 `driverApi.destroySync()` 销毁后端资源

## 依赖关系

- `details/Engine.h` -- 引擎驱动 API
- `backend/Platform.h` -- 平台接口定义
- `backend/CallbackHandler.h` -- 回调处理器

## 与公共 API 的关系

直接对应 `filament::Sync`。通过 `Engine::createSync()` 创建，用于跨 API/跨进程的 GPU 同步场景（如 Android HardwareBuffer 共享）。
