# main.cpp

## 文件概述
CSO-LUT 工具用于生成锥体/球体遮蔽（Cone-Sphere Occlusion）查找表。该 LUT 是一个 3D 纹理，存储两个锥体在不同角度和距离下的可见性值，用于实时环境光遮蔽计算。

## 核心函数
- `main()` - 入口：解析参数、生成 3D LUT 数据、输出为图像或文本
- `conesIntersection()` - 基于 Mazonka 2012 算法计算两个锥体的立体角交集
- `sphericalCapsIntersection()` - 基于 Oat & Sander 2007 算法计算球面帽交集（备用方法）
- `groundTruthVisibility()` - 蒙特卡洛模拟计算精确可见性（调试/验证用）
- `generateSampleRays()` - 在锥体内均匀生成采样光线

## 关键实现逻辑
1. LUT 三维坐标映射：X = cos(phi) 范围 [-1,1]，Y = cos(theta1) 范围 [0,1]，Z = cos(theta2) 范围 [0,1]
2. 解析解基于 Mazonka 的锥体交集公式，处理了多种退化情况（同轴、半球、点锥等）
3. 可见性 = 1 - 交集面积 / 锥体立体角
4. 支持 `--ground-truth` 模式使用 1024 样本蒙特卡洛模拟
5. 输出 3D 纹理展平为 2D 图像（宽度 = W * D，高度 = H）

## 依赖关系
- `image/LinearImage` - 线性图像存储
- `imageio/ImageEncoder` - 图像编码输出
- `math/` - 数学工具函数
