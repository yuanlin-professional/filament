# PlatformCocoaGL.mm

## 文件概述

macOS 平台的 OpenGL 上下文管理实现，使用 NSOpenGLContext 和 NSView 创建和管理 GL 上下文、交换链和外部图像。

## 核心类/结构体

### `CocoaGLSwapChain`
交换链实现，持有 NSView 引用并通过通知中心（NSNotificationCenter）监听窗口和视图的尺寸/位置变化。

### `PlatformCocoaGLImpl`
平台内部实现结构体：
- `mGLContext`: 主 NSOpenGLContext。
- `mCurrentSwapChain`: 当前活跃的交换链。
- `mTextureCache`: CVOpenGLTextureCache（用于外部图像）。
- `mAdditionalContexts`: 额外的共享上下文列表（用于着色器编译线程池）。

### 关键方法
- `createDriver()`: 创建 NSOpenGLContext（Core Profile 3.2），初始化 BlueGL，创建纹理缓存。
- `makeCurrent()`: 切换交换链，处理窗口大小变化时的 `clearDrawable`/`setView` 调用。
- `commit()`: 调用 `flushBuffer` 提交帧，刷新纹理缓存。
- `createContext()`: 创建额外的共享 GL 上下文（用于异步编译）。

## 关键实现逻辑

- NSOpenGLContext 的 `setView` 和 `update` 必须在主线程调用（macOS 10.15+ 强制要求），通过 `dispatch_sync` 确保线程安全。
- 窗口大小变化检测使用 NSNotification 机制，监听 `NSWindowDidResizeNotification`、`NSViewFrameDidChangeNotification` 等。
- 多窗口场景下需要小心处理 `clearDrawable`，避免错误清除其他窗口的内容。

## 依赖关系

- `backend/platforms/PlatformCocoaGL.h`
- `CocoaExternalImage.h` - 外部图像处理
- `bluegl/BlueGL.h` - GL 函数加载
- `Cocoa/Cocoa.h`, `OpenGL/OpenGL.h`
