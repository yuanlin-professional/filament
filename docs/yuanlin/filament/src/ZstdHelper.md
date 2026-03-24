# ZstdHelper.h / ZstdHelper.cpp

## 文件概述
ZstdHelper 提供 Zstandard (zstd) 压缩算法的工具函数封装，用于 Filament 中材质源代码等数据的解压缩。

## 核心类/结构体/函数
- **ZstdHelper** - Zstd 工具类（全部静态方法）
  - `isCompressed()` - 检测数据是否为 Zstd 压缩格式（通过魔数 0xFD2FB528）
  - `getDecodedSize()` - 获取解压后的数据大小
  - `decompress()` - 将 Zstd 压缩数据解压到预分配缓冲区

## 关键实现逻辑
- **魔数检测**：`isCompressed()` 逐字节读取前 4 个字节并按小端序重组为 uint32_t，避免未对齐访问导致的未定义行为（UBSan 兼容）
- **解压封装**：`getDecodedSize()` 和 `decompress()` 分别封装 `ZSTD_getFrameContentSize()` 和 `ZSTD_decompress()`
- 所有方法声明为 `noexcept`，错误通过返回值（0 或 ZSTD 错误码）传递

## 依赖关系
- `zstd.h` - Zstandard 压缩库

## 被引用关系
- `MaterialParser` - 解压材质文件中 Zstd 压缩的源代码块
- 其他需要 Zstd 解压的 Filament 内部组件
