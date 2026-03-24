# evmar_bloat.py

## 文件概述
基于 Evan Martin 的 bloat 分析脚本修改。解析 `nm` 和 `objdump` 的输出，将符号和 section 数据转换为 JSON 格式的树形结构，用于在 treemap 中可视化。

## 核心函数
- `parse_nm()` - 解析 nm 输出，生成 (符号名, 类型, 大小, 源文件路径) 元组
- `parse_cpp_name()` - 解析 C++ 符号名，按命名空间/模板分割为层级列表
- `treeify_syms()` - 将符号列表组织为嵌套字典树
- `jsonify_tree()` - 将字典树转换为 D3.js treemap 兼容的 JSON
- `parse_objdump()` - 解析 objdump -h 输出，提取 section 名称和大小
- `dump_nm()` / `dump_sections()` - 入口命令：输出符号/section JSON

## 关键实现逻辑
1. C++ 符号解析处理命名空间 `::`、模板 `<>`、运算符重载等复杂情况
2. `SuffixCleanup` 处理 GCC 特有的后缀（`.part`、`.constprop`、`.isra`），清除后再 demangle
3. 符号类型映射：t=code, d=data, r=read-only data, b=bss, w=weak symbol
4. 无命名空间的符号按源文件路径分组，或归入 `[ungrouped]`
5. 支持 `syms`（符号 treemap）、`sections`（section treemap）和 `dump`（按大小排序打印）三种模式

## 依赖关系
- 系统工具：`c++filt`（可选，用于符号反修饰）
