# uberz - Uber 着色器材质归档打包工具

## 模块名称和概述

`uberz` 是一个用于将多个 Filament 材质文件（`.filamat`）聚合并压缩为单个归档文件（`.uberz`）的命令行工具。每个材质在归档中附带一个 `.spec` 规格文件，描述该材质支持的特性集。该工具主要服务于 Filament 的 gltfio 模块，将一组 uber shader 材质打包用于 glTF 模型的渲染。

## 目录结构

```
tools/uberz/
    CMakeLists.txt      # CMake 构建配置
    src/
        main.cpp        # 主程序源码
```

## 核心功能

- **材质聚合**: 将多个 `.filamat` 文件合并为单个归档
- **Zstd 压缩**: 使用 Zstandard 算法压缩归档以减小体积
- **特性规格**: 每个材质配对一个 `.spec` 文件，描述着色模型和特性标记
- **追加模式**: 通过 `--append` 在现有归档基础上追加新材质
- **模板替换**: 通过 `--template` 参数在 spec 文件解析前执行宏替换
- **CMake 集成**: 通常由 CMake 构建系统自动调用

### 使用方式

```bash
# 基本用法：打包材质（自动查找 name.filamat 和 name.spec）
uberz material1 material2 material3

# 指定输出文件名
uberz -o custom_archive.uberz material1 material2

# 追加模式
uberz --append -o materials.uberz new_material

# 模板替换
uberz -T BACKEND=opengl material1

# 安静模式
uberz -q material1 material2
```

### 归档格式

对于每个材质名 `<name>`，工具在当前工作目录查找：
- `<name>.filamat` - 编译后的材质二进制文件
- `<name>.spec` - 材质特性规格描述文件

归档使用 Zstd 压缩后写入输出文件。

## 依赖关系

- **uberzlib** - Filament uber shader 归档读写库（ReadableArchive / WritableArchive）
- **utils** - 通用工具库（路径处理、内存对齐、命令行解析）
- **zstd** - Facebook Zstandard 压缩库
- **tsl::robin_map** - 高性能哈希表

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现：参数解析、现有归档读取（追加模式）、材质和 spec 文件加载、归档序列化输出 |
| `CMakeLists.txt` | 构建配置，链接 uberzlib 库 |
