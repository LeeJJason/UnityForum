# USS Writing style sheets
为了使事情井井有条，UIElements采用了[BEM](http://getbem.com/)作为样式元素的方法。虽然不是必须使用BEM，但建议使用。

## About BEM
BEM代表块元素修改器。 BEM是一个简单的系统，可以帮助您编写结构化，明确，易于维护的选择器。使用BEM，您可以为元素分配类，然后将这些类用作样式表中的选择器。

BEM类最多包含三个组件：
* Block name: a block is a meaningful, standalone entity or control. For example, menu, button, list-view 
* Element name: a part of a block that has no standalone meaning and is semantically tied to its block. Element names are appended to the block name using two underscores. For example, menu__item, button__icon, or list-view__item 
* Modifier: a flag that changes the appearance or behavior of a block or element. Append a modifier to a block or element name with two dashes. For example, menu--disabled, menu__item--disabled, button--small, or list-view__item--selected.

每个名称部分都可以由拉丁字母，数字和破折号组成。每个名称部分都用双下划线__或双破折号-连接在一起。

下面的示例显示菜单的XML代码：
```
<VisualElement class="menu">
    <Label class="menu__item" text="Banana" />
    <Label class="menu__item" text="Apple" />
    <Label class="menu__item menu__item--disabled" text="Orange" />
</VisualElement>
```

## Selectors
由于每个元素都配有描述其作用和外观的类，因此您可以仅使用一个类名来编写大多数选择器：
```
.menu {
}

.menu__item {
}

.menu__item--disabled {
}
```

您应该能够使用单个类名来设置元素的样式。但是，在某些情况下，您可能需要使用复杂的选择器。例如，当元素的样式取决于其块的修饰符时，可以使用复杂的选择器：
```
.button {
}

.button__icon {
}

.button--small {
}

.button--small .button__icon {
}
```

注意不要指定长选择符。选择器过长可能表示您的UI的图形设计不一致。您还应该避免在BEM选择器中使用类型名称（按钮，标签）或元素名称（＃my-button）。

## Making VisualElement BEM-friendly
UIElements遵守BEM。每个可视元素均附有必要的类名。例如，所有TextElement都具有unity-text-element类。每个从TextElement派生的Button实例，其类列表中都填充有unity-button和unity-text-element类。

如果从VisualElement或其后代之一派生新元素，请遵循以下准则，以确保使用BEM方法轻松对元素进行样式设置：
* 在构造函数中使用AddToClassList（）将相关的USS类添加到元素实例中。
* 如果新元素在其构造函数中实例化了子元素，则将相关类分配给子元素。例如，my-block__第一个孩子，my-block__other-孩子。
* 如果您的元素支持多种状态或变体（例如大小），请在元素状态更改或选择元素变体时添加和删除相关类。
* 如果要在其他项目中使用您的元素，请考虑在类前面加上前缀，以避免与现有的用户类名称冲突。