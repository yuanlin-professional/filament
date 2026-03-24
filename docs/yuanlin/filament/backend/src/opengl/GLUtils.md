# GLUtils.h / GLUtils.cpp

## 文件概述

提供 OpenGL 后端的工具函数集合，包括错误检查、枚举转换（Filament 枚举到 GL 枚举）、纹理格式映射和扩展字符串解析。

## 核心类/结构体

### 命名空间 `GLUtils`

**错误检查**:
- `checkGLError()` / `assertGLError()`: GL 错误检查和断言。
- `checkFramebufferStatus()` / `assertFramebufferStatus()`: 帧缓冲完整性检查。
- `CHECK_GL_ERROR` 等宏：Debug 模式下启用的检查宏。

**枚举转换函数**（均为 `constexpr`）:
- `getComponentCount/getComponentType`: ElementType 到 GL 类型转换。
- `getBufferUsage/getBufferBindingType`: 缓冲区用途转换。
- `getTextureFilter/getWrapMode`: 采样器参数转换。
- `getBlendEquationMode/getBlendFunctionMode`: 混合状态转换。
- `getInternalFormat`: TextureFormat 到 GL 内部格式的完整映射，覆盖所有压缩格式。
- `textureFormatToFormatAndType`: ES2 纹理格式到 format/type 对的映射。

**工具类**:
- `unordered_string_set`: 基于 `std::unordered_set` 的字符串集合，提供 `has()` 方法。
- `split()`: 按空格分割 GL 扩展字符串。

## 关键实现逻辑

- 大量使用 `constexpr` 确保枚举转换在编译期完成。
- `getInternalFormat` 通过条件编译处理不同平台支持的压缩纹理格式。

## 依赖关系

- `gl_headers.h` - OpenGL 类型和常量
- `backend/DriverEnums.h` - Filament 枚举
- `utils/debug.h`, `utils/ostream.h`
