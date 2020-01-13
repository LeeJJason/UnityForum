## ShaderLab: CustomEditor
可以为自己的**shader**定义CustomEditor。当你这样做，Unity会查找一个使用这个名字扩展ShaderGUI的类。如果找到一个，任意使用这个 **shader** 的材质都会使用 ShaderGUI。在 [Custom Shader GUI](../../AdvancedShaderLabTopics/CustomShaderGUI/README.md) 参考例子。

### Syntax
```cs
CustomEditor "name"
```
使用指定名字的 ShaderGUI。

## Details
CustomEditor 声明影响所有使用这个 Shader 的材质。

## Example
```cs
Shader "example" {
    // properties and subshaders here...
    CustomEditor "MyCustomEditor"
}
```