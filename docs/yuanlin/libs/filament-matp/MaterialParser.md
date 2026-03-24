# MaterialParser

## 文件概述

`MaterialParser` 是材质文本解析的核心类，负责将 `.mat` 材质定义文件（或纯 JSON 格式的材质定义）解析为 `MaterialBuilder` 可用的配置。支持两种格式：Filament 自定义的标识符-块（identifier-block）格式，以及标准 JSON 格式。

**源文件**: `include/filament-matp/MaterialParser.h`, `src/MaterialParser.cpp`

## 核心类/结构体

### `MaterialParser`
- **`resolveIncludes(...)`**: 递归解析材质文件中的 `#include` 指令，返回展开后的完整材质文本
- **`processTemplateSubstitutions(...)`**: 处理 `${MACRO}` 形式的模板宏替换，两遍扫描算法（第一遍计算新大小，第二遍执行替换）
- **`parse(...)`**: 主入口。先尝试 JSON 解析，失败则用自定义格式解析。解析后应用 Config 中的配置到 MaterialBuilder
- **命令模式**: 使用 `mConfigProcessor` 和 `mConfigProcessorJSON` 两个函数指针映射表，将 `material`、`vertex`、`fragment`、`compute`、`tool` 等关键字映射到对应的处理方法

### 处理方法
- `processMaterial` -- 解析 material 块中的 JSON 属性，委托给 ParametersProcessor
- `processVertexShader` / `processFragmentShader` / `processComputeShader` -- 提取着色器源码并设置到 builder

## 关键实现逻辑

**自定义格式解析** (`parseMaterial`): 使用 `MaterialLexer` 将输入分词为 IDENTIFIER+BLOCK 对，验证格式后按标识符查找处理函数。

**JSON 格式解析** (`parseMaterialAsJSON`): 先用 `isValidJsonStart` 快速判断是否为 JSON，然后用 `JsonishLexer` + `JsonishParser` 解析为 AST，再逐键处理。

**模板替换**: 两遍扫描算法。第一遍查找所有 `${...}` 并计算替换后的总大小，第二遍分配新缓冲区并执行实际替换。

## 依赖关系

- `DirIncluder.h`, `Includes.h` -- include 解析
- `JsonishLexer.h`, `JsonishParser.h` -- JSON 词法和语法分析
- `MaterialLexer.h`, `MaterialLexeme.h` -- 自定义格式词法分析
- `ParametersProcessor.h` -- 材质属性处理
- `filamat/MaterialBuilder.h` -- 构建材质的目标对象
