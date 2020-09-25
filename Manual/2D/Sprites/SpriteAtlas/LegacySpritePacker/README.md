## (Legacy) Sprite Packer
在设计sprite图形时，为每个角色使用单独的纹理文件是很方便的。然而，sprite纹理的很大一部分通常会被图形元素之间的空白空间所占用，而这些空间在运行时将导致视频内存的浪费。为了获得最佳性能，最好将几个精灵纹理的图形紧紧地打包在一个纹理中，这个纹理被称为地图集。Unity提供了一个**Sprite Packer**实用工具，自动处理从单独的Sprite纹理生成图集的过程。

Unity在幕后处理sprite atlas纹理的生成和使用，因此用户不需要手工分配。可以选择在进入播放模式或构建过程中打包图集，一旦生成图集，sprite对象的图形将从图集获得。

要求用户在纹理导入器中指定 Packing Tag，以启用打包该纹理的精灵。

### Using the Sprite Packer
Sprite Packer默认是禁用的，但你可以从编辑器窗口配置它(菜单:**Edit > Project Settings**，然后选择**Editor**类别)。sprite打包模式可以从禁用改为启用(打包用于构建，但不用于播放模式)或总是启用(打包同时用于播放模式和构建)。

如果打开Sprite Packer窗口（菜单：**Window > 2D > Sprite Packer**），然后单击左上角的 **Pack** 按钮，您将看到地图集中打包的纹理的排列。  
![](SpritePackerMain.jpg)  

如果您在项目面板中选择一个精灵，它也会被高亮显示，以显示它在地图集中的位置。轮廓实际上是渲染网格轮廓，它也定义了用于紧密填充的区域。

Sprite Packer窗口顶部的工具栏有许多控件，可以影响打包和查看。**Pack**按钮启动打包操作，但不会强制任何更新，如果地图集自上次包装以来没有变化。(当你执行自定义包装策略时，相关的**Repack**按钮将出现，如下面的**Customizing the Sprite Packer**中所述)。**View Atlas**和**Page #**菜单允许您选择在窗口中显示地图集的哪个页面(如果没有足够的空间让所有的精灵在最大的纹理大小中显示，单个地图集可能被分割成多个“页面”)。页码旁边的菜单选择哪个“包装策略”用于地图集(参见下面)。在工具栏的右侧是两个控件，用于缩放视图和切换颜色和阿尔法显示地图集。

### Packing Policy
Sprite Packer使用**packing policy**来决定如何将Sprite分配到atlases。你可以创建自己的包装政策(见下文)，但默认的**Default Packer Policy**，**Tight Packer Policy** 和**Tight Rotate Enabled Sprite Packer Policy**选项总是可用的。使用这些策略，纹理导入器中的**Packing Tag**属性将直接选择精灵将被包装的图集的名称，因此，所有带有相同包装标签的精灵将被包装在同一个图集中。然后根据纹理导入设置对Atlases进行进一步排序，以便它们与用户为源纹理设置的任何设置匹配。具有相同纹理压缩的精灵设置将在可能的情况下分组为相同的图集。
* 默认情况下，除非在**Packing Tag**中指定了“ [TIGHT]”，否则DefaultPackerPolicy将默认使用矩形包装（即，将包装标签设置为 “[TIGHT]Character” 将允许紧密包装）。
* 如果Sprite具有紧密网格，则默认情况下，TightPackerPolicy将使用紧密打包。如果在**Packing Tag**中指定了“[RECT]” ，则将完成矩形包装（即，将包装标签设置为 “[RECT]UI_Elements” 将强制进行矩形包装）。
* 如果Sprite具有紧密网格，则TightRotateEnabledSpritePackerPolicy默认情况下将使用紧密打包，并将启用精灵旋转。如果在包装标签中指定了“ [RECT]” ，则将完成矩形包装（即，将包装标签设置为“ [RECT] UI_Elements”将强制进行矩形包装）。

### Customizing the Sprite Packer
该**DefaultPackerPolicy**选项足以满足大多数目的，但你也可以实现自己的定制包装的政策，如果你需要。为此，您需要为编辑器脚本中的类实现UnityEditor.Sprites.IPackerPolicy接口。此接口需要以下方法：
* GetVersion-返回打包程序策略的版本值。如果对策略脚本进行了修改，并且该策略已保存到版本控制中。
* OnGroupAtlases-在这里实现您的打包逻辑。在PackerJob上定义地图集，并从给定的TextureImporters分配Sprite。

