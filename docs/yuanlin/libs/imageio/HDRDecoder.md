# HDRDecoder.h / HDRDecoder.cpp

## 文件概述

Radiance HDR（.hdr）格式图像解码器。支持 RGBE 编码的高动态范围图像，处理 RLE 压缩和非 RLE 两种存储方式。同时在 `IMAGEIO_LITE` 模式下提供简化的 `ImageDecoder::decode()` 入口。

## 核心类/结构体/函数

- **`HDRDecoder`** - HDR 格式解码器，继承自 `ImageDecoder::Decoder`
  - `create(stream)` - 工厂方法
  - `checkSignature(buf)` - 检查 "#?RADIANCE" 或 "#?RGBE" 文件签名
  - `decode()` - 解码流数据为 3 通道 `LinearImage`

## 关键实现逻辑

- 解析 HDR 头部：提取 FORMAT、GAMMA、EXPOSURE 和图像尺寸
- 像素解码公式：`(rgb + 0.5) * 2^(e - 128 - 8)`，其中 e 为指数字节
- RLE 压缩处理：每行先读取 2 字节魔数（0x0202）和宽度，然后逐平面（R/G/B/E）RLE 解码
- 非 RLE 模式：直接读取每行 width*4 字节 RGBE 数据
- 安全防护：限制最大尺寸 65536，最大像素数 16384*16384，RLE 溢出检查

## 依赖关系

- `imageio/ImageDecoder.h` - 解码器基类接口
- `image/ImageOps.h` - 图像翻转操作
- `math/vec3.h` - float3 类型
- `utils/Log.h` - 日志输出
