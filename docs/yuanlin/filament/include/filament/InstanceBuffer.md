# InstanceBuffer.h

## 文件概述

定义实例缓冲区，用于存储 GPU 绘制实例的局部变换，支持硬件实例化渲染以提高渲染效率。

## 核心类/结构体

- **`InstanceBuffer`** - 实例缓冲区，继承自 `FilamentAPI`。存储每个绘制实例相对于父渲染体的局部变换。
- **`InstanceBuffer::Builder`** - 构建器，需要指定实例数量。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder(instanceCount)` | 构造器，指定实例数量（1 ~ maxAutomaticInstances） |
| `Builder::localTransforms(transforms)` | 设置初始局部变换数组 |
| `Builder::build(engine)` | 创建 InstanceBuffer |
| `getInstanceCount()` | 获取实例数量 |
| `setLocalTransforms(transforms, count, offset)` | 更新局部变换 |
| `getLocalTransform(index)` | 获取指定实例的局部变换 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/Engine.h`
- `utils/compiler.h`, `utils/StaticString.h`, `math/mathfwd.h`

## 使用示例

```cpp
math::mat4f transforms[10];
// ... 设置每个实例的变换 ...
auto* ib = InstanceBuffer::Builder(10)
    .localTransforms(transforms)
    .build(*engine);
```
