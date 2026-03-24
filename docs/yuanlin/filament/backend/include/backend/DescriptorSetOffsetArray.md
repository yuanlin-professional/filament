# DescriptorSetOffsetArray.h

## 文件概述

定义了 `DescriptorSetOffsetArray` 类，用于在命令流中存储描述符集的动态偏移量数组。该数组的内存从命令流中分配，确保与命令缓冲区的生命周期一致。

## 核心类

### DescriptorSetOffsetArray

一个轻量级动态偏移量数组，其内存从 `CommandStream` 中分配。

**类型定义**:
- `value_type` = `uint32_t`
- `size_type` = `uint32_t`

**特性**:
- 不可复制，仅可移动
- 内存由命令流管理，无需手动释放
- 支持迭代器和下标访问

## 主要 API

| 方法 | 说明 |
|------|------|
| `DescriptorSetOffsetArray(size, driver)` | 从命令流分配指定大小的数组，初始化为 0 |
| `DescriptorSetOffsetArray(list, driver)` | 从初始化列表构造 |
| `empty()` | 检查数组是否为空 |
| `data()` | 返回底层数据指针 |
| `operator[](n)` | 下标访问 |
| `clear()` | 清空数组（置空指针） |

## 辅助函数

- `allocateFromCommandStream(driver, size, alignment)` - 从命令流分配内存

## 依赖关系

- `backend/DriverApiForward.h` - `DriverApi` 前向声明
