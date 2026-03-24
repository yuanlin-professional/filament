# Spirv (Spirv.h / Spirv.cpp)

## 文件概述

提供 SPIR-V 着色器字节码的后处理工具，主要功能是将 SPIR-V 中的特化常量（Specialization Constants）转换为普通常量。这是针对某些驱动程序 bug 的变通方案。

## 核心类/结构体

本文件仅包含 `filament::backend::fvkutils` 命名空间下的函数，无类定义。

## 关键实现逻辑

### `workaroundSpecConstant()`
核心变通函数，遍历 SPIR-V 指令流并就地修改：

1. **第一遍扫描**: 查找 `OpDecorate %var SpecId N` 指令，建立变量 ID 到 SpecId 的映射
2. **指令转换**: 遇到 `OpSpecConstant`/`OpSpecConstantTrue`/`OpSpecConstantFalse` 时：
   - 通过映射找到对应的 SpecId
   - 根据程序提供的特化常量值替换指令
   - 布尔值：转换为 `OpConstantTrue`/`OpConstantFalse`（3 字长度）
   - 浮点/整数值：转换为 `OpConstant`（4 字长度），值通过 reinterpret_cast 复制
3. **装饰删除**: SpecId 装饰指令不写入输出（静默跳过）
4. **其他指令**: 原样复制到输出缓冲区

### 变通原因
- Qualcomm 驱动：无法使用特化常量确定数组大小（issue #6444）
- 某内部驱动：不对由 spec-const 布尔值守护的代码块进行死代码消除

### 实现约束
不能直接修改原始 blob（写入 Nop 指令），因为 spirv-validator 不正确处理 Nop 指令，而 SwiftShader 在编译前会验证着色器。因此采用复制+跳过的策略。

## 依赖关系

- `backend/Program.h` - ShaderBlob 和 SpecializationConstant 定义
- `utils/FixedCapacityVector.h` - 特化常量容器
- `spirv/unified1/spirv.hpp` - SPIR-V 操作码定义（OpDecorate、OpSpecConstant 等）
