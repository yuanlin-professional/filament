# Filament 工具集概览

## 简介

Filament 是一个高性能的实时物理渲染引擎，其 `tools/` 目录下包含了一系列命令行工具，用于材质编译、资源处理、图像操作和代码生成等任务。这些工具共同构成了 Filament 的离线资产处理管线。

## 工具列表

| 工具名称 | 说明 | 实现语言 |
|---------|------|---------|
| [beamsplitter](beamsplitter/README.md) | C++ 头文件解析与多语言绑定代码生成器 | Go |
| [cmgen](cmgen/README.md) | 立方体贴图与环境光照 IBL 生成工具 | C++ |
| [cso-lut](cso-lut/README.md) | 锥体/球体遮蔽查找表生成器 | C++ |
| [diffimg](diffimg/README.md) | 图像差异比较工具 | C++ |
| [filamesh](filamesh/README.md) | 网格模型格式转换工具 | C++ |
| [glslminifier](glslminifier/README.md) | GLSL 着色器代码压缩工具 | C++ |
| [matc](matc/README.md) | Filament 材质编译器 | C++ |
| [matedit](matedit/README.md) | 已编译材质文件编辑工具 | C++ |
| [matinfo](matinfo/README.md) | 已编译材质信息查看工具 | C++ |
| [mipgen](mipgen/README.md) | Mipmap 多级纹理生成工具 | C++ |
| [normal-blending](normal-blending/README.md) | 法线贴图混合工具 | C++ |
| [resgen](resgen/README.md) | 二进制资源聚合与嵌入代码生成器 | C++ |
| [rgb-to-lmsr](rgb-to-lmsr/README.md) | RGB 到 LMSR 色彩空间变换矩阵计算工具 | C++ |
| [roughness-prefilter](roughness-prefilter/README.md) | 粗糙度预滤波贴图生成工具 | C++ |
| [specgen](specgen/README.md) | 光谱积分矩阵生成器（用于实时色散渲染） | C++ |
| [specular-color](specular-color/README.md) | 导体材质镜面反射颜色计算工具 | C++ |
| [uberz](uberz/README.md) | Uber 着色器材质归档打包工具 | C++ |
| [zbloat](zbloat/README.md) | 二进制体积分析与可视化工具 | Python |

## 工具分类

### 材质管线工具

- **matc** - 将 `.mat` 材质定义文件编译为 Filament 运行时可加载的 `.filamat` 格式
- **matinfo** - 查看已编译材质文件中的着色器代码、参数及元信息
- **matedit** - 对已编译材质执行后处理操作（如外部着色器编译）
- **uberz** - 将多个 `.filamat` 材质打包并压缩为单个 `.uberz` 归档文件

### 图像与纹理处理工具

- **cmgen** - 从 HDR 环境贴图生成立方体贴图、IBL 光照和球谐系数
- **mipgen** - 为纹理图像生成完整的 mipmap 层级链
- **normal-blending** - 使用重定向法线映射技术混合两张法线贴图
- **roughness-prefilter** - 根据法线贴图生成预滤波粗糙度贴图以减少着色走样
- **diffimg** - 对比两张图像并输出差异分析结果（JSON 格式）

### 资源与代码生成工具

- **resgen** - 将多个二进制文件聚合并生成可嵌入 C/C++ 项目的资源包
- **beamsplitter** - 解析 C++ 头文件并自动生成 Java/JavaScript 绑定代码
- **glslminifier** - 压缩 GLSL 着色器源码以减小嵌入体积
- **filamesh** - 将通用 3D 模型格式转换为 Filament 自定义的高效二进制网格格式

### 色彩科学与研究工具

- **specular-color** - 基于光谱数据计算导体材质（金属）的基础反射颜色
- **rgb-to-lmsr** - 计算 RGB (Rec.709) 到 LMSR 锥体响应空间的变换矩阵
- **cso-lut** - 生成锥体/球体遮蔽（Cone/Sphere Occlusion）查找表
- **specgen** - 为实时色散效果预计算光谱积分矩阵和 IOR 偏移量

### 分析与调试工具

- **zbloat** - 分析 Android 应用中 Filament 库的二进制体积组成并生成交互式报告

## 构建方式

大部分 C++ 工具通过 CMake 构建系统进行编译，在 Filament 项目的整体构建过程中自动生成。

```bash
# 构建 Filament 项目（包含所有 C++ 工具）
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)

# 构建后工具位于 build/tools/<name>/ 目录下
```

`beamsplitter` 使用 Go 编写，可独立运行：

```bash
cd tools/beamsplitter && go run .
```

`zbloat` 基于 Python 脚本，无需编译，直接运行即可：

```bash
./tools/zbloat/zbloat.py <target>
```

## 典型工作流

一个典型的 Filament 资产处理管线可能包含以下步骤：

1. 使用 `matc` 将材质定义（`.mat`）编译为运行时格式（`.filamat`）
2. 使用 `cmgen` 从 HDR 环境贴图生成 IBL 光照数据
3. 使用 `mipgen` 为纹理生成 mipmap 层级
4. 使用 `filamesh` 将 3D 模型转换为 Filament 网格格式
5. 使用 `resgen` 将所有资源聚合并嵌入到应用二进制中
6. 使用 `uberz` 打包 uber shader 材质归档（用于 glTF 加载）
7. 使用 `matinfo` 或 `zbloat` 进行调试和分析

## 源码位置

所有工具的源码位于 Filament 项目的 `tools/` 目录下，每个工具占据一个独立的子目录。

## 许可证

所有工具遵循 Apache License 2.0 许可证。部分工具依赖的第三方库有各自的许可证要求，具体信息可通过各工具的 `--license` 参数查看。
