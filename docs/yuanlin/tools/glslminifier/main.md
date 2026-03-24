# main.cpp (glslminifier)

## 文件概述
GLSLMINIFIER 命令行工具入口。读取 GLSL 着色器文件，执行压缩，并可选地插入 `#line` 指令用于调试。

## 核心函数
- `main()` - 读取输入文件 -> 调用 `minifyGlsl()` 压缩 -> 输出结果

## 关键实现逻辑
1. 支持 `-O none` 选项禁用优化（直通模式）
2. `--line` 选项在文件开头插入 `#line 0 "name"` 指令，用 `GL_GOOGLE_cpp_style_line_directive` 包裹
3. 输出可写入文件或标准输出，自动创建输出目录

## 依赖关系
- `GlslMinify.h` - GLSL 压缩库
- `utils/Path` - 路径工具
