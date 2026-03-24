# main.cpp (mipgen)

## 文件概述
MIPGEN 生成图像的 mipmap 链。支持多种输出格式（PNG、KTX1、KTX2、HDR、EXR 等）和多种过滤核（Lanczos、Gaussian、Mitchell 等），可选 BasisU 纹理压缩。

## 核心函数
- `main()` - 入口：读取图像 -> 生成 mipmap -> 输出文件
- `handleArguments()` - 解析丰富的命令行选项

## 关键实现逻辑
1. mipmap 生成使用 `image::generateMipmaps()`，支持法线贴图专用的 Gaussian 法线过滤器
2. KTX1 输出：根据通道数自动选择 GL 格式（R8/RGB8/RGBA8/SRGB8 等）
3. KTX2 输出：支持 UASTC/ETC1S 压缩（包括法线贴图专用压缩模式）
4. 法线贴图模式下在生成 mipmap 前将颜色转为向量，输出时再转回
5. 支持 `--add-alpha`/`--strip-alpha` 通道操作和 `--grayscale` 灰度模式
6. 可选生成 HTML 预览页面（`--page`）

## 依赖关系
- `image/` - 图像操作、KTX 容器、颜色转换、采样器
- `imageio/` - 编解码、BasisU 编码器
