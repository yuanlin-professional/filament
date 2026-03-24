# cmgen.cpp

## 文件概述
CMGEN（CubeMap Generator）是一个命令行工具，用于从环境贴图生成球谐函数（SH）分解和 mipmap 层级。支持立方体贴图和等距柱状投影格式的输入图像，可输出预过滤后的 IBL（基于图像的光照）数据。

## 核心函数
- `main()` - 入口函数：解析参数、解码图像、生成 mipmap、计算球谐、执行预过滤
- `generateMipmaps()` - 通过 box filter 逐级降采样生成立方体贴图 mipmap 链
- `sphericalHarmonics()` - 计算球谐系数，支持辐照度预缩放和窗口化
- `iblRoughnessPrefilter()` - 按粗糙度等级对 IBL 进行预过滤（核心 PBR 功能）
- `iblDiffuseIrradiance()` - 计算漫反射辐照度立方体贴图
- `iblLutDfg()` - 生成 DFG（BRDF）查找表
- `extractCubemapFaces()` - 提取立方体贴图六个面并保存

## 关键实现逻辑
1. 自动检测输入图像格式（十字形立方体贴图 / 等距柱状投影），按宽高比判断
2. 粗糙度预过滤使用感知粗糙度到 LOD 的二次拟合映射（`lodToPerceptualRoughness`）
3. 支持多种输出格式：KTX1（RGB_10_11_11_REV）、PNG、HDR、EXR、PSD、DDS
4. `--deploy` 模式一键生成部署所需的全部资源（SH 文本、面贴图、预过滤数据）
5. 使用 `JobSystem` 进行多线程并行处理

## 依赖关系
- `ibl/` - IBL 计算库（CubemapIBL、CubemapSH、CubemapUtils）
- `imageio/` - 图像编解码
- `image/` - KTX 容器和颜色转换
- `utils/` - 作业系统、路径处理
- `ProgressUpdater.h` - 进度条显示
