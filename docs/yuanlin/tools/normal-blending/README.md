# normal-blending - 法线贴图混合工具

## 模块名称和概述

`normal-blending` 是一个用于将两张法线贴图混合为单张纹理的命令行工具。它采用"重定向法线映射"（Reoriented Normal Mapping）技术进行混合，该方法在数学上比常见的线性混合或叠加混合更为准确，能正确保留两张法线贴图的细节特征。

## 目录结构

```
tools/normal-blending/
    CMakeLists.txt      # CMake 构建配置
    README.md           # 原始英文文档
    src/
        main.cpp        # 主程序，包含法线混合算法实现
        *.h             # 头文件（通过 GLOB 自动收集）
```

## 核心功能

- **重定向法线映射**: 使用数学上正确的 Reoriented Normal Mapping 混合算法
- **双法线贴图输入**: 接受两张法线贴图作为输入
- **精确混合**: 相比线性混合和 overlay 混合，提供数学上更准确的结果
- **多格式支持**: 通过 imageio 库支持 PNG、HDR、EXR 等格式
- **图像输出**: 生成混合后的单张法线贴图

### 使用方式

```bash
# 混合两张法线贴图
normal-blending <normal_map_1> <normal_map_2> <output>
```

### 算法说明

传统的法线贴图混合方法（如线性混合或 Photoshop 叠加模式）无法正确处理法线方向的组合。重定向法线映射通过在切线空间中正确旋转法线向量来实现精确混合，确保混合结果在物理上合理。

## 依赖关系

- **imageio** - 图像读写库
- **libpng** / **tinyexr** / **libz** - 图像编解码第三方库（许可证依赖）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现，包含 Reoriented Normal Mapping 算法和图像 IO 处理 |
| `CMakeLists.txt` | 构建配置，链接 imageio 库并设置安装和许可证规则 |
| `README.md` | 简要说明混合算法的优势 |
