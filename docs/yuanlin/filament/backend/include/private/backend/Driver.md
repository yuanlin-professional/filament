# Driver.h

## 文件概述

定义了 `Driver` 抽象基类，是所有图形后端实现的核心接口。包含了所有 GPU 操作的方法声明（通过宏展开自 `DriverAPI.inc`），分为同步调用和异步调用两类。

## 核心类

### Driver

图形后端驱动的抽象基类。

**调试级别常量**:
- `FILAMENT_DEBUG_COMMANDS_NONE` = 0x0 - 关闭
- `FILAMENT_DEBUG_COMMANDS_ENABLE` = 0x1 - 启用
- `FILAMENT_DEBUG_COMMANDS_LOG` = 0x2 - 记录日志
- `FILAMENT_DEBUG_COMMANDS_SYSTRACE` = 0x4 - Systrace 跟踪

## 主要 API

### 核心虚方法

| 方法 | 说明 |
|------|------|
| `purge()` | 主线程调用，执行用户回调 |
| `scheduleCallback(handler, user, callback)` | 调度回调执行 |
| `getShaderModel()` | 获取着色器模型 |
| `getShaderLanguages(preferred)` | 获取支持的着色器语言列表 |
| `getDispatcher()` | 获取命令分发器 |
| `execute(fn)` | 包装命令批次执行 |
| `debugCommandBegin/End(...)` | 调试命令跟踪 |

### 静态方法

| 方法 | 说明 |
|------|------|
| `getElementTypeSize(type)` | 获取元素类型的字节大小 |

### 通过宏展开的 API

- `DECL_DRIVER_API` - 异步方法（空实现，由具体后端实现）
- `DECL_DRIVER_API_SYNCHRONOUS` - 同步纯虚方法
- `DECL_DRIVER_API_RETURN` - 带返回值的异步方法

## 依赖关系

- `backend/CallbackHandler.h` / `backend/DriverEnums.h` / `backend/Handle.h` 等
- `utils/CString.h` / `utils/FixedCapacityVector.h` / `utils/compiler.h`
- `private/backend/DriverAPI.inc` - API 声明宏展开
