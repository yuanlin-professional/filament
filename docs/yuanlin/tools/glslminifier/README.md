# glslminifier - GLSL 着色器代码压缩工具

## 模块名称和概述

`glslminifier` 是一个 GLSL 着色器源码压缩（minify）工具，用于减小嵌入到 Filament 引擎中的着色器代码体积。它在保持语义不变的前提下，移除注释、多余空白和不必要的格式化字符，从而减小最终二进制文件的大小。

## 目录结构

```
tools/glslminifier/
    CMakeLists.txt      # CMake 构建配置
    src/
        main.cpp        # 命令行入口
        GlslMinify.h    # GLSL 压缩器头文件
        GlslMinify.cpp  # GLSL 压缩器核心实现
    tests/
        test_glslminifier.cpp  # 单元测试
```

## 核心功能

- **注释移除**: 剥离单行（`//`）和多行（`/* */`）注释
- **空白优化**: 合并连续空白字符，移除不影响语义的空格和换行
- **安全压缩**: 保证压缩后的 GLSL 代码在所有目标平台上行为一致
- **流式处理**: 从标准输入读取、向标准输出写入，便于集成到构建管线

### 使用方式

```bash
# 压缩单个着色器文件
glslminifier < input.glsl > output.glsl

# 在构建系统中作为管线步骤
cat shader.vert | glslminifier > shader.min.vert
```

## 依赖关系

- **utils** - Filament 通用工具库
- **gtest**（仅测试）- Google Test 测试框架

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 命令行入口，读取 stdin 并输出压缩结果到 stdout |
| `src/GlslMinify.h/cpp` | GLSL 压缩核心算法实现，处理词法分析和空白优化 |
| `tests/test_glslminifier.cpp` | 单元测试，验证各种 GLSL 代码模式的压缩正确性 |
| `CMakeLists.txt` | 构建配置，生成可执行文件和测试目标 |
