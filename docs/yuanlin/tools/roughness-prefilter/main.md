# main.cpp (roughness-prefilter)

## 文件概述
粗糙度预过滤工具。从法线贴图生成预过滤的粗糙度贴图 mipmap 链，用于缓解实时渲染中的镜面反射走样（specular aliasing）问题。

## 核心函数
- `main()` - 入口：加载法线贴图和可选粗糙度贴图 -> 逐 mip 级别预过滤 -> 输出
- `solveVMF()` - 使用 von Mises-Fisher 分布估计法线方差，计算过滤后的粗糙度
- `normalFiltering()` - 核心公式：`sqrt(roughness^2 + min(2*variance, threshold^2))`
- `prefilter()` - 对每个像素采样法线贴图并计算预过滤粗糙度

## 关键实现逻辑
1. 基于 Karis 2018 "Normal map filtering using vMF (part 3)" 的方法
2. 每个 mip 级别的采样窗口大小为 `2^mipLevel`，在法线贴图上采样平均法线
3. 从平均法线长度 `r` 计算 von Mises-Fisher 浓度参数 `kappa = (3r - r^3) / (1 - r^2)`
4. 方差 = `1 / (4 * kappa)`，阈值裁剪到 0.2
5. 使用 `JobSystem` 并行处理各 mip 级别

## 依赖关系
- `image/` - 图像操作、KTX 容器
- `imageio/` - 编解码
- `utils/JobSystem` - 多线程作业系统
