# MeshAssimp.h / MeshAssimp.cpp

## 文件概述
使用 Assimp 库加载 3D 模型文件（支持 glTF、OBJ 等格式），将其转换为 Filament 可渲染对象。处理材质、纹理、骨骼层级和 PBR 属性。

## 核心类

### `MeshAssimp`
- `addFromFile(path, materials, overrideMaterial)` - 从文件加载模型，创建顶点/索引缓冲区和 Renderable 实体
- `getRenderables()` - 获取所有可渲染实体

### 内部结构体
- `Part` - 网格的子部分，含材质名称和 PBR 属性
- `Mesh` - 一个节点的网格数据，含多个 Part 和变换矩阵
- `Asset` - 加载的完整资源数据

## 关键实现逻辑
- 使用 Assimp 进行三角化、法线生成、切线空间计算等预处理
- glTF 材质处理：运行时生成 GLSL 着色器代码并编译为 Filament Material
- 根据 UV 范围自动选择 SNORM16 或 HALF2 格式存储纹理坐标
- 使用 `MaterialConfig` 哈希实现材质缓存，避免重复编译相同配置的材质
- 支持嵌入式纹理（`*N` 格式路径）和外部纹理文件

## 依赖关系
- `assimp/*` - 模型导入
- `filamat/MaterialBuilder.h` - 运行时材质编译
- `stb_image.h` - 纹理图像加载
- `filament/*` - 各种 Filament 渲染对象
