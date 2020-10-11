# Special folder names
通常，您可以为创建用来组织Unity项目的文件夹选择所需的任何名称。但是，Unity会将许多文件夹名称解释为一种指令，指示应以特殊方式处理该文件夹的内容。例如，您必须放置编辑器脚本在名为Editor的文件夹中，以使其正常工作。

该页面包含Unity使用的特殊文件夹名称的完整列表。

## Assets
**Assets**文件夹是包含Unity项目使用的资产的主文件夹。项目窗口的内容在编辑器中直接对应于Assets文件夹的内容。大多数API函数都假定所有内容都位于Assets文件夹中，因此不需要明确提及。但是，某些功能确实需要将Assets文件夹作为路径名的一部分包括在内（例如，AssetDatabase类中的某些功能）。

## Editor
放置在名为Editor的文件夹中的脚本被视为Editor脚本，而不是运行时脚本。这些脚本在开发过程中向编辑器添加了功能，在运行时的构建中不可用。

您可以在Assets文件夹内的任何位置放置多个Editor文件夹。将您的编辑器脚本放在Editor文件夹或其中的子文件夹中。

编辑器文件夹的确切位置会影响其脚本相对于其他脚本的编译时间（有关此操作的完整说明，请参阅[特殊文件夹和脚本编译顺序](https://docs.unity3d.com/Manual/ScriptCompileOrderFolders.html)上的文档）。在编辑器脚本中使用[EditorGUIUtility.Load](https://docs.unity3d.com/ScriptReference/EditorGUIUtility.Load.html)函数可从Editor文件夹中的Resources文件夹加载Assets。这些资产只能通过编辑器脚本加载，并从构建中剥离。

**注意**：Unity不允许位于“编辑器”文件夹中继承MonoBehaviour的组件的脚本赋值给GameObjects。

## Editor Default Resources
编辑器脚本可以使用通过[EditorGUIUtility.Load](https://docs.unity3d.com/ScriptReference/EditorGUIUtility.Load.html)函数按需加载的Asset文件。此函数在名为**Editor Default Resources**的文件夹中查找资产文件。

您只能有一个Editor Default Resources文件夹，并且必须将其放置在Project的根目录中。直接在Assets文件夹中。将所需的Asset文件放置在此Editor Default Resources文件夹中或其中的一个子文件夹中。如果您的Asset文件位于子文件夹中，请始终在传递给EditorGUIUtility.Load函数的路径中包含子文件夹路径。

## Gizmos
[Gizmos](https://docs.unity3d.com/ScriptReference/Gizmos.html)允许您将图形添加到场景中视图有助于可视化设计细节，而这些细节在其他情况下是不可见的。 [Gizmos.DrawIcon](https://docs.unity3d.com/ScriptReference/Gizmos.DrawIcon.html)函数在场景中放置一个图标，以充当特殊对象或特定位置的标记。您必须将用于绘制此图标的图像文件放置在名为Gizmos的文件夹中为了通过DrawIcon函数定位它。

您只能有一个Gizmos文件夹，并且必须将其放置在Project的根目录中。直接在Assets文件夹中。将所需的资产文件放在此Gizmos文件夹或其中的子文件夹中。如果您的资产文件位于子文件夹中，请始终在传递给Gizmos.DrawIcon函数的路径中包括子文件夹路径。

## Resources
您可以从脚本中按需加载资产，而不是在场景中创建资产实例以用于游戏。您可以通过将Assets放置在名为Resources的文件夹中来进行此操作。通过使用[Resources.Load](https://docs.unity3d.com/ScriptReference/Resources.Load.html)函数来加载这些资产。

您可以在Assets文件夹内的任何位置放置多个Resources文件夹。将所需的Asset文件放置在Resources文件夹或其中的子文件夹中。如果您的Asset文件位于子文件夹中，请始终在传递给Resources.Load函数的路径中包括子文件夹路径。

请注意，如果“资源”文件夹是“编辑器”子文件夹，则可以从“编辑器”脚本中加载其中的资产，但从构建中删除它们。

## Standard Assets
导入标准资产包时（菜单：**Assets > Import Package**），资产将放置在名为**Standard Assets**的文件夹中。这些文件夹除了包含资产外，还对脚本编译顺序有影响。有关更多详细信息，请参见[特殊文件夹和脚本编译顺序](https://docs.unity3d.com/Manual/ScriptCompileOrderFolders.html)页面。

您只能有一个Standard Assets文件夹，并且必须将其放置在Project的根目录中。直接在Assets文件夹中。将所需的资产文件放置在此“标准资产”文件夹或其中的子文件夹中。

## StreamingAssets
您可能希望资产以其原始格式作为单独的文件提供，尽管直接将资产合并到构建中更为普遍。例如，您需要从文件系统访问视频文件，而不是将其用作MovieTexture在iOS上播放该视频。将文件放置在名为StreamingAssets的文件夹中，以便将其原样复制到目标计算机，然后可以从特定文件夹使用该文件。有关更多详细信息，请参见关于流资产的页面。

您只能有一个StreamingAssets文件夹，并且必须将其放置在Project的根目录中。直接在Assets文件夹中。将所需的资产文件放置在此StreamingAssets文件夹或其中的子文件夹中。如果您的资产文件位于子文件夹中，请始终在用于引用流式资产的路径中包括子文件夹路径。

## Hidden Assets
在导入过程中，Unity会完全忽略Assets文件夹（或其中的子文件夹）中的以下文件和文件夹：
* Hidden folders.
* Files and folders which start with ‘.’.
* Files and folders which end with ‘~’.
* Files and folders named cvs.
* Files with the extension .tmp.

这用于防止导入由操作系统或其他应用程序创建的特殊和临时文件。