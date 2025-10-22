## 如何快速安装
此插件使用C#编写，同时，为了兼容gds生态，我也设计了gds的birdge类进行简化调用。

### 1. 安装Godot

- 1. 安装Godot引擎，版本要求为4.3及以上（推荐4.4.1）。要求是.Net版本。
- 2. 下载.Net运行时，版本要求为8.0及以上。（推荐8.0）



### 2. 下载插件


### 3. 安装插件

下载完成后，将插件文件夹解压到Godot项目的根目录下。你的项目结构应该看起来像这样：

```
project_root/
├── Godot-SWF/
│   ├── bridge/
│   |   ├── GodotSwfNetBridge.cs
│   |   └── godot_swf_gds_bridge.gdscript
│   ├── dlls/
│   │   ├── Godot SWF.dll
│   │   └── xxx.dll
│   └── shaders/
│       ├── xxx.gdshader
│       └── xxx.gdshaderinc
├── project.csproj
├── project.godot
└── project.sln
```

### 4.改写项目配置文件

打开项目配置文件project.csproj

如果您的项目中没有project.godot文件，请点击标题栏中的项目，然后依次点击工具->C#->Create C# solution。

如下图所示
![21223](https://www.freeimg.cn/uploads/486/68f79aa765339.png)

开启aot编译
```xml
<PublishAot>true</PublishAot>
```

加入以下引用：

```xml
<Reference Include="Clipper2Lib">
    <HintPath>Godot-SWF/dlls/Clipper2Lib.dll</HintPath>
</Reference>
<Reference Include="Godot SWF">
    <HintPath>Godot-SWF/dlls/Godot SWF.dll</HintPath>
</Reference>
<Reference Include="LibTessDotNet">
    <HintPath>Godot-SWF/dlls/LibTessDotNet.dll</HintPath>
</Reference>
<Reference Include="LibTessDotNet.Double">
    <HintPath>Godot-SWF/dlls/LibTessDotNet.Double.dll</HintPath>
</Reference>
<Reference Include="SharpCompress">
    <HintPath>Godot-SWF/dlls/SharpCompress.dll</HintPath>
</Reference>
<Reference Include="SwfLib">
    <HintPath>Godot-SWF/dlls/SwfLib.dll</HintPath>
</Reference>
```

为了避免裁剪程序集，请在添加以下内容：

```xml
<TrimmerRootAssembly Include="GodotSharp" />
<TrimmerRootAssembly Include="Clipper2Lib" />
<TrimmerRootAssembly Include="Godot SWF" />
<TrimmerRootAssembly Include="LibTessDotNet" />
<TrimmerRootAssembly Include="LibTessDotNet.Double" />
<TrimmerRootAssembly Include="SharpCompress" />
<TrimmerRootAssembly Include="SwfLib" />
<TrimmerRootAssembly Include="$(TargetName)" />
```

现在，项目配置文件应该看起来像这样：

```xml
<Project Sdk="Godot.NET.Sdk/4.4.1">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <EnableDynamicLoading>true</EnableDynamicLoading>
    <RootNamespace>YourProjectNamespace</RootNamespace>
	<EnableDynamicLoading>true</EnableDynamicLoading>
	<PublishAot>true</PublishAot>
  </PropertyGroup>
  <ItemGroup>
    <TrimmerRootAssembly Include="GodotSharp" />
    <TrimmerRootAssembly Include="Clipper2Lib" />
    <TrimmerRootAssembly Include="Godot SWF" />
    <TrimmerRootAssembly Include="LibTessDotNet" />
    <TrimmerRootAssembly Include="LibTessDotNet.Double" />
    <TrimmerRootAssembly Include="SharpCompress" />
    <TrimmerRootAssembly Include="SwfLib" />
    <TrimmerRootAssembly Include="$(TargetName)" />
	
    <Reference Include="Clipper2Lib">
      <HintPath>Godot-SWF/dlls/Clipper2Lib.dll</HintPath>
    </Reference>
    <Reference Include="Godot SWF">
      <HintPath>Godot-SWF/dlls/Godot SWF.dll</HintPath>
    </Reference>
    <Reference Include="LibTessDotNet">
      <HintPath>Godot-SWF/dlls/LibTessDotNet.dll</HintPath>
    </Reference>
    <Reference Include="LibTessDotNet.Double">
      <HintPath>Godot-SWF/dlls/LibTessDotNet.Double.dll</HintPath>
    </Reference>
    <Reference Include="SharpCompress">
      <HintPath>Godot-SWF/dlls/SharpCompress.dll</HintPath>
    </Reference>
    <Reference Include="SwfLib">
      <HintPath>Godot-SWF/dlls/SwfLib.dll</HintPath>
    </Reference>
  </ItemGroup>
</Project>
```

### 5. 完成

完成以上步骤后，您应该可以正常使用Godot-SWF插件了。您可以查看文档中的示例代码和api说明。


