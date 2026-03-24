# iOS 平台支持 (`ios/`)

## 模块概述

`ios/` 目录提供 Filament 渲染引擎在 iOS 平台上的集成支持。包含 CocoaPods 发布规范和多个 iOS 示例应用，使 iOS 开发者能够通过 CocoaPods 或手动集成的方式在 iOS 应用中使用 Filament 进行高质量 PBR 渲染。

## 目录结构

```
ios/
├── CocoaPods/
│   ├── Filament.podspec         # CocoaPods 包规范 (v1.70.0)
│   └── README.md                # CocoaPods 集成说明
└── samples/
    ├── generate-samples.sh      # 示例工程生成脚本
    ├── app-template.yml         # Xcode 工程模板配置
    ├── hello-triangle/          # 基础三角形渲染示例
    ├── hello-pbr/               # PBR 材质渲染示例
    ├── hello-gltf/              # glTF 模型加载示例
    ├── hello-ar/                # AR 增强现实示例
    ├── gltf-viewer/             # glTF 模型查看器
    ├── transparent-rendering/   # 透明渲染示例
    ├── backend-test/            # 渲染后端测试应用
    ├── HelloCocoaPods/          # CocoaPods 集成示例
    └── README.md                # 示例总览说明
```

## 核心功能

- **CocoaPods 分发**: 通过 `Filament.podspec` 提供模块化的 Pod 分发，支持按需引入子模块
- **多子模块架构**: 包含 filament、filamat、gltfio_core、camutils、ktxreader、viewer 等 12 个子规范
- **静态库链接**: 所有子模块以 `.a` 静态库形式提供，位于 `lib/universal/` 目录
- **Metal 后端**: iOS 平台默认使用 Apple Metal 图形 API 作为渲染后端
- **示例应用**: 提供从基础三角形到 AR 场景的 8 个完整示例工程
- **Xcode 项目生成**: 通过 `xcodegen` 模板自动生成 Xcode 工程文件

## 依赖关系

| 依赖模块 | 说明 |
|---------|------|
| `filament/` | 核心渲染引擎 (libfilament.a) |
| `libs/filamat/` | 材质编译器 (libfilamat.a) |
| `libs/gltfio/` | glTF 加载器 (libgltfio_core.a) |
| `libs/camutils/` | 相机控制工具 (libcamutils.a) |
| `libs/ktxreader/` | KTX 纹理读取 (libktxreader.a) |
| `libs/viewer/` | 查看器组件 (libviewer.a) |
| `libs/math/` | 数学库 (头文件) |
| `libs/utils/` | 工具库 (libutils.a) |
| iOS SDK | 最低支持 iOS 11.0 |

## 关键文件说明

| 文件 | 说明 |
|-----|------|
| `CocoaPods/Filament.podspec` | CocoaPods 包的完整规范，定义 12 个子模块及其依赖关系，指定预编译静态库的下载地址 |
| `samples/generate-samples.sh` | 基于 `xcodegen` 工具和 YAML 模板自动生成 iOS 示例的 Xcode 工程 |
| `samples/app-template.yml` | Xcode 工程生成的 YAML 模板，定义通用构建设置 |
| `samples/backend-test/` | 用于验证 Metal 后端正确性的测试应用，包含 FilamentView (Objective-C++) |
| `samples/hello-ar/` | 演示 Filament 与 ARKit 结合的增强现实示例 |
| `samples/HelloCocoaPods/` | 演示如何通过 CocoaPods 集成 Filament 的最小化示例 |