默认情况下，DefaultPackerPolicy使用rect打包（请参阅SpritePackingMode）。如果您正在执行纹理空间效果，或者想使用其他网格物体进行渲染则此功能非常有用 Sprite。自定义策略可以覆盖此设置，而代替紧凑包装。
* 仅当选择了自定义策略时，“重新打包”按钮才启用。
    * 除非TextureImporter元数据或所选PackerPolicy版本值更改，否则不会调用OnGroupAtlases。
    * 在使用自定义策略时，请使用“重新打包”按钮。
* 可以使用TightRotateEnabledSpritePackerPolicy自动打包旋转精灵。
    * SpritePackingRotation是将来的Unity版本的保留类型。


### Other
* Atlases被缓存在 Project\Library\AtlasCache。
    * 删除此文件夹，然后启动Unity将强制重新打包地图集。这样做时必须关闭Unity。
* 启动时未加载Atlas缓存。
    * 重新启动Unity后首次打包时，必须检查所有纹理。此操作可能需要一些时间，具体取决于项目中纹理的总数。
    * 仅加载所需的地图集。
* 默认最大图集大小为2048x2048。
* 设置PackingTag时，不会压缩Texture，以便SpritePacker可以捕获原始像素值，然后在图集上进行压缩。


### DefaultPackerPolicy
```cs
using System;
using System.Linq;
using UnityEngine;
using UnityEditor;
using System.Collections.Generic;


public class DefaultPackerPolicySample : UnityEditor.Sprites.IPackerPolicy
{
    protected class Entry
    {
        public Sprite            sprite;
        public UnityEditor.Sprites.AtlasSettings settings;
        public string            atlasName;
        public SpritePackingMode packingMode;
        public int               anisoLevel;
    }

    private const uint kDefaultPaddingPower = 3; // Good for base and two mip levels.

    public virtual int GetVersion() { return 1; }
    protected virtual string TagPrefix { get { return "[TIGHT]"; } }
    protected virtual bool AllowTightWhenTagged { get { return true; } }
    protected virtual bool AllowRotationFlipping { get { return false; } }

    public static bool IsCompressedFormat(TextureFormat fmt)
    {
        if (fmt >= TextureFormat.DXT1 && fmt <= TextureFormat.DXT5)
            return true;
        if (fmt >= TextureFormat.DXT1Crunched && fmt <= TextureFormat.DXT5Crunched)
            return true;
        if (fmt >= TextureFormat.PVRTC_RGB2 && fmt <= TextureFormat.PVRTC_RGBA4)
            return true;
        if (fmt == TextureFormat.ETC_RGB4)
            return true;
        if (fmt >= TextureFormat.ATC_RGB4 && fmt <= TextureFormat.ATC_RGBA8)
            return true;
        if (fmt >= TextureFormat.EAC_R && fmt <= TextureFormat.EAC_RG_SIGNED)
            return true;
        if (fmt >= TextureFormat.ETC2_RGB && fmt <= TextureFormat.ETC2_RGBA8)
            return true;
        if (fmt >= TextureFormat.ASTC_RGB_4x4 && fmt <= TextureFormat.ASTC_RGBA_12x12)
            return true;
        if (fmt >= TextureFormat.DXT1Crunched && fmt <= TextureFormat.DXT5Crunched)
            return true;
        return false;
    }

    public void OnGroupAtlases(BuildTarget target, UnityEditor.Sprites.PackerJob job, int[] textureImporterInstanceIDs)
    {
        List<Entry> entries = new List<Entry>();

        foreach (int instanceID in textureImporterInstanceIDs)
        {
            TextureImporter ti = EditorUtility.InstanceIDToObject(instanceID) as TextureImporter;

            TextureFormat desiredFormat;
            ColorSpace colorSpace;
            int compressionQuality;
            ti.ReadTextureImportInstructions(target, out desiredFormat, out colorSpace, out compressionQuality);

            TextureImporterSettings tis = new TextureImporterSettings();
            ti.ReadTextureSettings(tis);

        Sprite[] sprites =
            AssetDatabase.LoadAllAssetRepresentationsAtPath(ti.assetPath)
                .Select(x => x as Sprite)
                .Where(x => x != null)
                .ToArray();
            foreach (Sprite sprite in sprites)
            {
                Entry entry = new Entry();
                entry.sprite = sprite;
                entry.settings.format = desiredFormat;
                entry.settings.colorSpace = colorSpace;
                // Use Compression Quality for Grouping later only for Compressed Formats. Otherwise leave it Empty.
            entry.settings.compressionQuality = IsCompressedFormat(desiredFormat) ? compressionQuality : 0;
            entry.settings.filterMode = Enum.IsDefined(typeof(FilterMode), ti.filterMode)
                ? ti.filterMode
                : FilterMode.Bilinear;
                entry.settings.maxWidth = 2048;
                entry.settings.maxHeight = 2048;
                entry.settings.generateMipMaps = ti.mipmapEnabled;
                entry.settings.enableRotation = AllowRotationFlipping;
                if (ti.mipmapEnabled)
                    entry.settings.paddingPower = kDefaultPaddingPower;
                else
                    entry.settings.paddingPower = (uint)EditorSettings.spritePackerPaddingPower;
                #if ENABLE_ANDROID_ATLAS_ETC1_COMPRESSION
                    entry.settings.allowsAlphaSplitting = ti.GetAllowsAlphaSplitting ();
                #endif //ENABLE_ANDROID_ATLAS_ETC1_COMPRESSION

                entry.atlasName = ParseAtlasName(ti.spritePackingTag);
                entry.packingMode = GetPackingMode(ti.spritePackingTag, tis.spriteMeshType);
                entry.anisoLevel = ti.anisoLevel;

                entries.Add(entry);
            }

            Resources.UnloadAsset(ti);
        }

        // First split sprites into groups based on atlas name
        var atlasGroups =
            from e in entries
            group e by e.atlasName;
        foreach (var atlasGroup in atlasGroups)
        {
            int page = 0;
            // Then split those groups into smaller groups based on texture settings
            var settingsGroups =
                from t in atlasGroup
                group t by t.settings;
            foreach (var settingsGroup in settingsGroups)
            {
                string atlasName = atlasGroup.Key;
                if (settingsGroups.Count() > 1)
                    atlasName += string.Format(" (Group {0})", page);

            UnityEditor.Sprites.AtlasSettings settings = settingsGroup.Key;
                settings.anisoLevel = 1;
                // Use the highest aniso level from all entries in this atlas
                if (settings.generateMipMaps)
                    foreach (Entry entry in settingsGroup)
                        if (entry.anisoLevel > settings.anisoLevel)
                            settings.anisoLevel = entry.anisoLevel;

                job.AddAtlas(atlasName, settings);
                foreach (Entry entry in settingsGroup)
                {
                    job.AssignToAtlas(atlasName, entry.sprite, entry.packingMode, SpritePackingRotation.None);
                }

                ++page;
            }
        }
    }

    protected bool IsTagPrefixed(string packingTag)
    {
        packingTag = packingTag.Trim();
        if (packingTag.Length < TagPrefix.Length)
            return false;
        return (packingTag.Substring(0, TagPrefix.Length) == TagPrefix);
    }

    private string ParseAtlasName(string packingTag)
    {
        string name = packingTag.Trim();
        if (IsTagPrefixed(name))
            name = name.Substring(TagPrefix.Length).Trim();
        return (name.Length == 0) ? "(unnamed)" : name;
    }

    private SpritePackingMode GetPackingMode(string packingTag, SpriteMeshType meshType)
    {
        if (meshType == SpriteMeshType.Tight)
            if (IsTagPrefixed(packingTag) == AllowTightWhenTagged)
                return SpritePackingMode.Tight;
        return SpritePackingMode.Rectangle;
    }
}
```

#### TightPackerPolicy
```cs
using System;
using System.Linq;
using UnityEngine;
using UnityEditor;
using UnityEditor.Sprites;
using System.Collections.Generic;

// TightPackerPolicy will tightly pack non-rectangle Sprites unless their packing tag contains "[RECT]".
class TightPackerPolicySample : DefaultPackerPolicySample
{
    protected override string TagPrefix { get { return "[RECT]"; } }
    protected override bool AllowTightWhenTagged { get { return false; } }
    protected override bool AllowRotationFlipping { get { return false; } }
}
```

#### TightRotateEnabledSpritePackerPolicy
```cs
using System;
using System.Linq;
using UnityEngine;
using UnityEditor;
using UnityEditor.Sprites;
using System.Collections.Generic;

// TightPackerPolicy will tightly pack non-rectangle Sprites unless their packing tag contains "[RECT]".
class TightRotateEnabledSpritePackerPolicySample : DefaultPackerPolicySample
{
    protected override string TagPrefix { get { return "[RECT]"; } }
    protected override bool AllowTightWhenTagged { get { return false; } }
    protected override bool AllowRotationFlipping { get { return true; } }
}
```