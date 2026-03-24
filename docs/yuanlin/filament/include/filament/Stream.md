# Stream.h

## 文件概述

定义视频/相机流对象，用于将外部视频或相机帧作为纹理数据源。主要面向 Android 平台，支持 ACQUIRED 和 NATIVE 两种模式。

## 核心类/结构体

- **`Stream`** - 流对象，继承自 `FilamentAPI`。
- **`Stream::Builder`** - 构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::stream(nativeStream)` | 创建 NATIVE 流（已废弃） |
| `Builder::width(w)` / `height(h)` | 设置流的宽高 |
| `Builder::build(engine)` | 创建 Stream |
| `getStreamType()` | 获取流类型（NATIVE/ACQUIRED） |
| `setAcquiredImage(image, callback, userdata, transform)` | 推送 AHardwareBuffer 图像 |
| `setDimensions(w, h)` | 更新流尺寸 |
| `getTimestamp()` | 获取当前帧的呈现时间戳（纳秒） |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/DriverEnums.h`, `backend/CallbackHandler.h`
- `utils/compiler.h`, `utils/StaticString.h`, `math/mat3.h`
