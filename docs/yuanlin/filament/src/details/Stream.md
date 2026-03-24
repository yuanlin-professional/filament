# Stream

## 文件概述

`FStream` 是 `Stream` 公共 API 的私有实现类，管理外部纹理数据流（如 Android 相机预览、视频帧等）。支持 NATIVE 和 ACQUIRED 两种流类型，允许外部数据源实时更新 Filament 纹理内容。

## 核心类/结构体

### `FStream`
- 继承自 `Stream`
- 持有 `backend::Handle<backend::HwStream>` 后端流句柄
- 支持设置尺寸变更和图像获取操作
- `mStreamType` 区分原生流和获取型流

## 关键实现逻辑

- **setAcquiredImage()** -- 设置外部获取的图像数据，支持可选的变换矩阵和回调处理器
- **setDimensions()** -- 动态更新流的宽高尺寸
- **getTimestamp()** -- 获取流的时间戳信息

## 依赖关系

- `details/Engine.h` -- 引擎驱动 API
- `backend/Handle.h` -- 后端资源句柄
- `math/mat3.h` -- 变换矩阵

## 与公共 API 的关系

直接对应 `filament::Stream`。通过 `Stream::Builder` 创建，与 `Texture::setExternalStream()` 配合使用，将外部数据源绑定到纹理。
