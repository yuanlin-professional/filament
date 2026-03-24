# main.cpp

## 文件概述
DIFFIMG 是一个图像比较工具，用于比较参考图像与候选图像之间的差异。支持 JSON 配置文件指定比较参数、可选遮罩图像，以及输出差异可视化图像。主要用于渲染回归测试。

## 核心函数
- `main()` - 入口：解析参数、加载图像、执行比较、输出 JSON 结果
- `loadImage()` - 加载图像，自动选择 ImageIO 或 ImageIO-Lite 解码器
- `saveDiffImage()` - 保存差异可视化图像
- `readFile()` - 读取 JSON 配置文件

## 关键实现逻辑
1. 支持双解码器策略：先尝试主解码器，失败后回退到轻量解码器（反之亦然，TIFF 格式优先使用 Lite）
2. 通过 `imagediff::compare()` 执行实际比较，支持 LEAF 模式
3. 可选遮罩图像用于排除特定区域不参与比较
4. 比较结果序列化为 JSON 输出到标准输出

## 依赖关系
- `imagediff/ImageDiff` - 图像比较核心库
- `imageio/` 和 `imageio-lite/` - 双图像编解码库
- `image/LinearImage` - 线性图像存储
