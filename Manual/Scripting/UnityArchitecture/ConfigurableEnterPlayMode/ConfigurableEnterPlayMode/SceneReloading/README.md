# Scene Reloading
默认情况下，场景重新加载已启用。这意味着，当您进入“播放模式”时，Unity会销毁所有现有的场景游戏对象并从磁盘重新加载场景。 Unity执行此操作所需的时间随着场景的复杂性而增加，这意味着随着您的项目变得越来越复杂，您在按下“播放”按钮到场景完全载入编辑器之间必须等待更长的时间。

禁用场景重新加载后，该过程将花费更少的时间。这使您可以更快地迭代项目的开发。 Unity不会从磁盘重新加载场景，而是仅重置场景的修改内容。这避免了卸载和重新加载场景的时间和性能影响。 Unity仍然调用与刚加载时相同的初始化函数（例如OnEnable，OnDisable和OnDestroy）。

重要的是要记住，当禁用场景重载时，在编辑器中启动应用程序所花费的时间不再代表内置版本中的启动时间。因此，如果您想调试或准确描述项目启动期间发生的情况，则应启用“场景重新加载”以更准确地表示构建版本中发生的真实加载时间和过程。