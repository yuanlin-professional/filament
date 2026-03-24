# ResourceAllocator

## 文件概述

`details/ResourceAllocator.h` 定义了帧图资源分配器模板，使用 SFINAE 技术自动适配不同类型资源的创建和销毁方式。

## 核心类/结构体

- **`ResourceAllocator<T>`**: 默认资源分配器模板。
  - `create()`: 使用 `if constexpr` 根据资源类型自动选择调用签名：
    - 后端资源（`is_backend_resource_v<T>` 为 true）：传递 `DriverApi&`
    - 通用资源：调用简单版本
  - `destroy()`: 类似逻辑

- **`ResourceAllocator<FrameGraphTexture>`**: 纹理资源的全特化。使用 `TextureCacheInterface` 分配，受保护内存模式下自动添加 `PROTECTED` 使用标志。

- **`ResourceAllocator<FrameGraphDummyLink>`**: 虚拟链接资源的全特化，create/destroy 均为空操作。

- **辅助模板**:
  - `check_create<T, Args...>`: 使用 SFINAE 检测 `T::create(Args...)` 是否存在
  - `is_backend_resource<T>`: 判断资源类型是否需要 `DriverApi&` 参数

## 关键实现逻辑

- 通过 C++17 的 `if constexpr` 在编译期选择正确的 create/destroy 调用路径。
- 支持三种 create 签名的自动适配：(driver, name, desc, usage)、(driver, name, desc)、(driver, desc)。

## 依赖关系

- `fg/details/ResourceCreationContext.h` - 资源创建上下文
- `fg/FrameGraphDummyLink.h`, `fg/FrameGraphTexture.h` - 已知资源类型
- `backend/DriverApiForward.h` - 后端 API 前向声明
