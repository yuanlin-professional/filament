# defaultMaterial

## 文件概述

`defaultMaterial.mat` 定义了 Filament 引擎的默认材质，当用户未指定材质时自动使用。

## 材质属性

- **名称**: Filament Default Material
- **着色模型**: `unlit`（无光照）
- **参数**: 无

## 着色器逻辑

片段着色器将 `baseColor.rgb` 设为固定的灰色值 `(0.8, 0.8, 0.8)`，这是一个简单的无光照灰色材质。

## 依赖关系

- Filament 材质系统内置函数 `prepareMaterial()`
