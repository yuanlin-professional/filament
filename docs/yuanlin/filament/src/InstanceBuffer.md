# InstanceBuffer.cpp

## 文件概述

`InstanceBuffer` 类的公共 API 实现文件。实例缓冲区用于 GPU 实例化渲染，存储每个实例的局部变换矩阵。

## 核心类/结构体/函数

### `InstanceBuffer` 类方法

- **`getInstanceCount()`** -- 获取实例数量。
- **`setLocalTransforms(const mat4f* localTransforms, size_t count, size_t offset)`** -- 批量设置实例的局部变换矩阵，支持偏移量以实现部分更新。
- **`getLocalTransform(size_t index)`** -- 获取指定实例的局部变换矩阵。

## 关键实现逻辑

标准 `downcast` 代理模式。`setLocalTransforms` 支持通过 `offset` 参数进行部分更新，避免每次都上传所有实例的变换数据。

## 依赖关系

- `details/InstanceBuffer.h` -- 内部实现类
- `filament/InstanceBuffer.h` -- 公共头文件
- `math/mat4.h` -- 4x4 矩阵类型

## 被引用关系

被用户代码用于 GPU 实例化渲染场景，配合 `RenderableManager` 使用，允许用一次绘制调用渲染同一网格的多个实例。
