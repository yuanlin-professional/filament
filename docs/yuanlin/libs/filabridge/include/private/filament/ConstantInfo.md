# ConstantInfo.h

## 文件概述
定义 `MaterialConstant` 结构体，用于描述材质中的特化常量（Specialization Constant）信息，包括常量名称、类型和默认值。

## 核心类/结构体/函数
- **`MaterialConstant`** - 材质常量描述结构体
  - `name` - 常量名称（CString）
  - `type` - 常量类型（ConstantType，来自后端驱动枚举）
  - `defaultValue` - 默认值（ConstantValue，来自后端驱动枚举）

## 关键实现逻辑
简单的 POD 结构体，通过构造函数初始化三个字段，使用移动语义传递字符串名称。

## 依赖关系
- `backend/DriverEnums.h`
- `utils/CString.h`
