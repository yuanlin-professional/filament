# DFG.h / DFG.cpp

## 文件概述

管理 DFG（Diffuse-Fresnel-Geometry）查找表纹理的创建和销毁。DFG LUT 是基于物理渲染（PBR）中 Cook-Torrance BRDF 积分的预计算查找表，用于环境光照的镜面反射计算。

## 核心类/结构体/函数

### `DFG` 类

- **`init(FEngine& engine)`** -- 创建并初始化 DFG LUT 纹理。支持 Zstd 压缩的 LUT 数据自动解压。
- **`terminate(FEngine& engine)`** -- 销毁 DFG LUT 纹理。
- **`getLutSize()`** -- 返回 LUT 的大小（默认 128x128）。
- **`isValid()`** -- 检查 LUT 是否已成功创建。
- **`getTexture()`** -- 返回 LUT 纹理的 GPU 句柄。

### 常量

- **`DFG_LUT_SIZE`** -- LUT 的边长，默认 128，可通过 `FILAMENT_DFG_LUT_SIZE` 宏自定义。

## 关键实现逻辑

- **LUT 格式**：使用 RGB16F 格式的 2D 纹理，存储预计算的 BRDF 积分值。
- **Zstd 压缩支持**：`init` 方法首先检查嵌入的 LUT 数据是否经过 Zstd 压缩。如果是，先解压到临时缓冲区再上传到 GPU，使用自定义释放回调 `free` 管理解压缓冲区的生命周期。
- **嵌入资源**：LUT 数据通过 `generated/resources/dfg.h` 嵌入到二进制文件中（`DFG_PACKAGE`），编译时生成。
- **纹理创建**：通过 `Texture::Builder` API 创建纹理，并使用 `setImage` 上传数据。

## 依赖关系

- `details/Engine.h`、`details/Texture.h` -- 引擎和纹理内部实现
- `ZstdHelper.h` -- Zstd 解压辅助
- `generated/resources/dfg.h` -- 编译时生成的 LUT 数据
- `backend/Handle.h`、`backend/DriverEnums.h` -- 后端资源句柄

## 被引用关系

在引擎初始化时由 `FEngine` 创建，被 PBR 材质的环境光照计算使用，用于查询预积分的镜面反射 BRDF 值。
