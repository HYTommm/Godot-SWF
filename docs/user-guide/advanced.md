# 高级使用指南

本文档介绍 Godot-SWF 插件的高级功能和最佳实践，帮助您充分发挥插件的潜力。

## 解析策略与性能优化

### 1. 正确的解析时机
```gdscript
# 推荐：预加载和预解析
func _ready():
    # 在场景加载时初始化并解析
    var swf = godot_swf_gds_bridge.new()
    swf.class_init("res://character.swf")
    add_child(swf)
    
    # 连接解析完成信号
    swf.parse_finished.connect(_on_parse_finished)
    
    # 开始解析（异步，需要等待几秒）
    swf.parse()

func _on_parse_finished():
    # 解析完成后才创建动画对象
    _idle_anim = swf.create_anim(10)
    _attack_anim = swf.create_anim(100)
    
    # 标记为可用
    _animations_ready = true
    print("SWF 解析完成，动画准备就绪")

func play_attack():
    # 确保动画已准备就绪
    if _animations_ready:
        _attack_anim.play()
    else:
        push_warning("动画尚未解析完成，无法播放")
```

### 2. 进度监控与用户体验
```gdscript
func load_with_progress():
    var swf = godot_swf_gds_bridge.new()
    swf.class_init("res://large_character.swf")
    add_child(swf)
    
    # 显示加载进度
    _show_loading_screen()
    
    # 在后台解析，同时更新进度
    var tween = create_tween()
    tween.tween_method(_update_loading_progress, 0.0, 1.0, 0.5) # 模拟进度
    
    swf.parse_finished.connect(_on_parse_complete)
    swf.parse()

func _update_loading_progress(progress):
    # 可以结合实际的解析进度
    var actual_progress = swf.get_parse_progress()
    _loading_bar.value = actual_progress * 100

func _on_parse_complete():
    _hide_loading_screen()
    _initialize_animations()
```

### 3. 内存管理与资源释放
```gdscript
# 管理多个 SWF 文件
var _loaded_swf_files = {}

func load_swf_asset(swf_path):
    if _loaded_swf_files.has(swf_path):
        return _loaded_swf_files[swf_path]
    
    var swf = godot_swf_gds_bridge.new()
    swf.class_init(swf_path)
    add_child(swf)
    
    _loaded_swf_files[swf_path] = swf
    return swf

func unload_swf_asset(swf_path):
    if _loaded_swf_files.has(swf_path):
        var swf = _loaded_swf_files[swf_path]
        swf.stop_all()
        remove_child(swf)
        swf.queue_free()
        _loaded_swf_files.erase(swf_path)
```

## 高级动画控制

### 1. 动画状态机
```gdscript
class_name CharacterAnimationStateMachine

enum State { IDLE, WALKING, ATTACKING, JUMPING }

var _current_state = State.IDLE
var _swf_player
var _animations = {}

func _init(swf_player):
    _swf_player = swf_player
    
    # 预定义动画映射
    _animations[State.IDLE] = swf_player.create_anim(10)
    _animations[State.WALKING] = swf_player.create_anim(20)
    _animations[State.ATTACKING] = swf_player.create_anim(30)
    _animations[State.JUMPING] = swf_player.create_anim(40)
    
    # 设置循环属性
    _animations[State.IDLE].is_looping = true
    _animations[State.WALKING].is_looping = true
    _animations[State.ATTACKING].is_looping = false
    _animations[State.JUMPING].is_looping = false
    
    # 连接攻击动画完成事件
    _animations[State.ATTACKING].animation_finished.connect(_on_attack_finished)

func transition_to(new_state):
    if _current_state == new_state:
        return
        
    # 停止当前状态动画
    if _animations.has(_current_state):
        _animations[_current_state].stop()
    
    # 播放新状态动画
    if _animations.has(new_state):
        _animations[new_state].play()
    
    _current_state = new_state

func _on_attack_finished():
    # 攻击完成后自动回到待机状态
    transition_to(State.IDLE)
```

