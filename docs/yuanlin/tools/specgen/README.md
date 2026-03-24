# specgen - 光谱积分矩阵生成器

## 模块名称和概述

`specgen`（Spectral Integration Matrix Generator）是一个用于实时色散渲染的预计算工具。它生成一组光谱积分矩阵和 IOR（折射率）偏移量，使 Filament 的着色器能够在 RGB 管线中近似模拟光的色散效果（如棱镜分光）。该工具基于 CIE 色彩匹配函数和 sRGB/XYZ 色彩空间变换进行严格的物理推导。

## 目录结构

```
tools/specgen/
    CMakeLists.txt              # CMake 构建配置
    README.md                   # 原始英文文档（含详细理论推导）
    specgen.cpp                 # 主程序源码（单文件实现）
    spectrum_integration.png    # 光谱积分说明图
```

## 核心功能

- **光谱积分矩阵预计算**: 计算 N 个采样波长的 3x3 颜色变换矩阵 Kn
- **CIE 色彩匹配函数**: 内置 CIE 1931 2 度观察者的 XYZ 色彩匹配函数数据
- **能量守恒归一化**: 确保所有矩阵之和为单位矩阵，白色输入产生白色输出
- **Cauchy 色散模型**: 基于 Abbe 数参数化的折射率-波长关系
- **IOR 偏移量生成**: 预计算各采样波长相对于基准波长的 IOR 偏移值
- **高斯求积**: 使用高斯积分规则对可见光谱范围进行数值积分

### 理论基础

该工具基于以下变换管线：

1. 输入 sRGB 颜色 -> 转换到 CIE XYZ 色彩空间
2. 对 N 个光谱采样波长，通过 CIE CMF 加权计算光谱贡献
3. 积分结果转换回 sRGB

每个采样矩阵 Kn 的推导公式为：

```
Kn = M_XYZ_to_sRGB * Diag(Wn) * M_sRGB_to_XYZ
```

### 使用方式

```bash
# 运行生成器
specgen
```

输出可直接用于着色器代码中的色散计算。

## 依赖关系

- **math** - Filament 数学库（矩阵和向量运算）
- **utils** - Filament 通用工具库

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `specgen.cpp` | 完整的工具实现，包含 CIE CMF 数据、高斯求积、矩阵计算和 IOR 偏移量生成 |
| `README.md` | 详细的理论推导文档，包含数学公式和归一化证明 |
| `spectrum_integration.png` | 光谱积分过程的可视化说明图 |
| `CMakeLists.txt` | 构建配置，链接 math 和 utils 库 |
