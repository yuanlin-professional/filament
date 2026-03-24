# Camera.cpp

## 文件概述

`Camera` 类的公共 API 实现文件。提供相机投影设置（透视、正交、自定义）、模型矩阵、曝光参数、对焦距离等功能的接口实现。所有方法通过 `downcast` 委托给内部实现类 `FCamera`。

## 核心类/结构体/函数

### 投影设置

- **`setProjection(fovInDegrees, aspect, near, far, direction)`** -- 通过 FOV 和纵横比设置透视投影。
- **`setLensProjection(focalLength, aspect, near, far)`** -- 通过焦距（毫米）设置透视投影。
- **`setCustomProjection(projection, near, far)`** -- 设置自定义投影矩阵。
- **`setCustomEyeProjection(...)`** -- 为立体渲染设置多眼投影矩阵。

### 变换操作

- **`setModelMatrix(mat4)`** -- 设置相机的模型矩阵。
- **`lookAt(eye, center, up)`** -- 通过目标点设置相机朝向。
- **`getViewMatrix()`** / **`getModelMatrix()`** -- 获取视图/模型矩阵。

### 曝光控制

- **`setExposure(aperture, shutterSpeed, sensitivity)`** -- 设置相机曝光参数。
- **`setFocusDistance(distance)`** -- 设置对焦距离。

### 静态工具方法

- **`inverseProjection(mat4)`** -- 计算投影矩阵的逆矩阵。
- **`computeEffectiveFocalLength(...)`** / **`computeEffectiveFov(...)`** -- 计算有效焦距和视场角。
- **`projection(...)`** -- 静态方法，根据参数生成投影矩阵。

## 关键实现逻辑

所有公共方法通过 `downcast` 模式委托给 `FCamera` 内部实现。前置条件检查（如 `near > 0 && far > near`）在公共层完成，通过 `FILAMENT_CHECK_PRECONDITION` 宏进行验证。

## 依赖关系

- `details/Camera.h` -- 内部实现类 `FCamera`
- `filament/Camera.h` -- 公共头文件
- `math/mat4.h`、`math/vec2-4.h` -- 数学库
- `utils/Panic.h` -- 前置条件检查

## 被引用关系

作为 Filament 核心公共 API，被所有需要相机操作的用户代码使用，包括视图设置、渲染和光照计算。
