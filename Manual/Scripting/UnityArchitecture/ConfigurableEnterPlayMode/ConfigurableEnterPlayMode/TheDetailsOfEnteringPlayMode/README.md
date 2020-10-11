# The details of entering Play Mode
当启用了 **Scene Reloading** 和 **Domain Reloading**，这是Unity进入播放模式时执行的所有进程和事件的完整列表：
1. 引发[AssemblyReloadEvent](https://docs.unity3d.com/ScriptReference/AssemblyReloadEvents.html) beforeAssemblyReload事件。
2. C＃域已停止：  
    a.  所有的ScriptableObject和MonoBehaviours都会调用OnDisable（）。  
    b.  Unity等待所有异步操作完成。
3. 所有MonoBehaviours和ScriptableObjects的状态都已序列化。  
    a. 调用**OnBeforeSerialize（）**。  
    b. 除标有[NonSerialized]的值外，所有公共字段和私有字段值均已序列化。
4. 托管包装器与本机Unity对象断开连接。
5. 重新加载了Unity子域：  
    a. 单声道域卸载：  

        i.引发**AppDomain.DomainUnload**事件。  
        ii. Unity子域被破坏  
            1.调用GC和终结器。  
            2.线程终止。  
            3.删除所有JIT信息。  
    b. 新的Unity子域已创建。  
6. 程序集已加载：  
    a. 系统程序集已加载。  
    b. Unity程序集已加载。  
    c. 用户程序集已加载。  
7. 同步上下文已初始化。
8. 脚本状态已恢复。  
    a. 所有Unity对象的可脚本化部分都会重新创建。    

        i.调用构造函数，并为statics分配其默认值。  
    b. 所有Unity对象的状态都反序列化：

        i.所有Unity对象的序列化声明将恢复。
            1.引发OnAfterDeserialize事件。
        ii. 调用OnValidate（）。
        iii. 对于脚本使用 [ExecuteInEditMode] 属性：
            1.调用OnEnable（）。
            2.调用OnDisable（）。
            3.调用OnDestroy（）。

调用带有[InitializeOnLoad](https://docs.unity3d.com/ScriptReference/InitializeOnLoadAttribute.html)和[InitializeOnLoadMethod](https://docs.unity3d.com/ScriptReference/InitializeOnLoadMethodAttribute.html)的方法。

调用[AssemblyReloadEvent](https://docs.unity3d.com/ScriptReference/AssemblyReloadEvents.html)的afterAssemblyReload。