# API 概览

Godot-SWF 提供两套 API 接口：C# 原生 API 和 GDScript 桥接 API。

## 架构说明

![QQ20251022-190552](https://www.freeimg.cn/uploads/486/68f8baa4ab9dd.png)

## 核心类说明

### SWF (C# 核心类)
主要的 SWF 动画控制类，提供完整的动画管理功能。

### Aid (动画标识符)
用于唯一标识和管理动画实例的结构体。

### GodotSwfNetBridge (C# 桥接类)
为 GDScript 提供接口的桥接类，不建议在 C# 代码中直接使用。

### godot_swf_gds_bridge (GDScript 类)
GDScript 专用的桥接类，提供简化的 API 接口。

## 选择建议

- **性能优先** - 推荐使用 C# 原生 API
- **项目兼容性** - 纯 GDScript 项目使用桥接 API
- **快速原型** - GDScript 接口更易于快速开发

详细 API 文档请参考 [接口文档](endpoints.md)。