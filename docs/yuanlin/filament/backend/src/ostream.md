# ostream

## 文件概述

`ostream.cpp` 为 Filament 后端的所有枚举类型和核心数据结构提供 `io::ostream` 流输出操作符重载。仅在调试构建（`NDEBUG` 未定义）时编译，用于日志输出和诊断信息格式化。

## 核心类/结构体

本文件不定义类，而是为以下类型提供 `operator<<` 重载：

### 枚举类型
- `ShaderModel` - 着色器模型（MOBILE / DESKTOP）
- `PrimitiveType` - 图元类型（TRIANGLES、LINES 等）
- `ElementType` - 顶点元素类型（BYTE~HALF4 共 24 种）
- `BufferUsage` - 缓冲区使用标志（十六进制输出）
- `CullingMode` - 面剔除模式
- `SamplerType` / `SamplerFormat` - 采样器类型和格式
- `Precision` - 精度级别
- `PixelDataFormat` / `PixelDataType` - 像素数据格式和类型
- `TextureFormat` - 纹理格式（包括全部压缩格式）
- `TextureCubemapFace` - 立方体贴图面
- `SamplerWrapMode` / `SamplerMinFilter` / `SamplerMagFilter` - 采样器参数
- `SamplerCompareMode` / `SamplerCompareFunc` - 采样器比较设置
- `BufferObjectBinding` / `TextureSwizzle` - 缓冲区绑定和纹理混排
- `ShaderStage` / `ShaderStageFlags` / `CompilerPriorityQueue` - 着色器阶段和编译优先级
- `TextureUsage` / `TargetBufferFlags` - 使用标志位域

### 复合类型
- `SamplerParams` - 采样器参数结构体
- `AttributeArray` - 顶点属性数组
- `RasterState` - 光栅化状态
- `TargetBufferInfo` / `PolygonOffset` / `PipelineState` - 渲染管线配置
- `BufferDescriptor` / `PixelBufferDescriptor` - 缓冲区描述符
- `Viewport` / `RenderPassParams` / `MRT` - 视口和渲染通道参数
- `DescriptorSetLayout` / `DescriptorSetOffsetArray` - 描述符集布局
- `PushConstantVariant` - 推送常量变体（使用 `std::visit`）

## 关键实现逻辑

- 使用 `CASE` 宏简化 switch-case 的枚举名输出模式。
- 所有操作符定义在全局命名空间（`filament` 命名空间之外），因为它们扩展的是 `utils::io::ostream`。
- `PushConstantVariant` 使用 `std::visit` 实现多态输出。

## 依赖关系

- `backend/BufferDescriptor.h` / `backend/DriverEnums.h` 等 - 全部后端枚举和结构体定义
- `backend/PipelineState.h` / `backend/TargetBufferInfo.h` - 管线和目标缓冲区信息
- `utils::ostream` - 自定义输出流
