# OpenGLContext.h / OpenGLContext.cpp

## 文件概述

OpenGLContext 是 OpenGL 后端的核心状态管理类，负责 GL 状态缓存与跟踪、扩展检测、Bug 规避数据库、功能级别解析，以及所有 GL 状态变更操作的封装。

## 核心类/结构体

### `OpenGLContext`
继承 `TimerQueryFactoryInterface`，标记为 `final`。

**状态缓存 `State`**:
- `program`: 当前使用的着色器程序。
- `vao`: 当前顶点数组对象（RenderPrimitive）。
- `raster`: 光栅化状态（面剔除、混合函数、深度掩码等）。
- `stencil`: 模板测试状态（前后面分离）。
- `buffers`: 通用绑定和索引绑定缓冲区状态。
- `textures`: 纹理单元绑定状态（最多 MAX_SAMPLER_COUNT 个单元）。
- `window`: 视口、裁剪矩形和深度范围。

**扩展 `Extensions`**: 运行时检测的 GL 扩展布尔标志集合（约 35 个扩展）。

**Bug 规避 `Bugs`**: 针对特定 GPU/驱动的已知问题规避开关（约 18 个 Bug）。

**功能查询 `Gets`**: GL 能力值缓存（最大纹理尺寸、UBO 对齐等）。

### `RenderPrimitive`
VAO 相关状态：双缓冲 VAO 名称、索引缓冲区、顶点属性数组状态、版本追踪。

## 关键实现逻辑

- **状态缓存模式**: 所有 GL 状态变更函数（`useProgram`、`bindTexture`、`enable` 等）使用 `update_state` 模板方法，仅当新值与缓存值不同时才发出实际 GL 调用。
- **扩展初始化**: 按 GLES/GL 分别解析扩展字符串，设置对应的布尔标志。
- **Bug 检测**: 通过 GPU 厂商/渲染器字符串和驱动版本号识别已知问题（Adreno、Mali、PowerVR、Intel 等）。
- **功能级别解析**: 根据 GL 版本、扩展和能力值确定 FEATURE_LEVEL_0 到 FEATURE_LEVEL_3。
- **采样器缓存**: 使用 `robin_map` 缓存 SamplerParams 到 GL 采样器对象的映射。
- **上下文切换**: `synchronizeStateAndCache` 和 `resetState` 支持保护/非保护上下文的切换。

## 依赖关系

- `OpenGLTimerQuery.h` - 时间查询接口
- `backend/platforms/OpenGLPlatform.h` - 平台抽象
- `backend/DriverEnums.h`, `backend/Handle.h`
- `gl_headers.h` - GL 类型
- `utils/bitset.h`, `utils/compiler.h`
- `tsl/robin_map.h` - 高性能哈希表
- `math/vec2.h`, `math/vec4.h`
