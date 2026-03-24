# cso-lut - 锥体/球体遮蔽查找表生成器

## 模块名称和概述

`cso-lut`（Cone/Sphere Occlusion LUT）是一个用于生成锥体/球体遮蔽查找表的命令行工具。该查找表在 Filament 的渲染管线中用于近似计算环境遮蔽效果，通过预计算的方式避免实时渲染中的昂贵积分运算。

## 目录结构

```
tools/cso-lut/
    CMakeLists.txt      # CMake 构建配置
    README.md           # 原始英文文档（简短）
    src/
        main.cpp        # 主程序，包含 LUT 计算逻辑和命令行处理
```

## 核心功能

- **3D LUT 生成**: 生成三维查找表，维度可通过命令行参数 X/Y/Z 指定（默认 32x32x32）
- **Ground Truth 模式**: 通过 `--ground-truth` 参数可生成精确参考数据用于验证
- **多格式输出**: 支持 PNG 等图像格式输出
- **自定义压缩**: 支持指定输出格式的压缩选项

### 使用方式

```bash
# 默认尺寸（32x32x32）
cso-lut output.png

# 自定义 LUT 维度
cso-lut 64 64 64 output.png

# 生成精确参考数据
cso-lut --ground-truth output.png
```

## 依赖关系

- **imageio** - 图像编码输出库
- **libpng** / **tinyexr** / **libz** - 图像编解码第三方库（许可证依赖）
- **math** - Filament 数学库（向量和标量运算）
- **image** - Filament 图像处理库（LinearImage）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现，包含 CSO 积分计算、命令行解析和图像输出 |
| `CMakeLists.txt` | 构建配置，链接 imageio 库并设置许可证生成 |
