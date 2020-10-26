# UQuery
UQuery提供了一组扩展方法，用于从任何UIElements可视树中检索元素。 UQuery基于JQuery或Linq，但UQuery旨在最大程度地限制动态内存分配。这样可以在移动平台上实现最佳性能。

要使用UQuery检索元素，请使用`UQueryExtensions.Q`或使用`UQueryExtensions.Query`初始化`QueryBuilder`。

例如，以下`UQuery`从根开始，并找到名称为`foo`的第一个`Button`：
```cs
root.Query<Button>("foo").First();
```

以下UQuery在名为foo的每个Button的同一组中进行迭代：
```cs
root.Query("foo").Children<Button>().ForEach(//do stuff);
```