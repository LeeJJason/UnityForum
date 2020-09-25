## 2D Sorting

### Overview
Unity根据渲染器的类型和用途对其优先级进行排序。您可以通过渲染器的[渲染队列](https://docs.unity3d.com/Manual/SL-SubShaderTags.html)指定渲染器的渲染顺序。通常，有两个主要队列：[不透明队列](https://docs.unity3d.com/ScriptReference/Rendering.RenderQueue.Geometry.html)和[透明队列](https://docs.unity3d.com/ScriptReference/Rendering.RenderQueue.Transparent.html)。2D渲染器主要位于“透明”队列中，并且包括 [Sprite Renderer](https://docs.unity3d.com/Manual/class-SpriteRenderer.html), [Tilemap Renderer](https://docs.unity3d.com/Manual/class-TilemapRenderer.html), 和 [Sprite Shape Renderer](https://docs.unity3d.com/ScriptReference/Experimental.U2D.SpriteShapeRenderer.html) 类型。

### Transparent Queue Sorting Order by Priority
透明队列中的2D渲染器通常遵循以下优先级顺序：
1.  [Sorting Layer and Order in Layer](#sorting-layer-and-order-in-layer)
2.  [Specify Render Queue](#specify-render-queue)
3.  [Distance to Camera](#distance-to-camera)
    * [Perspective](#perspective)/[Orthographic](#orthographic)
    * [Custom Axis sort mode](#custom-axis-sort-mode)
    * [Sprite Sort Point](#sprite-sort-point)
4.  [Sorting Group](#sorting-group)
5.  [Material/Shader](#materialshader)
6.  A [Tiebreaker](#tiebreaker) 发生在多个渲染器具有相同优先级。

还有其他因素可能导致排序顺序与常规优先级顺序不同。这些因素因项目而异。

#### Sorting Layer and Order in Layer
[Sorting Layer](https://docs.unity3d.com/Manual/class-TagManager.html#SortingLayers) 和 **Order in Layer**（Renderer的 **Property** 设置）对于所有的2D Renderers 可通过 **Inspector window** 或者 Unity Scripting API 使用。设置 Renderer 现有的 **Sorting Layer** 或者创建一个新 **Sorting Layer** 以确定其在渲染队列中优先级。更改 **Order in Layer** 的值，以设置同一 **Sorting Layer** 中其他渲染器之间的渲染器优先级。

#### Specify Render Queue
您可以在其 **Material** 或 **Shader** 设置中指定“渲染器”的 **Render Queue** 类型。这对于使用不同材质的渲染器进行分组和排序很有用。有关更多详细信息，请参见[ShaderLab：SubShader](https://docs.unity3d.com/Manual/SL-SubShaderTags.html)标签上的文档。

#### Distance to Camera
该[相机组件](https://docs.unity3d.com/Manual/class-Camera.html)排序基于其渲染投影环境。这两个选项是 **Perspective** 和 **Orthographic。**
##### Perspective
在此模式下，渲染器的排序距离是渲染器与“ 相机的位置的直接距离。
##### Orthographic
渲染器的排序距离是指渲染器的位置与相机沿相机朝向的距离。对于默认的2D设置，该设置沿（0，0，1）轴。

当您将Camera组件设置为 **Perspective** 或 **Orthoographic** 时， Unity会自动将Camera的[TransparencySortMode设置](https://docs.unity3d.com/ScriptReference/TransparencySortMode.html)为与所选模式匹配。您可以通过两种方式手动设置透明胶片排序模式：
* 打开 **Project Settings** 并转到 [图形](https://docs.unity3d.com/Manual/class-GraphicsSettings.html#Camera)，然后在 **Camera Settings** 下使用“透明排序模式”
* 通过脚本API 设置相机的TransparencySortMode。

相机 **Transparency Sort Mode** 设置位于**Project Settings**中的 **Graphics** 类别下（主菜单： **Edit > Project Settings > Graphics**）。将此设置为**Default**时，将优先使用Camera组件的Projection设置。当将其设置为**Default**以外的其他选项时，Camera组件的Projection设置保持不变，但是Camera的Transparency Sort Mode更改为该选项。

[自定义轴排序模式](https://docs.unity3d.com/ScriptReference/TransparencySortMode.CustomAxis.html)是通过Project设置和Scripting API提供的另一个选项。
#####  Custom Axis sort mode
选择此模式可以根据渲染器沿您在“项目”设置（主菜单：**Edit > Projecting Settings > Graphics > Transparency Sort Axis**）中设置的自定义轴上的距离对渲染器进行排序。这通常在带有[等距图块贴图](https://docs.unity3d.com/Manual/Tilemap-Isometric.html)的项目中使用，以在[图块贴图](https://docs.unity3d.com/Manual/Tilemap-Isometric.html)上正确排序和渲染图块精灵。有关更多信息，请参考[创建等轴测图](https://docs.unity3d.com/Manual/Tilemap-Isometric-CreateIso.html)。

#####  Sprite Sort Point
默认情况下，一个**Sprite**中的[排序点](https://docs.unity3d.com/Manual/class-SpriteRenderer.html#sortpoint)设置为**中心**，而Unity会测量相机的“变换”位置与“精灵”中心之间的距离，以确定排序期间的渲染顺序。另一种选择是将“精灵”的“ 排序点”设置为其在“世界空间”中的“ 透视”位置。在Sprite的[Sprite Renderer](../Sprites/SpriteRender/README.md)中选择“ Pivot”选项属性设置，并在[Sprite Editor](../Sprites/SpriteEditor/README.md)中编辑Sprite的Pivot位置。

#### Sorting Group
[Sorting Group](../Sprites/SpriteGroups/README.md)是一个组件，其一起共享一个公共根排序目的组渲染器。同一排序组中的所有渲染器共享相同的**Sorting Layer**, **Order in Layer**, 和 **Distance to Camera**。有关更多详细信息，请参阅有关[Sorting Group](../Sprites/SpriteGroups/README.md)组件及其设置的文档。

#### Material/Shader
Unity将具有相同[Material settings](https://docs.unity3d.com/Manual/Shaders.html)的渲染器分类在一起，以提高渲染性能，例如使用[动态批处理](https://docs.unity3d.com/Manual/DrawCallBatching.html)。

#### Tiebreaker
当多个渲染器具有相同的排序优先级时，决胜局是Unity将渲染器放入渲染队列的顺序。由于这是您无法控制的内部过程，因此应使用排序选项（例如**Sorting Layers**和**Sorting Groups**）来确保所有Renderer具有不同的排序优先级。