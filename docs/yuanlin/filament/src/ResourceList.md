# ResourceList.h / ResourceList.cpp

## 文件概述
ResourceList 是 Filament 引擎内部的资源跟踪容器，基于 robin_set 实现，用于跟踪引擎创建的所有资源对象（如纹理、缓冲区、材质等），便于在引擎销毁时检测资源泄漏。

## 核心类/结构体/函数
- **ResourceListBase** - 类型擦除的基类（操作 void*），减少模板代码膨胀
  - `insert()` / `remove()` / `find()` / `clear()` - 增删查操作
  - `empty()` / `size()` - 容量查询
  - `forEach()` - 遍历所有资源（函数指针回调）
  - `begin()` / `end()` - 迭代器
- **ResourceList\<T\>** - 类型安全的模板包装器
  - `forEach(F func)` - 接受闭包的遍历（内部转换为函数指针以减少代码膨胀）

## 关键实现逻辑
- **代码膨胀控制**：将通用逻辑放在 ResourceListBase 中操作 void*，ResourceList\<T\> 仅做类型转换
- **泄漏检测**：Debug 模式下析构时检查容器是否为空，非空则记录泄漏日志
- `forEach()` 的闭包通过 `+[]` 运算符转换为函数指针，避免每次实例化生成不同的迭代代码
- 使用 `tsl::robin_set<void*>` 作为底层容器，提供 O(1) 的插入和查找

## 依赖关系
- `tsl/robin_set.h` - Robin Hood 哈希集合
- `utils/Logger.h` - 日志系统

## 被引用关系
- `FEngine` - 使用多个 ResourceList 实例跟踪各类资源（Texture、Material、Buffer 等）
