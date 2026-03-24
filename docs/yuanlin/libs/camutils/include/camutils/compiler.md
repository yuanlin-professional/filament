# compiler.h - camutils 编译器宏

## 文件概述

为 camutils 库定义符号可见性宏，用于控制动态库的符号导出。

## 核心定义

- `CAMUTILS_PUBLIC` -- 标记为公开可见符号。支持 `visibility` 属性的编译器上展开为 `__attribute__((visibility("default")))`；不支持时为空宏。

## 依赖关系

无外部依赖。
