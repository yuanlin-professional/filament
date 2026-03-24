# CameraManager

## 文件概述

`CameraManager.h` / `CameraManager.cpp` 实现了 Filament 引擎中相机组件的管理器。它基于 ECS（实体-组件-系统）架构，负责将 `FCamera` 组件关联到实体上，并管理其生命周期。

## 核心类/结构体

- **`CameraManager`**: 公共 API 基类，继承自 `FilamentAPI`，定义了 `Instance` 类型别名。
- **`FCameraManager`**: 内部实现类，继承自 `CameraManager`，管理所有相机组件实例。
  - 使用 `SingleInstanceComponentManager<FCamera*, bool>` 作为底层存储。
  - 两个组件字段：`CAMERA`（FCamera 指针）和 `OWNS_TRANSFORM_COMPONENT`（是否拥有变换组件）。

## 关键实现逻辑

- **创建 (`create`)**: 通过引擎的堆分配器创建 `FCamera` 对象（FCamera 因历史原因不可移动，所以存储为指针）。如果实体没有 `TransformComponent`，会自动创建一个并标记为 "拥有"。
- **销毁 (`destroy`)**: 先销毁 `FCamera` 对象并移除组件，再根据 `OWNS_TRANSFORM_COMPONENT` 标志决定是否移除自动创建的变换组件。
- **垃圾回收 (`gc`)**: 委托给 `SingleInstanceComponentManager::gc`，当实体被销毁时自动清理关联的相机组件。
- **终止 (`terminate`)**: 在引擎关闭时，清理所有泄漏的相机组件并输出日志。

## 依赖关系

- `details/Engine.h` / `details/Camera.h` - 引擎核心和相机实现
- `filament/FilamentAPI.h` - 公共 API 基础设施
- `utils/SingleInstanceComponentManager.h` - ECS 组件管理基础模板
- `utils/Entity.h` - 实体系统
- `downcast.h` - 类型向下转换宏
