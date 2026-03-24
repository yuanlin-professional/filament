# matinfo - 已编译材质信息查看工具

## 模块名称和概述

`matinfo` 是一个用于查看 Filament 已编译材质文件（`.filamat`）内部信息的命令行工具。它可以提取并显示材质中的着色器代码（GLSL、ESSL、SPIR-V、Metal、WGSL）、材质参数、混合模式、着色器字典等详细元数据，是材质调试和分析的重要工具。

## 目录结构

```
tools/matinfo/
    CMakeLists.txt      # CMake 构建配置
    README.md           # 原始英文文档
    src/
        main.cpp        # 主程序，包含完整的参数解析和信息提取逻辑
```

## 核心功能

- **着色器代码查看**: 提取并显示各后端的着色器代码
  - GLSL（OpenGL 桌面端）
  - ESSL 1.0（OpenGL ES / WebGL）
  - SPIR-V（Vulkan）
  - Metal Shading Language
  - WGSL（WebGPU）
- **SPIR-V 反编译**: 可将 SPIR-V 二进制反编译为可读的 GLSL
- **着色器字典查看**: 显示各后端的着色器字典内容
- **材质元数据**: 展示材质属性、参数、混合模式等配置信息
- **二进制输出**: 支持以二进制模式输出着色器数据

### 使用方式

```bash
# 查看材质基本信息
matinfo material.filamat

# 查看 GLSL 着色器代码
matinfo --print-glsl material.filamat

# 查看 Metal 着色器
matinfo --print-metal material.filamat

# 查看 SPIR-V（反编译为 GLSL）
matinfo --print-spirv --transpile material.filamat

# 查看着色器字典
matinfo --print-dictionary-glsl material.filamat
```

## 依赖关系

- **matdbg** - 材质调试库，提供着色器提取和文本格式化功能
- **filaflat** - Filament 扁平化数据容器（ChunkContainer）
- **backend_headers** - 后端枚举定义（Backend 类型等）
- **glslang** - GLSL 参考编译器（提供 SPIR-V 头文件）
- **spirv-cross** - SPIR-V 反编译器（用于 SPIR-V -> GLSL 转译）
- **spirv-tools** - SPIR-V 反汇编工具
- **smol-v** - SPIR-V 压缩库（许可证依赖）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现，包含命令行解析、材质文件读取和多后端着色器提取显示 |
| `CMakeLists.txt` | 构建配置，链接 matdbg、filaflat 和 SPIR-V 工具链 |
