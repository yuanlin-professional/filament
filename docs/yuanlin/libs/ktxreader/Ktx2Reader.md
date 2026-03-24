# Ktx2Reader - KTX2 纹理文件读取器（BasisU 转码）

## 文件概述

提供从 KTX2 格式（基于 BasisU 编码）二进制数据创建 Filament `Texture` 的功能。KTX2 使用通用中间格式存储纹理，可以在运行时快速转码为目标平台支持的压缩/非压缩格式。支持同步和异步两种加载模式。

**源文件**: `libs/ktxreader/include/ktxreader/Ktx2Reader.h`, `libs/ktxreader/src/Ktx2Reader.cpp`

## 核心类/结构体

### `Ktx2Reader`
- **TransferFunction 枚举**: `LINEAR` 或 `sRGB`，用于匹配 KTX2 元数据中的传输函数
- **Result 枚举**: `SUCCESS`、`COMPRESSED_TRANSCODE_FAILURE`、`UNCOMPRESSED_TRANSCODE_FAILURE`、`FORMAT_UNSUPPORTED`、`FORMAT_ALREADY_REQUESTED`
- `requestFormat()`: 注册期望的目标格式，按优先级排序。支持压缩格式（ETC2、DXT/BC、ASTC、EAC、BPTC）和非压缩格式（RGBA8、RGB565、RGBA4）
- `load()`: 同步加载，初始化转码器、遍历所有 mip 级别转码并上传
- `asyncCreate()` / `asyncDestroy()`: 异步接口，分离纹理创建、转码和上传步骤

### `Ktx2Reader::Async`
异步加载句柄：
- `getTexture()`: 立即获取纹理对象（数据尚未就绪）
- `doTranscoding()`: 在后台线程执行所有 mip 级别的转码
- `uploadImages()`: 在前台线程上传已转码的 mip 数据

### `FAsync` (内部)
`Async` 的具体实现，使用 `std::atomic<PixelBufferDescriptor*>` 数组在转码线程和上传线程之间安全传递数据。每个 BasisU 转码器实例独立，支持纹理级别的并行化。

## 关键实现逻辑

1. **格式协商**: `createTexture()` 遍历用户请求的格式列表，检查平台能力（`isTextureFormatSupported`）、传输函数匹配、BasisU 可用性（`basis_is_format_supported`），选择第一个满足条件的格式
2. **转码流程**: `transcodeImageLevel()` 区分压缩和非压缩两种路径：压缩格式按块转码，非压缩格式按像素转码。使用 `ktx2_transcoder_state` 管理线程状态
3. **传输函数校验**: KTX2 元数据中的传输函数必须与请求的匹配，否则拒绝加载
4. **限制**: 暂不支持立方体贴图（使用 KTX1 代替）和纹理数组

## 依赖关系

- `<basisu_transcoder.h>` - BasisU 转码库
- `<filament/Engine.h>`, `<filament/Texture.h>` - Filament 纹理对象
- `<utils/Log.h>`, `<utils/FixedCapacityVector.h>` - 日志和固定容量容器
