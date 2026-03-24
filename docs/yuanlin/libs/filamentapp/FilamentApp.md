# FilamentApp.h / FilamentApp.cpp

## 文件概述
示例应用框架的核心类，基于 SDL2 封装了完整的 Filament 应用生命周期，包括窗口创建、事件循环、多视图管理、相机控制和 ImGui 集成。

## 核心类

### `FilamentApp`（单例）
- `get()` - 获取全局唯一实例
- `run(config, setup, cleanup, imgui, preRender, postRender, width, height)` - 主运行函数，创建窗口、引擎和渲染循环
- `loadIBL(path)` - 加载 IBL 环境光
- `getRootAssetsPath()` - 获取资源根路径（支持多配置 CMake 生成器）

### `FilamentApp::CView`
内部视图封装类，绑定 Filament View 与 CameraManipulator，处理鼠标/键盘事件的分发。

### `FilamentApp::GodView`
调试用"上帝视角"视图，继承自 CView，使用独立的调试相机观察主场景。

### `FilamentApp::Window`
SDL 窗口封装，管理 SwapChain、Renderer、多个相机和多个视图。负责 HiDPI 坐标修正和视口配置。

## 关键实现逻辑
- 主循环中先让 ImGui 处理事件，再让应用处理（双遍事件循环）
- 支持分割视图模式：主视图、深度视图、上帝视图、正交视图
- Froxel 网格可视化：根据 FroxelConfigurationInfo 动态更新 Grid
- 使用 SDL_Delay 实现简单的帧率限制
- 支持 Vulkan/Metal/WebGPU/OpenGL 多后端选择

## 依赖关系
- `SDL.h` - 窗口和事件系统
- `filament/*` - Engine, Renderer, Scene, View, Camera, SwapChain 等
- `filagui/ImGuiHelper.h` - ImGui 集成
- `filamentapp/Cube.h`, `filamentapp/Grid.h`, `filamentapp/IBL.h`
- `filamentapp/NativeWindowHelper.h` - 平台原生窗口
