# AsyncJobQueue.h / AsyncJobQueue.cpp

## 文件概述
简单的异步作业队列，管理单个工作线程按序执行提交的任务。

## 核心类/结构体/函数
- **AsyncJobQueue**: 异步作业队列类
  - `push(Job&&)`: 添加作业到队列
  - `cancelAll()`: 取消所有待处理作业
  - `drainAndExit()`: 等待所有作业完成后退出线程
  - `isValid()`: 检查工作线程是否可用

## 关键实现逻辑
- 工作线程在循环中通过条件变量等待新作业，每次唤醒批量取出所有作业执行
- `drainAndExit` 设置退出标志后，线程会先执行完当前所有作业再退出
- Emscripten 平台上所有操作为空实现

## 依赖关系
- `utils/JobSystem.h`, `utils/Invocable.h`, `utils/Mutex.h`, `utils/Condition.h`
