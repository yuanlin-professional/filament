# RemoteServer.h / RemoteServer.cpp

## 文件概述
管理一个轻量级 WebSocket 服务器，用于在运行时从 Web 客户端接收模型数据和查看器设置，实现远程调试和控制。

## 核心类

### `RemoteServer`
- **构造函数**: `RemoteServer(port=8082)` - 启动 CivetServer WebSocket 服务
- `peekIncomingLabel()` - 检查是否有下载中的消息
- `acquireReceivedMessage()` - 从队列中取出最旧的消息
- `releaseReceivedMessage(message)` - 释放消息内存
- `sendMessage(settings)` / `sendMessage(label, buffer, bufsize)` - 向所有连接的客户端发送消息

### `ReceivedMessage`
消息结构体，包含标签（label）、数据缓冲区和唯一 ID。

### `MessageReceiver`（内部类）
CivetWebSocketHandler 实现，处理 WebSocket 帧的拼接。消息分两部分接收：第一部分是标签，第二部分是数据。

### `MessageSender`（内部类）
CivetServer 子类，遍历所有连接发送消息。

## 关键实现逻辑
- 使用固定大小为 4 的稀疏数组作为消息队列
- 新消息如果与队列中已有消息同标签，则替换旧消息
- 消息接收在非主线程执行，使用 mutex 保护共享状态
- 通过 messageUid 递增确保消息按接收顺序被消费

## 依赖关系
- `CivetServer.h` - HTTP/WebSocket 服务器
- `viewer/Settings.h` - JsonSerializer 序列化
