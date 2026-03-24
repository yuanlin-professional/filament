# RenderPass.h / RenderPass.cpp

## 文件概述
RenderPass 是 Filament 渲染管线的核心类，负责生成、排序和执行绘制命令。它将场景中的可渲染对象转换为有序的命令序列，实现高效的 GPU 绘制调用。

## 核心类/结构体/函数
- **RenderPass** - 渲染通道
  - **Command** (64 字节) - 绘制命令，包含排序键（CommandKey）和图元信息（PrimitiveInfo）
  - **CommandKey** (uint64_t) - 64 位排序键，编码了通道类型、优先级、材质 ID、Z 桶等
  - **PrimitiveInfo** (56 字节) - 图元渲染信息（材质实例、RenderPrimitive 句柄、索引偏移/计数、光栅状态等）
  - **Executor** - 命令执行器，持有命令切片和自定义命令闭包
  - **Pass** 枚举 - DEPTH / COLOR / REFRACT / BLENDED / SENTINEL
  - **CommandTypeFlags** - COLOR / DEPTH / SHADOW / SSAO / SCREEN_SPACE_REFLECTIONS
  - **RenderFlags** - HAS_SHADOWING / HAS_INVERSE_FRONT_FACES / IS_INSTANCED_STEREOSCOPIC / HAS_DEPTH_CLAMP
- **RenderPassBuilder** - 渲染通道构建器（Builder 模式）
  - `commandTypeFlags()` / `geometry()` / `camera()` / `variant()` / `renderFlags()` - 链式配置
  - `customCommand()` - 添加自定义命令（prologue/epilogue）
  - `build()` - 构建 RenderPass

## 关键实现逻辑
- **命令键编码**：64 位键精心设计以实现正确的排序顺序
  - DEPTH/COLOR 命令：通道 > 优先级 > Z 桶 > 材质 ID
  - BLENDED 命令：通道 > 优先级 > 距离（取反）> 混合顺序
  - CUSTOM 命令：通道 > 序号 > 自定义命令索引
- **自动实例化**：`instanceify()` 将连续相同材质的命令合并为实例化绘制
- **并行命令生成**：`generateCommands()` 支持并行处理（`JOBS_PARALLEL_FOR_COMMANDS_COUNT`）
- 使用 Arena 分配器管理命令内存，避免频繁的堆分配
- SharedHandle 用于管理实例化 UBO 和描述符集的生命周期

## 依赖关系
- `details/Scene.h` / `details/Camera.h` - 场景和相机数据
- `details/Material.h` / `details/MaterialInstance.h` - 材质信息
- `SharedHandle.h` - 共享句柄
- `backend/` - 驱动 API 和管线状态

## 被引用关系
- `FRenderer` / `RendererUtils` - 构建和执行渲染通道
- `ShadowMapManager` - 构建阴影渲染通道
- `PostProcessManager` - 结构通道和 SSR 通道
