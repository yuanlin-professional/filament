# OpenGLPlatform.cpp

## 文件概述

OpenGLPlatform 基类的默认实现，为所有平台特化提供默认的空操作或基本实现。此文件也包含 `OpenGLDriverBase` 的析构函数实现。

## 核心类/结构体

### `OpenGLPlatform` 默认实现
提供以下虚函数的默认实现：
- `createDefaultDriver()`: 通过 `OpenGLDriverFactory` 创建驱动。
- `getDeviceInfo()` / `getVendorString()` / `getRendererString()`: 设备信息查询。
- `makeCurrent()`: 上下文切换（委托到带 ContextType 的重载）。
- `isProtectedContextSupported()` / `isSRGBSwapChainSupported()`: 默认返回 false。
- 围栏操作 (`createFence`, `waitFence`): 默认为空操作或返回错误。
- 流式纹理操作 (`createStream`, `updateTexImage`): 默认为空操作。
- 外部图像操作 (`createExternalImageTexture`, `setExternalImage`): 默认为空操作。
- 额外上下文 (`isExtraContextSupported`, `createContext`): 默认不支持。

## 关键实现逻辑

- 所有默认实现都是安全的空操作，确保未实现的平台方法不会崩溃。
- `getVendorString` 等方法通过 `static_cast` 将 `Driver*` 转换为 `OpenGLDriverBase*` 获取信息。

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 平台接口
- `OpenGLDriverBase.h`, `OpenGLDriverFactory.h`
- `backend/AcquiredImage.h`, `backend/DriverEnums.h`
