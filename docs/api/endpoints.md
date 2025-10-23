# 接口文档

## 信号与事件

### C# 原生事件

#### AnimationFinished
```csharp
public delegate void AnimationFinishedEventHandler(Aid aid);
public AnimationFinishedEventHandler AnimationFinished;
```
当动画播放完成时触发。参数为完成的动画标识符。

### GDScript 信号

#### animation_finished
```gdscript
signal animation_finished(id: int)
```
当动画播放完成时触发。参数为完成的动画ID。

## C# 原生 API

### SWF 类

#### 构造函数
```csharp
SWF(string swfPath, CanvasItem root)
```
- `swfPath`: SWF 文件路径
- `root`: 渲染根节点

#### 主要方法

**Parse**
```csharp
void Parse()
```
解析 SWF 文件，必须在播放前调用。

**Play**
```csharp
Aid Play(int shapeId)
```
播放指定动画，返回动画标识符。

**Stop**
```csharp
void Stop(Aid aid)
```
停止指定动画。

**StopAll**
```csharp
void StopAll()
```
停止所有动画。

**Pause**
```csharp
void Pause(Aid aid)
```
暂停指定动画。

**Continue**
```csharp
void Continue(Aid aid)
```
继续播放暂停的动画。

**SetTransform**
```csharp
void SetTransform(Aid aid, Transform2D transform)
```
设置动画的变换矩阵。

**SetColor**
```csharp
void SetColor(Aid aid, Color color)
```
设置动画颜色。

**SetVisible**
```csharp
void SetVisible(Aid aid, bool visible)
```
设置动画可见性。

**IsFinished**
```csharp
bool IsFinished(Aid aid)
```
检查动画是否播放完成。

**Clear**
```csharp
void Clear()
```
清理所有动画资源。

## GDScript 桥接 API

### godot_swf_gds_bridge 类

#### 初始化
```gdscript
func class_init() -> void
```
初始化桥接类，必须在其他方法前调用。

#### 主要方法

**load_swf**
```gdscript
func load_swf(path: String) -> void
```
加载 SWF 文件。

**parse**
```gdscript
func parse() -> void
```
解析 SWF 文件。

**play**
```gdscript
func play(id: int) -> int
```
播放动画，返回动画ID。

**stop**
```gdscript
func stop(id: int) -> void
```
停止指定动画。

**stop_all**
```gdscript
func stop_all() -> void
```
停止所有动画。

**pause**
```gdscript
func pause(id: int) -> void
```
暂停指定动画。

**continue_anim**
```gdscript
func continue_anim(id: int) -> void
```
继续播放动画。

**set_anim_transform**
```gdscript
func set_anim_transform(id: int, transform: Transform2D) -> void
```
设置动画变换。

**set_anim_color**
```gdscript
func set_anim_color(id: int, color: Color) -> void
```
设置动画颜色。

**set_anim_visible**
```gdscript
func set_anim_visible(id: int, visible: bool) -> void
```
设置动画可见性。

**is_finished**
```gdscript
func is_finished(id: int) -> bool
```
检查动画是否完成。

## 参数说明

### 动画ID (shapeId)
SWF 文件中定义的符号ID，需要通过工具查看或文档获取。

### 颜色值
使用 Godot 的 Color 类型，支持 RGBA 格式。

### 变换矩阵
使用 Transform2D 类型，支持平移、旋转、缩放等变换。

## 使用示例

### 信号使用示例

#### C# 事件使用
```csharp
public partial class AnimationPlayer : Node2D
{
    private SWF _swf;
    
    public override void _Ready()
    {
        _swf = new SWF("res://animations/character.swf", this);
        AddChild(_swf);
        _swf.Parse();
        
        // 订阅完成事件
        _swf.AnimationFinished += OnAnimationFinished;
        
        _swf.Play(100);
    }
    
    private void OnAnimationFinished(Aid aid)
    {
        GD.Print($"动画播放完成, ID: {aid.Id}");
        // 播放下一个动画或执行其他逻辑
    }
}
```

#### GDScript 信号使用

```gdscript
extends Node2D

func _ready():
    var swf = godot_swf_gds_bridge.new()
    swf.class_init()
    
    # 连接完成信号
    swf.animation_finished.connect(_on_animation_finished)
    
    swf.load_swf("res://animations/character.swf")
    swf.parse()
    add_child(swf)
    
    # 播放动画
    var aid = swf.play(100)

func _on_animation_finished(id: int):
    print("动画播放完成, ID: ", id)
    # 播放下一个动画或执行其他逻辑
```


### 复杂的动画序列控制
```csharp
// C# 示例
public class AnimationController : Node2D
{
    private SWF _swf;
    private Dictionary<string, Aid> _animations = new();
    
    public override void _Ready()
    {
        _swf = new SWF("res://animations/character.swf", this);
        AddChild(_swf);
        _swf.Parse();
        
        // 预加载多个动画
        _animations["walk"] = _swf.Play(100, float playScale = 0f); // 先暂停
        
        _animations["jump"] = _swf.Play(101);
        _swf.SetVisible(_animations["jump"], false); // 先隐藏
    }
    
    public void PlayWalkAnimation()
    {
        _swf.SetVisible(_animations["jump"], false);
        _swf.SetVisible(_animations["walk"], true);
        _swf.Continue(_animations["walk"]);
    }
}
```

## 错误处理

- 无效的动画ID会导致动画无法播放
- 文件路径错误可能会导致运行时崩溃
- 未调用 Parse() 直接播放会没有效果