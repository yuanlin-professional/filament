# WebGPUProgram

## 文件概述

`WebGPUProgram` 是 `HwProgram` 的 WebGPU 实现，持有一个着色器程序的顶点、片段和计算着色器模块。

## 核心类/结构体

- **`WebGPUProgram`** (继承 `HwProgram`)
  - `vertexShaderModule` / `fragmentShaderModule` / `computeShaderModule` - 三个 WGSL 着色器模块

## 关键实现逻辑

1. **特化常量替换**: `replaceSpecConstants` 函数在 WGSL 源码中查找 `FILAMENT_SPEC_CONST_<id>_` 格式的占位符，替换为运行时常量值（int32/float/bool）
2. **同步编译**: `createShaderModule` 使用 `instance.WaitAny` 同步等待着色器编译完成，超时为 1 秒
3. **编译错误处理**: 收集所有编译信息/警告/错误，若有错误则 panic 并输出详细信息
4. **空着色器处理**: 若某阶段无着色器源码（如无计算着色器），返回 nullptr

## 依赖关系

- `WebGPUConstants.h` - 编译超时常量
- `WebGPUStrings.h` - 着色器阶段名称转换
- `backend/Program.h` - Filament 程序抽象
