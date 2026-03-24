# ViewerGui.h / ViewerGui.cpp

## 文件概述
构建完整的 glTF 查看器 ImGui 界面，管理资产加载、动画播放、材质变体切换和渲染设置调整。可跨平台使用（包括 Web）。

## 核心类

### `ViewerGui`
- **构造函数**: 创建太阳光实体，初始化默认渲染设置
- `setAsset(asset, instance)` - 设置当前 glTF 资产和实例
- `populateScene()` - 逐步将已就绪的可渲染实体添加到场景（异步加载支持）
- `removeAsset()` - 从场景移除当前资产
- `setIndirectLight(ibl, sh3)` - 设置 IBL 并根据球谐系数估算太阳光方向和颜色
- `applyAnimation(currentTime, instance)` - 应用 glTF 动画，支持交叉淡入淡出
- `updateUserInterface()` - 构建完整的 ImGui 控件树
- `renderUserInterface(timeStep, guiView, pixelRatio)` - 使用内部 ImGuiHelper 渲染 UI
- `updateRootTransform()` - 根据 autoScaleEnabled 缩放模型到单位立方体

### `fitIntoUnitCube(bounds, zoffset)`
工具函数，计算将 AABB 适配到单位立方体的变换矩阵。

## 关键实现逻辑
- UI 面板包含：View（后处理/AA/SSAO）、Bloom/TAA/SSAO/SSR 详细选项、Dynamic Resolution、Light（IBL/太阳光/阴影）、Fog、Scene、Camera（DoF/Vignette）、Color Grading（色调映射曲线可视化）
- 动画支持交叉淡入淡出：在切换动画时同时应用新旧动画并线性插值
- 色彩分级 UI 包含完整的 SMH（阴影/中间调/高光）控制、CDL（色彩决策列表）和曲线编辑器
- Morph 目标权重可通过滑块手动调节（动画播放时禁用）

## 依赖关系
- `gltfio/Animator.h`, `gltfio/FilamentAsset.h` - glTF 资产和动画
- `filagui/ImGuiHelper.h`, `filagui/ImGuiExtensions.h` - ImGui 渲染
- `viewer/Settings.h` - 设置数据结构
- `filament/*` - 各种 Filament 渲染 API
