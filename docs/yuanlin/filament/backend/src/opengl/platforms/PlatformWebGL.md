# PlatformWebGL.cpp

## 文件概述

WebGL 平台实现，是所有平台中最简单的一个。WebGL 的 GL 上下文由浏览器管理，平台层仅提供最小的胶水代码。

## 核心类/结构体

### `PlatformWebGL`
- `createDriver()`: 直接调用 `createDefaultDriver`，不需要额外的上下文初始化。
- `terminate()`: 空操作。
- `createSwapChain(nativeWindow)`: 将原生窗口句柄直接作为交换链返回。
- `createSwapChain(width, height)`: 无头交换链暂未实现（返回 nullptr）。
- `destroySwapChain()`: 空操作。
- `makeCurrent()` / `commit()`: 由浏览器隐式管理。

## 关键实现逻辑

- WebGL 上下文由 Emscripten 运行时自动管理，平台层无需显式创建或销毁。
- 所有 GL 调用通过 Emscripten 的 WebGL 绑定映射到浏览器 API。

## 依赖关系

- `backend/platforms/PlatformWebGL.h`
- `backend/Platform.h`
