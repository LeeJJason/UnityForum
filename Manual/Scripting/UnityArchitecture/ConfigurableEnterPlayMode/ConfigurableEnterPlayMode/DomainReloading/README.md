# Domain Reloading
域重载将重置脚本状态，并且默认情况下处于启用状态。它为您提供了全新的脚本状态，并在您每次进入“播放模式”时重置所有静态字段和已注册的处理程序。这意味着每次您在Unity编辑器中进入播放模式时，您的项目就会以与首次在构建中启动时非常相似的方式开始播放。

域重新加载需要时间，并且此时间随着脚本的数量和复杂性而增加在您的项目中。当花费很长时间进入“播放模式”时，在项目上快速迭代变得越来越困难。这就是Unity提供关闭域重新加载选项的原因。

## Disabling Domain Reloading
禁用“域重新加载”后，进入播放模式的速度会更快，因为Unity不会每次都重置脚本状态。但是，这取决于您确保在进入“播放模式”时重置脚本状态。为此，您需要添加代码，以在“播放模式”启动时重置脚本状态。

禁用“域重新加载”后，当您根据[自动刷新设置](https://docs.unity3d.com/Manual/Preferences.html)更新或重新导入脚本时，Unity仍会刷新脚本状态。

## Modifying your scripts to perform correctly when Domain Reload is disabled:
为了确保您的脚本状态在“播放模式”下正确重置，您需要对脚本中的静态字段和静态事件处理程序进行调整。

### Static fields
禁用“域重新加载”后，代码中的静态字段值不会自动重置为其原始值。您需要添加明确执行此操作的代码。

下面的代码示例具有一个静态计数器字段，该字段在用户按下“跳转”按钮时递增。启用“域重装”后，进入“播放模式”时，计数器会自动重置为零。禁用“域重装”后，计数器不会重置；它会保持其值在“播放模式”中和不在“播放模式”中。这意味着在编辑器中第二次运行项目时，如果计数器在上一次运行中发生更改，则计数器可能不会为零。
```cs
using UnityEngine;

public class StaticCounterExample : MonoBehaviour
{
// this counter will not reset to zero when Domain Reloading is disabled
    static int counter = 0; 

    // Update is called once per frame
    void Update()
    {
            if (Input.GetButtonDown("Jump"))
            {
                    counter++;
                    Debug.Log("Counter: " + counter);
            }
    }
}
```

若要确保即使禁用“域重装”，计数器也会重置，您必须使用[RuntimeInitializeOnLoadMetho.（RuntimeInitializeLoadType.SubsystemRegistration）]属性，并明确重置该值：
```cs
using UnityEngine;

public class StaticCounterExampleFixed : MonoBehaviour
{
    static int counter = 0;

    [RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.SubsystemRegistration)]
    static void Init()
    {
            Debug.Log("Counter reset.");
            counter = 0;   
    }

    // Update is called once per frame
    void Update()
    {
            if (Input.GetButtonDown("Jump"))
            {
                counter++;
                Debug.Log("Counter: " + counter);
            }
    }
}
```

### Static event handlers
禁用域重新加载后，退出播放模式时，Unity不会从静态事件处理程序中注销方法。如果您有使用静态事件处理程序注册方法的代码，则可能导致复杂化。例如，在编辑器中项目的第一个Play中，方法将被正常注册。但是，在项目的第二次Play中，这些方法将在第一次注册之外再次注册，从而导致事件发生时将这些方法调用两次。

例如，此代码在静态事件处理程序Application.quitting中注册了一个方法，启用了“域重载”后，Unity会在“播放模式”启动时自动重置事件处理程序，因此该方法只注册一次。但是，在禁用“域重新加载”的情况下，不会清除事件处理程序，因此在编辑器中第二次运行Project时，该方法将第二次注册，并在事件发生时被调用两次-这通常是不希望的。
```cs
using UnityEngine;
public class StaticEventExample : MonoBehaviour
{
    void Start()
    {
            Debug.Log("Registering quit function");
            Application.quitting += Quit;
    }


    static void Quit()
    {
        Debug.Log("Quitting!");
    }
}
```

禁用“域重新加载”后，以上示例每次您进入“播放模式”时都会再次添加“退出”方法。每次您退出播放模式时，这会导致另外的“ Quiting”消息。

为确保即使禁用了域重装，事件处理程序也将重置，您必须使用[RuntimeInitializeOnLoadMethod]属性，并显式注销该方法，这样就不会添加两次。
```cs
using UnityEngine;
public class StaticEventExampleFixed : MonoBehaviour
{
    [RuntimeInitializeOnLoadMethod]
    static void RunOnStart()
    {
            Debug.Log("Unregistering quit function");
            Application.quitting -= Quit;
    }

    void Start()
    {
            Debug.Log("Registering quit function");
            Application.quitting += Quit;
    }

    static void Quit()
    {
        Debug.Log("Quitting the Player");
    }
}
```

对于运行时脚本，必须使用[RuntimeInitializeOnLoadMethod（RuntimeInitializeLoadType.SubsystemRegistration）]属性来重置静态字段和事件处理程序。

对于编辑器脚本，例如自定义编辑器窗口或检查器如果使用静态变量，则必须使用[InitializeOnEnterPlayMode]属性来重置静态字段和事件处理程序。