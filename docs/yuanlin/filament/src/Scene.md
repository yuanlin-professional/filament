# Scene.cpp

## 文件概述
Scene.cpp 实现了场景的公共 API。Scene 是 Filament 中管理实体集合的容器，包含可渲染实体、光源、天空盒和间接光照等。

## 核心类/结构体/函数
- **Scene** - 场景公共接口
  - `setSkybox()` / `getSkybox()` - 天空盒管理
  - `setIndirectLight()` / `getIndirectLight()` - 间接光照（IBL）管理
  - `addEntity()` / `addEntities()` - 添加实体到场景
  - `remove()` / `removeEntities()` / `removeAllEntities()` - 移除实体
  - `getEntityCount()` / `getRenderableCount()` / `getLightCount()` - 实体统计
  - `hasEntity()` - 检查实体是否在场景中
  - `forEach()` - 遍历场景中的所有实体

## 关键实现逻辑
所有方法通过 `downcast` 委托给内部 `FScene` 实现。场景中的实体会自动被分类为可渲染实体和光源实体。

## 依赖关系
- `details/Scene.h` - 内部实现
- `details/IndirectLight.h` / `details/Skybox.h` - 间接光照和天空盒

## 被引用关系
- 用户层代码通过 Scene 管理场景内容
- View 持有 Scene 引用以获取渲染数据
