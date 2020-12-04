# [Event System](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-EventSystem.html)
该子系统负责控制构成事件的所有其他元素。 它协调哪个输入模块当前处于活动状态，哪个GameObject当前被视为“选定”，以及许多其他高级事件系统概念。

事件系统每次**Update**都会接到调用，浏览其输入模块，然后找出应该用于此订单号的输入模块。 然后将处理委托给模块。

## Properties
|Property:|Function:
|:--------|:---------
|First Selected|首先选择的GameObject。
|Send Navigation Events|EventSystem是否应允许导航事件（移动/提交/取消）。
|Drag Threshold|用于拖动像素的软区域。

在“属性”表下面是“添加默认输入模块”按钮。