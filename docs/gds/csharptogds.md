## Godot Script (GDScript) 使用方案
本插件可以不使用C#调用，而使用原生的GDScript语言来编写Godot-SWF游戏。
### 准备工作
配置一份导出使用的C#脚本，并将Godot-SWF的DLL文件复制到该项目的bin目录下。
代码如下:
```csharp
using GodotSWF;
using Godot;

namespace swfplayer;

public partial class GodotSwfNetBridge : Node
{
	private SWF swf;

	private GodotSwfNetBridge()
	{
	}

	public void Load(string swfPath, CanvasItem root)
	{
		swf = new SWF(swfPath, root);
		GD.Print("SWF loaded, path: " + swfPath);
		AddChild(swf);
	}

	public long Play(int shapeID)
	{
		Aid aid = swf.Play(shapeID);
		return (long)aid.Id;
	}

	public void Parse()
	{
		swf.Parse();
	}

	public void Stop(long aidId)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		swf.Stop(aid);
	}

	public void StopAll()
	{
		swf.StopAll();
	}

	public void Pause(long aidId)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		swf.Pause(aid);
	}

	public void Clear()
	{
		swf.Clear();
	}

	public void PauseAndPlayChildren(long aidId)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		swf.PauseAndPlayChildren(aid);
	}

	public void Continue(long aidId)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		swf.Continue(aid);
	}

	public void SetTransform(long aidId, Transform2D transform)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		swf.SetTransform(aid, transform);
	}

	public void SetColor(long aidId, Color color)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		swf.SetColor(aid, color);
	}

	public void SetVisible(long aidId, bool visible)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		swf.SetVisible(aid, visible);
	}

	public bool IsFinished(long aidId)
	{
		Aid aid = new();
		aid.Id = (ulong)aidId;
		return swf.IsFinished(aid);
	}
}
```

## GDScript接入代码
```python
extends Node2D

class_name godot_swf_gds_bridge
var cs
var swf_node

func class_init() -> void:
	cs = load("res://GodotSwfNetBridge.cs")

func load_swf(path : String) -> void:
	swf_node = cs.new()
	swf_node.Load(path, self)
	add_child(swf_node)
	print(path)

func parse() -> void:
	swf_node.Parse()

func play(id : int) -> int:
	var i : int = swf_node.Play(id);
	print(i)
	return i
	
func stop(id : int) -> void:
	swf_node.Stop(id)

func stop_all() -> void:
	swf_node.StopAll()

func pause(id : int) -> void:
	swf_node.Pause(id)

func pause_and_play_children(id : int) -> void:
	swf_node.PauseAndPlayChildren(id)

func continue_anim(id : int) -> void:
	swf_node.Continue(id)

func set_anim_transform(id : int, transform : Transform2D) -> void:
	swf_node.SetTransform(id, transform)

func set_anim_color(id : int, color : Color) -> void:
	swf_node.SetColor(id, color)

func set_anim_visible(id : int, visible : bool) -> void:
	swf_node.SetVisible(id, visible)

func is_finished(id : int) -> bool:
	return swf_node.SetVisible()
```
### 使用方法
1. 在场景中创建一个空节点，命名为`SWFPlayer`。
2. 在该节点下创建一个脚本，并将上述代码粘贴进去。
3. 在脚本中，调用`Load`方法，传入SWF文件的路径和根节点。
4. 调用`Play`方法，传入需要播放的形状ID。
5. 调用`Stop`方法，传入需要停止的形状ID。
6. 调用`StopAll`方法，停止所有形状的播放。
7. 调用`Pause`方法，传入需要暂停的形状ID。
8. 调用`Clear`方法，清除所有形状。