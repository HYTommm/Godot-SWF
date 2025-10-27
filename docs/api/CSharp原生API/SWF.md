# C# 原生 API

## SWF 类

### 事件

**AnimationFinished**
```csharp
public delegate void AnimationFinishedEventHandler(Aid aid);
public AnimationFinishedEventHandler AnimationFinished;
```
当动画播放完成时触发。参数为完成的动画标识符。（此事件仅用于内部使用，普通用户请使用[SwfAnimation](.\SwfAnimation.md)的[AnimationFinished](.\SwfAnimation.md#animationfinished)事件代替）

**ParseFinished**
```csharp
public delegate void ParseFinishedEventHandler();
public ParseFinishedEventHandler ParseFinished;
```
当 SWF 文件解析完成时触发。

### 构造函数
```csharp
SWF(string swfPath, CanvasItem root)
```
- `swfPath`: SWF 文件路径
- `root`: 渲染根节点

### 方法

**Parse**
```csharp
void Parse()
```
**开始** 解析 SWF 文件，必须在播放前调用。请注意，此方法是异步的，调用后会立即返回，但解析可能需要几秒钟时间。请在 `ParseFinished` 事件（信号）中处理解析完成后的逻辑。

**CreateAnim**
```csharp
public SwfAnimation CreateAnim(int shapeID, float playScale = 1, Transform2D transform = default, Color color = default, bool loop = false)
```
创建动画，返回[SwfAnimation](.\SwfAnimation.md)实例。可以指定播放速度、变换矩阵、颜色、是否循环播放。

**StopAll**
```csharp
void StopAll()
```
停止所有动画。

**CreateAnimAid**
```csharp
Aid CreateAnimAid(int shapeID, float playScale = 1, Transform2D transform = default, Color color = default, bool loop = false)
```
创建动画，返回动画标识符。可以指定播放速度、变换矩阵、颜色、是否循环播放。此方法为内部方法，建议使用 `CreateAnim` 方法创建动画。


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


### 内部方法

因桥接类需要调用所以会向外暴露，普通用户不建议调用，相应功能在[SwfAnimation](.\SwfAnimation.md)中均有实现

**PlayAnim**
```csharp
void PlayAnim(Aid aid)
```
播放指定动画。

**Stop**
```csharp
void Stop(Aid aid)
```
停止指定动画。

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

