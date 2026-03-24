# DescriptorSetLayout

## 文件概述

`DescriptorSetLayout.h` / `DescriptorSetLayout.cpp` 实现了描述符集布局的封装，定义了描述符集中各个绑定点的类型和属性。

## 核心类/结构体

- **`DescriptorSetLayout`**: 描述符集布局管理类（不可复制，仅可移动）。
  - `mDescriptorSetLayoutHandle`: 后端布局句柄
  - `mSamplers`: 64 位位集，标记哪些绑定点是采样器
  - `mUniformBuffers`: 64 位位集，标记哪些绑定点是 UBO
  - `mMaxDescriptorBinding`: 最大绑定索引
  - `mDescriptorTypes`: 按绑定索引存储的描述符类型向量

## 关键实现逻辑

- **构造**: 遍历 `backend::DescriptorSetLayout` 中的所有描述符，计算最大绑定索引，使用 `DescriptorSetLayoutDescriptor::isSampler` 区分采样器和 UBO，记录每个绑定的精确类型。
- **后端句柄创建**: 通过 `HwDescriptorSetLayoutFactory` 工厂创建，支持布局的共享和复用。
- **查询接口**: `isValid(binding)` 检查绑定点是否存在，`isSampler(binding)` 判断类型，`getValidDescriptors()` 返回所有有效描述符的位集并集。
- **最大绑定索引**: 断言不超过 `bitset64::BIT_COUNT`（64），确保位集索引安全。

## 依赖关系

- `HwDescriptorSetLayoutFactory.h` - 布局工厂
- `backend/DriverEnums.h` - `DescriptorType`、`DescriptorSetLayout` 等后端类型
- `backend/Handle.h` - `DescriptorSetLayoutHandle`
- `utils/bitset.h`, `utils/FixedCapacityVector.h` - 位集和固定容量向量
