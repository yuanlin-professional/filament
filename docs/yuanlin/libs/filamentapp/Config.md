# Config.h

## 文件概述
定义示例应用程序的全局配置结构体 `Config`，用于控制窗口标题、渲染后端、相机模式、立体渲染等参数。

## 核心类/结构体

### `Config`
应用启动配置，包含以下字段：
- `title` / `iblDirectory` / `dirt` - 窗口标题、IBL 目录、脏纹理路径
- `scale` - 缩放因子（默认 1.0）
- `splitView` - 是否启用分割视图调试模式
- `backend` - 渲染后端（Vulkan/OpenGL/Metal 等）
- `featureLevel` - 后端功能级别
- `cameraMode` - 相机操作模式（轨道/飞行等）
- `vulkanGPUHint` - Vulkan GPU 偏好设置
- `forcedWebGPUBackend` - 强制指定的 WebGPU 后端
- `asynchronousMode` - 引擎异步模式

## 依赖关系
- `filament/Engine.h` - 后端枚举和功能级别
- `camutils/Manipulator.h` - 相机操作模式枚举
