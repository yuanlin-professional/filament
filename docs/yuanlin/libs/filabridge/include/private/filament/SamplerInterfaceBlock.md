# SamplerInterfaceBlock.h / SamplerInterfaceBlock.cpp

## 文件概述
定义和实现 `SamplerInterfaceBlock` 类，用于描述着色器中采样器（纹理）的接口块。管理采样器的名称、类型、格式、绑定点、精度等元信息，与 `BufferInterfaceBlock` 类似但专用于纹理采样器。

## 核心类/结构体/函数
- **`SamplerInterfaceBlock`** - 采样器接口块描述类
  - `SamplerInfo` - 采样器信息结构体，包含 name、uniformName、binding、type、format、precision、filterable、multisample、stages、transformName
  - `Builder` - 构建器，支持 `name()`、`stageFlags()`、`add()` 链式调用
- **`generateUniformName()`** - 静态方法，根据组名和采样器名生成 uniform 变量名（格式：`groupName_samplerName`）
- **`filterSamplerList()`** - 静态方法，根据描述符集布局过滤采样器列表

## 关键实现逻辑
- 构造时验证采样器格式与 filterable 标志的一致性（int/uint 不可过滤，shadow 必须可过滤）
- `generateUniformName()` 将组名首字母转小写，用下划线连接组名和采样器名
- `filterSamplerList()` 移除不在描述符集布局中的采样器，用于变体裁剪

## 依赖关系
- `private/filament/DescriptorSets.h`
- `backend/DriverEnums.h`
- `utils/CString.h`、`utils/FixedCapacityVector.h`、`utils/ImmutableCString.h`、`utils/Panic.h`
