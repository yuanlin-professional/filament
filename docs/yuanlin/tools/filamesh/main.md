# main.cpp

## 文件概述
FILAMESH 命令行工具的入口。将 FBX/OBJ 等网格文件转换为 Filament 优化的二进制格式（`.filamesh`）。使用 assimp 库进行网格导入，支持交错顶点、snorm UV 和 meshoptimizer 压缩。

## 核心函数
- `main()` - 入口：参数解析 -> assimp 导入 -> 预处理 -> 节点遍历 -> 序列化输出
- `preprocessNode()` - 预处理阶段：检查法线/纹理坐标，确定 UV 范围以决定是否使用 snorm 格式
- `processNode<INTERLEAVED, SNORMUVS>()` - 模板化的节点处理函数，将 assimp 数据转换为内部格式

## 关键实现逻辑
1. assimp 配置启用三角化、法线生成、切线计算、缓存优化和实例查找
2. 通过检查 UV 范围 [-1,1] 自动决定使用 half float 还是 snorm16 存储纹理坐标
3. 切线空间使用 `mat3f::packTangentFrame` 打包为四元数，以 snorm16 存储
4. 模板参数控制交错/分离布局和 UV 编码方式，编译时生成 4 种特化版本

## 依赖关系
- `MeshWriter` - 网格序列化器
- `assimp/` - 网格导入库
- `filameshio/filamesh.h` - 格式定义
