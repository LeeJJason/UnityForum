# [Text](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Text.html)
**Text**向用户显示非交互式文本。 这可用于为其他GUI控件提供标题或标签，或显示说明或其他文本。

## Properties
![](UI_TextInspector.png)

|Property:|Function:
|:--------|:---------
|Text|控件显示的文本。
|Character|
|Font|用于显示文本的字体。
|Font Style|应用于文本的样式。 选项包括*Normal*, *Bold*, *Italic* 和 *Bold And Italic*。
|Font Size|显示文字的大小。
|Line Spacing|文本行之间的垂直分隔。
|Rich Text|文本中的标记元素是否应解释为富文本样式？
|Paragraph|
|Alignment|文本的水平和垂直对齐方式。
|Align by Geometry|使用字形几何图形的范围执行水平对齐，而不是字形度量。
|Horizontal Overflow|该方法用于处理文本太宽而无法适合矩形的情况。 选项为环绕和溢出。
|Vertical Overflow|该方法用于处理包裹的文本过高而无法容纳在矩形中的情况。 选项为截断和溢出。
|Best Fit|Unity应该忽略大小属性，而只是尝试将文本适合控件的矩形吗？
|Color|用于呈现文本的颜色。
|Material|用于渲染文本的材料。

默认文本元素如下所示：  
![](UI_TextExample.png)

## Details
某些控件（例如[Buttons](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Button.html)和[Toggles](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Toggle.html)）具有内置的文本描述。 对于没有隐式文本的控件（例如[Sliders](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Slider.html)），可以使用由Text控件创建的标签来指示目的。 文本对于说明，故事文本，对话和法律免责声明列表也很有用。

文本控件为字体大小，样式等和文本对齐提供常用参数。 启用*Rich Text*选项时，文本中的标记元素将被视为样式信息，因此您只能使用粗体或其他颜色的单个单词或短节，例如（有关[Rich Texts](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/StyledText.html)的详细信息，请参阅 标记方案）。

## Hints
有关如何对文本应用简单的阴影或轮廓效果的信息，请参见[Effects](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/comp-UIEffects.html)页面。