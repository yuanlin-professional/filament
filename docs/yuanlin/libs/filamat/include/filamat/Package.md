# Package.h

## 文件概述
定义 `Package` 类，用于封装材质编译后的二进制输出数据包。是 `MaterialBuilder::build()` 的返回类型。

## 核心类/结构体/函数
- **`Package`** - 材质二进制包封装类
  - 管理堆上分配的 `uint8_t` 数组
  - `getData()` / `getSize()` / `getEnd()` - 访问数据
  - `isValid()` / `setValid()` - 有效性标志
  - `invalidPackage()` - 创建无效包（用于表示编译失败）
  - 支持移动语义，禁止拷贝

## 关键实现逻辑
简单的 RAII 包装器，构造时分配内存，析构时释放。`mValid` 标志默认为 true，编译失败时设为 false。

## 依赖关系
- `utils/compiler.h`
