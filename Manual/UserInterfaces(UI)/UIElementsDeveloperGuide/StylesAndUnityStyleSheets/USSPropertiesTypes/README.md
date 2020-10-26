# USS Properties types
## Built-in vs Custom properties
使用USS时，可以在UI代码中为内置VisualElement属性或自定义属性指定值。

除了从USS文件中读取其值之外，还可以使用VisualElement的C＃属性在C＃中将内置属性值分配给该属性。 C＃中分配的值会覆盖Unity样式表（USS）中的值。

您可以使用“自定义属性” API扩展USS。自定义USS属性需要`--`前缀。

## Property values
本节列出了支持的类型。

### Length
UIElements支持像素（px）和百分比（％）作为长度的度量单位。像素值是绝对的，而百分比通常是相对于元素父级的。

例子：
* width:200px : 表示200像素的宽度。
* width:50% :  表示父元素宽度的一半的宽度。

指定度量单位很重要。如果您未指定度量单位，则UIElements会假定属性值以像素表示。

注意：0是一个特殊的值，不需要度量单位。

### Numeric
数值表示为浮点数或整数文字。例如，flex：1.0。

### Keywords
一些内置属性支持特定的关键字。关键字提供描述性名称而不是数字。例如：position：absolute。所有属性都支持初始的全局关键字，该关键字将属性重置为其默认值。请参阅支持的属性以获取[关键字列表](https://docs.unity3d.com/Manual/UIE-USS-SupportedProperties.html)。

### Color
UIElements支持以下文字颜色值和函数：
* A Hexadecimal value: #FFFF00 (rgba one byte per channel), #0F0 (rgb)
* The RGB function: rgb(255, 255, 0) 
* The RGBA function: rgba(255, 255, 0, 1.0) 
* Color keywords: blue, transparent 

### Assets
您可以使用resource（）或url（）函数引用资产。例如，指定background-image：resource（“Images / img.png”）以将Images目录中的img.png指定为背景图像。引用的资产在导入期间解析。

resource（）函数接受位于`Resources`文件夹或`Editor Default Resources`文件夹下的文件，但有以下警告：
* 如果文件位于`Resources`文件夹下，则不包括文件扩展名。例如：`background-image：resource（“ Images / my-image”）`。
* 如果该文件位于`Editor Default Resources`下，则必须包括文件扩展名。例如：`background-image：resource（“ Images / default-image.png”）`。

另外，在加载纹理时，resource（）提供了一种方便的方式来处理高DPI /视网膜屏幕。如果在同一位置存在具有相同文件名和后缀@ 2x的纹理，则Unity会根据屏幕DPI自动加载它。例如，如果您在USS中使用resource（“ myimage”），则Unity会加载Resources / myimage.png或Resources/myimage@2x.png。

url（）函数期望文件路径相对于项目根目录或包含USS文件的文件夹。您必须包括文件扩展名。在以下示例中，USS文件位于Assets \ Editor \ USS，thumb.png背景图像位于Assets \ Editor \ Resources：
* 根据USS文件的位置，将此示例用于相对路径：url（“ ../ Resources / thumb.png”）;
* 根据项目的位置，使用以下示例之一获取绝对路径：url（“ / Assets / Editor / Resources / thumb.png”）;或url（“ project：/Assets/Editor/Resources/thumb.png”）;或url（“ project：///Assets/Editor/Resources/thumb.png”）;例如：background-image：url（“ Images / my-image.png”）。

### Strings
使用引号指定字符串值。例如：--my-property：“ foo”。