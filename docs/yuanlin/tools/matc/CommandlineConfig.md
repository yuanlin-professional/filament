# CommandlineConfig.h / CommandlineConfig.cpp

## 文件概述
MATC 的命令行配置解析模块。定义了文件系统 I/O 类和完整的命令行参数解析逻辑，支持目标平台、API、优化级别、变体过滤等丰富选项。

## 核心类
- `CommandlineConfig` - 继承 `matp::Config`，解析命令行参数
- `FilesystemInput` / `FilesystemOutput` - 文件系统读写的 I/O 适配器
- `toPIISafeString()` - 生成不含个人身份信息（PII）的参数字符串

## 关键实现逻辑
1. 支持选项别名：`-Os` 映射为 `-S`（优化大小），`-O0` 映射为 `-g`（禁用优化）
2. 变体过滤器解析逗号分隔的标志名称（如 `directionalLighting,fog,ssr`）
3. 预处理器定义支持 `KEY=VALUE` 和仅 `KEY`（默认值为 1）两种格式
4. `--template` 选项用于材质文件中的 `${MACRO}` 替换
5. PII 安全字符串过滤 `--output` 等可能包含用户路径的选项

## 依赖关系
- `filament-matp/Config.h` - 配置基类
- `filament/MaterialEnums.h` - 材质枚举定义
- `backend/DriverEnums.h` - 后端枚举定义
