# emitters/javascript.go

## 文件概述
JavaScript 和 TypeScript 代码生成器。负责生成 Emscripten 绑定代码（`.cpp`）、JavaScript 扩展（`.js`）以及编辑 TypeScript 类型声明文件（`.d.ts`）。

## 核心函数
- `EmitJavaScript()` - 生成三个输出文件：`jsbindings_generated.cpp`、`jsenums_generated.cpp`、`extensions_generated.js`
- `EditTypeScript()` - 编辑 `filament.d.ts` 文件，在标记行后追加类型声明
- `createJsCodeGenerator()` - 创建基于模板的 JS/TS 代码生成函数

## 关键实现逻辑
1. C++ 作用域分隔符 `::` 转换为 JS 中的 `$`（Emscripten 约定），枚举值用 `.` 分隔
2. TypeScript 类型映射：`float`/`uint8_t` -> `number`，`bool` -> `boolean`，`math::floatN` -> `floatN`
3. 枚举值前缀处理：添加 `Filament.` 命名空间前缀和 `$` 限定符
4. 使用 `kCodelineMarker` 常量标识生成代码的插入位置

## 依赖关系
- `beamsplitter/database` - 类型定义数据
- `emitters/javascript.template` - JS/TS 代码模板文件
