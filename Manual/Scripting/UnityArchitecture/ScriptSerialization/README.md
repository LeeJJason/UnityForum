# Script Serialization
序列化是将数据结构或对象状态转换为Unity以后可以存储和重建的格式的自动过程。 Unity的某些内置功能使用序列化。保存和加载，检查器等功能窗口，实例化和预制。有关所有这些的背景详细信息，请参见有关 [Built-in serialization use](https://docs.unity3d.com/Manual/script-Serialization-BuiltInUse.html) 的文档。

在Unity项目中组织数据的方式会影响Unity序列化该数据的方式，并且可能对Project的性能产生重大影响。本页概述了Unity中的序列化以及如何为其优化项目。

另请参阅有关以下内容的文档：[Serialization Errors](https://docs.unity3d.com/Manual/script-Serialization-Errors.html),[ Custom Serialization](https://docs.unity3d.com/Manual/script-Serialization-Custom.html), 和 [Built-in Serialization](https://docs.unity3d.com/Manual/script-Serialization-BuiltInUse.html)。

## Understanding hot reloading
热重装是创建或编辑脚本的过程打开编辑器并立即应用脚本行为。您不必重新启动应用程序或编辑器即可使更改生效。

当您更改并保存脚本时，Unity热重新加载所有当前加载的脚本数据。它首先将所有可序列化的变量存储在所有已加载的脚本中，并在加载脚本后将其还原。热重载后，所有不可序列化的数据都会丢失。

### Saving and loading
Unity使用序列化来从硬盘中加载和保存Scenes, Assets, 和 AssetBundles。这包括保存在您自己的脚本API对象（例如MonoBehaviour组件和ScriptableObjects）中的数据。

Unity Editor中的许多功能都建立在核心序列化系统之上。序列化需要特别注意的两件事是Inspector窗口和热重载。

### The Inspector window
当您在 **Inspector window** 中查看或更改GameObject的值时的“组件”字段中，Unity会序列化此数据，然后将其显示在 **Inspector window** 窗口中。当 **Inspector window** 显示字段值时，它不与Unity Scripting API通信。

如果您在脚本中使用属性，则当您在Inspector窗口中查看或更改值时，绝不会调用任何属性getter和setter，因为Unity会直接序列化Inspector窗口字段。这意味着：尽管 **Inspector window** 中的字段值表示脚本属性，但是在 **Inspector window** 中更改值不会在脚本中调用任何属性获取器和设置器。

## Serialization rules
Unity中的序列化器在实时游戏环境中运行。这对性能有重大影响。因此，Unity中的序列化行为与其他编程环境中的序列化行为不同。以下部分概述了如何在Unity中使用序列化。要使用字段序列化，您必须确保：
* Is public, or has a [SerializeField](https://docs.unity3d.com/ScriptReference/SerializeField.html) attribute
* Is not static
* Is not const
* Is not readonly
* Has a fieldtype that can be serialized. ([See Simple field types that can be serialized](https://docs.unity3d.com/Manual/script-Serialization.html#FieldSerliaized2), below.)

### Simple field types that can be serialized
* Custom non-abstract, non-generic classes with the Serializable attribute (See How to ensure a custom class can be serialized, below.)
* Custom structs with the Serializable attribute
* References to objects that derive from UnityEngine.Object
* Primitive data types (int, float, double, bool, string, etc.)
* Enum types
* Certain Unity built-in types: Vector2, Vector3, Vector4, Rect, Quaternion, Matrix4x4, Color, Color32, LayerMask, AnimationCurve, Gradient, RectOffset, GUIStyle

### Container field types that can be serialized
* An array of a simple field type that can be serialized
* A List<T> of a simple field type that can be serialized

**注意**：Unity不支持多级类型（多维数组，锯齿状数组和嵌套容器类型）的序列化。
如果要序列化它们，则有两个选择：将嵌套类型包装在类或结构中，或使用序列化回调[ISerializationCallbackReceiver](https://docs.unity3d.com/ScriptReference/ISerializationCallbackReceiver.html)进行自定义序列化。
有关更多信息，请参见有关[自定义序列化](https://docs.unity3d.com/Manual/script-Serialization-Custom.html)的文档。

### How to ensure a custom class can be serialized
Ensure it:
* Has the Serializable attribute
* Is not abstract
* Is not static
* Is not generic, though it may inherit from a generic class

## When might the serializer behave unexpectedly?
### Custom classes behave like structs
使用不是从UnityEngine.Object派生的自定义类，Unity会按值内联化对它们进行序列化，类似于对结构进行序列化的方式。如果您在几个不同的字段中存储对自定义类实例的引用，则在序列化时它们将成为单独的对象。然后，当Unity对字段进行反序列化时，它们包含具有相同数据的截然不同的对象。

当您需要使用引用序列化复杂的对象图时，请勿让Unity自动序列化对象。而是使用**ISerializationCallbackReceiver**手动序列化它们。这样可以防止Unity从对象引用创建多个对象。有关更多信息，请参见有关[ISerializationCallbackReceiver](https://docs.unity3d.com/ScriptReference/ISerializationCallbackReceiver.html)的文档。

这仅适用于自定义类。 Unity对其自定义类进行“内联”序列化，因为它们的数据成为使用它们的MonoBehaviour或ScriptableObject的完整序列化数据的一部分。当字段引用的是UnityEngine.Object派生的类（例如公共Camera myCamera）时，Unity会引用相机实际的UnityEngine.Object。如果脚本实例是从MonoBehaviour或ScriptableObject派生的，则脚本实例也会发生同样的情况，它们都从UnityEngine.Object派生。

### No support for **null** for custom classes
考虑使用下面脚本反序列化时分配多少内存。
```cs
class Test : MonoBehaviour
{
    public Trouble t;
}
[Serializable]
class Trouble
{
   public Trouble t1;
   public Trouble t2;
   public Trouble t3;
}
```

这并不奇怪的一种分配：**Test**对象的分配。这并不奇怪的两种种分配：一种分配给Test对象，另一种分配给Trouble对象。

但是，Unity实际上进行了上千次分配。序列化器不支持null。如果它序列化一个对象，并且字段为空，则Unity会实例化该类型的新对象并对其进行序列化。显然，这可能导致无限循环，因此深度限制为七个级别。那时，Unity停止序列化具有自定义类，结构，列表或数组类型的字段。

由于许多Unity子系统都建立在序列化系统之上，因此测试MonoBehaviour的意外大序列化流导致所有这些子系统的运行速度比必要的慢。

### No support for polymorphism
如果您有**public Animal[] animals**，并且放入了**Dog**，**Cat**和**Giraffe**的实例，则在序列化之后，您将拥有三个Animal实例。

解决此限制的一种方法是认识到它仅适用于自定义类，该类以内联方式序列化。对其他UnityEngine.Objects的引用将被序列化为实际引用，对于这些引用，多态性确实有效。您可以创建一个ScriptableObject派生类或另一个MonoBehaviour派生类，并对其进行引用。这样做的缺点是，您需要将该Monobehaviour或ScriptableObject的对象存储在某个位置，并且无法有效地内联序列化它。

这些限制的原因是串行化系统的核心基础之一是提前知道对象的数据流的布局。它取决于类字段的类型，而不是字段中存储的内容。

## Tips
### Optimal use of serialization
您可以整理数据，以确保从Unity的序列化中获得最佳利用。  
* 整理数据的目的是让Unity序列化最小的数据集。这样做的主要目的不是为了节省计算机硬盘上的空间，而是要确保您可以保持与项目先前版本的向后兼容性。如果要处理大量序列化数据，则向后兼容会在以后的开发中变得更加困难。
* 对数据进行组织，以使Unity永远不会序列化重复数据或缓存数据。这会导致向后兼容性的重大问题：由于数据太容易不同步，因此存在很高的错误风险。
* 避免在引用其他类的位置使用嵌套的递归结构。序列化结构的布局必须始终相同；与数据无关，并且仅取决于脚本中公开的内容。引用其他对象的唯一方法是通过从UnityEngine.Object派生的类。这些类是完全独立的。他们只会互相引用，不会嵌入内容。

### Making Editor code hot reloadable
重新加载脚本时，Unity会序列化所有变量并将其存储在所有已加载脚本中。重新加载脚本后，Unity然后将它们还原为其原始的，预序列化的值。

重新加载脚本时，即使变量没有Seri​​alizeField属性，Unity也会恢复所有满足序列化要求的变量-包括私有变量。在某些情况下，您特别需要防止还原私有变量：例如，如果您希望从脚本重新加载后引用为空。在这种情况下，请使用NonSerialized属性。

Unity永远不会恢复静态变量，因此请不要将静态变量用于重新加载脚本后需要保留的状态。