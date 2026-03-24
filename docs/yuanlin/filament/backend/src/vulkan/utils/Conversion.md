# Conversion (Conversion.h / Conversion.cpp)

## 文件概述

Filament 后端类型到 Vulkan 类型的转换工具集。提供纹理格式、元素类型、采样器参数、混合状态、模板操作以及 YCbCr 色彩空间转换等全面的类型映射功能。

## 核心类/结构体

本文件仅包含命名空间 `filament::backend::fvkutils` 下的自由函数，无类定义。

## 关键实现逻辑

### 格式转换
- `getVkFormat(ElementType, normalized, integer)` - 顶点元素类型到 VkFormat，支持归一化/整数/缩放三种模式
- `getVkFormat(TextureFormat)` - Filament 纹理格式到 VkFormat，覆盖所有格式（含压缩纹理 ETC2/ASTC/BC/EAC）。注意 24 位和 48 位格式会向上扩展到 32 位和 64 位
- `getVkFormat(PixelDataFormat, PixelDataType)` - 像素数据格式对到 VkFormat，仅返回支持 BLIT_SRC 的格式
- `getVkFormatLinear(VkFormat)` - SRGB 格式转对应线性 UNORM 格式
- `getTexelBlockSize(VkFormat)` - 返回纹素块字节数（用于暂存缓冲区对齐）

### 渲染状态转换
- `getCompareOp()` / `getStencilOp()` / `getBlendFactor()` - 深度比较/模板操作/混合因子
- `getCullMode()` / `getFrontFace()` - 面剔除模式/正面方向
- `getSwizzleMap()` - TextureSwizzle[4] 到 VkComponentMapping，优化使用 IDENTITY

### 采样器参数转换
- `getFilter()` / `getMipmapMode()` / `getWrapMode()` - 过滤/Mipmap 模式/寻址模式
- `getMaxLod()` - 对非 Mipmap 过滤返回 0.25f 禁用 Mipmap

### YCbCr 双向转换
- Filament -> Vulkan: `getYcbcrModelConversion()` / `getYcbcrRange()` / `getChromaLocation()`
- Vulkan -> Filament: `getYcbcrModelConversionFilament()` 等反向映射

## 依赖关系

- `vulkan/utils/Definitions.h` - VkFormatList 等类型定义
- `backend/DriverEnums.h` - Filament 枚举类型
- `private/backend/BackendUtils.h` - `getFormatSize()` 函数
- `bluevk/BlueVK.h` - Vulkan 类型定义
