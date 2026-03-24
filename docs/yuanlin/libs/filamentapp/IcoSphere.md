# IcoSphere.h / IcoSphere.cpp

## 文件概述
通过递归细分正二十面体生成球体网格，用于创建 Sphere 等渲染对象的几何数据。

## 核心类

### `IcoSphere`
- **构造函数**: `IcoSphere(subdivisions)` - 指定细分次数
- `getVertices()` / `getIndices()` / `getMesh()` - 获取顶点和三角形数据

## 关键实现逻辑
- 从 12 个顶点和 20 个三角面的正二十面体开始
- 每次细分将每个三角形分为 4 个：找到每条边的中点并归一化到单位球面
- 使用 `Lookup` (std::map) 缓存边的中点索引，避免重复创建顶点
- 细分 N 次后生成 `20 * 4^N` 个三角面

## 依赖关系
- `math/vec3.h` - 顶点坐标类型
