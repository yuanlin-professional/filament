# CountDownLatch.h / CountDownLatch.cpp

## 文件概述
倒计时门闩同步原语，阻塞线程直到门闩被触发指定次数。

## 核心类/结构体/函数
- **CountDownLatch**: 倒计时门闩类
  - `await()`: 阻塞直到计数器归零
  - `latch()`: 减少计数器，归零时唤醒所有等待线程
  - `reset(count)`: 重置计数器
  - `getCount()`: 获取已触发次数

## 关键实现逻辑
- 使用 Mutex + Condition 实现线程安全
- reset(0) 会立即唤醒所有等待线程

## 依赖关系
- `utils/Condition.h`, `utils/Mutex.h`
