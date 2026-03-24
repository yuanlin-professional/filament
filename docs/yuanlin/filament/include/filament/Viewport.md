# Viewport.h

## 文件概述

定义视口（Viewport），以像素坐标描述渲染区域的位置和大小。

## 核心类/结构体

- **`Viewport`** - 视口类，继承自 `backend::Viewport`。由左下角坐标和宽高定义。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Viewport()` | 默认构造，原点处零尺寸视口 |
| `Viewport(left, bottom, width, height)` | 构造指定位置和尺寸的视口 |
| `empty()` | 判断视口面积是否为零 |
| `operator==` / `operator!=` | 比较两个视口是否相等 |

## 成员变量（继承自 backend::Viewport）

- `left` - 左边界 x 坐标（像素）
- `bottom` - 下边界 y 坐标（像素）
- `width` - 宽度（像素）
- `height` - 高度（像素）

## 依赖关系

- `backend/DriverEnums.h`
- `utils/compiler.h`

## 使用示例

```cpp
Viewport vp(0, 0, 1920, 1080);
view->setViewport(vp);
if (!vp.empty()) {
    // 视口有效
}
```
