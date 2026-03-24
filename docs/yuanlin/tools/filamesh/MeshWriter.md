# MeshWriter.h / MeshWriter.cpp

## 文件概述
Filament 网格二进制格式的写入器。负责将 assimp 导入的网格数据优化并序列化为 `.filamesh` 格式，支持交错/分离顶点布局、meshoptimizer 压缩和索引优化。

## 核心类
- `MeshWriter` - 网格序列化器
  - `serialize()` - 将 Mesh 数据写入输出流
  - `optimize()` - 使用 meshoptimizer 优化三角形顺序和顶点获取
- `Mesh` - 内部网格数据结构，包含顶点（position/tangents/color/uv0/uv1）、索引和材质信息
- `Vertex` - 顶点结构：half4 位置 + short4 切线 + ubyte4 颜色 + ushort2 UV

## 关键实现逻辑
1. `optimize()` 先用 `meshopt_optimizeVertexCache` 优化三角形缓存局部性，再用 `meshopt_optimizeVertexFetch` 优化顶点获取顺序
2. 压缩模式下使用 `meshopt_encodeVertexBuffer` 和 `meshopt_encodeIndexBuffer` 对顶点和索引数据进行压缩编码
3. 分离布局下对每个顶点属性独立压缩，压缩头记录各属性的压缩大小
4. 支持 16 位和 32 位索引，根据顶点数自动选择

## 依赖关系
- `filameshio/filamesh.h` - 文件格式头定义
- `meshoptimizer` - 网格优化库
- `math/` - 半精度浮点和四元数数学
