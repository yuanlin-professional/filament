# Ktx1Bundle.h / Ktx1Bundle.cpp

## 文件概述

KTX1 纹理容器格式的读写实现。`Ktx1Bundle` 是一组不透明数据 blob 的结构化集合，可直接传递给 GPU。一个 bundle 对应一个纹理对象，支持多 mipmap 级别、立方体贴图面和纹理数组元素。

## 核心类/结构体/函数

- **`KtxInfo`** - 纹理元信息结构体（endianness、glType/Format/InternalFormat、尺寸等）
- **`KtxBlobIndex`** - blob 三维索引（mipLevel、arrayIndex、cubeFace）
- **`Ktx1Bundle`** - 主类，管理 KTX1 纹理数据
  - `Ktx1Bundle(numMipLevels, arrayLength, isCubemap)` - 创建空纹理层次
  - `Ktx1Bundle(bytes, nbytes)` - 从序列化数据反序列化
  - `serialize()` / `getSerializedLength()` - 序列化为 KTX 文件格式
  - `getBlob()` / `setBlob()` / `allocateBlob()` - blob 数据的读写和预分配
  - `getMetadata()` / `setMetadata()` - 键值对元数据管理
  - `getSphericalHarmonics()` - 解析 "sh" 元数据中的球谐系数（3 bands，9 个 RGB 系数）
- **`KtxBlobList`** - 内部连续存储结构，管理所有 blob 的数据和大小
- **`KtxMetadata`** - 内部键值对存储（基于 unordered_map）
- 大量 GL 格式常量定义（RGBA8、R11F_G11F_B10F、各种压缩格式等）

## 关键实现逻辑

- blob 索引通过 `flatten()` 函数将三维索引（mip, array, face）映射为一维
- KTX 规范中非数组立方体贴图的语义与数组贴图不同，代码中有对应处理
- 反序列化时允许 mip 和 array 维度为 0（规范允许），内部统一替换为 1
- 元数据对齐到 4 字节边界

## 依赖关系

- `math/vec3.h` - 球谐系数存储
- `utils/compiler.h`, `utils/Panic.h`, `utils/string.h` - 工具库
