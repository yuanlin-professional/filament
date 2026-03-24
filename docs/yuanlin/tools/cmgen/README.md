# cmgen - 立方体贴图与 IBL 环境光照生成工具

## 模块名称和概述

`cmgen` 是 Filament 的立方体贴图生成命令行工具，用于从 HDR 环境贴图生成球谐系数（SH）和 mipmap 层级。它支持等距矩形投影（equirectangular）和立方体贴图交叉（cross）格式作为输入，能够生成用于基于图像光照（IBL）的预滤波环境贴图和模糊天空盒。

## 目录结构

```
tools/cmgen/
    CMakeLists.txt           # CMake 构建配置
    README.md                # 原始英文文档
    src/
        cmgen.cpp            # 主程序源码，包含命令行参数解析和核心处理逻辑
        ProgressUpdater.h    # 进度更新器头文件
        ProgressUpdater.cpp  # 进度更新器实现
    tests/
        test_cmgen.cpp       # 单元测试
```

## 核心功能

- **多格式输入**: 支持 PNG（8/16位）、Radiance HDR、Photoshop PSD（16/32位）、OpenEXR 格式
- **多格式输出**: 支持 cubemap、equirect、octahedron、KTX 类型输出
- **IBL 生成**: 预计算漫反射球谐系数和镜面反射粗糙度预滤波贴图
- **天空盒生成**: 可生成带模糊效果的天空盒纹理
- **部署模式**: 通过 `--deploy` 参数一键生成部署所需的全部资源
- **DFG LUT**: 支持生成 DFG 查找表

### 使用方式

```bash
# 基本用法
cmgen [options] <input-file>

# 生成部署资源
cmgen --deploy=output_dir --size=256 environment.hdr

# 生成着色器用球谐系数
cmgen --sh-shader environment.hdr

# 指定输出格式
cmgen --format=ktx --type=ktx environment.hdr
```

## 依赖关系

- **ibl** - Filament 的 IBL 处理库，提供球谐计算和预滤波算法
- **imageio** - 图像读写库，支持多种 HDR/LDR 格式
- **libpng** / **tinyexr** / **libz** - 第三方图像编解码库（许可证依赖）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/cmgen.cpp` | 工具主入口，实现命令行解析、立方体贴图处理和输出生成 |
| `src/ProgressUpdater.h/cpp` | 进度指示器，用于长时间运算时显示处理进度 |
| `tests/test_cmgen.cpp` | 基于 Google Test 的单元测试 |
| `CMakeLists.txt` | 定义构建目标、链接依赖和安装规则 |
