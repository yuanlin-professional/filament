# DebugServer.h / DebugServer.cpp

## 文件概述
帧图调试服务器，管理一个 HTTP/WebSocket 服务器，接收和展示 Filament 引擎的帧图（FrameGraph）信息。提供 REST API 和静态文件服务。

## 核心类

### `DebugServer`
- **构造函数**: `DebugServer(port)` - 启动 CivetServer，注册文件处理器和 API 处理器
- `createView(name)` - 注册新的视图，返回 ViewHandle
- `destroyView(h)` - 删除视图
- `update(h, info)` - 更新视图的帧图信息，有变化时通知 API 处理器

### `FileRequestHandler`（内部类）
处理静态文件请求（index.html、app.js、api.js），支持从源码树或编译资源加载。

## 关键实现逻辑
- 默认监听端口 8085，使用 10 个线程
- 视图数据存储在 `unordered_map<ViewHandle, FrameGraphInfo>` 中，由 Mutex 保护
- `update()` 会比较新旧 FrameGraphInfo，仅在有变化时更新并通知客户端
- 静态资源可通过 `SERVE_FROM_SOURCE_TREE` 宏切换从文件系统或编译资源加载

## 依赖关系
- `CivetServer.h` - HTTP 服务器
- `fgviewer/FrameGraphInfo.h` - 帧图数据结构
- `ApiHandler.h`, `WebSocketHandler.h` - 请求处理器
