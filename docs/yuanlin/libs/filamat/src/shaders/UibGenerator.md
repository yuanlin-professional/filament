# UibGenerator.h / UibGenerator.cpp

## 文件概述
Uniform 接口块（UIB）生成器，为引擎内部使用的所有 UBO 生成 `BufferInterfaceBlock` 实例。这些 UBO 包括帧 uniform、对象 uniform、灯光、阴影、骨骼等。

## 核心类/结构体/函数
- **`UibGenerator`** - 静态工具类
  - `Ubo` 枚举 - 标识 9 种 UBO：FrameUniforms、ObjectUniforms、BonesUniforms、MorphingUniforms、LightsUniforms、ShadowUniforms、FroxelRecordUniforms、FroxelsUniforms、MaterialParams
  - `get(Ubo)` - 获取指定 UBO 的 BufferInterfaceBlock
  - `getBinding(Ubo)` - 获取 UBO 的描述符集和绑定点
  - 便捷方法：`getPerViewUib()`、`getPerRenderableUib()` 等

## 关键实现逻辑
每种 UBO 通过 `BufferInterfaceBlock::Builder` 定义其字段列表（名称、类型、精度、数组大小等），布局严格遵循 std140 规范以匹配 `UibStructs.h` 中的 C 结构体。

## 依赖关系
- `backend/DriverEnums.h`
- `utils/BitmaskEnum.h`
