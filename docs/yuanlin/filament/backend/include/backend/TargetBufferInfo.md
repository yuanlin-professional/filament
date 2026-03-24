# TargetBufferInfo.h

## 文件概述

定义了渲染目标缓冲区信息结构体 `TargetBufferInfo` 和多渲染目标（MRT）类 `MRT`，用于配置渲染目标的颜色/深度/模板附件。

## 核心结构体

### TargetBufferInfo

描述渲染目标的单个附件。

| 字段 | 类型 | 说明 |
|------|------|------|
| `handle` | `Handle<HwTexture>` | 纹理句柄 |
| `level` | `uint8_t` | Mipmap 级别（默认 0） |
| `layer` | `uint16_t` | 层索引：Cubemap 面/数组层/3D 切片/多视图起始层 |

### MRT (Multiple Render Targets)

多渲染目标容器，最多支持 8 个颜色附件。

**常量**:
- `MIN_SUPPORTED_RENDER_TARGET_COUNT` = 4
- `MAX_SUPPORTED_RENDER_TARGET_COUNT` = 8

## 主要 API

| 方法 | 说明 |
|------|------|
| `TargetBufferInfo(handle, level, layer)` | 完整构造 |
| `TargetBufferInfo(handle, level)` | 指定 mip 级别 |
| `TargetBufferInfo(handle)` | 仅指定纹理 |
| `MRT(color0, ...)` | 多个颜色附件构造（1-4 个） |
| `MRT::operator[]` | 下标访问各附件 |

## 依赖关系

- `backend/Handle.h` - 纹理句柄类型
