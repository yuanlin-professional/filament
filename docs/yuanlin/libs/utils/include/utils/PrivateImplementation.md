# PrivateImplementation.h / PrivateImplementation-impl.h

## 文件概述
PIMPL (Pointer to Implementation) 模式的模板基类，用于隐藏实现细节、保持二进制兼容性。

## 核心类/结构体/函数
- **PrivateImplementation<T>**: PIMPL 基类模板
  - 管理 T* mImpl 指针的生命周期
  - 提供 `operator->` 访问实现对象
  - 支持拷贝和移动语义

## 关键实现逻辑
- 构造/析构/拷贝的实现在单独的 -impl.h 文件中，确保不在公共头文件中暴露
- 移动操作可内联实现（仅交换指针）

## 依赖关系
- 无外部 utils 依赖
