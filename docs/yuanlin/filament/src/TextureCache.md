# TextureCache.h / TextureCache.cpp

## 文件概述
TextureCache 实现了纹理和渲染目标的缓存池，通过复用相同规格的 GPU 纹理减少内存分配和释放开销。它主要用于 FrameGraph 系统中临时纹理的管理。

## 核心类/结构体/函数
- **TextureCacheInterface** - 纹理缓存接口（用于单元测试 mock）
  - `createTexture()` / `destroyTexture()` - 纹理创建和销毁
  - `createRenderTarget()` / `destroyRenderTarget()` - 渲染目标创建和销毁
- **TextureCache** - 纹理缓存实现
  - `gc()` - 垃圾回收，淘汰超龄缓存项
  - `terminate()` - 释放所有缓存资源
- **TextureCacheDisposer** - 纹理回收器，跟踪使用中的纹理并在归还时放回缓存
- **TextureKey** - 缓存键（target、levels、format、samples、width、height、depth、usage、swizzle）
- **TextureCachePayload** - 缓存值（handle、age、size）
- **AssociativeContainer** - 内部关联容器（基于 `std::vector<pair>` 实现，适合小量数据）

## 关键实现逻辑
- **基于属性的键**：纹理名称不参与哈希和相等比较，仅用于调试
- **LRU 淘汰**：每帧调用 `gc()` 增加全局 age，超过 `mCacheMaxAge` 的纹理被销毁
- **AssociativeContainer** 使用 `std::vector` 而非 `std::multimap`，在小数据量（<1000 项）下性能更优
- TextureCacheDisposer 的 checkout/checkin 机制追踪纹理从缓存取出和归还的过程

## 依赖关系
- `filament/Engine.h` - 引擎配置
- `backend/DriverApi.h` - 驱动 API
- `utils/Hash.h` / `utils/StaticString.h` - 工具类

## 被引用关系
- `FEngine` - 持有 TextureCache 实例
- FrameGraph 系统 - 临时纹理的分配和回收
