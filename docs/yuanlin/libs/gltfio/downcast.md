# downcast

## 文件概述
downcast.h 提供 `FILAMENT_DOWNCAST` 宏，用于安全地将公共接口指针/引用静态转换为内部实现类型。这是 gltfio 中公共 API 与内部实现之间的桥接机制。

## 核心宏

- **`FILAMENT_DOWNCAST(CLASS)`**: 生成四个重载的 `downcast()` 内联函数，分别处理非 const 引用、const 引用、非 const 指针和 const 指针的转换。例如 `FILAMENT_DOWNCAST(AssetLoader)` 生成 `FAssetLoader& downcast(AssetLoader&)` 等。

## 使用方式

在声明内部实现类 `FXXX` 之后调用此宏，然后在公共方法实现中使用 `downcast(this)` 将 `this` 转换为内部类型以访问私有成员。

## 依赖关系
无外部依赖，纯头文件宏定义。
