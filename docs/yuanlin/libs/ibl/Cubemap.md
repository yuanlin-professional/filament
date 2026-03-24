# Cubemap.h / Cubemap.cpp

## 文件概述

通用立方体贴图类，处理 6 个面的读写操作，支持无缝三线性滤波。此类不拥有面数据，仅作为 6 个 `Image` 的"视图"。通常通过 `CubemapUtils` 创建。

## 核心类/结构体/函数

- **`Cubemap`** - 立方体贴图类
  - `Cubemap(dim)` - 指定尺寸初始化（不分配内存）
  - `Face` 枚举 - PX/NX/PY/NY/PZ/NZ（6 个面）
  - `Texel` = `float3` - 像素类型
  - `setImageForFace()` / `getImageForFace()` - 设置/获取面对应的 Image
  - `getDirectionFor(face, x, y)` - 计算面上像素坐标对应的方向向量
  - `sampleAt(direction)` - 最近邻采样
  - `filterAt(direction)` - 双线性滤波采样
  - `trilinearFilterAt(c0, c1, lerp, direction)` - 两个 cubemap 间的三线性插值
  - `makeSeamless()` - 处理面边缘和角落，实现无缝采样
  - `getAddressFor(direction)` - 方向向量到面+UV 坐标的转换
- **`Address`** 结构体 - 包含 face、s、t 三个字段

## 关键实现逻辑

- 方向向量计算：将 [0,dim] 映射到 [-1,1]，根据面方向组合 (cx, cy, 1) 并归一化
- `getAddressFor()` 通过比较 |rx|、|ry|、|rz| 确定主轴面，再计算 s/t 坐标
- `makeSeamless()` 通过 stitch 操作复制相邻面的边缘像素到当前面的 padding 区域，角落取三个相邻像素的平均值
- 双线性滤波利用 seamless padding 区域，允许读取 width/height 之外的有效数据

## 依赖关系

- `ibl/Image.h` - 面数据存储
- `math/vec2.h`, `math/vec3.h`, `math/vec4.h` - 数学类型
- `utils/compiler.h` - 编译器宏
