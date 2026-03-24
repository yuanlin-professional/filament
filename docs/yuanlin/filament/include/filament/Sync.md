# Sync.h

## 文件概述

定义同步对象 Sync，用于获取平台特定的外部同步句柄，使 Filament 渲染管线能与外部系统进行同步。

## 核心类/结构体

- **`Sync`** - 同步对象，继承自 `FilamentAPI`。

## 主要 API

| 方法 | 说明 |
|------|------|
| `getExternalHandle(handler, callback, userData)` | 异步获取平台特定的外部同步句柄 |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/CallbackHandler.h`, `backend/DriverEnums.h`, `backend/Platform.h`
