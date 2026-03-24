# Stream.cpp

## 文件概述
Stream.cpp 实现了视频/相机流的公共 API。Stream 用于将外部图像源（如视频帧、相机预览）绑定到纹理，主要用于 Android 平台的外部纹理。

## 核心类/结构体/函数
- **Stream** - 流公共接口
  - `getStreamType()` - 获取流类型
  - `setAcquiredImage()` - 设置获取的图像帧（支持回调和变换矩阵）
  - `setDimensions()` - 设置流尺寸
  - `getTimestamp()` - 获取当前帧时间戳

## 关键实现逻辑
通过 `downcast` 委托给内部 `FStream`。`setAcquiredImage()` 有两个重载：一个使用默认回调处理器，另一个允许指定 CallbackHandler。

## 依赖关系
- `details/Stream.h` - 内部实现

## 被引用关系
- `Texture` - 通过 `setExternalStream()` 绑定 Stream 到纹理
