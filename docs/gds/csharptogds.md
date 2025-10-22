## Godot Script (GDScript) 使用方案
本插件可以不使用C#调用，而使用原生的GDScript语言来编写Godot-SWF游戏。
### 快速使用
#### 1. 基本播放调用
代码如下:
```gdscript
var swf_animation_player = godot_swf_gds_bridge.new()
swf_animation_player.class_init()
swf_animation_player.load_swf("res://test.swf")
add_child(swf_animation_player)
swf_animation_player.parse()
swf_animation_player.scale /= 20.0
swf_animation_player.play(52)
```

### 运行前配置
1.下载解压Godot-SWF插件，确保桥接文件存在。

2.确保swf文件存在("res://test.swf")。

3.确保swf_animation_player.play(52)中的52是swf文件中动画的ID。

3.使用load——swf函数加载swf文件。swf_animation_player.class_init()函数是初始化函数，在使用之前须调用。