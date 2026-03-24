# SubpassInfo.h

## 文件概述
定义 `SubpassInfo` 结构体，用于描述子通道（Subpass）输入附件的信息。子通道是 Vulkan 渲染通道中的概念，允许片段着色器读取同一渲染通道中前序子通道的输出。

## 核心类/结构体/函数
- **`SubpassInfo`** - 子通道信息结构体
  - `block` - 所属的接口块名称，默认为 "MaterialParams"
  - `name` - 子通道名称
  - `type` - 子通道类型（SubpassType）
  - `format` - 采样器格式（SamplerFormat）
  - `precision` - 精度（Precision）
  - `attachmentIndex` - 附件索引
  - `binding` - 绑定点
  - `isValid` - 是否有效

## 关键实现逻辑
简单数据结构，通过构造函数初始化所有字段。`isValid` 默认为 false，仅在显式构造时设为 true。

## 依赖关系
- `backend/DriverEnums.h`
- `utils/CString.h`
