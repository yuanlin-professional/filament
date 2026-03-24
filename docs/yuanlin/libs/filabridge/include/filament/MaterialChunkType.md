# MaterialChunkType.h

## 文件概述
定义材质二进制文件（.filamat）中各数据块（Chunk）的类型标识符。每个 ChunkType 是一个 64 位整数，由 8 字符字符串编码而成，用于在序列化/反序列化材质文件时标识不同的数据段。

## 核心类/结构体/函数
- **`charTo64bitNum()`** - 将 8 字符字符串打包为 64 位整数的 constexpr 辅助函数
- **`ChunkType` 枚举** - 定义了所有材质数据块类型，包括：
  - 着色器相关：`MaterialGlsl`、`MaterialSpirv`、`MaterialMetal`、`MaterialWgsl`、`MaterialMetalLibrary`
  - Uniform/Sampler 接口块：`MaterialUib`、`MaterialSib`、`MaterialSubpass`
  - 材质属性：`MaterialShading`、`MaterialBlendingMode`、`MaterialDomain`、`MaterialRefraction` 等
  - 字典块：`DictionaryText`、`DictionarySpirv`、`DictionaryMetalLibrary`
  - 元数据：`MaterialName`、`MaterialVersion`、`MaterialCacheId`、`MaterialFeatureLevel`

## 关键实现逻辑
`charTo64bitNum` 通过位移操作将 8 个字符分别放置到 64 位整数的 8 个字节中，实现高效的编译期常量标识符生成。

## 依赖关系
- `<stdint.h>`
- `utils/compiler.h`
