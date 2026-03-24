# LightManager.cpp

## 文件概述
LightManager.cpp 是 Filament 光源管理器的公共 API 实现文件。它作为外部接口层，将所有公共方法调用通过 `downcast` 委托给内部实现类 `FLightManager`（位于 `components/LightManager.h`）。

## 核心类/结构体/函数
- **LightManager** - 光源管理器公共接口，提供对光源组件的 CRUD 操作
  - `hasComponent()` / `getInstance()` / `getEntity()` - 实体-组件查询
  - `setPosition()` / `setDirection()` / `setColor()` / `setIntensity()` - 光源属性设置
  - `setSpotLightCone()` - 聚光灯锥角控制
  - `setSunAngularRadius()` / `setSunHaloSize()` / `setSunHaloFalloff()` - 太阳光特有参数
  - `setShadowOptions()` / `setShadowCaster()` - 阴影配置
  - `setLightChannel()` - 光照通道控制
  - `setFalloff()` - 光照衰减半径
  - `setIntensityCandela()` - 使用坎德拉单位设置光照强度

## 关键实现逻辑
所有方法均采用 Pimpl（指针到实现）模式，通过 `downcast(this)` 转换为内部 `FLightManager` 类型后委托调用。光照强度支持两种单位：流明/勒克斯（`LUMEN_LUX`）和坎德拉（`CANDELA`）。太阳角半径在获取时会从弧度转换为角度（`RAD_TO_DEG`）。

## 依赖关系
- `components/LightManager.h` - 内部实现类 FLightManager

## 被引用关系
- 被用户层代码调用以创建和管理场景中的光源
- Engine 内部通过 FLightManager 管理光源组件的生命周期
