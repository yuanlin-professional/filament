# DriverEnums.h

## 文件概述

Filament 后端驱动的核心类型和枚举定义文件。定义了渲染引擎所使用的所有基础类型，包括后端选择、纹理格式、采样器参数、混合状态、模板测试等。这些类型虽然是公开的，但通常应通过公共类的内部重定义来使用。

## 核心枚举

| 枚举 | 说明 |
|------|------|
| `Backend` | 后端类型选择（OpenGL/Vulkan/Metal/WebGPU/Noop） |
| `FeatureLevel` | 后端功能级别（ES 2.0/3.0/3.1） |
| `ShaderModel` | 着色器模型（Mobile/Desktop） |
| `ShaderLanguage` | 着色器语言（ESSL1/ESSL3/SPIRV/MSL/WGSL） |
| `ShaderStage` | 着色器阶段（Vertex/Fragment/Compute） |
| `TextureFormat` | 纹理格式（100+ 种格式，含压缩格式） |
| `TextureUsage` | 纹理用途位掩码（附件/采样/上传等） |
| `PixelDataFormat` | 像素数据格式（R/RG/RGB/RGBA/深度等） |
| `PixelDataType` | 像素数据类型（UBYTE/FLOAT/HALF 等） |
| `SamplerType` | 采样器类型（2D/3D/Cubemap/Array 等） |
| `PrimitiveType` | 图元类型（点/线/三角形及条带） |
| `BlendEquation` / `BlendFunction` | 混合方程和函数 |
| `CullingMode` | 面剔除模式 |
| `DescriptorType` | 描述符类型（各种采样器/UBO/SSBO） |
| `CompilerPriorityQueue` | 着色器编译优先级 |

## 核心结构体

| 结构体 | 说明 |
|--------|------|
| `Viewport` | 视口定义（位置和尺寸） |
| `DepthRange` | 深度范围映射 |
| `SamplerParams` | 采样器参数（过滤/环绕/比较等，4字节位域） |
| `RasterState` | 光栅化状态（剔除/混合/深度/模板，4字节位域） |
| `RenderPassParams` | 渲染通道参数 |
| `StencilState` | 模板测试状态 |
| `PolygonOffset` | 多边形偏移 |
| `Attribute` | 顶点属性描述符 |
| `DescriptorSetLayout` | 描述符集布局 |

## 重要常量

- `MAX_VERTEX_ATTRIBUTE_COUNT` = 16
- `MAX_SAMPLER_COUNT` = 62
- `MAX_DESCRIPTOR_SET_COUNT` = 4
- `SWAP_CHAIN_CONFIG_*` - 交换链配置标志

## 依赖关系

- `backend/Platform.h` - 平台抽象类
- `backend/PresentCallable.h` - 帧呈现回调
- `utils/BitmaskEnum.h` - 位掩码枚举操作
- `utils/CString.h` / `utils/FixedCapacityVector.h` / `utils/Invocable.h`
- `math/vec4.h` - 数学向量
