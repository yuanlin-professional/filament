# MapManipulator.h - 地图模式相机操控器

## 文件概述

实现 `Manipulator` 的 MAP 模式子类。提供类似 Google Maps 的俯视地图交互：鼠标拖拽平移、滚轮缩放。相机始终朝向地平面。

## 核心类

### `MapManipulator<FLOAT>`
继承自 `Manipulator<FLOAT>`

**初始化**：
- 根据 `mapExtent` 和 `fovDegrees` 计算初始相机距离：`distance = halfExtent / tan(fov/2)`
- 相机沿地面法线方向（`groundPlane.xyz`）放置

**拖拽平移**（grabBegin/Update/End）：
- `grabBegin` -- 射线投射到地面获取抓取点 `mGrabScene`，同时记录远平面投影点
- `grabUpdate` -- 利用近远平面比例关系计算世界空间平移量：`translation = (grabFar - currentFar) * ulen / vlen`
- `moveWithConstraints` -- 应用平移（预留边界约束接口）

**滚轮缩放**（scroll）：
- 向抓取点方向推拉相机
- 缩放速度受 `zoomSpeed` 控制
- 当距离表面过近时阻止继续缩放

**书签**：
- `getCurrentBookmark` -- 通过射线投射到地面计算 extent 和 center
- `getHomeBookmark` -- 根据 mapExtent 计算初始视野
- `jumpToBookmark` -- 从 extent 和 center 恢复相机位置

## 关键实现逻辑

平移算法利用了透视投射的特性：远平面上的偏移量乘以近/远距离比值，得到地面上的等效偏移。这确保了平移速度与缩放级别成正比（远处拖得多、近处拖得少），符合用户对地图平移的直觉。

书签的 center 通过将地面投影点分解到 u/v 坐标系（由 upVector 和 groundPlane 法线叉积得到）来记录。

## 依赖关系

- `<camutils/Manipulator.h>`
- `<math/vec3.h>`
