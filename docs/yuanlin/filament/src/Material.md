# Material.cpp

## 文件概述
Material.cpp 是 Filament 材质系统的公共 API 实现文件。它将 Material 类的公共方法委托给内部实现类 FMaterial，提供材质属性查询、实例创建和着色器编译等功能。

## 核心类/结构体/函数
- **Material** - 材质公共接口
  - `createInstance()` - 创建 MaterialInstance 实例
  - `getName()` - 获取材质名称
  - `getShading()` / `getInterpolation()` / `getBlendingMode()` - 着色模型和混合属性查询
  - `getVertexDomain()` / `getMaterialDomain()` - 顶点域和材质域
  - `getCullingMode()` / `getTransparencyMode()` - 裁剪和透明模式
  - `isColorWriteEnabled()` / `isDepthWriteEnabled()` / `isDepthCullingEnabled()` - 渲染状态查询
  - `getRefractionMode()` / `getRefractionType()` / `getReflectionMode()` - 折射/反射模式
  - `getParameterCount()` / `getParameters()` / `hasParameter()` - 参数查询
  - `compile()` - 异步编译指定变体的着色器程序
  - `getDefaultInstance()` - 获取默认 MaterialInstance
  - `getSource()` - 获取材质源代码

## 关键实现逻辑
所有方法通过 `downcast(this)` 委托给 FMaterial 实现。`compile()` 支持异步编译，接受优先级队列、变体过滤掩码和回调函数。

## 依赖关系
- `details/Material.h` - 内部实现类 FMaterial
- `filament/Material.h` / `filament/MaterialEnums.h` - 公共头文件

## 被引用关系
- 用户层代码通过 Material 接口操作材质
- MaterialInstance 通过 Material 获取 Uniform 接口块等信息
