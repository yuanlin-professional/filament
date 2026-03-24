# matc - Filament 材质编译器

## 模块名称和概述

`matc` 是 Filament 的核心材质编译器，负责将 `.mat` 材质定义文件编译为 Filament 运行时可加载的 `.filamat` 二进制格式。它将材质中的着色器代码编译为多个目标后端（OpenGL、Vulkan、Metal、WebGL）的着色器二进制，并打包材质属性、参数和元数据。

## 目录结构

```
tools/matc/
    CMakeLists.txt                  # CMake 构建配置（含 matlang 库和 matc 可执行文件）
    src/
        main.cpp                    # matc 命令行入口
        matc/
            CommandlineConfig.h     # 命令行配置解析头文件
            CommandlineConfig.cpp   # 命令行参数处理实现
            Compiler.h              # 编译器接口定义
            Compiler.cpp            # 编译器核心实现
            MaterialCompiler.h      # 材质编译器高层封装头文件
            MaterialCompiler.cpp    # 材质编译器高层封装实现
```

## 核心功能

- **多后端编译**: 将材质着色器编译为 GLSL、ESSL、SPIR-V、Metal Shading Language 和 WGSL
- **材质打包**: 将着色器二进制、材质参数、混合模式等打包为单个 `.filamat` 文件
- **着色器优化**: 通过 SPIR-V 工具链进行着色器优化和验证
- **平台适配**: 根据目标平台生成对应的着色器变体
- **命令行配置**: 丰富的编译选项控制输出目标、优化级别等

### 使用方式

```bash
# 编译材质文件
matc -o output.filamat input.mat

# 指定目标平台
matc --platform=mobile -o output.filamat input.mat

# 查看帮助信息
matc --help
```

## 依赖关系

### matlang 静态库依赖
- **filamat** - 材质编译核心库
- **filabridge** - Filament 跨模块桥接库
- **utils** - 通用工具库
- **matp** - 材质解析库

### 第三方许可证依赖
- **glslang** - Khronos GLSL 参考编译器
- **spirv-cross** - SPIR-V 反编译器（用于跨编译到 Metal/GLSL）
- **spirv-tools** - SPIR-V 优化和验证工具
- **smol-v** - SPIR-V 压缩库

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | matc 命令行入口，解析参数并调用材质编译管线 |
| `src/matc/MaterialCompiler.h/cpp` | 材质编译器的高层封装，协调整个编译流程 |
| `src/matc/Compiler.h/cpp` | 底层编译器实现，驱动 glslang 和 SPIR-V 工具链 |
| `src/matc/CommandlineConfig.h/cpp` | 命令行参数解析和配置管理 |
| `CMakeLists.txt` | 定义 matlang 静态库和 matc 可执行文件两个构建目标 |
