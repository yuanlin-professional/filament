# ASTHelpers.h / ASTHelpers.cpp

## 文件概述
提供 glslang AST（抽象语法树）的遍历辅助工具，用于材质着色器的静态代码分析。是 `GLSLTools` 进行属性检测和语义分析的基础。

## 核心类/结构体/函数
- **`NodeToString`** - AST 节点调试打印遍历器
- **`getFunctionName()`** - 从 glslang 的混淆签名中提取函数名
- **`getFunctionBySignature()`** - 按完整签名查找函数定义节点
- **`getFunctionByNameOnly()`** - 仅按函数名查找（忽略参数签名）
- **`isFunctionCalled()`** - 递归检查某函数是否在调用链中被调用
- **`getFunctionParameters()`** - 获取函数参数列表（名称、类型、限定符）
- **`FunctionParameter`** - 函数参数描述结构体（IN/OUT/INOUT/CONST）

## 关键实现逻辑
- `FunctionDefinitionFinder` 遍历器通过匹配 `EOpFunction` 节点的名称查找函数定义
- `FunctionCallFinder` 递归跟踪函数调用链，检测目标函数是否被间接调用
- `getIndexDirectStructString()` 从二元索引节点提取结构体字段名

## 依赖关系
- glslang：`intermediate.h`、`localintermediate.h`
- `utils/Log.h`
