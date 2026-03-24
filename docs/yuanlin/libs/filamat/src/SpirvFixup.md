# SpirvFixup.h / SpirvFixup.cpp

## 文件概述
对 SPIR-V 反汇编文本进行修补，将自定义的 `filament_gl_ClipDistance` 输出变量标记为内建的 `gl_ClipDistance`。这是因为 glslang 不支持 `EXT_clip_cull_distance` 扩展的变通方案。

## 核心类/结构体/函数
- **`fixupClipDistance()`** - 在 SPIR-V 反汇编文本中查找 `OpDecorate %filament_gl_ClipDistance Location` 指令，替换为 `OpDecorate %filament_gl_ClipDistance BuiltIn ClipDistance`

## 关键实现逻辑
简单的字符串查找替换操作：定位 `Location` 装饰指令并替换为 `BuiltIn ClipDistance` 装饰。仅用于 ES 环境生成的 SPIR-V。

## 依赖关系
- `<string>`
