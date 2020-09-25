## Late Binding
Late Binding是您在程序运行时通过[Sprite Atlas API](https://docs.unity3d.com/ScriptReference/U2D.SpriteAtlas.html)加载或交换所需的Sprite的的名称。当构建在运行时没有自动加载准备好的Sprite Atlas时（例如， [no Sprite Atlases are included in the build](../README.md)）这是必需的。

### Sprites enumeration at run time
要在运行时检索Sprite Atlas的内容，请按照下列步骤操作：
1. 创建一个将SpriteAtlas作为公共变量的自定义组件。
2. 分配Sprite Atlas该字段。
3. 进入编辑器的 Play Mode。
4. 访问变量并调用属性.GetSprites以检索包装在所选Atlas中的Sprite数组。