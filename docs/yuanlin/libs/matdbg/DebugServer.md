# DebugServer

## 文件概述

`DebugServer` 是材质调试器的服务端核心，管理一个内嵌的 HTTP 服务器（基于 CivetWeb）。它接收来自 Filament 引擎或 matinfo 工具的材质包，通过 Web 界面提供材质查看和着色器实时编辑功能。

**源文件**: `include/matdbg/DebugServer.h`, `src/DebugServer.cpp`

## 核心类/结构体

### `MaterialRecord`
材质记录结构：`userdata`（引擎侧对象指针）、`package`/`packageSize`（材质包副本）、`name`、`key`（唯一标识）、`activeVariants`（当前活跃的着色器变体列表）。

### `DbgShaderModel` 枚举
`MOBILE`(1)、`DESKTOP`(2)、`MATINFO`(10) -- 标识调试上下文的着色器模型。

### `DebugServer`
- **构造函数**: 接收后端类型、着色器语言、偏好着色器模型和端口号。创建 CivetWeb 服务器（10 线程），注册 `FileRequestHandler`（静态资源）和 `ApiHandler`（REST API）
- **`addMaterial(name, data, size, userdata)`**: 注册材质。使用 MurmurHash 生成唯一 key，复制材质包数据以防止原始数据被释放
- **`removeMaterial(key)`**: 移除已注册的材质
- **`setEditCallback` / `setQueryCallback`**: 设置回调，分别用于通知引擎着色器编辑和查询活跃变体
- **`handleEditCommand(...)`**: 处理着色器编辑请求。解析材质包、使用 `ShaderReplacer` 替换指定着色器变体的源码、更新材质包副本、触发编辑回调

### `FileRequestHandler`
CivetWeb 请求处理器，服务静态 Web 资源（index.html、app.js、api.js）。支持从源码树直接加载（开发模式）或从 resgen 嵌入资源加载（发布模式）。

## 关键实现逻辑

**材质 Key 生成**: 将数据指针和 userdata 指针组合为 16 字节数据，用 MurmurHash（种子 42）生成 32 位哈希值。允许内容相同但 FMaterial 实例不同的材质共存。

**线程安全**: 所有对 `mMaterialRecords` 的访问都通过 `mMaterialRecordsMutex` 保护。

## 依赖关系

- `CivetServer.h` -- HTTP 服务器
- `ApiHandler.h` -- REST API 处理
- `matdbg/JsonWriter.h`, `matdbg/ShaderExtractor.h`, `matdbg/ShaderInfo.h`, `matdbg/ShaderReplacer.h` -- 材质查询和编辑
- `filaflat/ChunkContainer.h` -- 材质包解析
- `sca/GLSLTools.h` -- GLSL 工具初始化
