# generatePrefilterMipmap - 预滤波 Mipmap 生成器

## 文件概述

提供将环境立方体贴图（cubemap）进行预滤波生成反射贴图 mipmap 链的功能。每个 mip 层级对应不同的粗糙度值，用于基于物理的渲染（PBR）中的环境反射。输入为 HDR 立方体贴图像素数据，输出直接写入 Filament Texture 对象的各 mip 层级。

**源文件**: `libs/generatePrefilterMipmap/include/filament-generatePrefilterMipmap/generatePrefilterMipmap.h`, `libs/generatePrefilterMipmap/src/generatePrefilterMipmap.cpp`

## 核心类/结构体

### `FaceOffsets`
立方体贴图 6 个面的字节偏移量结构。包含 +x/-x/+y/-y/+z/-z 六个面的偏移，支持通过下标运算符访问。提供从单面大小自动计算连续偏移的便捷构造函数。

### `PrefilterOptions`
预滤波选项：
- `sampleCount`: 滤波采样数（默认 8）
- `mirror`: 是否镜像环境（默认 true）

### `generatePrefilterMipmap()`
核心函数，接受 Texture 指针、Engine 引用、像素缓冲区、面偏移和可选参数。

## 关键实现逻辑

1. **输入验证**: 检查像素格式必须为 RGB 或 RGBA，数据类型必须为 FLOAT、HALF 或 UINT_10F_11F_11F_REV，纹理尺寸必须为 2 的幂，纹理格式不能为压缩格式
2. **像素数据转换**: 根据输入类型（float3/half3/UINT_10F_11F_11F_REV）将数据读入 IBL 库的 Cubemap 结构。UINT_10F_11F_11F_REV 格式使用自定义浮点解析（fp10/fp11）
3. **Mipmap 链生成**: 使用 `CubemapUtils::downsampleCubemapLevelBoxFilter` 对基础立方体贴图进行逐级 box filter 下采样，每级调用 `makeSeamless()` 处理接缝
4. **粗糙度滤波**: 从最大 mip 到最小 mip（level 0 = 最大分辨率），计算线性粗糙度 `linearRoughness = lod^2`（lod 在 [0,1] 范围），调用 `CubemapIBL::roughnessFilter` 对完整 mipmap 链进行重要性采样滤波
5. **异步上传**: 每个面通过 `texture->setImage()` 上传到 GPU，使用回调在最后一个面（face 5）上传后释放 Image 内存

## 依赖关系

- `<filament/Engine.h>`, `<filament/Texture.h>` - Filament 引擎和纹理
- `<backend/PixelBufferDescriptor.h>`, `<backend/DriverEnums.h>` - 后端像素缓冲区
- `<ibl/Cubemap.h>`, `<ibl/CubemapIBL.h>`, `<ibl/CubemapUtils.h>`, `<ibl/Image.h>` - IBL 立方体贴图处理库
- `<utils/JobSystem.h>`, `<utils/FixedCapacityVector.h>`, `<utils/Panic.h>` - 多线程作业系统和工具
