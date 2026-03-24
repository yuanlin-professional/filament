# main.cpp (uberz)

## 文件概述
UBERZ 工具将多个 filamat 材质文件和对应的 spec 规格文件聚合压缩为单一的 `.uberz` 归档文件。该归档包含每个材质的二进制数据和特性标志元数据，供 gltfio 在运行时按需加载。

## 核心函数
- `main()` - 入口：解析参数 -> 可选读取已有归档 -> 逐个添加材质和 spec -> 序列化输出
- `handleArguments()` - 命令行参数解析
- `insertMapEntry()` - 解析 KEY=VALUE 格式的模板替换参数

## 关键实现逻辑
1. 追加模式（`--append`）：先解压并读取已有的 `.uberz` 归档，将其中的材质转移到新归档
2. 对每个材质，读取 `name.filamat`（二进制）和 `name.spec`（特性描述），逐行解析 spec
3. spec 文件支持 `${MACRO}` 模板替换，通过 `--template` 参数指定替换值
4. 最终使用 `WritableArchive` 序列化为 zstd 压缩的二进制格式
5. 解压使用 `ZSTD_getFrameContentSize` 和 `ZSTD_decompress`

## 依赖关系
- `uberz/ReadableArchive` / `WritableArchive` - 归档读写
- `zstd` - 压缩库
- `tsl/robin_map` - 高效哈希表
