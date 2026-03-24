# PushConstantDefinitions.h

## 文件概述
定义 Filament 内部使用的推送常量（Push Constant）列表。当前仅包含一个用于变形动画的缓冲区偏移常量。

## 核心类/结构体/函数
- **`PUSH_CONSTANT_STRUCT_VAR_NAME`** - 推送常量结构体在着色器中的变量名（"pushConstants"）
- **`PUSH_CONSTANTS`** - 预定义的推送常量列表，目前包含：
  - `morphingBufferOffset`（INT 类型，顶点阶段）

## 关键实现逻辑
通过 `static_assert` 验证 `PushConstantIds::MORPHING_BUFFER_OFFSET` 的枚举值与数组索引一致。

## 依赖关系
- `private/filament/EngineEnums.h`
- `private/filament/PushConstantInfo.h`
- `utils/FixedCapacityVector.h`
