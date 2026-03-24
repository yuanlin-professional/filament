# ApiHandler.h / ApiHandler.cpp

## 文件概述
处理帧图调试器的 REST API 请求，提供帧图列表查询、单个帧图详情和状态轮询接口。

## 核心类

### `ApiHandler`（继承 CivetHandler）
- `handleGet(server, conn)` - 路由 GET 请求到对应的处理函数
- `updateFrameGraph(view_handle)` - 帧图更新时通知等待中的状态轮询

## REST API 端点
- `GET /api/framegraphs` - 返回所有视图的帧图信息 JSON 数组
- `GET /api/framegraph?fgid={id}` - 返回指定视图的帧图详情
- `GET /api/status` - 长轮询接口，阻塞等待帧图更新（超时 10 秒）
- `GET /api/status?firstTime` - 首次连接时立即返回 "0"

## 关键实现逻辑
- `/api/status` 使用条件变量实现挂起效果（hanging GET），模拟服务端推送
- `mCurrentStatus` 计数器确保每次更新都能被客户端感知
- `statusFrameGraphId` 存储最近更新的视图 ID（8 位十六进制字符串）
- 超时时返回 "1" 表示无操作，防止客户端永久阻塞

## 依赖关系
- `fgviewer/DebugServer.h` - 访问视图数据
- `fgviewer/JsonWriter.h` - JSON 序列化
- `CivetServer.h` - HTTP 处理
