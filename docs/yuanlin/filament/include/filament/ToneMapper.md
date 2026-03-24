# ToneMapper.h

## 文件概述

定义色调映射算子接口及多种内置实现，负责将 HDR 场景的动态范围压缩到适合显示的范围。

## 核心类/结构体

| 类 | 说明 |
|----|------|
| **`ToneMapper`** | 色调映射基类/接口，定义 `operator()` 虚函数 |
| `LinearToneMapper` | 线性映射（钳位到 0~1），调试用 |
| `ACESToneMapper` | ACES RRT+ODT 标准实现 |
| `ACESLegacyToneMapper` | ACES 旧版（亮度提升版） |
| `FilmicToneMapper` | 电影感色调映射 |
| `PBRNeutralToneMapper` | Khronos PBR 中性映射 |
| `GT7ToneMapper` | Gran Turismo 7 色调映射 |
| `AgxToneMapper` | AgX 映射（支持 NONE/PUNCHY/GOLDEN 风格） |
| `GenericToneMapper` | 可配置通用映射（对比度、中灰点、HDR 最大值） |
| `DisplayRangeToneMapper` | 16 色曝光可视化，调试用 |

## 主要 API

| 方法 | 说明 |
|------|------|
| `operator()(color)` | 执行色调映射（线性 Rec.2020 输入输出） |
| `isOneDimensional()` | 是否为一维映射（可用 1D LUT） |
| `isLDR()` | 是否仅适用于 LDR |

## 依赖关系

- `utils/compiler.h`, `math/mathfwd.h`

## 使用示例

```cpp
GenericToneMapper toneMapper(1.55f, 0.18f, 0.215f, 10.0f);
auto* cg = ColorGrading::Builder()
    .toneMapper(&toneMapper)
    .build(*engine);
```
