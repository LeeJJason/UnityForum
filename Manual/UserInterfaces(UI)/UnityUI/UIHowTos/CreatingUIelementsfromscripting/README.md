# [Creating UI elements from scripting](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/HOWTO-UICreateFromScripting.html)
如果要创建动态UI，其中UI元素根据用户动作或游戏中的其他动作出现，消失或更改，则可能需要创建一个脚本，以基于自定义逻辑实例化新的UI元素。

## Creating a prefab of the UI element
为了能够轻松地动态实例化UI元素，第一步是为要实例化的UI元素类型创建预制。以您希望它在场景中显示的方式设置UI元素，然后将其拖动到“项目视图”中以使其成为预制件。

例如，用于按钮的预制件可以是具有图像组件和按钮组件的游戏对象，以及具有文本组件的子游戏对象。根据您的需要，您的设置可能会有所不同。

您可能想知道为什么我们没有API方法来创建各种类型的控件，包括视觉效果和所有内容。原因是有无数种方法，例如可以设置一个按钮。它使用图像，文字还是两者同时使用？甚至多张图片？文本字体，颜色，字体大小和对齐方式是什么？图像应使用哪种精灵？通过制作预制件并实例化它，可以完全按照所需的方式进行设置。而且，如果您以后要更改UI的外观，则只需更改预制件，然后它将反映在您的UI中，包括动态创建的UI。

## Instantiating the UI element
UI元素的预制件使用实例化方法按常规实例化。 设置实例化的UI元素的父级时，建议使用Transform.SetParent方法将worldPositionStays参数设置为false来执行此操作。

## Positioning the UI element
UI元素通常使用其Rect变换进行定位。如果UI元素是布局组的子级，它将被自动定位，并且可以跳过定位步骤。

放置Rect变换时，首先确定其是否具有拉伸行为非常有用。当anchorMin和anchorMax属性不相同时，会发生拉伸行为。

对于非拉伸Rect变换，通过设置anchoredPosition和sizeDelta属性可以最容易地设置位置。 anchoredPosition指定枢轴相对于锚点的位置。 sizeDelta与没有拉伸时的大小相同。

对于拉伸的Rect变换，使用offsetMin和offsetMax属性设置位置会更简单。 offsetMin属性指定rect的左下角相对于左下锚的角。 offsetMax属性指定相对于右上锚点的rect右上角的角。

## Customizing the UI Element
如果要动态实例化多个UI元素，则不太可能希望它们全部看起来相同并执行相同操作。 无论是菜单中的按钮，清单中的项目还是其他项目，您都可能希望各个项目具有不同的文本或图像，并在与之交互时执行不同的操作。

这是通过获取各种组件并更改其属性来完成的。 有关图像和文本组件，以及如何通过脚本使用UnityEvents，请参见脚本参考。