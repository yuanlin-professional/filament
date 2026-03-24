# Definitions (Definitions.h)

## 文件概述

定义 Vulkan 后端各模块共享的通用类型、常量和数据结构。包含所有 VkFormat 枚举值的完整列表、描述符集位掩码类型、以及 YCbCr 采样器转换参数结构体。

## 核心类/结构体

### 位掩码类型定义
- `VkFormatList` - `FixedCapacityVector<VkFormat>` 的别名
- `UniformBufferBitmask` / `SamplerBitmask` / `InputAttachmentBitmask` - 均为 `bitset64`，用于描述符集布局中标记活跃绑定
- `DescriptorSetMask` - `bitset8`，标记活跃的描述符集（最多 4 个）

### `SamplerYcbcrConversion` 结构体
紧凑的 4 字节位域结构，存储 YCbCr 采样器转换参数：
- `ycbcrModel` (4 位) - 色彩模型转换类型（RGB/YCbCr 601/709/2020）
- `r/g/b/a` (各 4 位) - 通道 Swizzle 映射
- `ycbcrRange` (1 位) - ITU_FULL 或 ITU_NARROW
- `xChromaOffset` / `yChromaOffset` (各 1 位) - 色度采样偏移
- `chromaFilter` (1 位) - 色度过滤模式

提供自定义的 Hasher、EqualTo、LessThan 比较器，直接对底层 4 字节进行整数比较。

### 全局常量数组
- `ALL_VK_FORMATS[]` - 包含 Vulkan 规范中所有 VkFormat 值的完整数组（含 KHR/EXT 后缀别名）
- `EXT_VK_FORMATS[]` - 需要扩展支持的格式子集（ycbcr_2plane_444、optical_flow 等）

### 辅助模板函数
- `getVertexStageShift<Bitmask>()` - 返回 0，表示位掩码低半部分用于顶点阶段
- `getFragmentStageShift<Bitmask>()` - 返回 `sizeof(Bitmask) * 4`，高半部分用于片段阶段

## 依赖关系

- `backend/DriverEnums.h` - Filament 枚举类型（TextureSwizzle、SamplerMagFilter 等）
- `utils/bitset.h` - bitset64/bitset8 位集合
- `utils/FixedCapacityVector.h` - 固定容量向量
- `bluevk/BlueVK.h` - VkFormat 等 Vulkan 类型