### 2. 动画层级与混合
```gdscript
var _animation_layers = {
    "base": null,      # 基础层 (待机/行走)
    "upper": null,     # 上半身层 (攻击/使用物品)
    "facial": null,    # 表情层
    "effect": null,    # 特效层
}

func setup_layered_animation(swf_player):
    # 初始化各层动画
    _animation_layers["base"] = swf_player.create_anim(100)
    _animation_layers["upper"] = swf_player.create_anim(200)
    _animation_layers["facial"] = swf_player.create_anim(300)
    _animation_layers["effect"] = swf_player.create_anim(400)
    
    # 设置基础层循环
    _animation_layers["base"].is_looping = true

func play_layer_animation(layer_name, shape_id, swf_player):
    # 停止该层当前动画
    if _animation_layers[layer_name]:
        _animation_layers[layer_name].stop()
    
    # 创建并播放新动画
    _animation_layers[layer_name] = swf_player.create_anim(shape_id)
    _animation_layers[layer_name].play()

# 使用示例：行走时攻击
func walk_and_attack():
    play_layer_animation("base", 20, swf_player)  # 行走动画
    play_layer_animation("upper", 30, swf_player) # 攻击动画
```

### 3. 动画事件系统
```gdscript
# 扩展动画类以支持自定义事件
class_name AdvancedSWFAnimation extends swf_animation

signal custom_event(event_name, frame_data)

func _init(aid, swf):
    super(aid, swf)
    # 这里可以添加自定义事件处理逻辑

# 在动画关键帧触发事件
func trigger_custom_event(event_name, frame_data):
    custom_event.emit(event_name, frame_data)

# 使用示例
func setup_attack_animation():
    var attack_anim = AdvancedSWFAnimation.new(aid, swf)
    attack_anim.custom_event.connect(_on_attack_event)
    
    # 假设在特定帧会触发事件
    # "hit_frame" - 命中帧
    # "effect_frame" - 特效帧

func _on_attack_event(event_name, frame_data):
    match event_name:
        "hit_frame":
            spawn_hit_effect()
            apply_damage()
        "effect_frame":
            spawn_magic_effect()
```

## 变换与效果高级应用

### 1. 动态变换与动画同步
```gdscript
func setup_dynamic_character():
    var character_anim = swf.create_anim(500)
    
    # 使用 Tween 创建复杂的变换序列
    var tween = create_tween()
    tween.set_parallel(true)  # 并行执行多个变换
    
    # 缩放动画
    tween.tween_method(_update_scale, Vector2(1, 1), Vector2(1.5, 1.5), 0.5)
    
    # 旋转动画
    tween.tween_method(_update_rotation, 0, 360, 1.0)
    
    # 颜色闪烁
    tween.tween_method(_update_color, Color.WHITE, Color.RED, 0.25)
    tween.tween_method(_update_color, Color.RED, Color.WHITE, 0.25)
    
    character_anim.play()

func _update_scale(scale):
    current_anim.transform = Transform2D().scaled(scale)

func _update_rotation(angle):
    var current_scale = current_anim.transform.get_scale()
    current_anim.transform = Transform2D(deg_to_rad(angle), Vector2.ZERO).scaled(current_scale)

func _update_color(color):
    current_anim.color = color
```

### 2. 视差与深度效果
```gdscript
func create_parallax_effect():
    var background_anim = swf.create_anim(600)
    var midground_anim = swf.create_anim(601)
    var foreground_anim = swf.create_anim(602)
    
    # 设置不同的变换来模拟深度
    background_anim.transform = Transform2D().scaled(Vector2(0.8, 0.8))
    midground_anim.transform = Transform2D().scaled(Vector2(0.9, 0.9))
    foreground_anim.transform = Transform2D().scaled(Vector2(1.0, 1.0))
    
    # 播放所有层
    background_anim.play()
    midground_anim.play()
    foreground_anim.play()
```

