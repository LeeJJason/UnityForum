# Built-in serialization
Unity的某些内置功能会自动使用序列化。这些概述如下。

有关更多信息，请参见[脚本序列化](https://docs.unity3d.com/Manual/script-Serialization.html)文档。

## Saving and loading
Unity使用序列化来从硬盘中加载和保存Scenes, Assets, 和 AssetBundles。这包括保存在您自己的脚本API对象（例如MonoBehaviour组件和ScriptableObjects）中的数据。

这发生在编辑器的播放模式和编辑模式下。

### Inspector window
当您在Inspector窗口中查看或更改GameObject的component字段的值时，Unity会序列化此数据，然后将其显示在Inspector中窗口。当Inspector窗口显示字段值时，它不与Unity Scripting API通信。如果您在脚本中使用属性，则当您在Inspector窗口中查看或更改值时，绝不会调用任何属性getter和setter，因为Unity会直接序列化Inspector窗口字段。

### Reloading scripts in the Unity Editor
当您更改并保存脚本时，Unity会重新加载所有当前加载的脚本数据。它首先将所有可序列化的变量存储在所有已加载的脚本中，并在加载脚本后将其还原。重新加载脚本后，所有无法序列化的数据都会丢失。

这会影响所有“编辑器”窗口以及项目中的所有MonoBehaviours。与Unity中的其他序列化情况不同，私有字段在重新加载时会默认进行序列化，即使它们没有'SerializeField'属性。

### Prefabs
在序列化的背景下，预制件是一个或多个GameObjects和组件的序列化数据。 Prefab实例包含对Prefab源的引用以及对其的修改列表。修改是Unity需要对Prefab源进行的操作，以创建特定的Prefab实例。

只有在Unity编辑器中编辑项目时，Prefab实例才存在。在项目构建期间，Unity编辑器会从其两组序列化数据实例化GameObject：Prefab源和Prefab实例的修改。

### Instantiation
当您对场景中存在的任何事物（例如，Prefab或GameObjects）调用实例化时，Unity会对其进行序列化。这在运行时和编辑器中都会发生。从UnityEngine.Object派生的所有内容都可以序列化。

然后，Unity创建一个新的GameObject并将数据反序列化到新的GameObject上。接下来，Unity在另一个变体中运行相同的序列化代码，以报告正在引用哪些其他UnityEngine.Objects。它检查所有引用的UnityEngine.Objects，以查看它们是否是实例化数据的一部分。如果引用指向诸如纹理之类的“外部”对象，则Unity会保持该引用不变。如果引用指向某个“内部”东西，例如子GameObject，则Unity会将引用修补到相应的副本。

### Unloading unused assets
**Resource.GarbageCollectSharedAssets（）**是本机Unity垃圾收集器，它执行的功能与标准C＃垃圾收集器不同。它在加载场景并检查不再引用的对象（例如纹理）并安全地卸载它们之后运行。本机Unity垃圾收集器以一种变体形式运行序列化程序，其中对象将所有对外部UnityEngine.Objects的引用报告给您。这就是在下一个场景中卸载一个场景使用的纹理的方式。