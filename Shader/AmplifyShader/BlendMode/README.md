![](BlendMode_1.png)
| 属性名称 | 描述 |
| :---- | :----|
| Blend Mode| 所选模式自动调整可用参数;不透明（Opaque），遮罩（Masked），透明（Transparent），Alpha预乘（Alpha  Transparent）或自定义（Custom）。 |
| Render Type| 该标记将着色器分为几个预定义组。可用标签：不透明（Opaque），透明（Transparent），透明抠出（Transparent Cutout），背景（Background），覆盖（Overlay），不透明树（Tree Opaque），透明树剪出（Tree Transparent Cutout），布告板树（Tree Billboard），草和布告板草皮（Grass and Grass Billboard）。|
|Render Queue| 为了获得最佳性能，通过几何体渲染队列排布，从而优化对象的绘制顺序。所有其他渲染队列按距离对对象进行排序，从最远的那些开始渲染，并以最接近的结束。可用选项有背景、几何、Alpha测试、透明和覆盖。|
|Mask Clip Value| 将默认值与不透明度alpha值进行比较。0完全不透明，1完全掩蔽;默认设置为0。通常用于透明的切断材料|
|Refraction Layer| 当指定的grabpass被这个值偏移时，有效地创建了一个用于折射效果的分层系统。|
|Alpha To Coverage|打开内部MSAA功能，使用不透明对象层混合alpha对象;只有在打开MSAA的情况下才能进行正向渲染。|
|Blend RGB and Blend Alpha| 当渲染图形时，在所有着色器执行并且所有纹理都应用后，像素被写入屏幕。它们如何与已经存在的内容组合由Blend命令控制。 ASE目前提供定制、Alpha混合（Alpha Blend）、预乘（Premultiplied）、加法（Additive）、软加法（Soft Additive）、乘法（Multiplicative）和2x乘法（2x Multiplicative）模式。|
|Blend Factors (SrcFactor & DstFactor)| 以下所有属性对于Blend命令中的SrcFactor和DstFactor都是有效的。源是指计算出的颜色，目的是指屏幕上已经出现的颜色。如果BlendOp使用逻辑操作，则忽略混合因子。|
|Blend Op| Add, Sub, Rev Sub, Min 和 Max|
|Color Mask|设置颜色通道写入掩码，将它们全部关闭，使其不可见。|


Blend Factors
| 属性名称 | 描述 |
| :---- | :----|
|One |使用这个来让源色或目标色都充分显示出来。|
|Zero |使用它删除源值或目标值。|
|SrcColor |这个阶段的值乘以源颜色值|
|SrcAlpha |这个阶段的值乘以源alpha值。|
|DstColor |这个阶段的值乘以帧缓冲源的颜色值。|
|DstAlpha |这个阶段的值乘以帧缓冲源alpha值。|
|OneMinusSrcColor |这个阶段的值乘以(1 -源颜色)。|
|OneMinusSrcAlpha |这个阶段的值乘以(1 -源alpha)。|
|OneMinusDstColor |这个阶段的值乘以(1 -目标颜色)。|
|OneMinusDstAlpha |这个阶段的值乘以(1 - destination alpha)。|