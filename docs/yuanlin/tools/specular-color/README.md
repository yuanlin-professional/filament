# specular-color - 导体材质镜面反射颜色计算工具

## 模块名称和概述

`specular-color` 是一个基于光谱数据计算导体材质（金属）基础反射颜色的命令行工具。它读取材质的光谱折射率和消光系数数据，通过 Fresnel 方程计算法向入射（0 度）时的反射率 f0（即材质的基础颜色），并可选地计算指定掠射角度的反射颜色，用于 Lazanyi-Schlick 模型的参数拟合。

## 目录结构

```
tools/specular-color/
    CMakeLists.txt      # CMake 构建配置
    README.md           # 原始英文文档
    data/               # 内置金属光谱数据
        aluminium.txt   # 铝
        brass.txt       # 黄铜
        chromium.txt    # 铬
        copper.txt      # 铜
        gold.txt        # 金
        iron.txt        # 铁
        platinum.txt    # 铂
        silver.txt      # 银
        titanium.txt    # 钛
    src/
        main.cpp        # 主程序源码
```

## 核心功能

- **光谱数据解析**: 读取包含波长、折射率 n 和消光系数 k 的光谱数据文件
- **Fresnel 反射率计算**: 基于完整的 Fresnel 方程计算导体材质的反射率
- **f0 基础颜色**: 计算法向入射（0 度）的反射率，即 PBR 中的基础颜色
- **掠射角颜色**: 计算指定角度（默认 82 度）的反射颜色，用于 Lazanyi-Schlick 拟合
- **双色彩空间输出**: 同时输出线性 RGB 和 sRGB 格式的结果
- **内置金属数据**: 提供 9 种常见金属的光谱数据

### 使用方式

```bash
# 计算金的基础反射颜色
specular-color data/gold.txt

# 自定义掠射角角度
specular-color -a 75 data/gold.txt

# 使用自定义光谱数据
specular-color my_material.txt
```

光谱数据文件可从 [Refractive Index](https://refractiveindex.info/) 获取。

## 依赖关系

- **utils** - Filament 通用工具库（路径处理、命令行解析）
- **math** - Filament 数学库（向量和标量运算）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现，包含光谱数据解析、Fresnel 计算和色彩空间转换 |
| `data/*.txt` | 9 种常见金属的光谱折射率和消光系数数据 |
| `README.md` | 使用说明，包含 Lazanyi-Schlick 模型的理论背景引用 |
| `CMakeLists.txt` | 构建配置，链接 utils 和 math 库 |
