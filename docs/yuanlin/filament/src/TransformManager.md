# TransformManager.cpp

## 文件概述
TransformManager.cpp 实现了变换管理器的公共 API。TransformManager 管理场景中所有实体的变换层级关系（父子关系）和变换矩阵。

## 核心类/结构体/函数
- **TransformManager** - 变换管理器公共接口
  - `hasComponent()` / `getInstance()` / `getEntity()` - 实体-组件查询
  - `create()` - 创建变换组件（支持 mat4f 和 mat4 精度，以及无参数版本）
  - `destroy()` - 销毁变换组件
  - `setTransform()` / `getTransform()` - 设置/获取局部变换（支持 mat4f 和 mat4）
  - `getTransformAccurate()` - 获取高精度局部变换
  - `getWorldTransform()` / `getWorldTransformAccurate()` - 获取世界变换
  - `setParent()` / `getParent()` - 父子关系管理
  - `getChildCount()` / `getChildren()` - 子节点查询
  - `getChildrenBegin()` / `getChildrenEnd()` - 子节点迭代器
  - `openLocalTransformTransaction()` / `commitLocalTransformTransaction()` - 变换事务（批量更新优化）
  - `setAccurateTranslationsEnabled()` / `isAccurateTranslationsEnabled()` - 高精度平移开关

## 关键实现逻辑
通过 `downcast` 委托给内部 `FTransformManager`。支持 float（mat4f）和 double（mat4）两种精度的变换矩阵。事务机制允许批量设置局部变换后再统一计算世界变换。

## 依赖关系
- `components/TransformManager.h` - 内部实现

## 被引用关系
- 用户层代码通过 TransformManager 管理实体层级和变换
- 引擎内部在每帧更新世界变换矩阵
