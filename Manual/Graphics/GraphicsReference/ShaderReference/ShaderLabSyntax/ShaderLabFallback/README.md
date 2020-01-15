## ShaderLab: Fallback
在所有 **Subshaders** 之后，能定义一个 Fallback。它的基本功能是 “如果没有一个 **Subshaders** 能在硬件上运行，尝试从其他 **shader**” 中使用一个。

### Syntax
```cs
 Fallback "name"
```

Fallback 指定shader名字或者
```cs
Fallback Off
```

明确指定这儿没有 fallback，并且没有警告会显示，即使没有 subshaders 能在当前硬件上运行。

## Details
fallback 与将其他shader中的所有 subshaders 插入当前shader的效果一样。

## Example
```cs
Shader "example" {
        // properties and subshaders here...
        Fallback "otherexample"
    }
```