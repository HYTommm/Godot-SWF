# GDScript 桥接 API

## godot_swf_gds_bridge 类

Node2D节点，负责加载SWF文件并创建动画对象，提供面向对象的API管理动画播放。

### 信号
- `animation_finished(id: int)` - 动画播放完成时触发
- `parse_finished()` - SWF文件解析完成时触发

### 初始化
```gdscript
func class_init(path: String) -> void
```
初始化桥接类并加载SWF文件。

**参数:**
- `path`: SWF文件路径

### 主要方法

**parse**
```gdscript
func parse() -> void
```
开始异步解析SWF文件。必须在创建动画前调用。此函数会建立一个子线程，解析完成后会触发`parse_finished`信号。自身立即返回，解析过程可能需要较长时间。

**create_anim**
```gdscript
func create_anim(shape_id: int) -> swf_animation
```
创建并返回动画对象。

**stop_all**
```gdscript
func stop_all() -> void
```
停止所有动画。警告：此函数性能较差，一般只在clear()前调用。停止动画请使用swf_animation.stop()。

**clear**
```gdscript
func clear() -> void
```
清除所有动画对象。一般在清理swf文件实例时调用。

### 内部方法 (不推荐直接使用)

**create_anim_aid**
```gdscript
func create_anim_aid(shape_id: int) -> int
```
创建动画并返回内部Aid。仅供内部使用。

**play**
```gdscript
func play(aid: int) -> void
```
播放指定Aid的动画。使用swf_animation.play()替代。

**stop**
```gdscript
func stop(aid: int) -> void
```
停止指定Aid的动画。使用swf_animation.stop()替代。

**pause**
```gdscript
func pause(aid: int) -> void
```
暂停指定Aid的动画。使用swf_animation.pause()替代。

**continue_anim**
```gdscript
func continue_anim(aid: int) -> void
```
继续播放指定Aid的动画。使用swf_animation.continue_anim()替代。

**pause_and_play_children**
```gdscript
func pause_and_play_children(aid: int) -> void
```
暂停当前动画并播放子动画。仅供内部使用。

### 内部属性设置方法 (不推荐直接使用)
```gdscript
func set_anim_transform(aid: int, anim_transform: Transform2D)
func set_anim_color(aid: int, color: Color) 
func set_anim_visible(aid: int, anim_visible: bool)
func set_anim_speed_scale(aid: int, speed_scale: float)
func set_anim_is_looping(aid: int, is_looping: bool)
```

### 状态查询
```gdscript
func is_finished(aid: int) -> bool
```
检查动画是否完成。

```gdscript
func get_parse_progress() -> float
```
获取解析进度。

### 使用示例
```gdscript
# 创建播放器
var player = godot_swf_gds_bridge.new()
player.class_init("res://anim.swf")
add_child(player)

# 等待解析完成
player.parse_finished.connect(_on_parse_finished)

func _on_parse_finished():
    # 创建动画
    var anim = player.create_anim(100)
    anim.is_looping = true
    anim.play()
```
