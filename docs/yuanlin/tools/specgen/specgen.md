# specgen.cpp

## 文件概述
SPECGEN（Spectral Integration Matrix Generator）为实时色散着色器生成光谱积分矩阵和折射率偏移量。输出的矩阵 Kn 和偏移量数组可直接用于着色器中模拟棱镜色散效果。

## 核心函数
- `main()` - 入口：配置解析 -> 生成采样点 -> 计算光谱权重 -> 生成矩阵和偏移量
- `computeGaussLegendre()` - 计算高斯-勒让德积分节点和权重
- `computeIOROffset()` - 计算给定波长的折射率偏移（Cauchy 或线性模型）

## 核心类
- `CMFProvider` / `LUTCMF` / `AnalyticCMF` - CIE 色彩匹配函数提供者（查表或 Wyman 解析近似）

## 关键实现逻辑
1. 矩阵推导：`Kn = M_XYZ_to_sRGB * Diag(Wn) * M_sRGB_to_XYZ`，其中 Wn 是波长 n 处的 XYZ 光谱权重
2. 归一化确保 `Sum(Kn) = I`（身份矩阵），即无色散时白色输入产生白色输出
3. 三种采样模式：`fraunhofer`（标准光学线 F/e/D/C）、`gaussian`（高斯积分）、`linear`（均匀采样）
4. Cauchy 色散模型：`n(lambda) = nD + ((nD-1)/Vd) * offset(lambda)`，offset 基于 `1/lambda^2` 变化
5. 输出格式支持 GLSL、C++ 和纯文本

## 依赖关系
- `math/` - 矩阵和向量运算
- CIE 1931 2 度色彩匹配函数数据表
