# 基础使用指南

## C# 基础用法

### 最小示例代码

```csharp
using Godot;
using GodotSWF;

public partial class SWFExample : Node2D
{
    private string _swfPath = "res://path/to/your/animation.swf";
    private SWF _swf;

    public override void _Ready()
    {
        // 调整缩放比例（根据实际需要）
        Scale /= 20f;
        
        // 创建 SWF 实例并加载文件
        _swf = new SWF(_swfPath, this);
        AddChild(_swf);
        
        // 解析 SWF 文件
        _swf.Parse();
        
        // 播放指定动画ID
        _swf.Play(256);
    }
}
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
