# SkinningBuffer.h

## 文件概述

定义蒙皮缓冲区，用于存储骨骼变换数据。多个可渲染对象可以共享同一个 SkinningBuffer 以节省内存。

## 核心类/结构体

- **`SkinningBuffer`** - 蒙皮缓冲区，继承自 `FilamentAPI`。封装结构化 UBO。
- **`SkinningBuffer::Builder`** - 构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::boneCount(count)` | 设置骨骼数量 |
| `Builder::initialize(init)` | 是否用单位矩阵初始化 |
| `Builder::build(engine)` | 创建 SkinningBuffer |
| `setBones(engine, transforms, count, offset)` | 更新骨骼变换（Bone 或 mat4f） |
| `getBoneCount()` | 获取骨骼数量 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/RenderableManager.h`
- `utils/compiler.h`, `utils/StaticString.h`, `math/mathfwd.h`

## 使用示例

```cpp
auto* sb = SkinningBuffer::Builder()
    .boneCount(64)
    .initialize(true)
    .build(*engine);
sb->setBones(*engine, boneMatrices, 64, 0);
```
