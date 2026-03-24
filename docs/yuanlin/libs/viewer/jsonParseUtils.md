# jsonParseUtils.h

## 文件概述
JSON 解析辅助头文件，封装 jsmn 轻量级 JSON 解析器的常用宏和工具函数，供 Settings.cpp 和 AutomationSpec.cpp 使用。

## 核心功能
- 提供 `CHECK_TOKTYPE`、`CHECK_KEY`、`STR` 等宏简化 token 操作
- 提供 `compare(token, json, str)` 用于比较 token 字符串
- 提供基本类型的 `parse` 重载（bool, int, float, float2, float3, float4 等）

## 依赖关系
- `jsmn.h` - 轻量级 JSON 解析库
