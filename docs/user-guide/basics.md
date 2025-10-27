# 基础使用指南

## GDScript 使用方式

### 基本工作流
```gdscript
extends Node2D

var swf_player
var idle_anim
var attack_anim

func _ready():
    # 1. 初始化 SWF 播放器
    swf_player = godot_swf_gds_bridge.new()
    swf_player.class_init("res://character.swf")
    add_child(swf_player)
    
    # 2. 创建动画对象
    idle_anim = swf_player.create_anim(10)
    attack_anim = swf_player.create_anim(100)
    
    # 3. 配置动画属性
    idle_anim.is_looping = true
    attack_anim.is_looping = false
    
    # 4. 连接信号
    attack_anim.animation_finished.connect(_on_attack_finished)
    
    # 5. 开始播放
    idle_anim.play()

func _on_attack_finished():
    # 攻击动画完成后回到待机
    idle_anim.play()
    attack_anim.stop()
```

### 动画属性控制
```gdscript
# 变换控制
anim.transform = Transform2D().scaled(Vector2(2, 2))

# 颜色控制
anim.color = Color(1, 0.5, 0.5)  # 红色调

# 可见性控制
anim.visible = false

# 播放速度
anim.speed_scale = 2.0  # 2倍速播放

# 循环设置
anim.is_looping = true
```

### 播放控制
```gdscript
# 播放动画
anim.play()

# 暂停动画
anim.pause()

# 继续播放
anim.continue_anim()

# 停止动画
anim.stop()

# 暂停但是播放子动画
anim.pause_and_play_children()
```

## C# 使用方式

### 基本工作流
```csharp
public partial class Character : Node2D
{
    private SWF _swf;
    private SwfAnimation _idleAnim;
    private SwfAnimation _attackAnim;

    public override void _Ready()
    {
        // 1. 初始化 SWF
        _swf = new SWF("res://character.swf", this);
        AddChild(_swf);
        _swf.Parse();
        
        // 2. 创建动画对象
        var _idleAnim = _swf.CreateAnim(10);
        
        // 3. 配置属性
        _idleAnim.IsLooping = true;
        
        // 4. 连接事件
        _idleAnim.AnimationFinished += OnIdleFinished;
        
        // 5. 开始播放
        _idleAnim.Play();
    }

    private void OnIdleFinished()
    {
        GD.Print("待机动画完成");
    }
}
```

## 最佳实践

### 1. 动画状态管理
```gdscript
var current_anim = null

func play_animation(anim):
    if current_anim and current_anim != anim:
        current_anim.stop()
    current_anim = anim
    anim.play()
```

### 2. 资源清理
```gdscript
func _exit_tree():
    if swf_player:
        swf_player.stop_all()
        swf_player.clear()
```

### 3. 错误处理
```gdscript
func safe_play(anim):
    if anim and anim is swf_animation:
        anim.play()
    else:
        push_error("无效的动画对象")
```

## 常见问题

### 动画不显示？
- 检查 SWF 文件路径是否正确
- 确认调用了 `parse()` 方法
- 验证动画 ID 是否存在

### 性能问题？
- 避免同时播放过多复杂动画
- 使用合适的缩放比例
- 及时停止不需要的动画
```

### 关键步骤说明

1. **创建 SWF 实例** - 使用 `new SWF(swfPath, canvasItem)` 创建实例
2. **添加到场景树** - 使用 `AddChild()` 将 SWF 实例添加到节点树
3. **解析文件** - 调用 `Parse()` 方法解析 SWF 文件内容
4. **播放动画** - 使用 `Play(shapeId)` 播放指定动画

### 参数说明

- `swfPath`: SWF 文件在项目中的路径（如：`"res://animations/character.swf"`）
- `canvasItem`: 渲染目标，通常传入 `this`
- `shapeId`: 动画ID，需要在 SWF 文件中查看或通过工具获取
