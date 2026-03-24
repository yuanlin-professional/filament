# PlatformOSMesa.cpp

## 文件概述

OSMesa（Off-Screen Mesa）平台实现，使用 Mesa 3D 的软件光栅化器进行完全的 CPU 端离屏渲染，适用于无 GPU 硬件或 CI/CD 环境。

## 核心类/结构体

### `OSMesaSwapchain`
内部交换链结构，包含宽高和 CPU 端帧缓冲区（使用 GLfloat 作为像素后备类型）。

### `PlatformOSMesa`（通过实现文件推断）
- 使用 `OSMesaCreateContextExt` 创建 OSMesa 上下文。
- 使用 `OSMesaMakeCurrent` 将上下文绑定到 CPU 端缓冲区。
- 通过 `OSMesaGetProcAddress` 获取 GL 函数指针。

## 关键实现逻辑

- 使用 `GL_FLOAT` 作为帧缓冲区的像素格式，支持 HDR 渲染。
- 通过 `__attribute__((weak))` 弱链接 `OSMesaGetProcAddress`，允许在不链接 OSMesa 库时编译。
- 每个交换链分配独立的 CPU 端缓冲区 (`width * height * 4 * sizeof(GLfloat)`)。

## 依赖关系

- `backend/platforms/PlatformOSMesa.h`
- OSMesa 库 (运行时)
