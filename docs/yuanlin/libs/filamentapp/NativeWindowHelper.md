# NativeWindowHelper.h / NativeWindowHelperCocoa.mm / NativeWindowHelperLinux.cpp / NativeWindowHelperWindows.cpp

## 文件概述
提供跨平台的原生窗口句柄获取和 Metal 层配置功能，将 SDL 窗口转换为各平台的原生窗口指针。

## 核心函数

### 通用接口
- `getNativeWindow(sdlWindow)` - 从 SDL_Window 获取平台原生窗口指针

### macOS 特有（Cocoa）
- `setUpMetalLayer(nativeView)` - 为 NSView 添加 CAMetalLayer，配置 drawableSize 和 contentsScale
- `prepareNativeWindow(sdlWindow)` - 设置窗口颜色空间为 sRGB
- `resizeMetalLayer(nativeView)` - 窗口大小变化时调整 Metal 层的 drawableSize

## 关键实现逻辑
- **macOS**: 通过 SDL_SysWMinfo 获取 NSWindow，再取 contentView
- **Linux**: 支持 X11（返回 Window 句柄）和 Wayland（返回包含 display/surface/size 的结构体指针）
- **Windows**: 返回 HWND 句柄
- Metal 层设置 opaque=YES 以支持全屏直通显示

## 依赖关系
- `SDL_syswm.h` - SDL 窗口系统信息
- `Cocoa/Cocoa.h`, `QuartzCore/QuartzCore.h` (macOS)
