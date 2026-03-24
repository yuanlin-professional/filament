# OpenGLDriverFactory.h

## 文件概述

OpenGL 驱动工厂类，提供静态方法创建 OpenGL 后端驱动实例。

## 核心类/结构体

### `OpenGLDriverFactory`
- `create()`: 静态工厂方法，接受平台对象、共享 GL 上下文和驱动配置，返回 `Driver*`。

## 关键实现逻辑

- 实际实现委托给 `OpenGLDriver::create()`，此类仅作为公开的创建入口。

## 依赖关系

- `backend/Platform.h` - 驱动配置类型
