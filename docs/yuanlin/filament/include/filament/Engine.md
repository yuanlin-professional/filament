# Engine.h

## 文件概述

Filament 渲染引擎的核心入口，负责管理所有渲染资源的创建/销毁、渲染线程和硬件上下文。每个 Engine 实例对应一个 GPU 硬件上下文。

## 核心类/结构体

- **`Engine`** - 引擎主类，管理所有 Filament 资源和渲染线程。
- **`Engine::Config`** - 引擎配置，控制内存分配大小、线程数、Metal/立体渲染等选项。
- **`Engine::Builder`** - 用于创建 Engine 实例的构建器。
- **`Engine::FeatureFlag`** - 功能标志，可在构建时或运行时启用/禁用特定功能。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Engine::create(backend, platform, ...)` | 创建引擎实例（静态方法） |
| `Engine::destroy(engine)` | 销毁引擎及所有关联资源 |
| `createSwapChain(nativeWindow, flags)` | 创建交换链 |
| `createRenderer()` | 创建渲染器 |
| `createView()` / `createScene()` | 创建视图/场景 |
| `createCamera(entity)` | 创建相机组件 |
| `destroy(resource)` | 销毁各类资源（重载多种类型） |
| `isValid(resource)` | 检查资源是否有效 |
| `flushAndWait()` | 刷新命令队列并等待完成 |
| `getBackend()` | 获取当前使用的后端（OpenGL/Vulkan/Metal） |
| `getRenderableManager()` / `getLightManager()` / `getTransformManager()` | 获取各管理器 |
| `setAutomaticInstancingEnabled(enable)` | 启用/禁用自动实例化 |
| `setFeatureFlag(name, value)` | 设置功能标志 |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/DriverEnums.h`, `backend/Platform.h`
- `utils/compiler.h`, `utils/Invocable.h`, `utils/Slice.h`

## 使用示例

```cpp
Engine* engine = Engine::create();
SwapChain* swapChain = engine->createSwapChain(nativeWindow);
Renderer* renderer = engine->createRenderer();
Scene* scene = engine->createScene();
View* view = engine->createView();

// 渲染循环
if (renderer->beginFrame(swapChain)) {
    renderer->render(view);
    renderer->endFrame();
}

Engine::destroy(&engine);
```
