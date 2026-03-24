# inline_dithering.fs / inline_vignette.fs

## 文件概述
内联后处理效果。在主着色流程末尾直接应用的轻量效果，避免额外的后处理通道。

## inline_dithering.fs
- 抖动处理，减少色带（banding）伪影
- 使用三角分布的噪声在量化前添加微小偏移
- 基于时间和像素坐标的伪随机噪声生成

## inline_vignette.fs
- 暗角效果，在画面边缘产生自然的亮度衰减
- 基于到画面中心距离的二次衰减
- 可配置暗角的半径和柔和度

## 依赖关系
- `frameUniforms` - 抖动和暗角参数
