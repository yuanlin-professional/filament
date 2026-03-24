# ColorPassDescriptorSet

## 文件概述

`ColorPassDescriptorSet.h` / `ColorPassDescriptorSet.cpp` 实现了颜色渲染通道所需的描述符集管理，是引擎中最复杂的描述符集之一，负责管理几乎所有的 PerView 级别着色器资源。

## 核心类/结构体

- **`ColorPassDescriptorSet`**: 管理颜色通道的 8 套描述符集布局和描述符集（索引由 lit/ssr/fog 三个布尔值组合得到）。
  - `mUniforms`: `TypedUniformBuffer<PerViewUib>` 引用，存储 PerView 级别的 uniform 数据
  - `mDescriptorSetLayout[8]` / `mDescriptorSet[8]`: 8 套布局和描述符集
  - `mIsVsm`: 是否使用 VSM（Variance Shadow Map）模式

## 关键实现逻辑

- **8 套变体**: 通过 `getIndex(lit, ssr, fog)` 计算索引，不同渲染路径使用不同的描述符集布局。
- **准备方法**: 提供大量 `prepare*` 方法设置各种渲染参数：
  - `prepareCamera`: 设置视图/投影矩阵
  - `prepareFog`: 设置雾效参数和 IBL
  - `prepareSSAO`: 设置环境光遮蔽纹理
  - `prepareDirectionalLight`: 设置方向光参数
  - `prepareAmbientLight`: 设置环境光和 IBL 纹理
  - `prepareDynamicLights`: 设置动态光源 froxel 数据
  - `prepareShadowVSM/PCF/DPCF/PCSS`: 设置不同阴影技术的纹理
- **提交与绑定**: `commit()` 上传 uniform 数据，`bind(driver, index)` 绑定到 `PER_VIEW` 绑定点。

## 依赖关系

- `ds/DescriptorSet.h`, `ds/DescriptorSetLayout.h` - 描述符集基础设施
- `ds/TypedUniformBuffer.h` - 类型化 UBO
- `private/filament/UibStructs.h` - `PerViewUib` 结构定义
- `filament/Viewport.h` - 视口
- `math/*.h` - 数学类型
