# BlobDictionary.h / BlobDictionary.cpp

## 文件概述

`BlobDictionary` 实现了一个二进制 Blob（块数据）字典，用于对 SPIR-V 和 Metal Library 等二进制着色器数据进行去重。每个唯一的二进制块被分配一个索引，相同的二进制数据共享同一索引。

## 核心类

### `BlobDictionary`

- **`addBlob(const vector<uint8_t>& blob)`**: 添加一个二进制块。若已存在则返回现有索引，否则创建新条目并返回新索引。内部使用 `string_view` 作为二进制数据的键来进行快速查重。
- **`getBlobCount()`**: 返回唯一 Blob 的数量。
- **`isEmpty()`**: 判断字典是否为空。
- **`getBlob(size_t index)`**: 根据索引获取 Blob 数据（返回 `string_view`）。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mBlobIndices` | `unordered_map<string_view, size_t>` | Blob 数据到索引的映射 |
| `mBlobs` | `vector<unique_ptr<string>>` | 拥有所有唯一 Blob 的存储（使用 string 承载二进制数据） |

## 关键实现逻辑

利用 `std::string` 能存储含 null 字符的二进制数据这一特性，将 `vector<uint8_t>` 转换为 `string_view` 进行哈希查重。`unique_ptr<string>` 确保存储的数据在移动后仍保持稳定的地址，使 `string_view` 键始终有效。

## 依赖关系

- `<unordered_map>` / `<vector>` / `<string>` / `<string_view>` / `<memory>` -- 标准库
