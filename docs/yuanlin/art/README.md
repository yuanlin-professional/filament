# 美术资源 (`art/`)

## 模块概述

`art/` 目录存放 Filament 项目的美术设计源文件，包括项目 Logo、架构图表和材质属性参考图。这些文件主要用于项目文档和品牌展示，使用 Affinity Designer、OmniGraffle、draw.io 等专业设计工具创建。

## 目录结构

```
art/
├── logo/                           # 项目 Logo
│   ├── Filament_Logo.svg           # 矢量格式 Logo
│   ├── Filament_Logo.png           # PNG 位图 Logo
│   └── Filament_Logo.afdesign      # Affinity Designer 源文件
├── diagrams/                       # 架构与技术图表
│   ├── branching.drawio            # Git 分支策略图 (draw.io)
│   ├── Lighting.graffle/           # 光照管线图 (OmniGraffle)
│   │   ├── data.plist              # 图表数据
│   │   └── image1~5.png            # 嵌入图片
│   ├── lispsm.graffle              # LiSPSM 阴影映射算法图
│   ├── NDC.png                     # 归一化设备坐标图
│   ├── ImageUploadReadPixels.png   # 图像上传/像素读取流程图
│   └── RenderingReadPixels.png     # 渲染/像素读取流程图
└── material_chart/                 # 材质属性参考
    └── Material Properties.afdesign  # 材质属性对照表 (Affinity Designer)
```

## 核心功能

- **品牌标识**: 提供 SVG、PNG 和源文件三种格式的 Filament Logo，用于文档、网站和演示
- **架构图表**: 记录光照管线、阴影算法、像素读取流程等技术架构的可视化图表
- **材质参考**: 材质属性对照表为美术人员提供材质参数设置的参考指南
- **Git 流程**: 分支策略图 (`branching.drawio`) 记录项目的 Git 分支管理规范

## 依赖关系

| 被依赖模块 | 说明 |
|-----------|------|
| `docs/` | 文档引用 Logo 图片和架构图表 |
| 项目 README | 引用 Filament Logo |
| 项目网站 | 使用 Logo 和图表资源 |

> 注: 此目录为纯设计资源，不参与代码构建流程，也不依赖其他代码模块。

## 关键文件说明

| 文件 | 说明 |
|-----|------|
| `logo/Filament_Logo.svg` | Filament 官方矢量 Logo，适用于网页和高分辨率场景 |
| `logo/Filament_Logo.afdesign` | Logo 的 Affinity Designer 可编辑源文件 |
| `diagrams/Lighting.graffle/` | 使用 OmniGraffle 绘制的光照管线架构图，详细展示光照计算流程 |
| `diagrams/lispsm.graffle` | LiSPSM (Light Space Perspective Shadow Maps) 阴影映射算法的图解 |
| `diagrams/branching.drawio` | 使用 draw.io 绘制的项目 Git 分支管理策略图 |
| `material_chart/Material Properties.afdesign` | 材质属性完整对照表，列出所有可用材质参数及推荐取值范围 |
| `diagrams/NDC.png` | 归一化设备坐标 (NDC) 空间说明图，辅助理解坐标变换 |
