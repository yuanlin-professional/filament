# Program.h

## 文件概述

定义了 `Program` 类，用于构建和描述 GPU 着色器程序。包含着色器源码、描述符绑定信息、特化常量、Push Constants 等元数据，是后端创建 GPU 程序所需的完整描述。

## 核心类

### Program

着色器程序描述类，采用 Builder 模式进行配置。

**内部类型**:
- `Descriptor` - 描述符绑定（名称/类型/绑定点）
- `Uniform` - ES2 uniform 信息
- `PushConstant` - 推送常量描述
- `ShaderBlob` / `ShaderSource` - 着色器二进制数据

**常量**:
- `SHADER_TYPE_COUNT` = 3（Vertex/Fragment/Compute）
- `UNIFORM_BINDING_COUNT` = 9
- `SAMPLER_BINDING_COUNT` = 4

## 主要 API

| 方法 | 说明 |
|------|------|
| `shader(stage, data, size)` | 设置指定阶段的着色器源码 |
| `shaderLanguage(lang)` | 设置着色器语言（默认 ESSL3） |
| `diagnostics(name, logger)` | 设置材质名称和日志（仅诊断用途） |
| `descriptorBindings(set, bindings)` | 设置描述符绑定信息 |
| `descriptorLayout(set, layout)` | 设置描述符集布局 |
| `specializationConstants(constants)` | 设置特化常量 |
| `pushConstants(stage, constants)` | 设置推送常量 |
| `cacheId(id)` | 设置缓存 ID |
| `multiview(enabled)` | 启用多视图支持 |
| `priorityQueue(queue)` | 设置编译优先级 |
| `uniforms(index, name, info)` | ES2 uniform 信息（仅 ES2） |

## 依赖关系

- `backend/DriverEnums.h` - 枚举定义
- `utils/CString.h` / `utils/FixedCapacityVector.h` / `utils/Invocable.h` / `utils/Slice.h`
