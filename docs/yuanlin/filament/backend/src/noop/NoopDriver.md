# NoopDriver

## 文件概述

`NoopDriver.h` / `NoopDriver.cpp` 实现了 Filament 后端的空操作（No-op）驱动。所有 Driver API 方法均为空实现或返回默认值，不执行任何实际的图形操作。主要用于测试、基准测量和无 GPU 环境下的运行。

## 核心类/结构体

### `NoopDriver`
- 继承自 `DriverBase`，是 `final` 类。
- **create()**: 静态工厂方法，创建 NoopDriver 实例。
- **getDispatcher()**: 返回通过 `ConcreteDispatcher<NoopDriver>::make()` 生成的命令分发表。
- **getShaderModel()**: Android/iOS/Emscripten 返回 MOBILE，其他返回 DESKTOP。
- **getShaderLanguages()**: 返回所有支持的着色器语言（ESSL3、ESSL1、SPIRV、MSL、METAL_LIBRARY、WGSL）。
- **nextFakeHandle**: 自增计数器，用于为 `DECL_DRIVER_API_RETURN` 类的方法生成虚拟句柄。

### API 实现策略
- **资源创建**: `DECL_DRIVER_API_RETURN` 宏自动生成，返回递增的假句柄 ID。
- **资源销毁**: 所有 `destroy*` 方法为空实现。
- **数据更新**: `updateIndexBuffer`、`updateBufferObject` 等方法仅调用 `scheduleDestroy` 释放缓冲区回调。
- **能力查询**: `isTextureFormatSupported` 等返回 true/false 的合理默认值。
- **渲染操作**: `beginRenderPass`、`draw`、`commit` 等均为空操作。
- **compilePrograms()**: 如果有回调则立即调度执行。

## 关键实现逻辑

- 通过 `DriverAPI.inc` 宏展开同时声明和定义所有 API 方法，最大化代码复用。
- `DECL_DRIVER_API` 方法标记为 `UTILS_ALWAYS_INLINE inline`，编译器可将空实现完全消除。
- `DECL_DRIVER_API_RETURN` 方法的 "S" 后缀版本返回假句柄，"R" 后缀版本为空。
- `ConcreteDispatcher<NoopDriver>` 的显式模板实例化在 .cpp 文件中完成。

## 依赖关系

- `DriverBase.h` - 驱动基类（同一 src 目录）
- `private/backend/Driver.h` - Driver 虚接口
- `CommandStreamDispatcher.h` - 命令分发器模板
- `utils::FixedCapacityVector` - 着色器语言列表返回类型
- `private/backend/DriverAPI.inc` - Driver API 宏定义
