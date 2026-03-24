# Unflattener

## 文件概述

`Unflattener` 是一个安全的二进制数据顺序读取器。filaflat 库中所有的数据反序列化操作都必须通过它进行，因为它在每次读取前都会执行边界检查，防止越界读取。

**源文件**: `include/filaflat/Unflattener.h`, `src/Unflattener.cpp`

## 核心类/结构体

### `Unflattener`
- **构造函数**: `Unflattener(const uint8_t* src, const uint8_t* end)` -- 接收数据起止指针
- **`read(T* out)`**: 模板方法，读取整数类型。使用逐字节小端序组装，避免对齐问题。读取前检查是否会越界
- **`read(float* f)`**: 通过 reinterpret_cast 委托给 uint32 版本的 read
- **`read(Variant* v)`**: 读取 Variant 的 key 字段
- **`read(CString* s)`**: 读取以 null 结尾的字符串，扫描到 '\0' 后构造 CString
- **`read(const char** blob, size_t* size)`**: 读取带长度前缀（uint64）的二进制 Blob
- **`read(const char** s)`**: 读取以 null 结尾的 C 字符串，返回指向内部数据的裸指针
- **`hasData()`**: 检查游标是否未到末尾
- **`willOverflow(size)`**: 检查读取指定字节数是否会越界
- **`skipAlignmentPadding()`**: 跳过填充字节使游标对齐到 8 字节边界
- **`getCursor()` / `setCursor()`**: 获取/设置当前读取位置（setCursor 有边界保护）

## 关键实现逻辑

整数读取采用逐字节小端序组装（`cursor[i] << (8*i)`），确保在任何平台上都能正确解析。所有 `read` 方法返回 bool 表示是否成功，调用者必须检查返回值。`setCursor` 做了安全检查：若新游标超出有效范围，则设置为末尾位置。

## 依赖关系

- `utils/compiler.h` -- UTILS_PUBLIC、UTILS_UNLIKELY 宏
- `utils/debug.h` -- assert_invariant
- `utils/CString.h` -- CString 字符串类型
- `private/filament/Variant.h` -- Variant 类型
