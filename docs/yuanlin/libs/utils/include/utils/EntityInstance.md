# EntityInstance.h

## 文件概述
组件管理器中实体实例的句柄，作为组件数组的索引使用。

## 核心类/结构体/函数
- **EntityInstanceBase**: 基类，包含 uint32_t 类型的实例索引
- **EntityInstance<T, EDIT>**: 模板实例类
  - 支持有效性检查、比较、排序、迭代
  - EDIT 模板参数区分可编辑和只读实例
  - 可隐式转换为 uint32_t 索引

## 关键实现逻辑
- EDIT 实例可转换为非 EDIT 实例（只读），反向不可
- 值为 0 表示无效实例

## 依赖关系
- `utils/compiler.h`
