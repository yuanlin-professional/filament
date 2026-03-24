# IBLPrefilterContext.h / IBLPrefilterContext.cpp

## 文件概述

GPU 端 IBL 预滤波上下文，封装了 Filament 引擎进行环境贴图滤波所需的 GPU 资源和渲染管线。提供三个核心滤波器：等距矩形到立方体贴图的转换（EquirectangularToCubemap）、漫反射辐照度滤波（IrradianceFilter）和高光预滤波（SpecularFilter）。

## 核心类/结构体/函数

- **`IBLPrefilterContext`** - GPU 预滤波上下文
  - 构造时创建 Renderer、Scene、View、Camera、全屏三角形 VBO/IBO、Material 等 GPU 资源
  - `Kernel` 枚举 - D_GGX（Trowbridge-Reitz 分布）
- **`EquirectangularToCubemap`** - 等距矩形投影转立方体贴图
  - `Config` - mirror 选项（水平镜像源图像）
  - `operator()` - 输入 2D 纹理，输出立方体贴图（默认 256x256 R11G11B10）
- **`IrradianceFilter`** - 漫反射辐照度滤波器
  - `Config` - sampleCount（最大 2048）、kernel 类型
  - `Options` - hdrLinear/hdrMax（HDR 压缩范围）、lodOffset、generateMipmap
  - `operator()` - 输入环境 cubemap，输出辐照度 cubemap
- **`SpecularFilter`** - 高光预滤波器
  - `Config` - sampleCount、levelCount（粗糙度级别数）、kernel
  - `Options` - 与 IrradianceFilter 类似
  - `operator()` - 输入环境 cubemap，输出多级预滤波 cubemap

## 关键实现逻辑

- **渲染管线**：使用全屏三角形 + 自定义 Material 渲染到 RenderTarget 的 cubemap 面
- **分批渲染**：每次渲染 3 个面（COLOR0/1/2），正面和负面分两批
- **Kernel 纹理**：预计算采样方向和 LOD 存储为 RGBA16F 纹理（宽=级别数，高=采样数）
- **粗糙度映射**：`lodToPerceptualRoughness()` 使用二次拟合的逆映射
- **HDR 压缩**：通过 compress 参数控制 hdrLinear 以下不压缩、hdrMax 以上截断
- **LOD 偏移**：`lodOffset - log4(omegaP)` 用于控制预滤波采样 LOD
- **Emscripten 兼容**：针对 WebGL 的 Material Instance 复制 workaround

## 依赖关系

- `filament/Engine.h`, `filament/Texture.h`, `filament/Material.h` 等 - Filament 引擎 API
- `filament/RenderTarget.h`, `filament/Renderer.h` - 离屏渲染
- `backend/DriverEnums.h` - 后端枚举
- `utils/EntityManager.h`, `utils/Panic.h` - 实体管理和断言
- `iblprefilter_materials.h` - 预编译的 Material 数据
