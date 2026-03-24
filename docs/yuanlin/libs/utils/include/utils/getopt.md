# getopt.h

## 文件概述
getopt 命令行参数解析的跨平台封装，统一系统和第三方 getopt 实现。

## 核心类/结构体/函数
- **utils::getopt** 命名空间包含:
  - `getopt()`, `getopt_long()`, `getopt_long_only()`: 命令行解析函数
  - `optarg`, `optind`, `opterr`, `optopt`: 全局状态变量
  - `no_argument`, `required_argument`, `optional_argument`: 参数类型常量

## 关键实现逻辑
- 如果系统有 getopt 则直接使用，否则使用 third_party/getopt
- 通过 C++ 命名空间避免与系统 getopt 的命名冲突

## 依赖关系
- 系统 `getopt.h` 或 `getopt/getopt.h` (第三方)
