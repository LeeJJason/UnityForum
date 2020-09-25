## Sprites
**Sprites**是2D图形对象。如果您习惯于3D工作，则**Sprite**本质上只是标准纹理，但是有一些特殊的技术可以组合和管理Sprite纹理，从而在开发过程中提高效率和便利性。

Unity提供了一个占位符[Sprite Creator](SpriteCreator/README.md)，一个内置的 [Sprite Editor](SpriteEditor/README.md)，一个[Sprite Renderer](SpriteRender/README.md)和 [Sprite Packer](SpriteAtlas/LegacySpritePacker/README.md)。

有关在Unity项目中将资产设置为Sprite的信息，请参见下面的导入和设置Sprite。

### Sprite Tools
#### Sprite Creator
使用[Sprite Creator](SpriteCreator/README.md)在项目中创建占位符sprite，因此您可以继续进行开发而不必获取或等待图形。

#### Sprite Editor
使用Sprite Editor，您可以从较大的图像中提取Sprite图形，并在图像编辑器中的单个纹理内编辑大量组件图像。例如，您可以使用此功能将角色的手臂，腿部和身体作为一个单独的元素保留在一个图像中。


#### Sprite Renderer
Sprite是使用[Sprite Renderer](SpriteRender/README.md)组件而不是与3D对象一起使用[Mesh Renderer](https://docs.unity3d.com/Manual/class-MeshRenderer.html)渲染。使用它以Sprites形式显示图像以用于2D和3D 场景。

#### Sprite Packer
使用[Sprite Packer](SpriteAtlas/LegacySpritePacker/README.md)可以优化项目对视频内存的使用和性能。

## Importing and Setting Up Sprites
**Sprites** 是Unity工程中的一种资源。 你可以通过 Project view 看到他们并使用。

有两种将Sprites引入项目的方法：
1. 在计算机的Finder（Mac OS X）或File Explorer（Windows）中，将图像直接放置在Unity Project的**Assets**文件夹中。Unity会检测到此情况并将其显示在项目的 **Project** 视图中。
2. 在Unity中，**Assets > Import New Asset**以打开计算机的Finder（Mac OS X）或文件资源管理器（Windows）。从那里选择所需的图像，然后Unity将其放入**Project**视图中。

有关此细节以及有关组织Assets文件夹的重要信息，请参阅[导入](https://docs.unity3d.com/Manual/ImportingAssets.html)。

### Setting your Image as a Sprite
如果项目模式设置为2D，则导入的图像将自动设置为Sprite。有关将项目模式设置为2D的详细信息，请参见[2D或3D项目](https://docs.unity3d.com/Manual/2Dor3D.html)。

但是，如果您的项目模式设置为3D，则图像将设置为 **Texture**，因此您需要更改资产的 **Texture Type**：
1. 单击资产以查看其导入检查器。
2. 将纹理类型设置为Sprite（2D and UI）：
![](TextureTypeSprite.png)
*在素材资源检查器中将“纹理类型”设置为Sprite（2D and UI)*

有关 Sprite **Texture Type**设置的详细信息，请参见纹理类型：[Sprite（2D and UI）](https://docs.unity3d.com/Manual/TextureTypes.html#Sprite)。

## Sorting Sprites
Unity中的渲染器按几个条件排序，例如其图层顺序或与摄影机的距离。Unity的GraphicsSettings（菜单：**Edit > Project Settings**，然后选择 **Graphics** 类别。)提供一个称为 **Transparency Sort Mode** 的设置，该设置使您可以控制精灵的排序方式，具体取决于它们相对于摄影机的位置。更具体地说，它使用Sprite在轴上的位置来确定哪些相对于其他透明，哪些不透明。

一个示例是当沿Y轴对精灵进行排序。这在2D游戏中非常常见，在2D游戏中，较高的Sprite排在较低的Sprite后面，以使它们看起来更远。
![](AxisDistanceSort2.png)

共有四个透明度排序模式选项：  
![](AxisDistanceSort.png)
* Default - 排序基于**Camera Projection**模式设置为**Perspective**或**Orthographic**
* Perspective - 根据透视图排序。透视图基于从相机位置到精灵中心的距离对精灵进行排序。
* Orthographic -基于正交视图进行排序。正交视图基于沿视图方向的距离对Sprite进行排序。
* Custom Axis -根据“透明度排序轴”中设置的给定轴进行排序

如果将**Transparency Sort Mode**设置为 **Custom**，则需要设置**Transparency Sort Axis**：  
![](AxisDistanceSort1.png)  
如果 **Transparency Sort Mode** 设置为 **Custom Axis**，则**Scene view**中
渲染器是根据该轴距相机的距离排序的。使用介于–1和1之间的值来定义轴。例如：X = 0，Y = 1，Z = 0将轴方向设置为向上。X = 1，Y = 1，Z = 0将轴设置为X和Y之间的对角线方向。

例如，如果您要让Sprites像上图所示（在y轴上位于轴上较低的Sprites后面的那些），将透明度排序模式设置为Custom Axis，然后将Y值设置为的透明度排序轴的值大于0。

## Sorting Sprites using script

您还可以通过脚本对每个摄像机的Sprite进行排序，方法是在Camera中修改以下属性：
* [TransparencySortMode](https://docs.unity3d.com/ScriptReference/Camera-transparencySortMode.html)（对应 **Transparency Sort Mode**）
* [TransparencySortAxis](https://docs.unity3d.com/ScriptReference/Camera-transparencySortAxis.html)（对应 **Transparency Sort Axis**）

例如：

```cs
    var camera = GetComponent<Camera>();

    camera.transparencySortMode = TransparencySortMode.CustomAxis;

    camera.transparencySortAxis = new Vector3(0.0f, 1.0f, 0.0f);
```