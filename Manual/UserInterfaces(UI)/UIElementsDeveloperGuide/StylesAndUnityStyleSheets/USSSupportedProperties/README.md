# USS supported properties
本主题列出了受支持的USS属性及其接受的值。

## USS data types
USS数据类型定义了USS属性接受的值和关键字。
|Property|Description
|:-------|:-------
|`<length>`|表示距离值。
|`<number>`|表示整数或带小数的数字。
|`<integer>`|代表整数。
|`<color>`|代表一种颜色。您可以使用＃十六进制代码，rgb（）或rgba（）函数或颜色关键字（例如，蓝色或透明）定义颜色。
|`<resource>`|表示资源文件夹中的资产。
|`<url>`|表示路径指定的资产。它可以表示为相对路径或绝对路径。

## USS syntax
UIElements样式属性使用与W3C CSS文档相同的语法：
* 关键字值按原样显示。例如：自动，基线。
* 基本数据类型出现在尖括号（<和>）之间。例如：`<length>`，`<color>`。
* 与属性共享相同名称的非终端出现在尖括号和单引号（<'和'>）之间。例如，<'width'>。

如果一个属性值包含多个组成部分：
* 并排单词意味着所有单词都必须以给定顺序出现。
* 竖线（|）分隔了两个或多个选择：必须出现一个。
* 双杠（||）分隔两个或多个选项：必须以任何顺序出现一个或多个。
* 双与号（&&）分隔两个或多个组件，所有组件必须以任何顺序出现。
* 方括号（[]）表示分组。

每个类型，关键字或方括号括起来的组都可以跟有修饰符：
* 星号（*）表示前面的类型，单词或组出现0次或多次。
* 加号（+）表示前面的类型，单词或组出现了一次或多次。
* 问号（？）表示前面的类型，单词或组是可选的。
* 一对大括号（{A，B}）中的数字表示前面的类型，单词或组至少出现A且最多出现B次

## Inherited properties
如果没有为继承的属性指定值，则元素从其父元素获取值。例如，使用继承的属性来设置所有元素的字体。
```css
css
:root {
    -unity-font: resource("Font/consola.ttf");
}
```
继承了以下属性：
* color
* font-size
* -unity-font
* -unity-font-style
* -unity-text-align
* visibility
* whitespace

## Box model
![](style-box-model.png)  
*Box model*

### Dimensions
```
margin-left: <length> | auto;
margin-top: <length> | auto
margin-right: <length> | auto
margin-bottom: <length> | auto
```

宽度和高度指定元素的大小。如果未指定width，则宽度基于元素内容的宽度。如果未指定height，则高度基于元素内容的高度。

### Margins
```
margin-left: <length> | auto;
margin-top: <length> | auto
margin-right: <length> | auto
margin-bottom: <length> | auto
```

### Shorthand
```
margin: [<length> | auto]{1,4}
```

边距速记选项的应用如下：
|Option|Description
|:-----|:-----
|1 length| Applied to all four margins.
|2 lengths| The first is applied to `margin-top` and `margin-bottom`. The second is applied to `margin-left` and `margin-right`.
|3 lengths| The first is applied to `margin-top`. The second is applied to `margin-left` and `margin-right`. The third is applied to margin-bottom.
|4 lengths| The lengths are applied in this order : `margin-top`, `margin-right`, `margin-bottom`, `margin-left` 

### Borders
```
border-left-width: <length>
border-top-width: <length>
border-right-width: <length>
border-bottom-width: <length>
```

### Padding
```
padding-left: <length>
padding-top: <length>
padding-right: <length>
padding-bottom: <length>
```

## Flex layout
本节列出了放置视觉元素的属性。 UIElements包含一个[布局引擎](https://docs.unity3d.com/Manual/UIE-LayoutEngine.html)，该引擎根据布局和样式属性定位视觉元素。布局引擎实现了Flexbox（HTML / CSS布局系统）的子集。

默认情况下，所有项目均垂直放置在其容器中。
### Items
```
flex-grow: <number>
flex-shrink: <number>
flex-basis: <length> | auto
flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
align-self: auto | flex-start | flex-end | center | stretch
```

### Containers
```
flex-direction: row | row-reverse | column | column-reverse
flex-wrap: nowrap | wrap | wrap-reverse
align-content: flex-start | flex-end | center | stretch
align-items: flex-start | flex-end | center | stretch
justify-content: flex-start | flex-end | center | space-between | space-around
```

## Relative and absolute positioning
### Positioning
```
position: absolute | relative
```
默认情况下，此属性设置为relative，它根据元素的父元素放置元素。如果将此属性设置为absolute，则元素将保留其父级布局，并根据父级边界指定值。

### Position
```
left: <length> | auto
top: <length> | auto
right: <length> | auto
bottom: <length> | auto
```
距父边缘或元素原始位置的距离。

## Drawing properties
绘图属性设置视觉元素的背景，边框和外观。

### Background
```
background-color: <color>
background-image: <resource> | <url> | none
-unity-background-scale-mode: stretch-to-fill | scale-and-crop | scale-to-fit
-unity-background-image-tint-color: <color>
```

### Slicing
分配背景图像时，可以根据简化的9切片规范进行绘制：
```
-unity-slice-left: <integer>
-unity-slice-top: <integer>
-unity-slice-right: <integer>
-unity-slice-bottom: <integer>
```

### Borders
```
border-color: <color>
border-top-left-radius: <length>
border-top-right-radius: <length>
border-bottom-left-radius: <length>
border-bottom-right-radius: <length>
```

### Shorthand
```
border-radius: <length>{1,4}
```

边框半径速记选项的应用如下：
|Option|Description
|:-----|:-----
|1 length| Applied to all four margins.
|2 lengths| The first is applied to `margin-top` and `margin-bottom`. The second is applied to `margin-left` and `margin-right`.
|3 lengths| The first is applied to `margin-top`. The second is applied to `margin-left` and `margin-right`. The third is applied to margin-bottom.
|4 lengths| The lengths are applied in this order : `margin-top`, `margin-right`, `margin-bottom`, `margin-left` 

### Appearance
```
overflow: hidden | visible
-unity-overflow-clip-box: padding-box | content-box
opacity: <number>
visibility: visible | hidden
display: flex | none
```

`display`默认值为`flex`。将`display`设置为`none`将删除该元素。将`visibility`设置为`hidden`会隐藏该元素，但是该元素仍会占用布局中的空间。

`-unity-overflow-clip-box`定义元素内容的剪切矩形。默认值为`padding-box`，即填充区域外的矩形（上方的框模型图像中的绿色矩形）； content-box表示填充区域内的值（上面的框模型图像中的蓝色矩形）。

## Text properties
文本属性设置颜色，字体，字体大小以及Unity特定的属性，用于字体资源，字体样式，对齐方式，自动换行和剪切。
```
color: <color>
-unity-font: <resource> | <url>
font-size: <number>
-unity-font-style: normal | italic | bold | bold-and-italic
-unity-text-align: upper-left | middle-left | lower-left | upper-center | middle-center | lower-center | upper-right | middle-right | lower-right
white-space: normal | nowrap
```

## Cursor property
使用光标的默认纹理类型可以为光标导入自定义纹理。
```
cursor: [ [ <resource> | <url> ] [ <integer> <integer>]? , ] [ arrow | text | resize-vertical | resize-horizontal | link | slide-arrow | resize-up-right | resize-up-left | move-arrow | rotate-arrow | scale-arrow | arrow-plus | arrow-minus | pan | orbit | zoom | fps | split-resize-up-down | split-resize-left-right ]
```