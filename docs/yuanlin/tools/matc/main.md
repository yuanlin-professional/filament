# main.cpp (matc)

## 文件概述
MATC（Material Compiler）命令行工具入口。编译 Filament 材质定义文件（`.mat`）为二进制材质包。

## 核心函数
- `main()` - 创建命令行配置 -> 创建材质编译器 -> 执行编译

## 关键实现逻辑
- 极简的入口文件，将命令行参数传递给 `CommandlineConfig`，然后交由 `MaterialCompiler` 处理

## 依赖关系
- `matc/CommandlineConfig` - 命令行参数解析
- `matc/MaterialCompiler` - 材质编译核心
