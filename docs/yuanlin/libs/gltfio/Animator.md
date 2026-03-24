# Animator

## 文件概述
Animator 是 gltfio 的动画引擎，负责根据 glTF 的 `animation` 和 `skin` 定义更新实体的变换矩阵和骨骼矩阵。支持关键帧动画（平移/旋转/缩放）、变形目标动画（morph weights）以及动画交叉淡入淡出。

## 核心类/结构体

- **`Animator`**: 公共接口类，提供 `applyAnimation()`、`updateBoneMatrices()`、`applyCrossFade()` 等方法。
- **`AnimatorImpl`**: 内部实现结构体，存储动画数据、骨骼矩阵缓冲、以及对 TransformManager/RenderableManager 的引用。
- **`Sampler`**: 存储关键帧时间值（红黑树）和源数据，支持 LINEAR、STEP、CUBIC 三种插值模式。
- **`Channel`**: 将采样器绑定到目标实体和变换类型（TRANSLATION/ROTATION/SCALE/WEIGHTS）。
- **`Animation`**: 包含动画名称、持续时间、采样器列表和通道列表。

## 关键实现逻辑

- **关键帧插值**: `applyAnimation()` 使用 `lower_bound` 在红黑树中查找时间点，计算插值因子 t，支持线性插值、阶跃和三次样条（cubicSpline）。
- **骨骼矩阵更新**: `updateBoneMatrices()` 遍历所有关节，计算 `inverseGlobalTransform * globalJointTransform * inverseBindMatrix`，然后通过 `RenderableManager::setBones()` 上传。
- **交叉淡入淡出**: 先暂存当前变换层次（stashCrossFade），应用前一个动画，再通过 slerp/mix 将两个动画的 TRS 分量进行 alpha 混合。
- **变形权重**: 直接通过 `RenderableManager::setMorphWeights()` 设置，支持三次样条插值。

## 依赖关系
- `gltfio/FilamentAsset.h`, `gltfio/FilamentInstance.h`, `gltfio/math.h`
- `FFilamentAsset.h`, `FFilamentInstance.h`, `FTrsTransformManager.h`, `downcast.h`
- `filament/VertexBuffer.h`, `filament/RenderableManager.h`, `filament/TransformManager.h`
- `math/mat4.h`, `math/quat.h`, `math/scalar.h`, `math/vec3.h`, `math/vec4.h`
