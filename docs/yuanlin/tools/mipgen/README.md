# mipgen - Mipmap 多级纹理生成工具

## 模块名称和概述

`mipgen` 是一个命令行工具，用于为输入图像生成完整的 mipmap 层级链，从原始分辨率一直缩小到 1x1 像素。Mipmap 是实时渲染中用于减少纹理走样和提高采样效率的关键技术，该工具支持多种滤波算法和图像格式。

## 目录结构

```
tools/mipgen/
    CMakeLists.txt      # CMake 构建配置
    README.md           # 原始英文文档
    src/
        main.cpp        # 主程序源码，包含 mipmap 生成逻辑和命令行处理
```

## 核心功能

- **完整 Mipmap 链生成**: 从输入图像逐级缩小到 1x1 像素
- **多种滤波内核**: 支持 box、nearest、hermite、gaussian、normals、mitchell、lanczos、min 等滤波器
- **多格式输出**: 支持 PNG、EXR、HDR、RGBM、PSD、DDS、KTX、KTX2 等格式
- **KTX2 纹理压缩**: 支持 UASTC 和 ETC1S 压缩（含法线专用模式）
- **线性色彩空间**: 可指定输入为线性图像，避免不必要的色彩空间转换
- **Alpha 通道控制**: 支持添加 Alpha 通道（`--add-alpha`）或剥离 Alpha（`--strip-alpha`）
- **灰度模式**: 通过 `--grayscale` 生成单通道图像
- **HTML 预览**: 通过 `--page` 生成 HTML 预览页面，方便直观查看各级 mipmap
- **层级数控制**: 通过 `--mip-levels` 指定生成的 mipmap 层数

### 使用方式

```bash
# 基本用法（printf 风格输出模式）
mipgen input.png "mip_%03d.png"

# 使用 Hermite 滤波器生成灰度 mipmap
mipgen -g --kernel=hermite grassland.png mip_%03d.png

# 输出为 KTX2 容器（含 UASTC 压缩）
mipgen -f ktx2 --compression=uastc grassland.png mips.ktx

# 输出为 KTX1 容器
mipgen -f ktx grassland.png mips.ktx

# 指定线性输入并限制层数
mipgen --linear --mip-levels=5 input.exr output_%02d.exr
```

## 依赖关系

- **imageio** - 图像读写库，提供多格式编解码支持（含 BasisEncoder 用于 KTX2 压缩）
- **image** - Filament 图像处理库（LinearImage、ImageSampler、ImageOps、ColorTransform）
- **utils** - 通用工具库（路径处理、命令行解析）
- **libpng** / **tinyexr** / **libz** / **stb** - 第三方图像处理库（许可证依赖）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现，包含命令行解析、滤波器选择、mipmap 逐级生成、多格式输出和 HTML 预览生成 |
| `CMakeLists.txt` | 构建配置，链接 imageio 库并设置许可证和安装规则 |
| `README.md` | 简要使用说明 |
