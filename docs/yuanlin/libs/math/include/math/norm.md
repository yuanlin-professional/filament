# norm.h - 归一化整数打包/解包

## 文件概述

提供浮点值与归一化整数之间的打包（pack）和解包（unpack）函数。用于顶点属性压缩、纹理数据编码等 GPU 数据传输场景。

## 核心函数

### 16 位归一化
- `packUnorm16(float v)` -- 将 [0, 1] 浮点数打包为 uint16（0~65535）
- `packUnorm16(float4 v)` -- 向量版本，返回 ushort4
- `unpackUnorm16(uint16_t v)` -- 解包 uint16 到 [0, 1] 浮点
- `unpackUnorm16(ushort4 v)` -- 向量版本
- `packSnorm16(float v)` -- 将 [-1, 1] 浮点打包为 int16（-32767~32767）
- `packSnorm16(float2/float4)` -- 向量版本
- `unpackSnorm16(int16_t v)` -- 解包 int16 到 [-1, 1] 浮点
- `unpackSnorm16(short4 v)` -- 向量版本

### 8 位归一化
- `packUnorm8(float v)` -- 打包到 uint8（0~255）
- `packUnorm8(float4 v)` -- 向量版本，返回 ubyte4
- `unpackUnorm8(uint8_t v)` -- 解包到 [0, 1]
- `unpackUnorm8(ubyte4 v)` -- 向量版本
- `packSnorm8(float v)` -- 打包到 int8（-127~127）
- `packSnorm8(byte4 v)` -- 向量版本
- `unpackSnorm8(int8_t v)` -- 解包到 [-1, 1]
- `unpackSnorm8(byte4 v)` -- 向量版本

## 关键实现逻辑

打包使用 `round(clamp(v) * maxVal)` 确保正确舍入；解包使用 `v / maxVal` 并对有符号版本再次 clamp 以处理 -128 的对称性问题。

## 依赖关系

- `<math/scalar.h>` -- clamp 函数
- `<math/vec4.h>` -- 向量类型
