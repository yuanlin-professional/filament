# emitters/java.go

## 文件概述
Java 代码生成器。负责将类型定义数据库中的结构体和枚举转换为 Java 源码，直接编辑已有的 Java 文件（在标记行之后追加生成代码）。

## 核心函数
- `EditJava()` - 读取并编辑 Java 文件，在标记行后插入生成的结构体和枚举代码
- `createJavaCodeGenerator()` - 创建基于模板的 Java 代码生成函数

## 关键实现逻辑
1. 读取目标 Java 文件至标记行 `kCodelineMarker`，保留标记行之前的内容
2. 通过模板函数将 C++ 类型映射为 Java 类型（如 `float3` -> `float[]`，`bool` -> `boolean`）
3. 将 C++ 默认值转换为 Java 字面量（处理 `nullptr`、浮点数后缀、向量花括号等）
4. 支持 `java_float` 和 `java_flatten` 等 emitter 标志控制生成行为
5. 自动生成 `@Nullable`、`@NonNull`、`@Size` 等注解

## 依赖关系
- `beamsplitter/database` - 类型定义数据
- `emitters/java.template` - Java 代码模板文件
