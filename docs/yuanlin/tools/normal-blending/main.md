# main.cpp (normal-blending)

## 文件概述
法线贴图混合工具。使用 Reoriented Normal Mapping (RNM) 算法将基础法线贴图与细节法线贴图混合，生成组合后的法线贴图。

## 核心函数
- `main()` - 入口：加载两张法线贴图 -> 混合 -> 输出
- `blend()` - 逐像素执行 RNM 混合算法

## 关键实现逻辑
1. RNM 算法核心：`t = normal * (2,2,2) + (-1,-1,0)`; `u = detail * (-2,-2,2) + (1,1,-1)`; `r = normalize(t * dot(t,u) - u * t.z)`
2. 输入法线贴图以线性空间加载（范围 [0,1]），混合后转回 [0,1]
3. 两张贴图必须具有相同尺寸
4. 支持 PNG、HDR、EXR、PSD、DDS 输出格式

## 依赖关系
- `imageio/` - 图像编解码
- `math/vec3` - 向量运算
