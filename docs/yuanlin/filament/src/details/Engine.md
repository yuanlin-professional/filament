# Engine

## 文件概述

`FEngine` 是 Filament 渲染引擎的核心类，也是 `Engine` 公共 API 的私有实现。它是整个渲染系统的中枢，负责管理所有硬件资源、组件管理器、驱动线程、命令缓冲区队列，以及所有渲染对象的创建和销毁生命周期。

## 核心类/结构体

### `FEngine`
- 继承自 `Engine` 和 `utils::FeatureFlagManager`
- 管理所有渲染资源的 `ResourceList<T>` 列表（BufferObject、Texture、Material 等共 17 类）
- 持有后端驱动 `backend::Driver*`、命令缓冲区队列 `CommandBufferQueue`、JobSystem 等核心子系统
- 包含组件管理器：`FRenderableManager`、`FTransformManager`、`FLightManager`、`FCameraManager`
- 内嵌 `debug` 结构体，存储所有运行时调试属性（阴影、视图、渲染器、光照、立体等）

## 关键实现逻辑

- **双线程架构** -- 主线程录制命令，驱动线程（`FEngine::loop()`）执行命令。单线程模式下在主线程同步执行
- **创建流程** -- `FEngine::create()` 启动驱动线程 -> 等待驱动初始化 -> `init()` 初始化全屏三角形、默认纹理/材质/IBL/ColorGrading 等
- **资源管理** -- 模板化的 `create()`/`terminateAndDestroy()` 方法统一管理所有资源类型
- **异步对象销毁** -- 支持异步创建的对象在创建完成前推迟前端销毁，通过 `mDeferredAsyncObjectDestruction` 列表管理
- **UBO 批处理** -- 可选功能，通过 `UboManager` 将多个 MaterialInstance 的 uniform 合并到共享 UBO
- **特性级别** -- 支持 FEATURE_LEVEL_0 到 FEATURE_LEVEL_3 的功能分层

## 依赖关系

- 几乎所有 `details/` 下的头文件（作为资源工厂需要了解所有类型）
- `components/` 下的四大组件管理器
- `PostProcessManager`、`DFG`、`MaterialCache` 等引擎子系统
- `private/backend/` -- 命令缓冲区和驱动 API

## 与公共 API 的关系

直接对应 `filament::Engine`。是用户使用 Filament 的入口，通过 `Engine::Builder::build()` 创建。提供所有渲染对象的工厂方法和引擎全局配置。
