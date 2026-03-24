# GLDescriptorSet.h / GLDescriptorSet.cpp

## 文件概述

实现 OpenGL 后端的描述符集（Descriptor Set），将 Filament 的描述符集抽象映射到 OpenGL 的绑定模型（UBO、SSBO、纹理/采样器）。支持 ES2 到 ES3.1+ 的多种代码路径。

## 核心类/结构体

### `GLDescriptorSet`
继承自 `HwDescriptorSet`，管理一组描述符绑定。

- **内部描述符类型**（使用 `std::variant`）:
  - `Buffer`: 静态偏移的 UBO/SSBO 描述符
  - `DynamicBuffer`: 动态偏移的 UBO/SSBO 描述符
  - `BufferGLES2`: ES2 模式下的 UBO 模拟描述符
  - `Sampler`: 标准采样器描述符
  - `SamplerWithAnisotropyWorkaround`: 各向异性过滤在采样器对象上有 bug 时使用的替代方案
  - `SamplerGLES2`: ES2 模式下的采样器描述符

### 关键方法
- `update(buffer)` / `update(sampler)`: 更新缓冲区或采样器描述符。
- `bind()`: 将描述符集绑定到当前 GL 状态，遍历活跃描述符并调用对应的 GL API。
- `validate()`: 验证描述符集布局与管线布局的一致性。

## 关键实现逻辑

- 构造时根据布局和 GL 版本选择适当的描述符变体（variant）。
- `bind()` 中使用 `std::visit` 对 variant 进行分发，按类型调用 `glBindBufferRange`、`glBindTexture`、`glBindSampler` 等。
- 对外部纹理和深度纹理有特殊的采样参数约束处理。
- 纹理视图（View）通过 `updateTextureView` 动态设置 `GL_TEXTURE_BASE_LEVEL`、`GL_TEXTURE_MAX_LEVEL` 和 swizzle。

## 依赖关系

- `GLBufferObject.h`, `GLDescriptorSetLayout.h`, `GLTexture.h`, `GLUtils.h`
- `OpenGLDriver.h`, `OpenGLContext.h`, `OpenGLProgram.h`
- `backend/DriverEnums.h`, `backend/Handle.h`
- `utils/FixedCapacityVector.h`, `utils/bitset.h`
