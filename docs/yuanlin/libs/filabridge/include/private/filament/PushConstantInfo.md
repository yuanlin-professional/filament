# PushConstantInfo.h

## 文件概述
定义 `MaterialPushConstant` 结构体，用于描述材质中使用的推送常量（Push Constant）信息。推送常量是 Vulkan 中用于快速传递少量数据到着色器的机制。

## 核心类/结构体/函数
- **`MaterialPushConstant`** - 推送常量描述结构体
  - `name` - 常量名称（CString）
  - `type` - 常量类型（ConstantType）
  - `stage` - 所属着色器阶段（ShaderStage：顶点/片段）

## 关键实现逻辑
简单数据结构，记录推送常量的名称、数据类型和作用的着色器阶段。

## 依赖关系
- `backend/DriverEnums.h`
- `utils/CString.h`
