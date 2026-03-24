# parse/ast.go

## 文件概述
定义 beamsplitter 解析器使用的抽象语法树（AST）节点类型。这些节点表示 C++ 头文件中的命名空间、类、结构体、枚举、字段、方法等语法结构。

## 核心类型
- `Node` - 空接口，所有 AST 节点的基类型
- `RootNode` - 根节点，包含一个顶层命名空间子节点
- `NamespaceNode` - 命名空间节点，包含名称和子节点列表
- `ClassNode` / `StructNode` - 类/结构体节点，包含成员列表和模板标志
- `EnumNode` - 枚举节点，包含枚举值列表及其行号（用于关联文档注释）
- `FieldNode` - 字段节点，包含类型、名称、默认值（RHS）和数组长度
- `MethodNode` - 方法节点，包含返回类型、参数和函数体
- `UsingNode` / `AccessSpecifierNode` - 类型别名和访问修饰符节点

## 依赖关系
- 无外部依赖，为纯数据结构定义
