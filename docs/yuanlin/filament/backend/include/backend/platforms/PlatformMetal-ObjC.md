# PlatformMetal-ObjC.h

## 文件概述

定义了 Metal 后端中 Objective-C 对象的 C++ 包装结构体。由于 Metal API 基于 Objective-C，此文件提供了跨语言兼容的桥接类型，使 C++ 代码能够引用 Metal 对象。

## 核心结构体

### MetalDevice

```cpp
struct MetalDevice {
    id<MTLDevice> device;  // Metal 设备对象
};
```

### MetalCommandQueue

```cpp
struct MetalCommandQueue {
    id<MTLCommandQueue> commandQueue;  // Metal 命令队列
};
```

### MetalCommandBuffer

```cpp
struct MetalCommandBuffer {
    id<MTLCommandBuffer> commandBuffer;  // Metal 命令缓冲区
};
```

## 设计说明

这些包装结构体的存在是为了让 `PlatformMetal.h` 能够同时兼容 Objective-C 和纯 C++ 编译。在 Objective-C 环境中包含此头文件可获得完整的 `id<MTL*>` 类型信息。

## 依赖关系

- `Metal/Metal.h` - Apple Metal 框架
