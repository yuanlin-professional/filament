# GLDescriptorSetLayout.h

## 文件概述

定义描述符集布局（Descriptor Set Layout）的 OpenGL 特化结构体，用于描述一组描述符的类型和绑定位置。

## 核心类/结构体

### `GLDescriptorSetLayout`
继承自 `HwDescriptorSetLayout` 和 `DescriptorSetLayout`。

- 构造时将描述符按 `binding` 升序排列。
- 计算并存储 `maxDescriptorBinding`（最大绑定索引），用于在 `GLDescriptorSet` 中确定容器大小。

## 关键实现逻辑

- 使用 `std::sort` 和 `std::max_element` 对描述符列表进行排序和查找最大绑定值。

## 依赖关系

- `DriverBase.h` - 基类 `HwDescriptorSetLayout`
- `backend/DriverEnums.h` - `DescriptorSetLayout` 类型
