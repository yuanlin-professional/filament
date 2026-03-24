# NullGLES.h

## 文件概述

提供一组空操作（no-op）的 OpenGL ES 函数存根，用于调试目的。通过取消注释 `using namespace nullgles;`，可以将所有 GL 调用替换为空函数，方便隔离和定位问题。

## 核心类/结构体

### 命名空间 `nullgles`
定义了常用 GL 函数的内联空实现，包括：
- 状态设置：`glScissor`、`glViewport`、`glDepthRangef`、`glClearColor` 等
- 对象绑定：`glBindTexture`、`glBindBuffer`、`glBindFramebuffer`、`glUseProgram` 等
- 数据操作：`glBufferData`、`glBufferSubData`、`glTexSubImage2D` 等
- 渲染操作：`glClear`、`glDrawRangeElements`、`glBlitFramebuffer` 等
- `glGetError()` 始终返回 `GL_NO_ERROR`

## 关键实现逻辑

- 默认情况下 `using namespace nullgles;` 被注释掉，不影响正常编译。
- 开发者在调试时可启用该命名空间以屏蔽所有 GL 调用。

## 依赖关系

- 无外部依赖（仅依赖已被 gl_headers.h 引入的 GL 类型）
