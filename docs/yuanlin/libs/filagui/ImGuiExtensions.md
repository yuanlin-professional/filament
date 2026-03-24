# ImGuiExtensions.h / ImGuiExtensions.cpp

## 文件概述
ImGui 自定义扩展控件，提供方向箭头控件和多系列折线图控件，遵循 ImGui API 风格。

## 核心函数

### `ImGuiExt::DirectionWidget(label, v[3])`
可拖拽的 3D 箭头方向控件（灵感来自 AntTweakBar），包含一个可视化箭头和三个可拖拽的数值输入框。用于编辑光照方向等单位向量。

### `ImGuiExt::PlotLinesSeries(...)`
多系列折线图，扩展了 ImGui::PlotLines 的功能：
- `series_start(series)` / `series_end(series)` - 系列渲染前后的回调（可用于设置颜色等样式）
- `values_getter(series, data, idx)` - 带系列索引的数据获取回调

## 关键实现逻辑

### ArrowWidget（内部类）
- 使用四元数表示方向旋转
- 几何体由圆锥体（箭头头部）、圆柱体（箭头杆部）及其端盖组成，共 15 段细分
- 拖拽时计算鼠标轨迹对应的旋转四元数，相对于按下时的初始四元数进行旋转
- 使用简单的背面剔除（2D 叉积判断三角形朝向）和法线光照着色
- `quatFromDirection` 将方向向量转换为旋转 (1,0,0) 到该方向的四元数

### PlotEx
- 直接操作 ImGui 内部绘制列表，支持线段和直方图两种模式
- 对每个系列分别调用 start/end 回调，允许独立设置渲染样式

## 依赖关系
- `imgui.h`, `imgui_internal.h` - ImGui 核心和内部 API
- `math/vec3.h`, `math/quat.h` - 数学运算
