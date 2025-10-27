# GDScript 动画 API

## swf_animation 类

封装单个动画实例的所有操作，提供面向对象的动画控制接口。

### 信号
- `animation_finished()` - 动画播放完成时触发

### 属性 (setter方法)
```gdscript
var transform: Transform2D
```
设置动画的变换矩阵。

```gdscript
var color: Color
```
设置动画的颜色。

```gdscript
var visible: bool
```
设置动画的可见性。

```gdscript
var speed_scale: float
```
设置动画的播放速度倍数。

```gdscript
var is_looping: bool
```
设置动画是否循环播放。

### 主要方法

**class_init**
```gdscript
func class_init(aid: int, swf: godot_swf_gds_bridge) -> void
```
初始化动画对象。由`godot_swf_gds_bridge.create_anim()`自动调用。

**play**
```gdscript
func play() -> void
```
开始播放动画。

**pause**
```gdscript
func pause() -> void
```
暂停动画播放。

**continue_anim**
```gdscript
func continue_anim() -> void
```
继续播放暂停的动画。

**stop**
```gdscript
func stop() -> void
```
停止动画播放。

**pause_and_play_children**
```gdscript
func pause_and_play_children() -> void
```
暂停当前动画并播放子动画。

### 使用示例
```gdscript
# 创建动画对象
var anim = swf_player.create_anim(256)

# 设置属性
anim.transform = Transform2D().scaled(Vector2(0.5, 0.5))
anim.color = Color(1, 0.8, 0.8)
anim.speed_scale = 1.5
anim.is_looping = true

# 播放控制
anim.play()
anim.animation_finished.connect(_on_anim_done)

# 暂停/继续/停止
anim.pause()
anim.continue_anim()
anim.stop()
```

### 最佳实践
1. **属性设置应在播放前**
```gdscript
# 推荐：先设置属性再播放
anim.is_looping = true
anim.speed_scale = 1.0
anim.play()
```

2. **及时连接信号**
```gdscript
# 创建后立即连接信号
anim.animation_finished.connect(_on_finished)
```

3. **合理管理动画生命周期**
```gdscript
func _exit_tree():
    anim.stop()  # 节点退出时停止动画
```
