# Engine.cpp

## 文件概述

`Engine` 类的公共 API 实现文件，是 Filament 渲染引擎的核心入口。提供引擎的创建/销毁、所有渲染资源（Renderer、View、Scene、Camera、Texture、Material 等）的创建和销毁、资源有效性验证、命令队列管理、特性标志控制等功能。

## 核心类/结构体/函数

### 引擎生命周期

- **`destroy(Engine*)`** -- 销毁引擎实例。
- **`getEngine(void* token)`** -- 通过 token 获取引擎实例（仅多线程环境）。

### 资源创建

- **`createRenderer()`** / **`createView()`** / **`createScene()`** -- 创建渲染器、视图、场景。
- **`createCamera(Entity)`** / **`getCameraComponent(Entity)`** -- 创建/获取相机组件。
- **`createFence()`** / **`createSwapChain()`** / **`createSync()`** -- 创建同步和交换链对象。

### 资源销毁

- **`destroy(const T*)`** -- 重载 20+ 种资源类型的销毁方法（BufferObject、VertexBuffer、IndexBuffer、Material、Texture、View、Scene 等）。
- **`destroy(Entity)`** -- 销毁实体。

### 资源验证

- **`isValid(const T*)`** -- 重载 20+ 种资源类型的有效性检查方法。

### 命令执行

- **`flush()`** / **`flushAndWait()`** -- 刷新命令队列。
- **`execute()`** -- 单线程环境下执行命令。
- **`runCommandAsync(...)`** / **`cancelAsyncCall()`** -- 异步命令执行。

### 管理器访问

- **`getEntityManager()`** / **`getRenderableManager()`** / **`getLightManager()`** / **`getTransformManager()`** -- 获取各 ECS 管理器。

### 特性和配置

- **`setActiveFeatureLevel()`** / **`getActiveFeatureLevel()`** -- 设置/获取特性级别。
- **`setFeatureFlag()`** / **`getFeatureFlag()`** -- 特性标志管理。
- **`getConfig()`** -- 获取引擎配置。

## 关键实现逻辑

所有方法通过 `downcast` 委托给 `FEngine` 内部实现。部分方法包含前置条件检查（如 `execute()` 要求单线程平台，`setPaused()` 要求多线程平台）。

## 依赖关系

- `details/Engine.h` -- 核心内部实现
- `details/*.h` -- 所有资源类型的内部实现（Camera、Fence、IndexBuffer、Material、Renderer 等）
- `filament/Engine.h` -- 公共头文件
- `backend/DriverEnums.h`、`backend/CallbackHandler.h` -- 后端接口

## 被引用关系

作为 Filament 的主入口点，被所有用户代码使用。是创建和管理所有渲染资源的唯一途径。
