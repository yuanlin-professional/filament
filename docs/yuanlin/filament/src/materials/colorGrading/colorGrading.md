# Color Grading

## 文件概述

`colorGrading/` 目录包含色彩分级后处理效果的实现（`colorGrading.h`/`colorGrading.cpp`/`colorGrading.mat`/`colorGrading.fs` 及变体），负责色调映射、颜色校正和最终输出变换。

## 材质文件

| 文件 | 说明 |
|------|------|
| `colorGrading.mat` | 主色彩分级材质 |
| `colorGradingAsSubpass.mat` | 作为子通道的色彩分级（优化，避免额外的渲染通道） |
| `customResolveAsSubpass.mat` | 自定义解析子通道变体 |

## 核心类/结构体

- `colorGrading.h`/`colorGrading.cpp` 提供 C++ 端的 3D LUT 生成和色彩空间变换配置。

## 关键实现逻辑

- **3D LUT**: 将色调映射和颜色校正烘焙到 3D 查找表纹理中，片段着色器只需一次纹理查找即可完成全部色彩变换。
- **色调映射**: 将 HDR 线性颜色映射到 LDR 显示范围，支持多种色调映射曲线（ACES、Filmic 等）。
- **子通道优化**: 在支持子通道的平台上（Vulkan、Metal），色彩分级可以作为主渲染通道的子通道执行，避免额外的全屏 blit。
- **抖动（Dithering）**: 在量化到输出位深前添加抖动以减少色带。

## 依赖关系

- `colorGrading.fs` - 片段着色器实现
- 3D LUT 纹理 - 在 C++ 端生成并上传
