# zbloat.py

## 文件概述
Filament 二进制体积分析工具。分析共享库（.so）或静态库（.a）文件的符号大小分布，生成交互式 HTML 树形图（treemap），帮助识别代码膨胀的来源。支持从 APK/AAR/ZIP 中提取 DSO。

## 核心函数
- `main()` - 入口：选择目标文件 -> 扫描 resgen 资源 -> 运行 nm/objdump -> 生成 treemap HTML
- `extract_resgen()` - 从二进制文件中提取嵌入的 resgen 资源元数据（JSON 格式）
- `treeify_resgen()` - 将 resgen 资源数据转换为树形图 JSON 结构
- `choose_from_zip()` / `choose_from_dir()` - 交互式选择分析目标

## 关键实现逻辑
1. 使用 `nm -C -S` 获取符号信息，`objdump -h` 获取 section 信息
2. 调用 `evmar_bloat.py` 将 nm/objdump 输出转换为 treemap JSON
3. 识别 `__RESGEN__` 魔术标记提取嵌入的材质资源信息
4. 材质资源按 filamat chunk 结构解析，显示每个 chunk 的类型和大小
5. 最终将符号 JSON 和 section JSON 注入 HTML 模板

## 依赖关系
- `evmar_bloat.py` - 符号和 section JSON 生成器
- `template.html` - HTML 树形图模板
- 系统工具：`nm`、`objdump`
