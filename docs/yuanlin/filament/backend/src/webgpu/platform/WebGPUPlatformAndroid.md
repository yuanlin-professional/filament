# WebGPUPlatformAndroid

## 文件概述

Android 平台的 WebGPU 后端实现，处理 Android 原生窗口的 surface 创建和尺寸获取。

## 核心实现

- **适配器选项**: 请求 Vulkan 和 OpenGLES 后端，高/低功耗偏好，含/不含回退适配器
- **Surface 创建**: 使用 `wgpu::SurfaceSourceAndroidNativeWindow` 从 ANativeWindow 创建
- **尺寸获取**: 通过 `ANativeWindow_getWidth/Height` 获取窗口尺寸

## 依赖关系

- `backend/platforms/WebGPUPlatformAndroid.h` - 平台子类声明
- `android/native_window.h` - Android NDK
