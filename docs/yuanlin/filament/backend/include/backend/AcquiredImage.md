# AcquiredImage.h

## 文件概述

定义了 `AcquiredImage` 结构体，用于封装 ACQUIRED 类型外部图像流的处理状态。该结构体是一个轻量级 POD（Plain Old Data）类型，便于在队列中传递和移动外部图像相关的状态信息。

## 核心结构体

### AcquiredImage

```cpp
struct AcquiredImage {
    void* image = nullptr;                    // 外部图像指针
    backend::StreamCallback callback = nullptr; // 释放回调函数
    void* userData = nullptr;                  // 传递给回调的用户数据
    CallbackHandler* handler = nullptr;        // 回调调度处理器
};
```

- **image**: 指向外部图像资源的指针
- **callback**: 图像使用完毕后的释放回调（`StreamCallback` 类型）
- **userData**: 与回调关联的用户自定义数据
- **handler**: 用于调度回调执行的 `CallbackHandler` 指针

## 主要用途

在外部纹理流（如 Android 的 ACQUIRED 流）中，需要将图像获取、使用和释放的状态打包传递。`AcquiredImage` 将这些状态封装在一起，使得外部图像可以在不同组件之间排队和传递。

## 依赖关系

- `backend/DriverEnums.h` - 提供 `StreamCallback` 类型定义
- `math/mat3.h` - 数学库（mat3 矩阵）
