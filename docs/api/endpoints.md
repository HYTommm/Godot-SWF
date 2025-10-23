# 接口文档

## 信号与事件

### C# 原生事件

#### AnimationFinished
```csharp
public delegate void AnimationFinishedEventHandler(Aid aid);
public AnimationFinishedEventHandler AnimationFinished;
```
当动画播放完成时触发。参数为完成的动画标识符。

#### ParseFinished
```csharp
public delegate void ParseFinishedEventHandler();
public ParseFinishedEventHandler ParseFinished;
```
当 SWF 文件解析完成时触发。

### GDScript 信号

#### animation_finished
```gdscript
signal animation_finished(id: int)
```
当动画播放完成时触发。参数为完成的动画ID。

#### parse_finished
```gdscript
signal parse_finished()
```
当 SWF 文件解析完成时触发。

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
**开始** 解析 SWF 文件，必须在播放前调用。请注意，此方法是异步的，调用后会立即返回，但解析可能需要几秒钟时间。请在 `ParseFinished` 事件（信号）中处理解析完成后的逻辑。

**Play**
```csharp
Aid Play(int shapeID, float playScale = 1, Transform2D transform = default, Color color = default, bool loop = false)
```
播放指定动画，返回动画标识符。可以指定播放速度、变换矩阵、颜色、是否循环播放。

```csharp
List<Aid> Play(List<int> shapeIDs, List<float> playScales = null)
```
批量播放动画，返回动画标识符列表。只能指定播放速度列表，如果不指定，则使用默认值。

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

**SetIsLooping**
```csharp
void SetIsLooping(Aid aid, bool isLooping)
```
设置动画是否循环播放。

**IsFinished**
```csharp
bool IsFinished(Aid aid)
```
检查动画是否播放完成。

**GetParseProgress**
```csharp
float GetParseProgress()
```
获取解析进度。返回值范围为 0.0-1.0，表示已解析的比例。注意，此方法仅返回已解析的比例，并不表示解析是否完成。若要检查解析是否完成，请使用 `ParseFinished` 事件（信号）。

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

**set_anim_is_looping**
```gdscript
func set_anim_is_looping(id: int, is_looping: bool) -> void
```
设置动画是否循环播放。

**is_finished**
```gdscript
func is_finished(id: int) -> bool
```
检查动画是否完成。

**get_parse_progress**
```gdscript
func get_parse_progress() -> float
```
获取解析进度。返回值范围为 0.0-1.0，表示已解析的比例。注意，此方法仅返回已解析的比例，并不表示解析是否完成。若要检查解析是否完成，请使用 `parse_finished` 信号。

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
        _swf.ParseFinished += () => _swf.Play(100);
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
var aid : int
func _ready():
    var swf = godot_swf_gds_bridge.new()
    swf.class_init()
    
    # 连接完成信号
    swf.animation_finished.connect(_on_animation_finished)
    swf.parse_finished.connect(func():
        aid = swf.play(100)
    )
    swf.load_swf("res://animations/character.swf")
    swf.parse()
    add_child(swf)

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
        _swf.ParseFinished += () => OnParseFinished();
        AddChild(_swf);
        _swf.Parse();
    }

    private void OnParseFinished()
    {
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