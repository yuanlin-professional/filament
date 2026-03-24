# FixedCapacityVector.h / FixedCapacityVectorBase.cpp

## 文件概述
运行时固定容量的向量容器，几乎是 std::vector 的替代品，但容量在初始化时确定且不自动扩容。

## 核心类/结构体/函数
- **FixedCapacityVector<T, A, CapacityCheck>**: 固定容量向量
  - `with_capacity(n)`: 静态工厂方法创建指定容量的空向量
  - 提供完整的 STL 容器接口: push_back, emplace_back, insert, erase, resize 等
  - `reserve()` / `shrink_to_fit()`: 可手动调整容量
- **FixedCapacityVectorBase**: 基类，提供容量检查失败处理

## 关键实现逻辑
- 对 trivially constructible 类型跳过默认构造（与 std::vector 不同）
- 容量检查在 Debug 模式强制启用，Release 模式可选
- 使用 compressed_pair 优化 capacity + allocator 的存储
- 容量溢出时调用 FILAMENT_CHECK_PRECONDITION 报告错误

## 依赖关系
- `utils/Slice.h`, `utils/compiler.h`, `utils/compressed_pair.h`, `utils/Panic.h`
