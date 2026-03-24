# PlatformFactory

## 文件概述

`PlatformFactory.cpp` 实现了平台对象的工厂方法。根据请求的后端类型（OpenGL、Vulkan、Metal、WebGPU、Noop）和当前操作系统，创建对应的 `Platform` 实例。

## 核心类/结构体

### `PlatformFactory`
- **create()**: 静态方法，接受 `Backend*` 参数（可被修改为实际使用的后端）。返回新创建的 Platform 对象，调用者负责销毁。
- **destroy()**: 销毁 Platform 对象并将指针置空。

## 关键实现逻辑

### 后端选择逻辑
1. Android 上可通过系统属性 `debug.filament.backend` 覆盖后端选择。
2. `Backend::DEFAULT` 的解析规则：
   - Emscripten / Android -> OpenGL
   - iOS / macOS -> Metal
   - 支持 Vulkan 的平台 -> Vulkan
   - 其他 -> OpenGL
3. 各后端的平台类映射：
   - **Noop**: `PlatformNoop`
   - **Vulkan**: `VulkanPlatformAndroid` / `VulkanPlatformApple` / `VulkanPlatformLinux` / `VulkanPlatformWindows`
   - **WebGPU**: `WebGPUPlatformAndroid` / `WebGPUPlatformApple` / `WebGPUPlatformLinux` / `WebGPUPlatformWindows`
   - **Metal**: 通过 `createDefaultMetalPlatform()` 工厂函数创建
   - **OpenGL**: `PlatformEGLAndroid` / `PlatformCocoaTouchGL` / `PlatformCocoaGL` / `PlatformGLX` / `PlatformEGLHeadless` / `PlatformOSMesa` / `PlatformWGL` / `PlatformWebGL`

### 条件编译
- 通过 `FILAMENT_SUPPORTS_OPENGL`、`FILAMENT_DRIVER_SUPPORTS_VULKAN`、`FILAMENT_SUPPORTS_METAL`、`FILAMENT_SUPPORTS_WEBGPU` 等宏控制可用后端。
- Linux 上进一步通过 `FILAMENT_SUPPORTS_X11`、`FILAMENT_SUPPORTS_EGL_ON_LINUX`、`FILAMENT_SUPPORTS_OSMESA` 区分窗口系统。
- `FILAMENT_USE_EXTERNAL_GLES3` 时 OpenGL 平台返回 nullptr（由外部提供）。

## 依赖关系

- 各平台头文件（按条件编译包含）
- `noop/PlatformNoop.h` - 空后端平台
- `private/utils/Tracing.h` - 性能追踪
