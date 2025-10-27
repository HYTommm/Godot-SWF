# API 概览

Godot-SWF 提供多层次的 API 设计，满足不同用户的需求。从高性能的底层接口到易用的高级封装，您可以根据项目需求选择合适的 API 层级。

## API 层级结构

## 架构说明

![QQ20251027-174104](https://www.freeimg.cn/uploads/486/68ff3e4b77e47.png)

## 各层级说明

### 1. 高级 API (推荐)

**面向对象设计，直观易用**

#### GDScript: `swf_animation`
```gdscript
var anim = swf_player.create_anim(256)
anim.is_looping = true
anim.speed_scale = 1.5
anim.play()
anim.animation_finished.connect(_on_finished)
```

#### C#: `SwfAnimation`
```csharp
var anim = new SwfAnimation(aid, swf);
anim.IsLooping = true;
anim.SpeedScale = 1.5f;
anim.Play();
anim.AnimationFinished += OnFinished;
```

**适用场景：**
- 大多数游戏开发场景
- 需要直观的动画控制
- 希望减少底层细节

### 2. 桥接层 (GDScript)

**GDScript 专用桥接**

#### `godot_swf_gds_bridge`
```gdscript
var player = godot_swf_gds_bridge.new()
player.class_init("res://anim.swf")
var aid = player.create_anim_aid(256)
player.play(aid)
```

**适用场景：**
- 纯 GDScript 项目
- 需要直接控制 Aid 的情况
- 过渡从旧版本迁移

### 3. 核心层 (C#)

**高性能底层接口**

#### `SWF` 类
```csharp
var swf = new SWF("res://anim.swf", this);
swf.Parse();
var aid = swf.CreateAnimAid(256);
swf.PlayAnim(aid);
```

**适用场景：**
- 性能关键的场景
- 需要最大控制权的应用
- C# 专用项目

## 选择指南

### 新项目推荐使用高级 API

| 需求 | 推荐 API | 原因 |
|------|----------|------|
| 快速开发 | `swf_animation` / `SwfAnimation` | 直观易用，减少代码量 |
| 性能优先 | `SWF` (C#) | 直接控制，无桥接开销 |
| GDScript 项目 | `swf_animation` | 原生 GDScript 体验 |
| C# 项目 | `SwfAnimation` | 类型安全，性能良好 |

### 迁移指南

**从旧版本迁移：**

```gdscript
# 旧版本 (不推荐)
var aid = swf.play(256)
swf.stop(aid)

# 新版本 (推荐)
var anim = swf.create_anim(256)
anim.play()
anim.stop()
```

## 信号与事件系统

所有 API 层级都支持完整的信号/事件系统：

| 层级 | 动画完成信号 | 解析完成信号 |
|------|-------------|-------------|
| 高级 API | `animation_finished()` | 通过桥接层 |
| 桥接层 | `animation_finished(id)` | `parse_finished()` |
| 核心层 | `AnimationFinished` (Aid) | `ParseFinished` |

## 性能特性

### 渲染性能
- 所有层级都使用相同的 Rendering Server 渲染
- 矢量图形实时渲染，支持任意缩放
- 几乎不依赖 Godot 节点树

### 内存使用
- 解析后的 SWF 数据共享 across 动画实例
- 及时停止动画可以释放资源
- `Clear()` 方法可以完全重置状态

## 详细文档

- [SWF 核心类](SWF.md) - C# 底层 API
- [godot_swf_gds_bridge](godot_swf_gds_bridge.md) - GDScript 桥接 API  
- [swf_animation](swf_animation.md) - GDScript 高级 API
- [SwfAnimation](SwfAnimation.md) - C# 高级 API
```