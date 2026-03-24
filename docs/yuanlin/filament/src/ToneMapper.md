# ToneMapper.cpp

## 文件概述
ToneMapper.cpp 实现了 Filament 支持的所有色调映射算法，将 HDR 线性值映射到可显示范围。支持多种行业标准和自定义色调映射曲线。

## 核心类/结构体/函数
- **ToneMapper** - 色调映射器基类
- **LinearToneMapper** - 线性映射（简单的 saturate 截断）
- **ACESToneMapper** - ACES 参考渲染变换（brightness=1.0）
- **ACESLegacyToneMapper** - ACES 遗留版（brightness=1/0.6，更亮）
- **FilmicToneMapper** - Narkowicz 2015 ACES 曲线近似
- **PBRNeutralToneMapper** - PBR 中性色调映射（modelviewer.dev 方案）
- **GT7ToneMapper** - Gran Turismo 7 物理色调映射（Polyphony Digital, SIGGRAPH 2025）
  - `GT7Curve` - 分段映射曲线（toe + linear + shoulder）
  - `State` - 内部状态（SDR 校正因子、ICtCp 色彩空间变换）
- **AgxToneMapper** - AgX 色调映射（支持 NONE / GOLDEN / PUNCHY 三种风格）
- **DisplayRangeToneMapper** - 调试用显示范围可视化（16 色伪彩色）
- **GenericToneMapper** - 可配置的通用色调映射（contrast、midGrayIn、midGrayOut、hdrMax）

## 关键实现逻辑
- **ACES 实现**：完整的 ACES 流程，包括 Glow 模块、红色修正、全局去饱和、RRT+ODT 拟合和暗环境补偿
- **GT7 实现**：使用 ICtCp 感知均匀色彩空间进行亮度映射和色度缩放，支持 SDR/HDR 输出
- **AgX 实现**：Rec.2020 色域下的 Log2 编码 + Sigmoid 对比度近似 + ASC CDL 色彩校正
- **GenericToneMapper**：参数化的 S 形曲线 `outputScale * x^contrast / (x^contrast + inputScale)`
- 所有色调映射器在 Rec.2020 色彩空间下工作

## 依赖关系
- `ColorSpaceUtils.h` - 色彩空间转换矩阵和工具函数
- `math/vec3.h` / `math/scalar.h` - 数学工具

## 被引用关系
- `ColorGrading` - 在色彩分级流程中应用色调映射