## 最佳实践与架构模式

### 1. 资源管理与加载策略
```gdscript
# SWF 资源管理器
class_name SWFResourceManager extends Node

var _swf_cache = {}
var _loading_callbacks = {}

func load_swf(path, on_loaded_callback = null):
    if _swf_cache.has(path):
        # 已缓存，直接返回
        if on_loaded_callback:
            on_loaded_callback.call(_swf_cache[path])
        return _swf_cache[path]
    
    # 新加载
    var swf = godot_swf_gds_bridge.new()
    swf.class_init(path)
    add_child(swf)
    
    # 存储回调
    if on_loaded_callback:
        if not _loading_callbacks.has(path):
            _loading_callbacks[path] = []
        _loading_callbacks[path].append(on_loaded_callback)
    
    # 连接解析完成事件
    swf.parse_finished.connect(_on_swf_loaded.bind(path, swf))
    swf.parse()
    
    return swf

func _on_swf_loaded(path, swf):
    _swf_cache[path] = swf
    
    # 调用所有等待的回调
    if _loading_callbacks.has(path):
        for callback in _loading_callbacks[path]:
            callback.call(swf)
        _loading_callbacks.erase(path)
```

### 2. 配置驱动的动画系统
```gdscript
# animation_config.gd
extends Node

const CHARACTER_ANIMATIONS = {
    "hero": {
        "idle": 10,
        "walk": 20,
        "run": 30,
        "attack": 40,
        "jump": 50,
        "death": 60
    },
    "enemy": {
        "idle": 100,
        "walk": 110,
        "attack": 120,
        "hurt": 130,
        "death": 140
    }
}

static func get_animation_id(character_type, animation_name):
    if CHARACTER_ANIMATIONS.has(character_type):
        return CHARACTER_ANIMATIONS[character_type].get(animation_name, 0)
    return 0

static func get_looping_animations():
    return ["idle", "walk", "run"]

# 使用方式
func setup_character_animations(character_type, swf_player):
    var animations = {}
    var char_anims = CHARACTER_ANIMATIONS[character_type]
    
    for anim_name in char_anims:
        var anim_id = char_anims[anim_name]
        var anim = swf_player.create_anim(anim_id)
        anim.is_looping = anim_name in get_looping_animations()
        animations[anim_name] = anim
    
    return animations
```

## 调试与问题排查

### 1. 解析问题排查
```gdscript
func debug_parsing_issues():
    var swf = godot_swf_gds_bridge.new()
    swf.class_init("res://problematic.swf")
    add_child(swf)
    
    # 监控解析过程
    var timer = Timer.new()
    add_child(timer)
    timer.timeout.connect(_check_parse_progress.bind(swf))
    timer.start(0.1)  # 每100ms检查一次
    
    swf.parse_finished.connect(_on_debug_parse_finished.bind(timer))
    swf.parse()

func _check_parse_progress(swf):
    var progress = swf.get_parse_progress()
    print("解析进度: ", progress)
    
    # 如果进度长时间卡住，可能是文件有问题
    if progress > 0.0 and progress < 1.0:
        # 解析中...
        pass

func _on_debug_parse_finished(timer):
    timer.stop()
    print("解析完成")
```

### 2. 动画播放问题排查
```gdscript
func debug_animation_playback():
    # 测试所有可能的动画ID
    for i in range(1, 1000):
        var anim = swf.create_anim(i)
        anim.animation_finished.connect(_on_anim_test_finished.bind(i))
        
        # 播放短暂时间后停止
        anim.play()
        await get_tree().create_timer(0.1).timeout
        
        if anim.is_playing():
            print("发现有效动画ID: ", i)
        
        anim.stop()

func _on_anim_test_finished(anim_id):
    print("动画完成: ", anim_id)
```