# MetalArgumentBuffer.h / MetalArgumentBuffer.cpp

## 文件概述
生成 Metal Shading Language（MSL）中的 Argument Buffer 结构体定义，并替换 spirv-cross 自动生成的参数缓冲区。解决了旧 Apple 设备要求参数缓冲区结构精确匹配的兼容性问题。

## 核心类/结构体/函数
- **`MetalArgumentBuffer`** - MSL 参数缓冲区定义生成器
  - `Builder` - 构建器，支持 `name()`、`texture()`、`sampler()`、`buffer()` 方法
  - `getMsl()` - 获取生成的 MSL 结构体文本
  - `replaceInShader()` - 在 MSL 源码中查找并替换目标参数缓冲区定义
  - `destroy()` - 释放堆分配的实例

## 关键实现逻辑
- 构造时按 `[[id(n)]]` 索引排序参数，检查索引唯一性
- `TextureArgument::write()` 根据 SamplerType 和 SamplerFormat 组合生成正确的 MSL 纹理类型名（如 `texture2d<float>`、`depth2d_array<float>` 等）
- `replaceInShader()` 通过文本解析定位 `struct targetName { ... }` 结构并替换

## 依赖关系
- `backend/DriverEnums.h`
