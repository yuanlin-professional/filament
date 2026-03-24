# BufferInterfaceBlock.h / BufferInterfaceBlock.cpp

## 文件概述
定义和实现 `BufferInterfaceBlock` 类，用于描述 GPU 缓冲区（UBO/SSBO）的接口块布局。该类管理缓冲区中各字段的名称、类型、对齐、偏移等元信息，是连接 CPU 数据与 GPU 着色器 uniform 的桥梁。

## 核心类/结构体/函数
- **`BufferInterfaceBlock`** - 缓冲区接口块描述类
  - `FieldInfo` - 字段信息结构体，包含名称、偏移、步长、类型、精度等
  - `Alignment` - 对齐方式（std140/std430）
  - `Target` - 目标类型（UNIFORM/SSBO）
  - `Qualifier` - 限定符（COHERENT、WRITEONLY、READONLY、VOLATILE、RESTRICT）
- **`Builder`** - 构建器模式，通过链式调用 `name()`、`target()`、`alignment()`、`add()` 等方法构建接口块
- **`InterfaceBlockEntry`** - 构建器的输入条目结构体

## 关键实现逻辑
- 构造函数根据 std140/std430 规则计算每个字段的对齐和偏移量
- `baseAlignmentForType()` 返回各类型的基础对齐值（1/2/4 个 uint32_t）
- `strideForType()` 返回各类型的步长
- 数组在 std140 模式下强制对齐到 float4（4 个 uint32_t）
- 变长数组仅允许作为 SSBO 的最后一个条目

## 依赖关系
- `backend/DriverEnums.h`
- `utils/CString.h`、`utils/FixedCapacityVector.h`、`utils/Panic.h`
- `math/vec4.h`
