# 高级功能

## 动画控制

### 多动画管理
Godot-SWF 支持同时播放多个动画，并通过 Aid（动画ID）进行管理：

```csharp
// C# 示例
private SWF _swf;
private ulong _animationId;

public void PlayComplexAnimation()
{
    _swf = new SWF("res://animations/complex.swf", this);
    AddChild(_swf);
    _swf.Parse();
    
    // 播放动画并保存ID用于后续控制
    var aid = _swf.Play(256);
    _animationId = aid.Id;
    
    // 检查动画是否完成
    if (_swf.IsFinished(aid))
    {
        GD.Print("Animation finished");
    }
}
```

### 动画状态控制

```csharp
// 暂停指定动画
_swf.Pause(aid);

// 继续播放暂停的动画
_swf.Continue(aid);

// 停止指定动画
_swf.Stop(aid);

// 停止所有动画
_swf.StopAll();
```

## 变换与效果

### 动态变换
```csharp
// 设置动画的变换矩阵
var transform = new Transform2D(
    Mathf.DegToRad(45), // 旋转45度
    new Vector2(100, 50) // 位置
);
_swf.SetTransform(aid, transform);
```

### 颜色控制
```csharp
// 设置动画颜色
var tintColor = new Color(1, 0.5, 0.5, 0.8); // 红色调，半透明
_swf.SetColor(aid, tintColor);
```

### 可见性控制
```csharp
// 显示/隐藏特定动画
_swf.SetVisible(aid, false); // 隐藏
_swf.SetVisible(aid, true);  // 显示
```

## 高级渲染特性

### 矢量图形优势
- **无限缩放** - 保持清晰度，不受分辨率限制
- **小文件体积** - 相比位图动画，文件体积更小
- **动态修改** - 运行时可以修改颜色、变换等属性

### 渲染性能优化
- 使用 `Rendering Server` 直接渲染，减少节点开销
- 批量处理渲染指令


## 性能建议

1. **合理使用动画数量** - 同时播放过多动画可能影响性能
2. **优化SWF文件** - 在Adobe Animate中优化矢量图形
3. **使用合适的缩放** - 避免不必要的放大渲染
4. **及时清理** - 不使用的动画及时调用 `Clear()` 方法

## 限制说明

### 不支持的功能
- 按钮交互和鼠标事件
- ActionScript 脚本执行
- 声音播放
- 视频嵌入
- 复杂的滤镜效果（如模糊、投影）

### 已知问题
- 颜色转换可能存在颜色空间不匹配
- 极复杂的矢量图形可能渲染性能下降
- 某些特殊的SWF特性可能无法完全支持
```