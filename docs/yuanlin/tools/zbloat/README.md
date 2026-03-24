# zbloat - 二进制体积分析与可视化工具

## 模块名称和概述

`zbloat` 是一个基于 Python 的二进制体积分析工具，专门用于分析使用 Filament 的 Android 应用的二进制组成。它能够解析 `.so`、`.a` 文件、文件夹或 zip 归档（APK/AAR），生成自包含的交互式 HTML 报告（treemap 可视化），帮助开发者了解各模块和 Filament 材质在最终二进制中的体积占比。

## 目录结构

```
tools/zbloat/
    zbloat.py           # 主程序 Python 脚本
    zbloat.sh           # Docker 辅助启动脚本
    evmar_bloat.py      # Evan Martin 的 bloat 分析库
    template.html       # 交互式 treemap 报告 HTML 模板
    Dockerfile          # Docker 构建文件（用于 Linux 环境）
    README.md           # 原始英文文档
```

## 核心功能

- **多格式输入**: 支持 `.so` 共享库、`.a` 静态库、文件夹、APK/AAR zip 归档
- **符号分析**: 通过 `nm` 和 `objdump` 工具解析二进制符号表
- **Filament 材质分析**: 当构建使用 `resgen --json` 时，可显示各材质的体积
- **gzip 压缩尺寸**: 报告 Filament 材质的 gzip 压缩后大小
- **交互式报告**: 生成自包含的 HTML treemap 可视化报告
- **Docker 支持**: 提供 Dockerfile 和辅助脚本，解决跨平台 `nm` 工具差异
- **自动文件发现**: 对于 zip/文件夹输入，交互式选择要分析的文件

### 使用方式

```bash
# 分析 Android AAR
./tools/zbloat/zbloat.py ./android/filament-android
open index.html

# 分析特定 .so 文件
./tools/zbloat/zbloat.py libfilament.so

# 使用 Docker（跨平台兼容）
./tools/zbloat/zbloat.sh ./android/filament-android
```

### 前提条件

- **可执行文件必须包含调试信息** - 分析目标需要在编译时启用调试符号
- **系统工具**: 需要 Python、`nm`、`objdump`
- **Docker**（可选）: 用于 Linux 环境下运行

## 依赖关系

- **Python** - 运行时依赖（Python 3）
- **nm** - 二进制符号分析系统工具
- **objdump** - 二进制反汇编系统工具
- **Docker**（可选）- 用于跨平台 Linux 环境

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `zbloat.py` | 主脚本，实现输入解析、符号提取、数据聚合和 HTML 报告生成 |
| `evmar_bloat.py` | Evan Martin 开发的 bloat 分析核心库，提供符号解析和分类 |
| `template.html` | 交互式 treemap 可视化报告的 HTML/JavaScript 模板 |
| `zbloat.sh` | Docker 辅助脚本，自动构建容器并在其中执行分析 |
| `Dockerfile` | Docker 镜像定义，安装 Linux 下的分析工具和 Python 依赖 |
