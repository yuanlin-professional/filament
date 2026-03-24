# CubemapUtils.h / CubemapUtils.cpp / CubemapUtilsImpl.h

## 文件概述

立方体贴图的创建、格式转换和处理工具集。提供多线程 scanline 处理框架、格式转换（equirectangular/cross/octahedron）、降采样和镜像等操作。

## 核心类/结构体/函数

- **`CubemapUtils`** - 工具类
  - `create(image, dim)` - 创建 Cubemap 及其后备 Image
  - `process<STATE>()` - 多线程 scanline 处理（使用 JobSystem）
  - `processSingleThreaded<STATE>()` - 单线程版本
  - `solidAngle(dim, u, v)` - 计算立方体贴图像素的立体角
  - `setAllFacesFromCross()` - 从十字交叉图设置 6 个面
  - `downsampleCubemapLevelBoxFilter()` - Box 滤波降采样
  - `clamp()` / `highlight()` - HDR 范围压缩和高亮标记
  - `equirectangularToCubemap()` - 等距矩形投影转立方体贴图
  - `cubemapToEquirectangular()` - 立方体贴图转等距矩形
  - `cubemapToOctahedron()` - 立方体贴图转八面体映射
  - `crossToCubemap()` - 十字交叉图转立方体贴图
  - `mirrorCubemap()` - 水平镜像
  - `generateUVGrid()` - 生成 UV 网格调试图
- **`ScanlineProc<STATE>`** / **`ReduceProc<STATE>`** - 处理和归约回调类型

## 关键实现逻辑

- **process()** 模板（CubemapUtilsImpl.h）：为每个面创建独立 STATE，使用 `parallel_for` 并行处理 scanline。无状态时（EmptyState）使用完全并行化，有状态时退化为串行
- **立体角计算**：使用 `atan2(x*y, sqrt(x^2+y^2+1))` 的四象限面积公式
- **equirectangular 转换**：通过 Hammersley 序列进行超采样，动态计算采样数（基于投影面积）
- **Cubemap 内存布局**：面尺寸 dim+2（每边加 1 像素 padding），排列为 4x3 十字交叉

## 依赖关系

- `ibl/Cubemap.h`, `ibl/Image.h`, `ibl/utilities.h` - IBL 基础类型
- `utils/JobSystem.h` - 多线程
- `math/mat4.h` - 矩阵变换
