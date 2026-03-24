# ProgressUpdater.h / ProgressUpdater.cpp

## 文件概述
终端进度条显示组件。使用独立渲染线程和条件变量实现非阻塞的多进度条更新，通过 ANSI 转义序列控制光标位置。

## 核心类
- `ProgressUpdater` - 管理多个进度条的显示和更新
  - `start()` - 启动渲染线程，隐藏光标
  - `stop()` - 停止渲染线程，显示光标
  - `update(index, value)` - 线程安全地更新指定进度条的进度值

## 关键实现逻辑
1. 渲染线程通过条件变量等待更新通知，避免忙等待
2. 使用互斥锁保护更新队列，每次唤醒时批量处理所有累积更新
3. 通过 ANSI 转义码 `\033[nF` 向上移动光标，实现多行原地更新
4. SIGINT 信号处理确保异常退出时恢复光标可见性
5. 跨平台支持：Windows 使用 `_write`，Unix 使用 `write` 进行信号安全输出

## 依赖关系
- 标准库：`<thread>`、`<mutex>`、`<condition_variable>`
