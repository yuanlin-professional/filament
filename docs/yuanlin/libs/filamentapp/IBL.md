# IBL.h / IBL.cpp

## 文件概述
封装基于图像的光照（IBL）资源加载，支持从等距矩形贴图、KTX 文件或目录结构加载间接光照和天空盒。

## 核心类

### `IBL`
- `loadFromEquirect(path)` - 从 HDR/EXR 等距矩形贴图加载，内部使用 IBLPrefilter 进行预滤波
- `loadFromDirectory(path)` - 从预处理的 cubemap 目录加载（含 sh.txt 球谐系数文件）
- `loadFromKtx(prefix)` - 从 KTX 文件加载
- `getIndirectLight()` / `getSkybox()` / `getFogTexture()` - 获取生成的 Filament 对象
- `getSphericalHarmonics()` - 获取球谐系数（9 个 float3）

## 关键实现逻辑
- 等距矩形贴图加载流程：解码图像 -> 创建 2D 纹理 -> 等距矩形转 cubemap -> 高光滤波 -> 辐照度滤波 -> 构建 IndirectLight 和 Skybox
- 目录加载流程：读取 sh.txt 球谐系数 -> 加载多级 mipmap cubemap 面 -> 构建 IndirectLight
- 使用 R11F_G11F_B10F 格式存储 HDR cubemap 数据
- IBL 默认强度为 30000.0

## 依赖关系
- `filament/IndirectLight.h`, `filament/Skybox.h`, `filament/Texture.h`
- `ktxreader/Ktx1Reader.h` - KTX 文件读取
- `imageio/ImageDecoder.h` - EXR 图像解码
- `filament-iblprefilter/IBLPrefilterContext.h` - IBL 预滤波
- `stb_image.h` - 通用图像解码
