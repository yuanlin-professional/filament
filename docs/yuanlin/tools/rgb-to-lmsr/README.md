# rgb-to-lmsr - RGB 到 LMSR 色彩空间变换矩阵计算工具

## 模块名称和概述

`rgb-to-lmsr` 是一个色彩科学计算工具，用于生成从 RGB（Rec.709）色彩空间到 LMSR 锥体响应空间的变换矩阵。LMSR 代表人眼视锥细胞（L/M/S 三种锥体）和视杆细胞（R）的光谱响应，该矩阵在色彩适应、色觉模拟等高级渲染技术中有重要应用。

## 目录结构

```
tools/rgb-to-lmsr/
    CMakeLists.txt      # CMake 构建配置
    src/
        main.cpp        # 主程序，包含光谱数据和矩阵计算逻辑
```

## 核心功能

- **光谱积分**: 基于 CIE 标准光谱数据通过数值积分计算变换矩阵
- **Rec.709 反射率频谱**: 内置 CIE 1931 反射率频谱数据（390nm-790nm，401 采样点）
- **LMSR 锥体数据**: 内置 Stockman & Sharpe (2000) 的 2 度锥体基函数数据
- **视杆细胞数据**: 内置 CIE (1951) 暗视觉光谱光效函数
- **D65 光源**: 基于 CIE D65 标准光源进行积分
- **可选归一化**: 通过 `--normalize` 参数控制是否归一化输出矩阵

### 使用方式

```bash
# 计算变换矩阵
rgb-to-lmsr

# 归一化输出
rgb-to-lmsr --normalize
```

### 输出格式

输出一个 4x3 列主序矩阵，用于将 Rec.709 RGB 值变换到 LMSR 空间：

```
L_r, L_g, L_b,
M_r, M_g, M_b,
S_r, S_g, S_b,
R_r, R_g, R_b
```

## 依赖关系

- **utils** - Filament 通用工具库（路径处理、命令行解析）
- **math** - Filament 数学库（mat3、vec3、scalar）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现，包含大量内置光谱数据表和矩阵积分计算逻辑 |
| `CMakeLists.txt` | 构建配置，链接 utils 和 math 库 |
