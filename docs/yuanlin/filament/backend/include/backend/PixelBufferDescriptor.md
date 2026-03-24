# PixelBufferDescriptor.h

## 文件概述

定义了 `PixelBufferDescriptor` 类，继承自 `BufferDescriptor`，用于描述主内存中的图像数据。通常用于从 CPU 向 GPU 传输图像数据（纹理上传）。支持普通和压缩格式。

## 核心类

### PixelBufferDescriptor

图像缓冲区描述符，在 `BufferDescriptor` 基础上增加了像素格式和布局信息。

**公开成员**:
- `left`, `top` - 像素坐标偏移
- `stride` - 行步长（像素）
- `format` - 像素数据格式（`PixelDataFormat`）
- `type` - 像素数据类型（`PixelDataType`）
- `alignment` - 行对齐（字节）
- `imageSize` - 压缩图像大小（与 `stride`/`format` 共用 union）
- `compressedFormat` - 压缩格式类型

## 主要 API

| 方法 | 说明 |
|------|------|
| 多个构造函数 | 支持普通格式和压缩格式，可选回调和处理器 |
| `make<T, &T::method>(...)` | 静态工厂方法，使用成员函数指针作为回调 |
| `make<T>(...)` | 静态工厂方法，使用函子作为回调 |
| `computePixelSize(format, type)` | 静态方法，计算单个像素的字节大小 |
| `computeDataSize(format, type, stride, height, alignment)` | 静态方法，计算图像缓冲区所需的总字节数 |

## 依赖关系

- `backend/BufferDescriptor.h` - 基类
- `backend/DriverEnums.h` - 像素格式枚举
- `utils/compiler.h` / `utils/debug.h`
