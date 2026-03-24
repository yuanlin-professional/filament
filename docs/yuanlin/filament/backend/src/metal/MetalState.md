# MetalState

## 文件概述

MetalState 定义了 Metal 后端的渲染状态管理系统，包括管线状态、深度/模板状态、采样器状态等的结构体定义、状态缓存（StateCache）和状态跟踪器（StateTracker）。同时定义了 Metal buffer binding 的布局约定和描述符集绑定管理。

**源文件**: `MetalState.h`, `MetalState.mm`

## 核心类/结构体

- **`VertexDescription`** - 顶点属性布局描述（528 字节），包含属性数组和布局数组
- **`BlendState`** - 混合状态（56 字节），包含混合操作、混合因子和开关
- **`MetalPipelineState`** - 完整管线状态（696 字节），包含着色器函数、顶点描述、附件格式、采样数、混合等
- **`DepthStencilState`** - 深度/模板状态（96 字节），包含前/后面模板描述和深度比较/写入
- **`SamplerState`** - 采样器状态（4 字节），封装 `SamplerParams`
- **`ArgumentEncoderState`** - Argument Encoder 状态
- **`BufferState`** - 缓冲区绑定状态
- **`StateCache<StateType, MetalType, Creator, HashFn>`** - 通用状态缓存模板，使用 robin_map 缓存已创建的 Metal 状态对象
- **`StateTracker<StateType>`** - 通用状态跟踪器模板，通过脏标记避免冗余状态切换
- **`MetalBufferBindings<N, stage>`** - 描述符集缓冲区绑定管理，跟踪 buffer 和 offset 的脏位

## 关键实现逻辑

- **Buffer Binding 布局**:
  - 光栅化: 0=零缓冲区, 1-16=顶点缓冲区, 20=推送常量, 21-24=描述符集, 25=动态偏移
  - 计算: 0-3=SSBO, 20=推送常量, 21-24=描述符集, 25=动态偏移
- **管线状态创建**: `PipelineStateCreator` 根据 `MetalPipelineState` 构造 `MTLRenderPipelineDescriptor` 并创建管线状态对象，失败时抛出 NSException
- **深度/模板状态创建**: `DepthStateCreator` 分别配置前/后面模板描述符
- **采样器状态创建**: `SamplerStateCreator` 映射过滤、包裹、各向异性、比较函数等参数，启用 `supportArgumentBuffers`
- **增量绑定**: `MetalBufferBindings` 使用 `bitset8` 跟踪脏 buffer 和脏 offset，仅脏 offset 时调用 `setBufferOffset`（更轻量），buffer 变化时调用 `setBuffer`
- **状态哈希**: 使用 MurmurHash 对状态结构体进行哈希（依赖 static_assert 确保无隐藏填充字节）

## 依赖关系

- `MetalEnums.h` - 枚举转换函数（采样器、混合等）
- `private/backend/Driver.h`、`backend/DriverEnums.h`、`backend/Program.h` - Filament 类型
- `Metal/Metal.h` - Metal 状态对象类型
- `tsl/robin_map.h` - 哈希表
- `utils/Hash.h`、`utils/bitset.h`、`utils/Invocable.h` - 工具库
