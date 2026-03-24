# WebSocketHandler.h / WebSocketHandler.cpp

## 文件概述
帧图调试器的 WebSocket 连接管理器，维护活跃连接列表并支持向所有连接广播消息。

## 核心类

### `WebSocketHandler`（继承 CivetWebSocketHandler）
- `handleConnection(server, conn)` - 新连接建立时记录日志
- `handleReadyState(server, conn)` - 连接就绪后加入活跃连接集合
- `handleData(server, conn, bits, data, data_len)` - 接收数据（当前不处理客户端消息）
- `handleClose(server, conn)` - 连接关闭时从集合中移除
- `broadcast(data, len)` - 向所有活跃连接发送二进制消息

## 关键实现逻辑
- 使用 `unordered_set<mg_connection*>` 存储活跃连接
- 所有连接操作通过 Mutex 保护线程安全
- 广播使用 `MG_WEBSOCKET_OPCODE_BINARY` 操作码

## 依赖关系
- `CivetServer.h` - WebSocket 处理基类
- `fgviewer/DebugServer.h` - 服务器引用
- `utils/Mutex.h` - 线程同步
