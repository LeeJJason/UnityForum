## Methods of distribution
你以后[准备要发布的 Sprite Atlases](../README.md)，有几种方法来发布Atlases。每种方法都更适合不同的项目需求或特征，例如Atlas资源的规模或项目的目标平台。

下面是发布Sprite时要考虑的两个主要方法：
1. 将Sprite Atlases放入构建的[Resources](https://docs.unity3d.com/Manual/SpecialFolders.html)文件夹中。
2. 将它们作为可下载的[AssetBundles](https://docs.unity3d.com/Manual/AssetBundles-Workflow.html)分发。

### Method A: Resources folder
Unity仍然可以在下载的最终版本中包含一些不大的Sprite地图集。要将它们与最终的构建版本一起发布，请将它们放在项目的资源文件夹中，其中包括用户下载应用程序时导出的构建版本中的资源文件夹中的Sprite地图集。然后Unity通过脚本从资源文件夹中加载这些地图集)。有关更多信息，请参阅参考资料API的文档。


### Method B: Downloadable AssetBundles
如果Unity将较大的Sprite地图集包含在导出的构建下载中，则会给应用程序用户带来不便，因为它们需要大量的带宽和设备存储空间。为了避免问题，将大型的精灵地图集以资源包的形式分发，这样玩家就可以根据自己的意愿去下载每个资源包。然后，Unity通过脚本在运行时加载Sprite地图集。有关更多信息，请参考AssetBundle工作流。