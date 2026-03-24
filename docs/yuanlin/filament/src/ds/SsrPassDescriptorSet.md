# SsrPassDescriptorSet

## 文件概述

`SsrPassDescriptorSet.h` / `SsrPassDescriptorSet.cpp` 实现了屏幕空间反射（SSR）通道所需的描述符集管理，处理 SSR 和屏幕空间折射所需的纹理和 uniform 绑定。

## 核心类/结构体

- **`SsrPassDescriptorSet`**: SSR 通道的描述符集管理器。
  - `mDescriptorSet`: 描述符集实例
  - `mShadowUbh`: 阴影 uniform 缓冲句柄
  - `setFrameUniforms()`: 设置帧级 PerViewUib uniform 数据
  - `prepareStructure()`: 设置结构纹理（用于屏幕空间效果）
  - `prepareHistorySSR()`: 设置历史 SSR 纹理（用于时间滤波）
  - `commit()` / `bind()`: 提交和绑定描述符集

## 关键实现逻辑

- SSR 通道需要访问上一帧的颜色缓冲和当前帧的深度/法线结构纹理。
- 历史 SSR 纹理用于时间累积，提升反射质量。
- 独立的描述符集避免了与主渲染通道的资源绑定冲突。

## 依赖关系

- `ds/DescriptorSet.h` - 描述符集基础
- `ds/TypedUniformBuffer.h` - `TypedUniformBuffer<PerViewUib>`
- `private/filament/UibStructs.h` - `PerViewUib`
- `backend/Handle.h` - 纹理和缓冲句柄
