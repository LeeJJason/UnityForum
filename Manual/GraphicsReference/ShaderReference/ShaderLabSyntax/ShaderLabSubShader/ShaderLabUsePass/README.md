## ShaderLab: UsePass
UsePass 命令使用其他shader中命名的pass

### Syntax
```
UsePass "Shader/Name"
```
插入指定shader中指定名字的所有pass。 *Shader/Name*包含shader的名字和pass的名字，用 "/" 分割。注意，只考虑第一个支持的[subshader](../README.md)。

### Details
一些shader会重用在其他shader中存在的pass，减少代码复制。比如，你可能有一个shader pass用于绘制物体的轮廓，并且你想要在其他shader重用这个pass。该 UsePass 命令做的仅仅是 - 从其他shader包含一个指定的pass。比如下面的例子，使用内置 VertexLit shader 中名字为“SHADOWCASTER”的 pass：
```
UsePass "VertexLit/SHADOWCASTER"
```

为了让 UsePass 工作，希望被使用的pass必须被命名。在pass中的[Name](../ShaderLabPass/ShaderLabName/README.md)命令设置名字。
```
Name "MyPassName"
```

注意所有pass中的名字都背转换为了大写，所以 UsePass 使用的名字必须为大写。