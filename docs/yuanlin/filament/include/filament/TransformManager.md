# TransformManager.h

## 文件概述

定义变换管理器，用于管理实体的位置和朝向。支持父子层级关系，自动计算世界空间变换。

## 核心类/结构体

- **`TransformManager`** - 变换管理器，继承自 `FilamentAPI`。使用 ECS 模式。
- **`TransformManager::children_iterator`** - 子节点前向迭代器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `create(entity, parent, localTransform)` | 创建变换组件（可指定父节点） |
| `destroy(entity)` | 销毁变换组件（子节点变为孤立） |
| `setTransform(ci, localTransform)` | 设置局部变换（mat4f 或 mat4 双精度） |
| `getTransform(ci)` | 获取局部变换 |
| `getWorldTransform(ci)` | 获取世界变换 |
| `getTransformAccurate(ci)` | 获取双精度局部变换 |
| `getWorldTransformAccurate(ci)` | 获取双精度世界变换 |
| `setParent(i, newParent)` | 重新设置父节点 |
| `getParent(i)` | 获取父实体 |
| `getChildCount(i)` / `getChildren(i, ...)` | 获取子节点 |
| `setAccurateTranslationsEnabled(enable)` | 启用双精度平移模式 |
| `openLocalTransformTransaction()` | 开始变换事务（批量更新优化） |
| `commitLocalTransformTransaction()` | 提交变换事务 |

## 依赖关系

- `filament/FilamentAPI.h`
- `utils/compiler.h`, `utils/EntityInstance.h`, `math/mathfwd.h`

## 使用示例

```cpp
auto& tcm = engine->getTransformManager();
tcm.create(entity);
auto i = tcm.getInstance(entity);
tcm.setTransform(i, mat4f::translation({0, 0, -5}));
```
