# Sync.cpp

## 文件概述
Sync.cpp 实现了 GPU 同步对象的公共 API。Sync 用于获取 GPU 同步栅栏的外部句柄，可用于跨进程或跨 API 的 GPU 同步。

## 核心类/结构体/函数
- **Sync** - 同步对象公共接口
  - `getExternalHandle()` - 异步获取外部同步句柄，通过回调返回结果

## 关键实现逻辑
通过 `downcast` 委托给内部 `FSync`。外部句柄的获取是异步的，需要提供 CallbackHandler 和回调函数。

## 依赖关系
- `details/Sync.h` - 内部实现
- `backend/Platform.h` - 平台层

## 被引用关系
- 用户层代码在需要 GPU 同步原语（如 Vulkan Fence 导出）时使用
