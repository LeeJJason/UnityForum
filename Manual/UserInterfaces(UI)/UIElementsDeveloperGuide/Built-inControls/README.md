# Built-in Controls
以下标准控件内置于UIElements中：
* Button
* Contextual menu
* EditorTextField
* Label
* ScrollView
* TextField
* Toggle

## Contextual menus
上下文菜单根据上下文向用户显示一组选择或操作。此上下文可以引用任何内容，通常是当前选择。

### Enabling contextual menus
要启用上下文菜单，请将ContextualMenuManipulator操纵器附加到可视元素。此操纵器将在右键单击鼠标向上事件或菜单向上键事件后显示上下文菜单。 ContextualMenuManipulator操纵器还添加了一个响应ContextualMenuPopulateEvent的回调。
下面的代码示例演示如何执行此操作：
```cs
void InstallManipulator(VisualElement element)
{
    ContextualMenuManipulator m = new ContextualMenuManipulator(MyDelegate);
    m.target = element;
}

void MyDelegate(ContextualMenuPopulateEvent event)
{
    // Modify event.menu
    event.menu.AppendAction("Properties", DisplayProperties, DropdownMenu.MenuAction.AlwaysEnabled);
}

void DisplayProperties(DropdownMenu.MenuAction menuItem)
{
    // ...
}
```

最后调用给ContextualMenuManipulator构造函数的回调，以便子元素可以填充菜单。

机械手在内部发送一个ContextualMenuPopulateEvent事件，该事件沿着传播路径传播到目标元素层次结构：从可视树的根到事件目标，然后再将可视树备份到根。
沿着传播路径，具有ContextualMenuPopulateEvent事件的回调的元素可以添加，删除或修改上下文菜单中的项目。

### Responding to the user selection
当元素接收到ContextualMenuPopulateEvent时，它将通过调用DropdownMenu.InsertAction（）或DropdownMenu.AppendAction（）将菜单项添加到上下文菜单。

这些函数中的每一个都将两个回调作为参数。
当用户在菜单中选择项目时，将执行第一个回调。
第二个回调在显示菜单之前执行，并且还会检查菜单项是否已启用。

这两个回调都接收MenuAction作为参数。 
MenuAction代表菜单项，并具有以下其他有用的属性：
* MenuAction.userData包含对可能与AppendAction（）或InsertAction（）一起使用的用户数据的引用。
* MenuAction.eventInfo包含有关触发上下文菜单显示的事件的信息。
在响应事件的操作中使用MenuAction.eventInfo。
例如，您可以使用鼠标位置基于所选的上下文菜单项创建和放置对象。