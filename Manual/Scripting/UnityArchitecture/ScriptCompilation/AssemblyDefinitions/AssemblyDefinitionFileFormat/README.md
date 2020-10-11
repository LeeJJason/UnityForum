# Assembly Definition File Format
程序集定义和程序集定义参考资产是JSON文件。您可以使用检查器在Unity Editor中编辑资产文件窗口，但您也可以使用外部工具修改JSON内容。

## Assembly Definition JSON
程序集定义是具有以下字段的JSON对象：

### allowUnsafeCode bool
可选的。默认为false。请参阅[Allow ‘unsafe’ Code](https://docs.unity3d.com/Manual/class-AssemblyDefinitionImporter.html#general)。
```json
"allowUnsafeCode" : true
```

### autoReferenced bool
可选的。默认为false。请参阅[Auto Referenced](https://docs.unity3d.com/Manual/class-AssemblyDefinitionImporter.html#general)。
```json
"autoReferenced": false
```

### defineConstraints string[]
可选的。用作约束的符号。可以为空。请参阅[Define Constraints](https://docs.unity3d.com/Manual/class-AssemblyDefinitionImporter.html#define-constraints)。
```json
"defineConstraints": [
        "UNITY_2019",
        "UNITY_INCLUDE_TESTS"
    ]
```

### excludePlatforms string[]
可选的。要排除的平台名称字符串或一个空数组。如果includePlatforms包含值，则excludePlatforms数组必须为空。您可以使用CompilationPipeline.GetAssemblyDefinitionPlatforms函数检索平台名称字符串（调用此函数时，必须为当前编辑器安装对平台的支持。）请参阅平台。
```json
"includePlatforms": [],
"excludePlatforms": [
        "iOS",
        "macOSStandalone",
        "tvOS"
]
```

### includePlatforms string[]
可选的。要包括的平台名称字符串或一个空数组。如果excludePlatforms包含值，则includePlatforms数组必须为空。您可以使用CompilationPipeline.GetAssemblyDefinitionPlatforms函数检索平台名称字符串（调用此函数时，必须为当前编辑器安装对平台的支持。）请参阅平台。
```json
"includePlatforms": [
        "Android",
        "LinuxStandalone64",
        "WebGL"
],
"excludePlatforms": []
```

### name string
需要。任何[合法的程序集名称](https://docs.microsoft.com/en-us/dotnet/standard/assembly/names)。
```json
"name" : "MyAssemblyName" 
```

### noEngineReferences bool
可选的。默认为false。请参阅 [No Engine References](https://docs.unity3d.com/Manual/class-AssemblyDefinitionImporter.html#general)。
```json
"noEngineReferences": false
```

### optionalUnityReferences string[]
可选的。在早期版本的Unity中，此字段序列化了Unity References：Test Assemblies选项，该选项用于将程序集指定为测试程序集。从Unity 2019.3开始，该选项不再显示。仍然支持该字段，但是如果资产在更高版本的Unity Editor中重新序列化，则该字段将被等效的程序集引用替换。

请参阅[创建测试程序集](https://docs.unity3d.com/Manual/ScriptCompilationAssemblyDefinitionFiles.html#create-test-assembly)，以获取有关测试程序集的更多信息。
```json
"optionalUnityReferences": [
    "TestAssemblies"
  ]
```

### overrideReferences bool
可选的。如果[precompiledReferences](#precompiledreferences-string)包含值，则设置为true。默认为false。
请参见[替代参考]。
```json
"overrideReferences": true
```
### precompiledReferences string[]
可选的。引用的DLL库的文件名，包括扩展名，但不包含其他路径元素。可以为空。除非您将[overrideReferences](https://docs.unity3d.com/Manual/AssemblyDefinitionFileFormat.html#override-references)设置为true，否则将忽略此数组。

请参见 [Assembly References](https://docs.unity3d.com/Manual/class-AssemblyDefinitionImporter.html#assembly-references)。
```json
"overrideReferences": true,
"precompiledReferences": [
        "Newtonsoft.Json.dll",
        "nunit.framework.dll"
]
```

### references string[]
可选的。对使用“装配定义”资产创建的其他装配的引用。您可以使用程序集定义资产文件的GUID或程序集的名称（由程序集定义的名称字段定义）。您必须为列表中的所有引用使用相同的表单。可以为空。

您可以使用AssetDatabase.AssetPathToGUID函数来检索资产的GUID。 （GUID也是与每个资产关联的元数据的一部分。）

请注意，编辑器在装配定义检查器中显示“使用GUID”选项。此选项未在关联的JSON文件中序列化。而是从文件中找到的引用形式推断出选择。

参考 [Referencing another assembly](https://docs.unity3d.com/Manual/ScriptCompilationAssemblyDefinitionFiles.html#reference-another-assembly).

Using GUIDs:
```json
"references": [
        "GUID:17b36165d09634a48bf5a0e4bb27f4bd",
        "GUID:b470eee7144904e59a1064b70fa1b086",
        "GUID:2bafac87e7f4b9b418d9448d219b01ab",
        "GUID:27619889b8ba8c24980f49ee34dbb44a",
        "GUID:0acc523941302664db1f4e527237feb3"
]
```
Using assembly names:
```json
"references": [
        "Unity.CollabProxy.Editor",
        "AssemblyB",
        "UnityEngine.UI",
        "UnityEngine.TestRunner",
        "UnityEditor.TestRunner"
]
```

### versionDefines object[]
可选的。包含每个版本定义的对象。该对象具有三个字段：
* name：string –资源名称
* expression：string –定义资源版本或版本范围的表达式
* define：string –定义的符号

参考 [Version Defines](https://docs.unity3d.com/Manual/class-AssemblyDefinitionImporter.html#version-defines).
```json
"versionDefines": [
    {
        "name": "com.unity.ide.vscode",
        "expression": "[1.7,2.4.1]",
        "define": "MY_SYMBOL"
    },
    {
        "name": "com.unity.test-framework",
        "expression": "[2.7.2-preview.8]",
        "define": "TESTS"
    }
]
```

## Example Assembly Definition JSON strings
使用程序集名称引用其他程序集定义和includePlatforms：
```json
{
    "name": "BeeAssembly",
    "references": [
        "Unity.CollabProxy.Editor",
        "AssemblyB",
        "UnityEngine.UI",
        "UnityEngine.TestRunner",
        "UnityEditor.TestRunner"
    ],
    "includePlatforms": [
        "Android",
        "LinuxStandalone64",
        "WebGL"
    ],
    "excludePlatforms": [],
    "overrideReferences": true,
    "precompiledReferences": [
        "Newtonsoft.Json.dll",
        "nunit.framework.dll"
    ],
    "autoReferenced": false,
    "defineConstraints": [
        "UNITY_2019",
        "UNITY_INCLUDE_TESTS"
    ],
    "versionDefines": [
        {
            "name": "com.unity.ide.vscode",
            "expression": "[1.7,2.4.1]",
            "define": "MY_SYMBOL"
        },
        {
            "name": "com.unity.test-framework",
            "expression": "[2.7.2-preview.8]",
            "define": "TESTS"
        }
    ],
    "noEngineReferences": false
}
```

使用GUIDS引用其他程序集定义和excludePlatforms：
```json
{
    "name": "BeeAssembly",
    "references": [
        "GUID:17b36165d09634a48bf5a0e4bb27f4bd",
        "GUID:b470eee7144904e59a1064b70fa1b086",
        "GUID:2bafac87e7f4b9b418d9448d219b01ab",
        "GUID:27619889b8ba8c24980f49ee34dbb44a",
        "GUID:0acc523941302664db1f4e527237feb3"
    ],
    "includePlatforms": [],
    "excludePlatforms": [
        "iOS",
        "macOSStandalone",
        "tvOS"
    ],
    "allowUnsafeCode": false,
    "overrideReferences": true,
    "precompiledReferences": [
        "Newtonsoft.Json.dll",
        "nunit.framework.dll"
    ],
    "autoReferenced": false,
    "defineConstraints": [
        "UNITY_2019",
        "UNITY_INCLUDE_TESTS"
    ],
    "versionDefines": [
        {
            "name": "com.unity.ide.vscode",
            "expression": "[1.7,2.4.1]",
            "define": "MY_SYMBOL"
        },
        {
            "name": "com.unity.test-framework",
            "expression": "[2.7.2-preview.8]",
            "define": "TESTS"
        }
    ],
    "noEngineReferences": false
}
```

## Assembly Definition Reference JSON
程序集定义参考是具有以下字段的JSON对象：

### reference string
需要。程序集定义以供参考。请参见装配体定义参考。

您可以使用程序集名称或资产的GUID引用程序集定义资产。可以使用AssetDatabase.AssetPathToGUID函数来检索资产的GUID。 （GUID也是与每个资产关联的元数据的一部分。）

使用程序集名称：
```json
{
    "reference": "AssemblyA"
}
```

使用程序集定义资产GUID
```json
{
    "reference": "GUID:f4de40948f4904ecb94b59dd38aab8a1"
}
```

参考 [Creating an Assembly Definition Reference asset](https://docs.unity3d.com/Manual/ScriptCompilationAssemblyDefinitionFiles.html#create-asmref).