# ImGuiHelper.h / ImGuiHelper.cpp

## 文件概述
将 ImGui 的绘制命令转换为 Filament 渲染原语，负责管理 UI 专用的 Scene、Camera、VertexBuffer、IndexBuffer 和 Material。

## 核心类

### `ImGuiHelper`
- **构造函数**: 创建 UI 场景、正交相机、alpha 混合材质，加载字体纹理图集
- `setDisplaySize(width, height, scaleX, scaleY, flipVertical)` - 设置显示尺寸和 DPI 缩放
- `render(timeStep, imguiCommands)` - 高层接口：调用 ImGui::NewFrame -> 执行用户回调 -> ImGui::Render -> processImGuiCommands
- `processImGuiCommands(commands, io)` - 低层接口：将 ImDrawData 转换为 Filament 对象
- `createAtlasTexture(engine)` - 创建/重建字体纹理图集
- `getView()` - 获取 UI 视图

## 关键实现逻辑
- 每帧根据 ImDrawData 重建 Renderable 组件，包含多个 primitive
- 动态管理 VertexBuffer 和 IndexBuffer，容量不足时自动增长（需要 Fence 同步）
- 顶点格式匹配 ImDrawVert：FLOAT2 位置 + FLOAT2 UV + UBYTE4 颜色
- 使用 `uiblit` 材质（alpha 混合 2D 贴图）
- 支持 ImGui 纹理管理（WantCreate/WantUpdates/WantDestroy 生命周期）
- Android 上额外支持 SAMPLER_EXTERNAL 材质
- 裁剪矩形通过 MaterialInstance 的 setScissor 实现
- 正交投影相机，y 轴可翻转

## 依赖关系
- `imgui.h` - ImGui 核心
- `filament/*` - Engine, Scene, View, Camera, Material, VertexBuffer, IndexBuffer, Texture 等
- `utils/EntityManager.h`
