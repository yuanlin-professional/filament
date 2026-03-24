# builtinResource.h

## 文件概述
定义 glslang 解析器所需的 `TBuiltInResource` 默认配置常量。这些值指定了着色器编译时的各种硬件限制（如最大纹理单元数、最大 uniform 分量数等）。

## 核心类/结构体/函数
- **`DefaultTBuiltInResource`** - glslang 内建资源限制的全局常量实例，包含：
  - 纹理单元限制（32 个纹理单元、80 个组合纹理单元）
  - 顶点属性限制（64 个）
  - Uniform 限制（4096 分量）
  - 计算着色器工作组大小（1024x1024x64）
  - Varying 限制（60 个分量）
  - 其他图形硬件限制

## 关键实现逻辑
纯常量定义，直接初始化 `TBuiltInResource` 结构体。这些值对应 OpenGL ES 3.x 规范的最小要求或合理默认值。

## 依赖关系
- `glslang/Include/intermediate.h`
