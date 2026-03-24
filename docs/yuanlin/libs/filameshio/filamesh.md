# filamesh - Filamesh 二进制网格文件格式定义

## 文件概述

定义了 Filament 专用的二进制网格文件格式 "filamesh" 的数据结构。该格式由同名命令行工具生成，包含顶点数据、索引数据、子网格部件和材质引用。支持交错/非交错布局、SNORM16 纹理坐标和 meshoptimizer 压缩。

**源文件**: `libs/filameshio/include/filameshio/filamesh.h`

## 核心类/结构体

### 常量
- **MAGICID**: 文件魔数 `"FILAMESH"`（8 字节）
- **VERSION**: 当前版本号 1

### `IndexType` 枚举
- `UI32`: 32 位索引
- `UI16`: 16 位索引

### `Flags` 枚举（位标志）
- `INTERLEAVED`: 顶点数据交错排列
- `TEXCOORD_SNORM16`: 纹理坐标使用 SNORM16 格式（否则使用 HALF2）
- `COMPRESSION`: 顶点/索引数据使用 meshoptimizer 压缩

### `CompressionHeader`
非交错模式下各属性的压缩数据大小（字节）：`positions`、`tangents`、`colors`、`uv0`、`uv1`

### `Header`
文件头，包含版本号、部件数、AABB 包围盒、标志位、各属性在顶点中的偏移和步幅、顶点数/大小、索引类型/数/大小

### `Part`
子网格部件描述：索引偏移、索引数量、最小/最大顶点索引、材质索引、AABB 包围盒

## 依赖关系

- `<filament/Box.h>` - 包围盒类型
