# Settings.h / Settings.cpp

## 文件概述
定义 glTF 查看器的完整设置数据结构及其 JSON 序列化/反序列化。提供将设置推送到 Filament 对象的 `applySettings` 系列函数。

## 核心结构体

### `Settings` - 顶层设置聚合体
包含以下子设置：
- `ViewSettings` - 视图渲染选项（AA、后处理、阴影、ColorGrading 等）
- `MaterialSettings` - 材质参数覆盖（最多 4 个 scalar/float3/float4）
- `LightSettings` - 光照设置（阴影开关、太阳光参数、IBL 强度/旋转、自定义灯光列表）
- `ViewerOptions` - 查看器选项（天空盒、地面阴影、自动缩放等）
- `CameraSettings` - 相机参数（位置、FOV、近远平面、曝光、立体视觉等）
- `AnimationSettings` - 动画控制
- `RenderSettings` - 渲染器选项（清除选项、帧率选项）
- `DebugOptions` - 调试选项（跳帧数）
- `ColorGradingSettings` - 色彩分级完整参数

### `JsonSerializer`
- `writeJson(settings)` - 将 Settings 序列化为 JSON 字符串
- `readJson(jsonChunk, size, out)` - 从 JSON 反序列化到 Settings（支持部分更新）

## 核心函数
- `applySettings(engine, viewSettings, view)` - 将视图设置推送到 Filament View
- `applySettings(engine, lightSettings, ibl, sunlight, ...)` - 将光照设置推送到场景
- `applySettings(engine, cameraSettings, camera, aspect)` - 应用相机参数
- `createColorGrading(settings, engine)` - 从 ColorGradingSettings 创建 ColorGrading 对象

## 关键实现逻辑
- JSON 解析使用 jsmn 库，每个结构体都有对应的 `parse` 重载函数
- JSON 序列化使用 `operator<<` 重载到 `std::ostream`
- 支持 9 种色调映射算法的创建和参数化
- ColorGradingSettings 使用 static_assert 检查结构体大小变化

## 依赖关系
- `filament/*` - View, Camera, Renderer, ColorGrading, LightManager 等
- `jsonParseUtils.h` / `Settings_generated.h` - JSON 解析基础设施
