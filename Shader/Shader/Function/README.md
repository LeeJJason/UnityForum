
表 3-1 HLSL内置函数

函数名|用法
:--|:---
abs|计算输入值的绝对值。
acos|返回输入值反余弦值。
all|测试非0值。
any|测试输入值中的任何非零值。
asin|返回输入值的反正弦值。
atan|返回输入值的反正切值。
atan2|返回y/x的反正切值。
ceil|返回大于或等于输入值的最小整数。
clamp|把输入值限制在[min,|max]范围内。
clip|如果输入向量中的任何元素小于0，则丢弃当前像素。
cos|返回输入值的余弦。
cosh|返回输入值的双曲余弦。
cross|返回两个3D向量的叉积。
ddx|返回关于屏幕坐标x轴的偏导数。
ddy|返回关于屏幕坐标y轴的偏导数。
degrees|弧度到角度的转换
determinant|返回输入矩阵的值。
distance|返回两个输入点间的距离。
dot|返回两个向量的点积。
exp|返回以e为底数，输入值为指数的指数函数值。
exp2|返回以2为底数，输入值为指数的指数函数值。
faceforward|检测多边形是否位于正面。
floor|返回小于等于x的最大整数。
fmod|返回a|/|b的浮点余数。
frac|返回输入值的小数部分。
frexp|返回输入值的尾数和指数
fwidth|返回|abs|(|ddx|(x)|+|abs|(|ddy(x))。
isfinite|如果输入值为有限值则返回true，否则返回false。
isinf|如何输入值为无限的则返回true。
isnan|如果输入值为NAN或QNAN则返回true。
ldexp|frexp的逆运算，返回|x|*|2|^|exp。
len|/|lenth|返回输入向量的长度。
lerp|对输入值进行插值计算。
lit|返回光照向量（环境光，漫反射光，镜面高光，1）。
log|返回以e为底的对数。
log10|返回以10为底的对数。
log2|返回以2为底的对数。
max|返回两个输入值中较大的一个。
min|返回两个输入值中较小的一个。
modf|把输入值分解为整数和小数部分。
mul|返回输入矩阵相乘的积。
normalize|返回规范化的向量，定义为|x|/|length(x)。
pow|返回输入值的指定次幂。
radians|角度到弧度的转换。
reflect|返回入射光线i对表面法线n的反射光线。
refract|返回在入射光线i，表面法线n，折射率为eta下的折射光线v。
round|返回最接近于输入值的整数。
rsqrt|返回输入值平方根的倒数。
saturate|把输入值限制到[0,|1]之间。
sign|计算输入值的符号。
sin|计算输入值的正弦值。
sincos|返回输入值的正弦和余弦值。
sinh|返回x的双曲正弦。
smoothstep|返回一个在输入值之间平稳变化的插值。
sqrt|返回输入值的平方根。
step|返回（x|>=|a）?|1|:|0。
tan|返回输入值的正切值。
fanh|返回输入值的双曲线切线。
transpose|返回输入矩阵的转置。
tex1D*|1D纹理查询。
tex2D*|2D纹理查询。
tex3D*|3D纹理查询。
texCUBE*|立方纹理查询。

Intrinsic Functions (DirectX HLSL)
The following table lists the intrinsic functions available in HLSL. Each function has a brief description, and a link to a reference page that has more detail about the input argument and return type.

Name | Syntax | Description 
 :--- | :--- | :---- 
abs|abs(x)|Absolute value (per component).
acos|acos(x)|Returns the arccosine of each component of x.
all|all(x)|Test if all components of x are nonzero.
any|any(x)|Test if any component of x is nonzero.
asfloat|asfloat(x)|Convert the input type to a float.
asin|asin(x)|Returns the arcsine of each component of x.
asint|asint(x)|Convert the input type to an integer.
asuint|asuint(x)|Convert the input type to an unsigned integer.
atan|atan(x)|Returns the arctangent of x.
atan2|atan2(y, x)|Returns the arctangent of of two values (x,y).
ceil|ceil(x)|Returns the smallest integer which is greater than or equal to x.
clamp|clamp(x, min, max)|Clamps x to the range [min, max].
clip|clip(x)|Discards the current pixel, if any component of x is less than zero.
cos|cos(x)|Returns the cosine of x.
cosh|cosh(x)|Returns the hyperbolic cosine of x.
cross|cross(x, y)|Returns the cross product of two 3D vectors.
D3DCOLORtoUBYTE4|D3DCOLORtoUBYTE4(x)|Swizzles and scales components of the 4D vector x to compensate for the lack of UBYTE4 support in some hardware.
ddx|ddx(x)|Returns the partial derivative of x with respect to the screen-space x-coordinate.
ddy|ddy(x)|Returns the partial derivative of x with respect to the screen-space y-coordinate.
degrees|degrees(x)|Converts x from radians to degrees.
determinant|determinant(m)|Returns the determinant of the square matrix m.
distance|distance(x, y)|Returns the distance between two points.
dot|dot(x, y)|Returns the dot product of two vectors.
exp|exp(x)|Returns the base-e exponent.
exp2|exp2(x)|Base 2 exponent (per component).
faceforward|faceforward(n, i, ng)|Returns -n * sign(•(i, ng)).
floor|floor(x)|Returns the greatest integer which is less than or equal to x.
fmod|fmod(x, y)|Returns the floating point remainder of x/y.
frac|frac(x)|Returns the fractional part of x.
frexp|frexp(x, exp)|Returns the mantissa and exponent of x.
fwidth|fwidth(x)|Returns abs(ddx(x)) + abs(ddy(x))
GetRenderTargetSampleCount|GetRenderTargetSampleCount()|Returns the number of render-target samples.
GetRenderTargetSamplePosition|GetRenderTargetSamplePosition(x)|Returns a sample position (x,y) for a given sample index.
isfinite|isfinite(x)|Returns true if x is finite, false otherwise.
isinf|isinf(x)|Returns true if x is +INF or -INF, false otherwise.
isnan|isnan(x)|Returns true if x is NAN or QNAN, false otherwise.
ldexp|ldexp(x, exp)|Returns x * 2exp
length|length(v)|Returns the length of the vector v.
lerp|lerp(x, y, s)|Returns x + s(y - x).
lit|lit(n • l, n • h, m)|Returns a lighting vector (ambient, diffuse, specular, 1)
log|log(x)|Returns the base-e logarithm of x.
log10|log10(x)|Returns the base-10 logarithm of x.
log2|log2(x)|Returns the base-2 logarithm of x.
max|max(x, y)|Selects the greater of x and y.
min|min(x, y)|Selects the lesser of x and y.
modf|modf(x, out ip)|Splits the value x into fractional and integer parts.
mul|mul(x, y)|Performs matrix multiplication using x and y.
noise|noise(x)|Generates a random value using the Perlin-noise algorithm.
normalize|normalize(x)|Returns a normalized vector.
pow|pow(x, y)|Returns xy.
radians|radians(x)|Converts x from degrees to radians.
reflect|reflect(i, n)|Returns a reflection vector.
refract|refract(i, n, R)|Returns the refraction vector.
round|round(x)|Rounds x to the nearest integer
rsqrt|rsqrt(x)|Returns 1 / sqrt(x)
saturate|saturate(x)|Clamps x to the range [0, 1]
sign|sign(x)|Computes the sign of x.
sin|sin(x)|Returns the sine of x
sincos|sincos(x, out s, out c)|Returns the sine and cosine of x.
sinh|sinh(x)|Returns the hyperbolic sine of x
smoothstep|smoothstep(min, max, x)|Returns a smooth Hermite interpolation between 0 and 1.
sqrt|sqrt(x)|Square root (per component)
step|step(a, x)|Returns (x >= a) ? 1 : 0
tan|tan(x)|Returns the tangent of x
tanh|tanh(x)|Returns the hyperbolic tangent of x
tex1D|tex1D(s, t)|1D texture lookup.
tex1Dbias|tex1Dbias(s, t)|1D texture lookup with bias.
tex1Dgrad|tex1Dgrad(s, t, ddx, ddy)|1D texture lookup with a gradient.
tex1Dlod|tex1Dlod(s, t)|1D texture lookup with LOD.
tex1Dproj|tex1Dproj(s, t)|1D texture lookup with projective divide.
tex2D|tex2D(s, t)|2D texture lookup.
tex2Dbias|tex2Dbias(s, t)|2D texture lookup with bias.
tex2Dgrad|tex2Dgrad(s, t, ddx, ddy)|2D texture lookup with a gradient.
tex2Dlod|tex2Dlod(s, t)|2D texture lookup with LOD.
tex2Dproj|tex2Dproj(s, t)|2D texture lookup with projective divide.
tex3D|tex3D(s, t)|3D texture lookup.
tex3Dbias|tex3Dbias(s, t)|3D texture lookup with bias.
tex3Dgrad|tex3Dgrad(s, t, ddx, ddy)|3D texture lookup with a gradient.
tex3Dlod|tex3Dlod(s, t)|3D texture lookup with LOD.
tex3Dproj|tex3Dproj(s, t)|3D texture lookup with projective divide.
texCUBE|texCUBE(s, t)|Cube texture lookup.
texCUBEbias|texCUBEbias(s, t)|Cube texture lookup with bias.
texCUBEgrad|texCUBEgrad(s, t, ddx, ddy)|Cube texture lookup with a gradient.
texCUBElod|tex3Dlod(s, t)|Cube texture lookup with LOD.
texCUBEproj|texCUBEproj(s, t)|Cube texture lookup with projective divide.
transpose|transpose(m)|Returns the transpose of the matrix m.
trunc|trunc(x)|Truncates floating-point value(s) to integer value(s)

Intrinsic Functions (DirectX HLSL)
The following table lists the intrinsic functions available in HLSL. Each function has a brief description, and a link to a reference page that has more detail about the input argument and return type.

Name|Description|Minimum shader model
:---|:---|:---
abs|Absolute value (per component).|11
acos|Returns the arccosine of each component of x.|11
all|Test if all components of x are nonzero.|11
AllMemoryBarrier|Blocks execution of all threads in a group until all memory accesses have been completed.|5
AllMemoryBarrierWithGroupSync|Blocks execution of all threads in a group until all memory accesses have been completed and all threads in the group have reached this call.|5
any|Test if any component of x is nonzero.|11
asdouble|Reinterprets a cast value into a double.|5
asfloat|Convert the input type to a float.|4
asin|Returns the arcsine of each component of x.|11
asint|Convert the input type to an integer.|4
asuint|Reinterprets the bit pattern of a 64-bit type to a uint.|5
asuint|Convert the input type to an unsigned integer.|4
atan|Returns the arctangent of x.|11
atan2|Returns the arctangent of of two values (x,y).|11
ceil|Returns the smallest integer which is greater than or equal to x.|11
clamp|Clamps x to the range [min, max].|11
clip|Discards the current pixel, if any component of x is less than zero.|11
cos|Returns the cosine of x.|11
cosh|Returns the hyperbolic cosine of x.|11
countbits|Counts the number of bits (per component) in the input integer.|5
cross|Returns the cross product of two 3D vectors.|11
D3DCOLORtoUBYTE4|Swizzles and scales components of the 4D vector xto compensate for the lack of UBYTE4 support in some hardware.|11
ddx|Returns the partial derivative of x with respect to the screen-space x-coordinate.|21
ddx_coarse|Computes a low precision partial derivative with respect to the screen-space x-coordinate.|5
ddx_fine|Computes a high precision partial derivative with respect to the screen-space x-coordinate.|5
ddy|Returns the partial derivative of x with respect to the screen-space y-coordinate.|21
ddy_coarse|Computes a low precision partial derivative with respect to the screen-space y-coordinate.|5
ddy_fine|Computes a high precision partial derivative with respect to the screen-space y-coordinate.|5
degrees|Converts x from radians to degrees.|11
determinant|Returns the determinant of the square matrix m.|11
DeviceMemoryBarrier|Blocks execution of all threads in a group until all device memory accesses have been completed.|5
DeviceMemoryBarrierWithGroupSync|Blocks execution of all threads in a group until all device memory accesses have been completed and all threads in the group have reached this call.|5
distance|Returns the distance between two points.|11
dot|Returns the dot product of two vectors.|1
dst|Calculates a distance vector.|5
EvaluateAttributeAtCentroid|Evaluates at the pixel centroid.|5
EvaluateAttributeAtSample|Evaluates at the indexed sample location.|5
EvaluateAttributeSnapped|Evaluates at the pixel centroid with an offset.|5
exp|Returns the base-e exponent.|11
exp2|Base 2 exponent (per component).|11
f16tof32|Converts the float16 stored in the low-half of the uint to a float.|5
f32tof16|Converts an input into a float16 type.|5
faceforward|Returns -n * sign(dot(i, ng)).|11
firstbithigh|Gets the location of the first set bit starting from the highest order bit and working downward, per component.|5
firstbitlow|Returns the location of the first set bit starting from the lowest order bit and working upward, per component.|5
floor|Returns the greatest integer which is less than or equal to x.|11
fmod|Returns the floating point remainder of x/y.|11
frac|Returns the fractional part of x.|11
frexp|Returns the mantissa and exponent of x.|21
fwidth|Returns abs(ddx(x)) + abs(ddy(x))|21
GetRenderTargetSampleCount|Returns the number of render-target samples.|4
GetRenderTargetSamplePosition|Returns a sample position (x,y) for a given sample index.|4
GroupMemoryBarrier|Blocks execution of all threads in a group until all group shared accesses have been completed.|5
GroupMemoryBarrierWithGroupSync|Blocks execution of all threads in a group until all group shared accesses have been completed and all threads in the group have reached this call.|5
InterlockedAdd|Performs a guaranteed atomic add of value to the dest resource variable.|5
InterlockedAnd|Performs a guaranteed atomic and.|5
InterlockedCompareExchange|Atomically compares the input to the comparison value and exchanges the result.|5
InterlockedCompareStore|Atomically compares the input to the comparison value.|5
InterlockedExchange|Assigns value to dest and returns the original value.|5
InterlockedMax|Performs a guaranteed atomic max.|5
InterlockedMin|Performs a guaranteed atomic min.|5
InterlockedOr|Performs a guaranteed atomic or.|5
InterlockedXor|Performs a guaranteed atomic xor.|5
isfinite|Returns true if x is finite, false otherwise.|11
isinf|Returns true if x is +INF or -INF, false otherwise.|11
isnan|Returns true if x is NAN or QNAN, false otherwise.|11
ldexp|Returns x * 2exp|11
length|Returns the length of the vector v.|11
lerp|Returns x + s(y - x).|11
lit|Returns a lighting vector (ambient, diffuse, specular, 1)|11
log|Returns the base-e logarithm of x.|11
log10|Returns the base-10 logarithm of x.|11
log2|Returns the base-2 logarithm of x.|11
mad|Performs an arithmetic multiply/add operation on three values.|5
max|Selects the greater of x and y.|11
min|Selects the lesser of x and y.|11
modf|Splits the value x into fractional and integer parts.|11
mul|Performs matrix multiplication using x and y.|1
noise|Generates a random value using the Perlin-noise algorithm.|11
normalize|Returns a normalized vector.|11
pow|Returns xy.|11
Process2DQuadTessFactorsAvg|Generates the corrected tessellation factors for a quad patch.|5
Process2DQuadTessFactorsMax|Generates the corrected tessellation factors for a quad patch.|5
Process2DQuadTessFactorsMin|Generates the corrected tessellation factors for a quad patch.|5
ProcessIsolineTessFactors|Generates the rounded tessellation factors for an isoline.|5
ProcessQuadTessFactorsAvg|Generates the corrected tessellation factors for a quad patch.|5
ProcessQuadTessFactorsMax|Generates the corrected tessellation factors for a quad patch.|5
ProcessQuadTessFactorsMin|Generates the corrected tessellation factors for a quad patch.|5
ProcessTriTessFactorsAvg|Generates the corrected tessellation factors for a tri patch.|5
ProcessTriTessFactorsMax|Generates the corrected tessellation factors for a tri patch.|5
ProcessTriTessFactorsMin|Generates the corrected tessellation factors for a tri patch.|5
radians|Converts x from degrees to radians.|1
rcp|Calculates a fast, approximate, per-component reciprocal.|5
reflect|Returns a reflection vector.|1
refract|Returns the refraction vector.|11
reversebits|Reverses the order of the bits, per component.|5
round|Rounds x to the nearest integer|11
rsqrt|Returns 1 / sqrt(x)|11
saturate|Clamps x to the range [0, 1]|1
sign|Computes the sign of x.|11
sin|Returns the sine of x|11
sincos|Returns the sine and cosine of x.|11
sinh|Returns the hyperbolic sine of x|11
smoothstep|Returns a smooth Hermite interpolation between 0 and 1.|11
sqrt|Square root (per component)|11
step|Returns (x >= a) ? 1 : 0|11
tan|Returns the tangent of x|11
tanh|Returns the hyperbolic tangent of x|11
tex1D(s, t)|1D texture lookup.|1
tex1D(s, t, ddx, ddy)|1D texture lookup.|21
tex1Dbias|1D texture lookup with bias.|21
tex1Dgrad|1D texture lookup with a gradient.|21
tex1Dlod|1D texture lookup with LOD.|31
tex1Dproj|1D texture lookup with projective divide.|21
tex2D(s, t)|2D texture lookup.|11
tex2D(s, t, ddx, ddy)|2D texture lookup.|21
tex2Dbias|2D texture lookup with bias.|21
tex2Dgrad|2D texture lookup with a gradient.|21
tex2Dlod|2D texture lookup with LOD.|3
tex2Dproj|2D texture lookup with projective divide.|21
tex3D(s, t)|3D texture lookup.|11
tex3D(s, t, ddx, ddy)|3D texture lookup.|21
tex3Dbias|3D texture lookup with bias.|21
tex3Dgrad|3D texture lookup with a gradient.|21
tex3Dlod|3D texture lookup with LOD.|31
tex3Dproj|3D texture lookup with projective divide.|21
texCUBE(s, t)|Cube texture lookup.|11
texCUBE(s, t, ddx, ddy)|Cube texture lookup.|21
texCUBEbias|Cube texture lookup with bias.|21
texCUBEgrad|Cube texture lookup with a gradient.|21
texCUBElod|Cube texture lookup with LOD.|31
texCUBEproj|Cube texture lookup with projective divide.|21
transpose|Returns the transpose of the matrix m.|1
trunc|Truncates floating-point value(s) to integer value(s)|1
 



## 参考文章
[每天30分钟看Shader--(1)HLSL固有函数 【Intrinsic Functions (DirectX HLSL)】](http://www.cppblog.com/lai3d/archive/2008/10/23/64889.html)  
[Intrinsic Functions (DirectX HLSL)](https://docs.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl-intrinsic-functions)