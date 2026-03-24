# Texture

## 文件概述

`FTexture` 是 `Texture` 公共 API 的私有实现类，管理 GPU 纹理资源。支持 2D、3D、立方体贴图、数组纹理等多种类型，提供图像数据上传、mipmap 生成、外部图像/流绑定、LOD 范围管理和异步创建等功能。

## 核心类/结构体

### `FTexture`
- 继承自 `Texture`
- 持有两个纹理句柄：`mHandle`（主纹理）和 `mHandleForSampling`（采样用，支持 LOD 视图切换）
- `LodRange` 内部结构跟踪已上传的 mip 级别范围
- 支持纹理通道重排（Swizzle）
- `mCreationComplete` 原子标志支持异步创建

## 关键实现逻辑

- **LOD 范围管理** -- `updateLodRange()` 跟踪已上传的 mip 级别，`getHwHandleForSampling()` 在 LOD 不完整时返回占位纹理避免未定义行为
- **setImage()** -- 支持 3D 区域更新和旧版立方体贴图面偏移上传
- **setImageAsync()** -- 通过 `CallbackAdapter` 实现异步图像上传
- **外部图像** -- `setExternalImage()`/`setExternalStream()` 支持外部纹理源（相机、视频等）
- **generateMipmaps()** -- GPU 端 mipmap 生成
- **格式查询** -- 静态方法检查格式支持、压缩格式、最大纹理尺寸等

## 依赖关系

- `details/Engine.h` -- 引擎驱动 API
- `backend/DriverEnums.h` -- 纹理格式和类型枚举
- `utils/Invocable.h` -- 可调用对象

## 与公共 API 的关系

直接对应 `filament::Texture`。通过 `Texture::Builder` 创建，绑定到 `MaterialInstance` 的采样器参数上。是渲染管线中最基础的 GPU 资源之一。
