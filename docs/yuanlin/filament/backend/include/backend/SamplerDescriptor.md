# SamplerDescriptor.h

## 文件概述

定义了 `SamplerDescriptor` 结构体，用于将纹理句柄与采样器参数绑定在一起。这是描述符集中采样器绑定的基本单元。

## 核心结构体

### SamplerDescriptor

```cpp
struct SamplerDescriptor {
    Handle<HwTexture> t;   // 纹理句柄
    SamplerParams s{};     // 采样器参数（过滤模式、环绕模式等）
};
```

- **t**: 引用的纹理资源句柄
- **s**: 采样器配置参数，包含过滤模式、纹理环绕模式、各向异性、比较模式等

## 用途

在设置描述符集时，`SamplerDescriptor` 将纹理资源和采样方式作为一个整体绑定到着色器的采样器槽位上。

## 依赖关系

- `backend/DriverEnums.h` - `SamplerParams` 定义
- `backend/Handle.h` - `Handle<HwTexture>` 类型
- `utils/compiler.h` - `UTILS_PUBLIC` 宏
