# 资产文件 (`assets/`)

## 模块概述

`assets/` 目录包含 Filament 项目使用的共享资产文件，包括 3D 模型、环境贴图、字体和参考图像。这些资产主要供示例应用 (`samples/`) 和测试系统 (`test/`) 使用，在构建时由 CMake 自动复制到构建输出目录。

## 目录结构

```
assets/
├── environments/               # 环境贴图 (用于 IBL 光照)
│   ├── debug/
│   │   └── debug.png           # 调试用环境贴图
│   └── white_furnace/
│       └── white_furnace.exr   # 白炉测试环境 (能量守恒验证)
├── fonts/                      # 字体文件
│   ├── Roboto-Medium.ttf       # Roboto 字体
│   └── Roboto-License.txt      # 字体许可证
├── models/                     # 3D 模型集合
│   ├── monkey/                 # Suzanne 猴头 (含 PBR 纹理)
│   │   ├── monkey.obj          # OBJ 网格
│   │   ├── color.png           # 颜色贴图
│   │   ├── roughness.png       # 粗糙度贴图
│   │   ├── metallic.png        # 金属度贴图
│   │   ├── ao.png              # 环境光遮蔽贴图
│   │   └── normal.png          # 法线贴图
│   ├── camera/                 # 相机模型 (.obj + .blend)
│   ├── cloth/                  # 布料模型
│   ├── cornell_box/            # 康奈尔盒 (光照测试经典场景)
│   ├── cube/                   # 立方体
│   ├── material_sphere/        # 材质展示球体
│   ├── sphere/                 # 球体
│   ├── torus/                  # 环面体
│   ├── shapes/                 # 基础几何形状
│   ├── prism/                  # 棱柱
│   ├── rounded_cube/           # 圆角立方体
│   ├── thinglass/              # 薄玻璃模型
│   └── translucent/            # 半透明模型
└── reference/                  # 参考图像
    ├── plastic_sphere/         # 塑料球体参考渲染
    └── README                  # 参考图像说明
```

## 核心功能

- **PBR 纹理集**: `monkey/` 提供完整的 PBR 纹理工作流示例 (颜色、粗糙度、金属度、AO、法线)
- **IBL 环境**: `white_furnace` 用于验证 PBR 着色的能量守恒性
- **测试模型集**: 13 种几何模型覆盖从基础形状到特殊材质 (玻璃、半透明) 的测试需求
- **字体资源**: Roboto 字体供 UI 渲染使用
- **参考基准**: `reference/` 目录存放渲染效果的参考基准图像

## 依赖关系

| 被依赖模块 | 说明 |
|-----------|------|
| `samples/` | 示例应用在构建时引用模型、纹理和环境贴图 |
| `test/` | 测试系统使用资产进行渲染验证和差异比较 |
| `samples/CMakeLists.txt` | 通过 `file(COPY)` 将 assets 复制到构建目录 |
| `tools/cmgen` | 处理环境贴图生成 IBL 数据 |
| `tools/mipgen` | 为纹理生成 Mip 级别 |
| `tools/filamesh` | 将 OBJ 网格转换为 Filamesh 格式 |

## 关键文件说明

| 文件 | 说明 |
|-----|------|
| `models/monkey/` | Filament 标志性示例模型 Suzanne，包含完整 PBR 纹理集，被 `suzanne` 示例大量使用 |
| `environments/white_furnace/white_furnace.exr` | 白炉测试环境贴图 (HDR 格式)，用于验证渲染器的能量守恒 |
| `environments/debug/debug.png` | 调试用环境贴图，便于在开发时快速识别反射方向 |
| `fonts/Roboto-Medium.ttf` | Google Roboto 字体，用于示例和查看器中的文字渲染 |
| `models/cornell_box/` | 经典的康奈尔盒场景，常用于全局光照和光照算法的标准化测试 |
| `reference/` | 存放已知正确的渲染参考图像，用于渲染质量回归测试 |
