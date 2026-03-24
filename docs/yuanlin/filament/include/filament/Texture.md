# Texture.h

## 文件概述

定义纹理对象，支持 2D 纹理、3D 纹理、立方体贴图和 mipmap，以及外部图像和视频流绑定。是 Filament 中最重要的资源类型之一。

## 核心类/结构体

- **`Texture`** - 纹理对象，继承自 `FilamentAPI`。
- **`Texture::Builder`** - 构建器。
- **`Texture::FaceOffsets`** - 立方体贴图面偏移（已废弃）。

## 类型别名

`Sampler`, `InternalFormat`, `CubemapFace`, `Format`, `Type`, `Usage`, `Swizzle` 等。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::width(w)` / `height(h)` / `depth(d)` | 设置纹理尺寸 |
| `Builder::levels(n)` | 设置 mipmap 级数 |
| `Builder::sampler(type)` | 设置采样器类型（2D/3D/Cubemap等） |
| `Builder::format(fmt)` | 设置内部格式 |
| `Builder::usage(usage)` | 设置用途标志 |
| `Builder::swizzle(r, g, b, a)` | 设置通道 swizzle |
| `Builder::build(engine)` | 创建纹理 |
| `setImage(engine, level, buffer)` | 上传纹理数据 |
| `setImageAsync(...)` | 异步上传纹理数据 |
| `setExternalImage(engine, image)` | 绑定外部图像 |
| `setExternalStream(engine, stream)` | 绑定外部流 |
| `generateMipmaps(engine)` | 自动生成 mipmap |
| `getWidth(level)` / `getHeight(level)` | 获取指定级别尺寸 |
| `isTextureFormatSupported(engine, fmt)` | 查询格式支持（静态） |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/DriverEnums.h`, `backend/PixelBufferDescriptor.h`, `backend/Platform.h`
- `utils/compiler.h`, `utils/Invocable.h`, `utils/StaticString.h`

## 使用示例

```cpp
auto* texture = Texture::Builder()
    .width(512).height(512)
    .levels(0xFF) // 自动计算 mip 级数
    .format(Texture::InternalFormat::RGBA8)
    .build(*engine);
texture->setImage(*engine, 0, std::move(buffer));
texture->generateMipmaps(*engine);
```
