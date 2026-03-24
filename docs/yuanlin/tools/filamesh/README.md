# filamesh - 网格模型格式转换工具

## 模块名称和概述

`filamesh` 是一个网格格式转换工具，将 Assimp 支持的各种 3D 模型格式（OBJ、FBX、glTF 等）转换为 Filament 自定义的高效二进制网格格式（`.filamesh`）。该格式专为快速加载和高效渲染而设计，是 Filament 测试应用和示例程序的主要网格数据来源。

## 目录结构

```
tools/filamesh/
    CMakeLists.txt      # CMake 构建配置
    README.md           # 原始英文文档（含详细二进制格式规范）
    src/
        main.cpp        # 主程序入口
        MeshWriter.h    # 网格写入器头文件
        MeshWriter.cpp  # 网格写入器实现，将 Assimp 数据序列化为 filamesh 格式
```

## 核心功能

- **多格式输入**: 通过 Assimp 支持 OBJ、FBX、glTF、3DS 等数十种模型格式
- **优化的二进制输出**: 生成包含单一顶点缓冲和单一索引缓冲的紧凑格式
- **自动属性计算**: 自动生成顶点位置、UV 坐标、切线、副切线和法线
- **网格压缩**: 支持通过 meshoptimizer 进行顶点和索引数据压缩
- **多子网格支持**: 每个子网格（Part）通过索引偏移和材质 ID 区分
- **AABB 包围盒**: 自动计算整体和每个子网格的轴对齐包围盒

### 使用方式

```bash
filamesh source_mesh.obj destination.filamesh
```

### 二进制格式概览

filamesh 文件结构依次包含:
1. **魔数标识** - 8 字节 "FILAMESH"
2. **文件头** - 版本号、子网格数、AABB、顶点属性偏移/步幅等
3. **顶点数据** - half4 位置 + short4 切线四元数 + ubyte4 颜色 + half2 UV
4. **索引数据** - uint32 或 uint16 索引
5. **子网格描述** - 每个 Part 的索引范围、材质 ID 和包围盒
6. **材质列表** - 材质名称字符串

## 依赖关系

- **assimp** - Open Asset Import Library，提供多格式模型加载能力
- **filameshio** - Filament 网格 IO 库
- **meshoptimizer** - 网格数据压缩与优化库

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 程序入口，调用 Assimp 加载模型并驱动 MeshWriter 输出 |
| `src/MeshWriter.h/cpp` | 核心转换逻辑，将 Assimp 场景数据序列化为 filamesh 二进制格式 |
| `CMakeLists.txt` | 构建配置，链接 assimp、filameshio 和 meshoptimizer |
