# C# 原生动画 API

## SwfAnimation 类

为C#用户提供直接的动画控制接口，无需桥接层，性能更优。

### 事件

**AnimationFinished**

```csharp
public delegate void AnimationFinishedEventHandler();
public AnimationFinishedEventHandler AnimationFinished;
```

动画播放完成时触发。

### 属性
```csharp
Transform2D Transform
```
设置动画的变换矩阵。

```csharp
Color Color
```
设置动画的颜色。

```csharp
bool Visible
```
设置动画的可见性。

```csharp
float SpeedScale
```
设置动画的播放速度倍数。

```csharp
bool IsLooping
```
设置动画是否循环播放。

```csharp
bool IsFinished
```
获取动画是否已完成播放。

### 主要方法

**构造函数**
```csharp
SwfAnimation(Aid aid, SWF swf)
```
创建动画实例。

**参数:**
- `aid`: 动画标识符
- `swf`: SWF实例

**Play**
```csharp
void Play()
```
开始播放动画。

**Pause**
```csharp
void Pause()
```
暂停动画播放。

**Continue**
```csharp
void Continue()
```
继续播放暂停的动画。

**Stop**
```csharp
void Stop()
```
停止动画播放。

**PauseAndPlayChildren**
```csharp
void PauseAndPlayChildren()
```
暂停当前动画并播放子动画。

#### 使用示例
```csharp
// 创建动画实例
// 可以：
//var aid = _swf.CreateAnimAid(256);
//var anim = new SwfAnimation(aid, _swf);
// 也可以：
var anim = _swf.CreateAnim(256); // 推荐

// 设置属性
anim.Transform = Transform2D.Identity.Scaled(new Vector2(0.5f, 0.5f));
anim.Color = new Color(1, 0.8f, 0.8f);
anim.SpeedScale = 1.5f;
anim.IsLooping = true;

// 播放控制
anim.AnimationFinished += OnAnimationFinished;
// 信号要绑定在Play前面，避免因为动画太短而导致事件未触发
anim.Play();


// 状态查询
// 注意：IsFinished是只读属性，不能赋值，不推荐每帧都查询
if (anim.IsFinished)
{
    GD.Print("动画已完成");
}
```

### 性能提示
1. **直接使用C# API**
```csharp
// 推荐：直接使用C# API，性能最佳
var anim = _swf.CreateAnim(256);
```

2. **事件处理**
```csharp
// 正确的事件订阅
anim.AnimationFinished += OnFinished;
// 及时取消订阅
anim.AnimationFinished -= OnFinished;
```

3. **资源管理**
```csharp
// 及时停止不再需要的动画
anim.Stop();
```
