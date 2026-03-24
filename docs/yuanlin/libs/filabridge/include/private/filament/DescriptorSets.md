# DescriptorSets.h / DescriptorSets.cpp

## 文件概述
定义和实现描述符集（Descriptor Set）的布局管理。描述符集是现代图形 API（Vulkan/Metal）中绑定资源到着色器的核心机制。该文件为 Filament 的不同渲染通道（深度、SSR、颜色）定义预设的描述符集布局。

## 核心类/结构体/函数
- **`getDepthVariantLayout()`** - 返回深度/阴影贴图渲染通道的描述符集布局（仅 FRAME_UNIFORMS）
- **`getSsrVariantLayout()`** - 返回屏幕空间反射通道的描述符集布局
- **`getPerRenderableLayout()`** - 返回每个可渲染对象的描述符集布局（ObjectUniforms、Bones、Morphing 等）
- **`getPerViewDescriptorSetLayout()`** - 根据材质域和光照属性返回每视图描述符集布局
- **`getPerViewDescriptorSetLayoutWithVariant()`** - 根据变体类型选择合适的描述符集布局
- **`getDescriptorName()`** - 根据绑定集和绑定点返回描述符名称字符串
- **`getDescriptorType()`** - 将 SamplerType+SamplerFormat 映射为 DescriptorType

## 关键实现逻辑
- 预定义了 4 种描述符集布局模板（depth、ssr、perView、perRenderable）
- `getPerViewDescriptorSetLayout()` 根据材质特性（是否光照、是否 SSR、是否雾效）动态移除不需要的描述符
- VSM 阴影模式下将 SHADOW_MAP 描述符从深度类型改为浮点数组类型
- 编译期 `checkConsistency()` 验证 SSR 布局与 perView 布局的顶点阶段兼容性

## 依赖关系
- `private/filament/EngineEnums.h`、`private/filament/Variant.h`
- `filament/MaterialEnums.h`
- `backend/DriverEnums.h`
- `utils/CString.h`、`utils/Panic.h`、`utils/StaticString.h`
