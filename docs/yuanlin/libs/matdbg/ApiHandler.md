# ApiHandler

## 文件概述

`ApiHandler` 是 matdbg 调试服务器的 REST API 请求处理器，继承自 CivetWeb 的 `CivetHandler`。它实现了 Web 调试客户端与材质数据之间的所有 HTTP 交互。

**源文件**: `src/ApiHandler.h`, `src/ApiHandler.cpp`

## 核心类/结构体

### `ApiHandler`
支持的 API 端点：

- **`GET /api/matids`**: 返回所有材质 ID 的 JSON 数组
- **`GET /api/materials`**: 返回所有材质的完整信息 JSON 数组
- **`GET /api/material?matid={id}`**: 返回指定材质的详细信息
- **`GET /api/shader?matid={id}&type=[glsl|spirv|msl|wgsl]&[glindex|vkindex|metalindex|wgpuindex]={index}`**: 返回指定着色器变体的源码
- **`GET /api/active`**: 返回所有材质的活跃变体信息
- **`GET /api/status`**: 长轮询端点，阻塞直到有材质更新。使用条件变量实现 10 秒超时的挂起等待
- **`POST /api/edit`**: 接收着色器编辑请求，body 格式为 `[matid] [api] [shaderIndex] [shader source...]`

### 内部方法
- `handleGetApiShader` -- 处理着色器获取请求，根据类型参数选择对应的 ShaderExtractor 和格式转换
- `handleGetStatus` -- 实现长轮询
- `addMaterial` / `updateMaterial` -- 触发状态更新通知
- `getMaterialRecord` -- 从查询字符串提取 matid 并查找材质记录

## 关键实现逻辑

**长轮询机制**: `/api/status` 使用 `mStatusMutex` + `mStatusCondition` 实现。客户端请求会阻塞最多 10 秒，直到有新材质添加或编辑。超时返回 "1" 表示无操作。客户端持续轮询以模拟推送。

**着色器获取**: 支持多种格式。Vulkan 着色器可选择 SPIR-V 反汇编或反编译为 GLSL 输出。文本着色器（GLSL/MSL/WGSL）可通过 `SourceFormatter`（clang-format）进行格式化。

**着色器编辑**: POST body 通过 `mg_read` 分块读取，然后解析出 matid、api、shaderIndex 和着色器源码，委托给 `DebugServer::handleEditCommand`。

## 依赖关系

- `SourceFormatter.h` -- 着色器源码格式化
- `matdbg/DebugServer.h` -- 服务器主类
- `matdbg/JsonWriter.h`, `matdbg/ShaderExtractor.h`, `matdbg/ShaderInfo.h` -- 材质数据查询
- `CivetServer.h` -- HTTP 服务器框架
