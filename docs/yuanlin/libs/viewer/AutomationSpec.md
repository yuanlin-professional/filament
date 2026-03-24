# AutomationSpec.h / AutomationSpec.cpp

## 文件概述
从 JSON 规格生成不可变的 Settings 排列组合列表，用于自动化测试。支持基础设置和排列组合（笛卡尔积）两种模式。

## 核心类

### `AutomationSpec`
- `generate(jsonSpec, size)` - 解析 JSON 规格，生成 Settings 对象列表
- `generateDefaultTestCases()` - 使用内置默认 JSON 生成测试用例
- `size()` - 返回测试用例总数
- `get(index, out)` - 获取指定索引的 Settings 副本
- `getName(index)` - 获取指定索引所属组的名称

## 关键实现逻辑
- JSON 格式：顶层数组，每个元素包含 `name`、`base`（基础设置）和可选的 `permute`（排列变量）
- `base` 中的 dotted key（如 `view.bloom.enabled`）会被展开为嵌套 JSON 对象
- `permute` 对所有变量做笛卡尔积，每个组合都基于 base 设置叠加修改
- 使用 jsmn 轻量级 JSON 解析器
- 默认测试序列包含：关闭后处理、相机选项、以及 MSAA/TAA/FXAA/SSAO/SSR/Bloom/DoF/GuardBand 的全排列

## 依赖关系
- `viewer/Settings.h` - Settings 数据结构和 JsonSerializer
- `jsonParseUtils.h` - jsmn JSON 解析辅助宏
