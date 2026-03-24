# WebGPUConstants

## 文件概述

定义 WebGPU 后端使用的全局常量、调试标志和宏。这是一个纯头文件，无对应实现文件。

## 核心定义

### 缓冲区常量
- `FILAMENT_WEBGPU_BUFFER_SIZE_MODULUS` = 4 - WebGPU 缓冲区大小必须为 4 的倍数

### 调试标志（位掩码）
- `FWGPU_DEBUG_VALIDATION` - 运行时验证
- `FWGPU_PRINT_SYSTEM` - 打印系统组件（实例、适配器、设备等）
- `FWGPU_DEBUG_FORCE_LOG_TO_I` - 强制所有日志输出到 INFO 流
- `FWGPU_DEBUG_UPDATE_IMAGE` - 纹理更新日志
- `FWGPU_DEBUG_BLIT` - Blit 操作日志
- `FWGPU_DEBUG_BIND_GROUPS` - 绑定组日志
- `FWGPU_DEBUG_SYSTRACE` - Android systrace 支持
- `FWGPU_DEBUG_PROFILING` - 性能分析模式（与其他调试标志互斥）

### 超时常量
- 适配器请求、设备请求、着色器编译均为 1000 毫秒

### 缓存过期常量
- 渲染管线过期帧数: 45 帧
- 管线布局过期帧数: 90 帧

## 关键实现逻辑

- `FWGPU_ENABLED(flags)` 宏用于编译期检查调试标志是否启用
- 调试模式自动启用 `FWGPU_DEBUG_PERFORMANCE`；Release 模式仅启用 `FWGPU_DEBUG_SYSTRACE`
- `FWGPU_LOGD/LOGI/LOGW/LOGE` 宏支持日志分级，可通过 `FWGPU_DEBUG_FORCE_LOG_TO_I` 统一输出

## 依赖关系

- `utils/Logger.h` - 日志基础设施
- `private/utils/Tracing.h` - Systrace 跟踪（条件编译）
