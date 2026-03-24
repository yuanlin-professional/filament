# OpenGLDriverBase.h

## 文件概述

定义 OpenGL 驱动基类，为 OpenGLDriver 提供平台信息查询的抽象接口。

## 核心类/结构体

### `OpenGLDriverBase`
继承自 `DriverBase`，定义三个纯虚函数：
- `getVendorString()`: 获取 GPU 供应商字符串。
- `getRendererString()`: 获取 GPU 渲染器字符串。
- `getVersionString()`: 获取 GL 版本字符串。

## 关键实现逻辑

- 仅作为接口层，实际实现在 `OpenGLDriver` 中通过 `OpenGLContext::state` 获取。

## 依赖关系

- `DriverBase.h` - 基类
- `backend/Platform.h` - DriverConfig
- `utils/CString.h` - 字符串类型
