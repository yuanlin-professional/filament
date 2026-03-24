# Renderer.cpp

## 文件概述
Renderer.cpp 实现了 Filament 渲染器的公共 API。Renderer 是渲染流程的入口点，负责管理帧的开始/结束、View 渲染、像素回读等核心操作。

## 核心类/结构体/函数
- **Renderer** - 渲染器公共接口
  - `getEngine()` - 获取所属引擎
  - `beginFrame()` / `endFrame()` - 帧的开始和结束
  - `render()` - 渲染指定 View
  - `shouldRenderFrame()` - 判断当前帧是否需要渲染
  - `skipFrame()` - 跳过当前帧
  - `skipNextFrames()` / `getFrameToSkipCount()` - 跳帧控制
  - `setPresentationTime()` - 设置呈现时间戳
  - `readPixels()` - 从默认渲染目标或指定 RenderTarget 回读像素
  - `copyFrame()` - 复制帧到目标交换链
  - `getUserTime()` / `resetUserTime()` - 用户时间管理
  - `setDisplayInfo()` - 设置显示信息
  - `setFrameRateOptions()` / `setClearOptions()` - 帧率和清除选项

## 关键实现逻辑
所有方法通过 `downcast` 委托给内部 `FRenderer` 实现。`beginFrame()` 接受 SwapChain 和 VSync 时间戳，返回是否应该渲染当前帧。

## 依赖关系
- `details/Renderer.h` / `details/Engine.h` / `details/View.h` - 内部实现
- `TextureCache.h` - 纹理缓存

## 被引用关系
- 用户层代码通过 Renderer 驱动渲染循环
- Engine 管理 Renderer 的生命周期
