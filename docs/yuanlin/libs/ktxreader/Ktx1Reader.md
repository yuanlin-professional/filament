# Ktx1Reader - KTX1 纹理文件读取器

## 文件概述

提供从 `Ktx1Bundle` 对象创建 Filament `Texture` 的功能。支持 2D 纹理和立方体贴图（cubemap），自动处理所有 mipmap 层级的上传。包含 GL 枚举到 Filament 纹理格式的完整映射表，覆盖压缩格式（ASTC、ETC2、DXT/BC、RGTC、BPTC、EAC）和非压缩格式。

**源文件**: `libs/ktxreader/include/ktxreader/Ktx1Reader.h`, `libs/ktxreader/src/Ktx1Reader.cpp`

## 核心函数

### `createTexture(Engine*, const Ktx1Bundle&, bool srgb, Callback, void* userdata)`
从 KTX1 bundle 创建纹理并上传所有面和 mip 层级。使用引用计数回调机制：所有 mip 层级上传完成后触发用户回调。

### `createTexture(Engine*, Ktx1Bundle*, bool srgb)`
便捷版本，上传完成后自动销毁 Ktx1Bundle。

### 格式转换函数
- `toTextureFormat()`: GL 内部格式 -> Filament TextureFormat（覆盖约 60 种格式）
- `toCompressedPixelDataType()`: GL 内部格式 -> 压缩纹理类型（通过模板函数处理约 40 种压缩格式）
- `toPixelDataType()`: GL 类型 -> 像素数据类型（UBYTE/USHORT/HALF/FLOAT/UINT_10F_11F_11F_REV）
- `toPixelDataFormat()`: GL 格式 -> 像素数据格式（R/RG/RGB/RGBA）
- `isCompressed()`: 通过 `glFormat == 0` 判断是否为压缩格式
- `isSrgbTextureFormat()`: 判断格式是否为 sRGB（非压缩 RGB8/RGBA8 和大部分压缩格式返回 false）

## 关键实现逻辑

1. **纹理创建**: 从 KtxInfo 获取宽高、mip 数、采样器类型（2D 或 cubemap），选择合适的内部格式
2. **数据上传**: 区分压缩/非压缩和 2D/cubemap 四种情况。Cubemap 使用 `setImage` 的 6 面版本，一次性上传所有面
3. **回调管理**: 使用 `Userdata` 结构体跟踪剩余 buffer 数，当全部上传完毕时触发一次用户回调

## 依赖关系

- `<image/Ktx1Bundle.h>` - KTX1 文件内存表示
- `<filament/Engine.h>`, `<filament/Texture.h>` - Filament 纹理对象
- `<utils/Log.h>` - 日志输出
