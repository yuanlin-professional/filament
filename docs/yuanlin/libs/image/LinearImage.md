# LinearImage.h / LinearImage.cpp

## 文件概述

Filament 核心图像处理库的基础数据结构。`LinearImage` 是一个行优先存储的浮点像素数据句柄，作为图像处理算法的输入/输出格式，与具体的源和目标格式无关。

## 核心类/结构体/函数

- **`LinearImage`** - 浮点线性图像类
  - `LinearImage(width, height, channels)` - 分配零初始化图像
  - `LinearImage(const LinearImage&)` - 浅拷贝（共享像素数据）
  - `LinearImage()` - 创建空（无效）图像
  - `getPixelRef()` - 获取像素数据指针（多个重载：全局/指定坐标/const版本）
  - `get<T>()` - 类型化像素访问（如 `get<float3>(x, y)`）
  - `getWidth()` / `getHeight()` / `getChannels()` - 图像维度查询
  - `isValid()` / `operator bool()` - 有效性检查
  - `reset()` - 重置为空图像

## 关键实现逻辑

- 像素数据通过 `SharedReference` 内部结构实现共享所有权语义（基于 `shared_ptr<float>`）
- 行步幅 = width * channels * sizeof(float)，无额外对齐
- 通道数任意（1 或更多），常用 3 通道表示颜色数据
- 像素按交错格式存储（非平面格式），但 `ImageOps` 提供平面数据合并功能
- 拷贝赋值创建新的 `SharedReference` 但共享底层 `shared_ptr` 数据

## 依赖关系

- `utils/compiler.h` - 编译器工具宏（UTILS_PUBLIC 导出标记）
