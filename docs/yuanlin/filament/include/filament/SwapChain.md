# SwapChain.h

## 文件概述

定义交换链，代表操作系统的原生可渲染表面（通常是窗口或视图），是 Filament 渲染输出的目标。

## 核心类/结构体

- **`SwapChain`** - 交换链，继承自 `FilamentAPI`。

## 配置标志

| 常量 | 说明 |
|------|------|
| `CONFIG_TRANSPARENT` | 请求带 alpha 通道的交换链 |
| `CONFIG_READABLE` | 允许读回渲染结果 |
| `CONFIG_SRGB_COLORSPACE` | 自动执行线性到 sRGB 编码 |
| `CONFIG_HAS_STENCIL_BUFFER` | 分配模板缓冲区 |
| `CONFIG_PROTECTED_CONTENT` | 受保护内容 |
| `CONFIG_MSAA_4_SAMPLES` | 4 倍 MSAA |

## 主要 API

| 方法 | 说明 |
|------|------|
| `isProtectedContentSupported(engine)` | 查询是否支持受保护内容（静态） |
| `isSRGBSwapChainSupported(engine)` | 查询是否支持 sRGB 交换链（静态） |
| `getNativeWindow()` | 获取原生窗口句柄 |
| `setFrameScheduledCallback(handler, callback)` | 设置帧调度回调 |
| `setFrameCompletedCallback(handler, callback)` | 设置帧完成回调（仅 Metal） |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/CallbackHandler.h`, `backend/DriverEnums.h`
- `utils/compiler.h`, `utils/Invocable.h`
