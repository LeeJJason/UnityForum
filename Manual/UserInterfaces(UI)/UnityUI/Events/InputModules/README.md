# Input Modules
输入模块是可以配置和定制事件系统的主要逻辑的地方。开箱即用，有两个提供的输入模块，一个设计用于独立输入，另一个设计用于触摸输入。每个模块都按照给定的配置接收和调度事件。

输入模块是事件系统的“业务逻辑”发生的地方。启用事件系统后，它将查看连接了哪些输入模块，并将更新处理传递给特定模块。

输入模块旨在根据您希望支持的输入系统进行扩展或修改。它们的目的是将硬件特定的输入（例如触摸，操纵杆，鼠标，运动控制器）映射到通过消息传递系统发送的事件中。

内置的输入模块旨在支持常见的游戏配置，例如触摸输入，控制器输入，键盘输入和鼠标输入。如果您在MonoBehaviours上实现特定的接口，则它们会将各种事件发送到应用程序中的控件。所有UI组件均实现对给定组件有意义的接口。