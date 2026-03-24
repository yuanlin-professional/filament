# Image.h / Image.cpp

## 文件概述

IBL 库的基础图像类，用于存储立方体贴图的面数据。与 `image::LinearImage` 不同，这个 `Image` 类固定使用 `float3` 作为像素类型（每像素 12 字节），并支持子图像视图（subset）操作。

## 核心类/结构体/函数

- **`Image`** - 图像数据容器
  - `Image()` - 默认构造（空图像）
  - `Image(w, h, stride)` - 分配指定尺寸的图像（stride 默认为 width）
  - `reset()` - 释放数据并重置
  - `set(image)` - 设置为另一图像的视图（不拥有数据）
  - `subset(image, x, y, w, h)` - 创建子区域视图（共享父图像的内存）
  - `getPixelRef(x, y)` - 获取指定坐标的像素指针
  - `getData()` - 获取原始数据指针
  - `detach()` - 转移数据所有权
  - `getWidth()`, `getHeight()`, `getStride()` - 尺寸查询
  - `getBytesPerRow()`, `getBytesPerPixel()` - 字节大小查询
  - `isValid()` / `getSize()` - 有效性和总大小

## 关键实现逻辑

- 固定像素格式为 `math::float3`（3 个 float，12 字节/像素）
- 支持所有权和视图两种模式：`mOwnedData` 持有自有数据，`mData` 可能指向外部内存
- `subset()` 通过计算偏移指针创建子视图，共享父图像的行步幅
- stride 允许大于 width，使得子图像可以有独立的宽度但共享行步幅

## 依赖关系

- `math/scalar.h`, `math/vec3.h`, `math/vec4.h` - 数学类型
- `utils/compiler.h` - 编译器宏
