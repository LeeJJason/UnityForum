## Resolving different Sprite Atlas scenarios

Unity解决Sprites和Sprite Atlas之间的交互的方式，具体取决于各种条件。以下示例详细介绍了最常见的方案：

### Scenario 1: Basic Sprite Atlas usage
1. **Sprite Atlas A**包含 **Sprite 1**。
2. **Sprite Atlas A**已启用 **Include in Build**。
3. 结果：项目公布的构建包括 **Sprite Atlas A**。**Sprite 1**使用**Sprite Atlas A**的Texture进行渲染。


### Scenario 2: ‘Include in Build’ is disabled
1. **Sprite Atlas A**包含**Sprite 1**。
2. **Sprite Atlas A**已禁用 **Include in Build**。
3. 结果：该项目的已发布版本不包含**Sprite Atlas A**，也不包含**Sprite 1**的 Texture。**Sprite 1**在运行时在构建中不可见，因为它所引用的纹理不可用且未加载。

### Scenario 3: One Sprite in two Sprite Atlases
1. **Sprite Atlas A**和**Sprite Atlas B**都在其 [Objects for Packing](../../SpriteAtlasWorkflow/README.md) 列表中包含了**Sprite 1**。
2. **Sprite Atlas A** 和 **Sprite Atlas B** 都启用了 **Include in Build**。
3. 在此示例中，两个**Sprite Atlas**具有不同的Texture输出设置。
4. 结果：该项目的已发布版本同时包含了两个Sprite Atlas。Unity 在您无法控制的内部过程中随机选择使用哪个Texture渲染**Sprite 1**。

在这种情况下，要确保Sprite使用你想要的Sprite Atlas中Texture渲染 。请按照以下步骤操作：

1. 禁用两个Sprite Atlas的 **Include in Build**。
2. 在运行时初始化Sprite时，请使用SpriteAtlas.GetSprite直接从其中一个Atlases请求Sprite 。这样可以确保Unity始终从正确的Sprite Atlas绘制Sprite Texture。