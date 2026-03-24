# Options.h

## 文件概述

定义 View 级别的各种渲染选项结构体，包括动态分辨率、泛光、雾效、景深、暗角、SSAO、TAA、阴影类型等后处理配置。

## 核心结构体/枚举

| 名称 | 说明 |
|------|------|
| `DynamicResolutionOptions` | 动态分辨率配置（最小/最大缩放、锐度、上采样质量） |
| `BloomOptions` | 泛光效果配置（强度、级别、分辨率、镜头光晕） |
| `FogOptions` | 大气雾效配置（距离、高度衰减、颜色、日光散射） |
| `DepthOfFieldOptions` | 景深效果配置（弥散圈、采样环数） |
| `VignetteOptions` | 暗角效果配置（中点、圆度、羽化） |
| `RenderQuality` | HDR 颜色缓冲区质量 |
| `AmbientOcclusionOptions` | 环境光遮蔽配置（SAO/GTAO、半径、强度） |
| `MultiSampleAntiAliasingOptions` | MSAA 配置 |
| `TemporalAntiAliasingOptions` | TAA 配置（反馈、抖动模式） |
| `ScreenSpaceReflectionsOptions` | 屏幕空间反射配置 |
| `VsmShadowOptions` / `SoftShadowOptions` | VSM/软阴影配置 |
| `QualityLevel` | 质量枚举：LOW/MEDIUM/HIGH/ULTRA |
| `AntiAliasing` | 后处理抗锯齿：NONE/FXAA |
| `Dithering` | 抖动：NONE/TEMPORAL |
| `ShadowType` | 阴影类型：PCF/VSM/DPCF/PCSS |

## 依赖关系

- `filament/Color.h`
- `math/vec2.h`, `math/vec3.h`
