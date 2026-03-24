# ShaderMinifier.h / ShaderMinifier.cpp

## 文件概述
简单的着色器代码压缩器，用于缩减生成的 GLSL 和 MSL 着色器体积。主要针对生成代码（如 uniform 结构体）进行优化。

## 核心类/结构体/函数
- **`ShaderMinifier`** - 着色器压缩器
  - `removeWhitespace()` - 移除行首空白和空行，可选合并单独的花括号行
  - `renameStructFields()` - 重命名 uniform 结构体字段为短名称（a, b, c, ...）

## 关键实现逻辑
- `renameStructFields()` 解析 `uniform TypeName { ... } instanceName;` 结构体定义，为每个字段生成短名（a-z，超过后 aa-az...），然后在整个着色器中替换所有引用
- 替换时按键名长度从长到短排序，避免 "fogColor" 被 "fog" 的替换规则误匹配
- 使用简单的状态机解析器（OUTSIDE -> STRUCT_OPEN -> STRUCT_DEFN）

## 依赖关系
- `utils/Log.h`
