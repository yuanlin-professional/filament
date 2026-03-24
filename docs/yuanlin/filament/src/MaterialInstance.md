# MaterialInstance.cpp

## 文件概述
MaterialInstance.cpp 实现了材质实例的公共 API，提供 Uniform 参数设置/获取、纹理绑定、渲染状态覆盖等功能。每个 MaterialInstance 对应一个 Material 的特定参数配置。

## 核心类/结构体/函数
- **MaterialInstance** - 材质实例公共接口
  - `setParameter()` - 设置 Uniform 参数（支持标量、向量、矩阵、数组等多种类型）
  - `getParameter()` - 获取 Uniform 参数值
  - `setParameter(texture, sampler)` - 绑定纹理和采样器
  - `setParameter(RgbType/RgbaType, color)` - 设置颜色参数（自动转换到线性空间）
  - `setScissor()` / `unsetScissor()` - 裁剪矩形控制
  - `setPolygonOffset()` - 多边形偏移
  - `setMaskThreshold()` / `setDoubleSided()` / `setTransparencyMode()` - 材质属性覆盖
  - `setCullingMode()` - 支持分别设置颜色通道和阴影通道的裁剪模式
  - `setDepthWrite()` / `setDepthCulling()` / `setDepthFunc()` / `setColorWrite()` - 深度和颜色写入控制
  - `setStencilWrite()` / `setStencilCompareFunction()` 等 - 模板测试配置
  - `duplicate()` - 复制一个已有的 MaterialInstance
  - `commit()` - 提交参数变更到 GPU

## 关键实现逻辑
- **类型擦除优化**：`setParameterUntypedImpl<Size>()` 使用模板 Size 参数避免内联，减少代码膨胀
- **mat3f 特殊处理**：mat3f 按 std140 布局转换为 3 个 float3 的数组处理
- **bool 类型转换**：bool/bool2/bool3/bool4 在设置时转换为 uint32/uint2/uint3/uint4
- **颜色空间转换**：RGB/RGBA 颜色通过 `Color::toLinear()` 从指定色彩空间转换到线性空间
- 所有模板函数均有显式实例化，支持 float、int32_t、uint32_t 和各种向量/矩阵类型

## 依赖关系
- `details/Material.h` / `details/MaterialInstance.h` - 内部实现
- `filament/MaterialInstance.h` / `filament/Color.h` - 公共头文件
- `math/` - 数学类型（vec2-4, mat3-4）

## 被引用关系
- 用户层代码通过 MaterialInstance 配置渲染参数
- RenderPass 在生成绘制命令时读取 MaterialInstance 的状态
