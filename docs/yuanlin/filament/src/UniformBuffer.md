# UniformBuffer.h / UniformBuffer.cpp

## 文件概述
UniformBuffer 是 Filament 的通用 Uniform 缓冲区实现，用于存储和管理着色器 Uniform 数据。它支持各种数值类型，遵循 std140 布局规则，并提供脏标记追踪以优化 GPU 上传。

## 核心类/结构体/函数
- **UniformBuffer** - 通用 Uniform 缓冲区
  - 构造函数 `UniformBuffer(size_t size)` - 创建指定字节大小的缓冲区
  - `setUniform<T>(offset, value)` - 按偏移设置单个 Uniform（支持 float、int32、uint32、vec2-4、mat4f 等）
  - `setUniformArray<T>(offset, begin, count)` - 设置 Uniform 数组
  - `getUniform<T>(offset)` - 获取 Uniform 值
  - `setUniforms(rhs)` - 从另一个 UniformBuffer 复制所有数据
  - `setUniformUntyped<Size>(offset, v)` - 按字节大小设置（类型擦除）
  - `invalidateUniforms()` / `invalidate()` - 标记脏区域
  - `isDirty()` / `clean()` - 脏标记查询/清除
  - `toBufferDescriptor()` - 拷贝数据到驱动缓冲区
  - `getBuffer()` / `getSize()` - 原始数据访问

## 关键实现逻辑
- **小缓冲区优化**：96 字节以内使用栈上 `mStorage` 数组，超出则堆分配
- **脏标记优化**：`setUniformUntyped` 先通过 `invalidateNeeded` 比较新旧值（memcmp），仅在值变化时标记脏
- **mat3f 特殊处理**：按 std140 规则转换为 3 个 float3 的数组（每个对齐到 16 字节）
- **数组布局**：`setUniformArrayUntyped` 按 `stride = (Size + 0xF) & ~0xF` 的步长写入，满足 std140 对齐要求
- 模板显式实例化覆盖 4、8、12、16、64 字节大小

## 依赖关系
- `private/backend/DriverApi.h` - 驱动 API
- `backend/BufferDescriptor.h` - 缓冲区描述符
- `math/` - 数学类型

## 被引用关系
- `FMaterialInstance` - 持有 UniformBuffer 存储材质参数
- 各种描述符集类 - 通过 UniformBuffer 管理 Uniform 数据
