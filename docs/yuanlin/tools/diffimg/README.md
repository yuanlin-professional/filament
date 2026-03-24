# diffimg - 图像差异比较工具

## 模块名称和概述

`diffimg` 是一个命令行图像比较工具，用于对比参考图像和候选图像之间的差异。它基于 Filament 的 `imagediff` 库实现像素级容差比较，支持 JSON 配置驱动的阈值控制、遮罩图像和差异图像输出，主要用于渲染回归测试。

## 目录结构

```
tools/diffimg/
    CMakeLists.txt      # CMake 构建配置
    main.cpp            # 主程序源码
    README.md           # 原始英文文档
```

## 核心功能

- **像素级图像比较**: 对比两张图像的每个像素，计算差异值
- **JSON 配置**: 通过 JSON 配置文件定义比较模式和容差阈值
- **遮罩支持**: 可指定灰度遮罩图像，仅比较遮罩非零区域
- **差异图像输出**: 可生成可视化差异图像，直观展示两图的不同之处
- **多格式支持**: 支持 PNG、HDR、EXR、TIFF 等图像格式（通过 imageio 和 imageio-lite）
- **JSON 结果输出**: 比较结果以 JSON 格式输出到 stdout

### 使用方式

```bash
# 基本比较
diffimg reference.png candidate.png

# 使用配置文件和遮罩
diffimg --config config.json --mask mask.png reference.png candidate.png

# 输出差异图像
diffimg --diff diff.png reference.png candidate.png
```

### 输出格式

```json
{
  "status": 0,
  "passed": true,
  "failingPixelCount": 0,
  "maxDiffFound": [0.0, 0.0, 0.0, 0.0]
}
```

状态码: `0` = 通过, `1` = 尺寸不匹配, `2` = 像素差异超出容差。

## 依赖关系

- **imagediff** - Filament 图像差异比较核心库
- **imageio** - 主要图像编解码库（PNG、HDR、EXR）
- **imageio-lite** - 轻量图像编解码库（TIFF 支持）
- **image** - LinearImage 图像容器
- **utils** - 通用工具库（路径处理、命令行解析）

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `main.cpp` | 完整的工具实现：命令行解析、图像加载、差异计算和结果序列化 |
| `CMakeLists.txt` | 构建配置，链接 imagediff/imageio/imageio-lite/image/utils 五个库 |
