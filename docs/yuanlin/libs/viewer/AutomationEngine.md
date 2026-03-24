# AutomationEngine.h / AutomationEngine.cpp

## 文件概述
自动化测试引擎，用于将设置参数推送到 Filament 对象，或按预定义的设置排列组合迭代执行测试。支持批处理模式和截图导出。

## 核心类

### `AutomationEngine`
- **构造函数**: 接收 `AutomationSpec`（测试规格）和 `Settings`（可变设置对象）
- `createFromJSON(jsonSpec, size)` - 从 JSON 字符串创建引擎
- `createDefault()` - 创建使用内置默认测试序列的引擎
- `startRunning()` / `startBatchMode()` - 启动运行/批处理模式
- `tick(engine, content, deltaTime)` - 每帧调用，推送设置、计时、推进测试
- `applySettings(engine, json, jsonLength, content)` - 从 JSON 字符串直接应用设置（远程控制模式）
- `getColorGrading(engine)` - 获取或创建缓存的 ColorGrading 对象
- `exportScreenshot(view, renderer, filename, ...)` - 异步截图导出（支持 PPM 和 TIFF）

### `Options`
控制测试间隔时间、最小帧数、是否导出截图/设置等。

### `ViewerContent`
Filament 对象集合：View、Renderer、MaterialInstance 数组、LightManager、Scene 等。

## 关键实现逻辑
- tick() 中使用 `activateTest` lambda 应用当前测试的设置到所有 Filament 对象
- 截图使用 `renderer->readPixels` 异步读取像素数据，在回调中写入文件
- 批处理模式在最后一个测试截图完成后自动触发关闭
- 支持自定义光源（`updateCustomLights`）的动态创建和销毁

## 依赖关系
- `viewer/AutomationSpec.h`, `viewer/Settings.h`
- `image/*`, `imageio-lite/ImageEncoder.h` - 图像处理和编码
- `filament/*` - Engine, View, Renderer, Camera 等
