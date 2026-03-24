# Camera.h

## 文件概述

定义相机组件，用于控制场景的观察视角、投影方式和曝光参数，支持透视/正交投影及立体渲染。

## 核心类/结构体

- **`Camera`** - 相机组件，继承自 `FilamentAPI`。控制投影矩阵、视图矩阵和曝光。
- **`Camera::Projection`** - 枚举：`PERSPECTIVE`（透视）/ `ORTHO`（正交）。
- **`Camera::Fov`** - 枚举：`VERTICAL` / `HORIZONTAL`，指定视场角方向。

## 主要 API

| 方法 | 说明 |
|------|------|
| `setProjection(fov, aspect, near, far)` | 通过视场角设置透视投影 |
| `setLensProjection(focalLength, aspect, near, far)` | 通过焦距设置投影 |
| `setCustomProjection(projection, near, far)` | 设置自定义投影矩阵 |
| `setCustomEyeProjection(...)` | 为立体渲染设置每只眼的投影 |
| `setScaling(scaling)` / `setShift(shift)` | 缩放/偏移投影矩阵 |
| `setModelMatrix(mat)` | 设置相机的模型矩阵（位置和朝向） |
| `lookAt(eye, center, up)` | 便捷方法设置相机朝向 |
| `setExposure(aperture, shutterSpeed, sensitivity)` | 设置曝光参数 |
| `setFocusDistance(distance)` | 设置对焦距离（用于景深效果） |
| `getProjectionMatrix()` / `getViewMatrix()` | 获取投影/视图矩阵 |
| `getPosition()` / `getForwardVector()` | 获取相机位置/朝向 |
| `inverseProjection(p)` | 静态方法，计算投影矩阵的逆 |

## 依赖关系

- `filament/FilamentAPI.h`
- `utils/compiler.h`
- `math/mathfwd.h`, `math/vec2.h`, `math/vec4.h`, `math/mat4.h`

## 使用示例

```cpp
auto* camera = engine->createCamera(entity);
camera->setProjection(45.0, 16.0/9.0, 0.1, 100.0);
camera->lookAt({0, 1.6, 1}, {0, 0, 0});
camera->setExposure(16.0f, 1.0f/125.0f, 100.0f);
```
