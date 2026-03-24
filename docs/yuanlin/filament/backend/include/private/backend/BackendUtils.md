# BackendUtils.h

## 文件概述

提供后端通用的工具函数，包括 GLSL 着色器处理、纹理格式信息查询和像素数据格式转换等功能。

## 主要 API

### 着色器处理

| 函数 | 说明 |
|------|------|
| `requestsGoogleLineDirectivesExtension(source)` | 检查着色器是否请求了 Google 风格的行指令扩展 |
| `removeGoogleLineDirectives(shader, length)` | 就地编辑 GLSL 着色器，将 Google 风格行指令转为标准行指令 |

### 纹理格式查询

| 函数 | 说明 |
|------|------|
| `getFormatSize(format)` | 返回指定纹理格式每像素（或每块）的字节数 |
| `getFormatComponentCount(format)` | 返回格式的分量数（1-4） |
| `getBlockWidth(format)` | 压缩格式的水平块大小（非压缩返回 0） |
| `getBlockHeight(format)` | 压缩格式的垂直块大小（非压缩返回 0） |

### 像素数据转换

| 函数 | 说明 |
|------|------|
| `reshape(data, reshaped)` | 将 3 分量像素数据重塑为 4 分量数据 |

## 依赖关系

- `backend/DriverEnums.h` - `TextureFormat` 等类型
- `backend/PixelBufferDescriptor.h` - 像素缓冲区描述符
