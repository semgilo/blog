+++
title = "实现photoshop里面的各种效果"
author = ["Donghai Ruan"]
date = 2021-10-18T10:46:00+08:00
lastmod = 2021-10-19T09:35:50+08:00
tags = ["Shader", "Unity"]
draft = false
+++

## 问题描述 {#问题描述}

需要配置美术实现，photoshop 里面的各种效果，比如叠加效果，强光。。。
<!--more-->


## 问题分析 {#问题分析}


### 颜色空间 {#颜色空间}

Unity 提供了设置[颜色空间](https://docs.unity3d.com/cn/current/Manual/LinearLighting.html)的设置，这个会对 shader 里面 tex2D 会有影响。


### 如何取到当前场景的颜色 Buffer {#如何取到当前场景的颜色-buffer}


#### 使用另外一台摄像机导出 RenderTexture {#使用另外一台摄像机导出-rendertexture}


#### 使用[GrabPass](https://docs.unity3d.com/cn/current/Manual/SL-GrabPass.html)来生成一张临时贴图 {#使用-grabpass-来生成一张临时贴图}


### 根据网上找到的 ps 的各种算法 {#根据网上找到的-ps-的各种算法}

{{< figure src="/images/photoshop_formula.jpg" >}}


### 关于 shader 的相关操作 {#关于-shader-的相关操作}

[示例shader](https://github.com/TwoTailsGames/Unity-Built-in-Shaders/blob/master/DefaultResourcesExtra/Particle%20Standard%20Unlit.shader)
[示例操作](https://github.com/TwoTailsGames/Unity-Built-in-Shaders/blob/master/Editor/StandardParticlesShaderGUI.cs)


### 调试 shader 的工具 {#调试-shader-的工具}

这个特别重要，因为只有通过观察里面的具体值才有办法分析，效果是否如期到屏幕上
[参考连接](https://zhuanlan.zhihu.com/p/74622572)


## 解决方案 {#解决方案}


### shader 管理类 {#shader-管理类}

```csharp
using UnityEngine;
using UnityEngine.UI;
using System.Collections;

using UnityEngine.Rendering;

[ExecuteInEditMode]
public class PhotoshopBlendMode : MonoBehaviour
{
    public enum BlendModeOption
    {
        Blend,          // SrcColor * SrcAlpha + DstColor * OneMinusSrcAlpha
        Darken,         // Min(SrcColor, DstColor)                              : SrcColor = lerp(1, SrcColor, SrcAlpha)
        Multiply,       // SrcColor * DstColor + DstColor * OneMinusSrcAlpha    : SrcColor = SrcColor * SrcAlpha
        ColorBurn,      // SrcColor + DstColor * (1 - SrcColor)                 : SrcColor = 1 - (1 / SrcColor)
        LinearBurn,     // DstColor + SrcColor - 1                              : SrcColor = (SrcColor - 1.0) * SrcAlpha
        Lighten,        // Max(SrcColor, DstColor)                              : SrcColor = lerp(0, SrcColor, SrcAlpha)
        Screen,         // SrcColor + DstColor * OneMinusSrcColor               : SrcColor = SrcColor * SrcAlpha
        ColorDodge,     // 0 + DstColor * SrcColor                              : SrcColor = (1 / (1 - SrcColor * SrcAlpha));
        LinearDodge,    // SrcColor * SrcAlpha + DstColor                       : SrcColor = SrcColor
        Overlay,
        // Overlay requires quite a lengthy explanation. We start off with the original formula
        //
        // 2 * Dst * Src,                       Dst < 0.5
        // 1 - 2 * (1 - Dst) * (1 - Src)        Dst > 0.5
        //
        // Because there are no conditionals in the blend units, an approximation would be to linearly
        // blend. The resulting formula after simplification is
        //
        // (4 * Src - 1) * Dst + (2 - 4 * Src) * Dst * Dst
        //
        // The only way to get Dst * Dst is to isolate the term and do it in two passes, therefore we divide
        // and end up with the following formula:
        //
        // [(4 * Src - 1) * Dst / (2 - 4 * Src) +  Dst * Dst ] * (2 - 4 * Src)
        //
        // This formula does not respect the background color. What we need is to linearly interpolate this
        // big formula with alpha, where an alpha of 0 returns Dst. Therefore
        //
        // [(4 * Src - 1) * Dst / (2 - 4 * Src) +  Dst * Dst ] * (2 - 4 * Src) * a + Dst * (1 - a)
        //
        // If we include the last term into the original formula, we can still do it in 2 passes. We need to
        // be careful to clamp the alpha value with max(0.001, a) because we're now potentially dividing by 0.
        // The final formula is
        //
        // K_1 = (4 * Src - 1) / (2 - 4 * Src)
        // K_2 = (1 - a) / ((2 - 4 * Src) * a)
        //
        // [Dst * [K_1 + K_2] +  Dst * Dst ] * (2 - 4 * Src) * a
        SoftLight,      // Same as overlay but main formula after simplification is 2 * Src * Dst + (1 - 2 * Src) * Dst * Dst
                        // Source: Pegtop's formula
        HardLight,
        VividLight,
        LinearLight,
        //PinLight, // Seems impossible to implement because it uses min and max
        //Difference,
        //Exclusion,
        //Luminosity
        BetterOverlay,

    }

    public BlendModeOption blendModeOption;
    public Shader shader;

    // Use this for initialization
    void Start ()
    {

    }

    void SetBlendModeHint(Material mat, BlendModeOption blendMode)
    {
        mat.SetFloat("_BlendMode", (float) blendMode);
    }

    void SetBlendOperation1(Material mat, BlendOp blendOp, BlendMode blendSrc, BlendMode blendDst)
    {
        SetBlendOperation1(mat, blendOp, blendSrc, blendDst, blendSrc, blendDst);
    }

    void SetBlendOperation1(Material mat, BlendOp colorBlendOp, BlendMode blendSrc, BlendMode blendDst, BlendMode alphaBlendSrc, BlendMode alphaBlendDst)
    {
        mat.SetFloat("_BlendOp1", (float) colorBlendOp);
        mat.SetFloat("_BlendSrc1", (float) blendSrc);
        mat.SetFloat("_BlendDst1", (float) blendDst);
        mat.SetFloat("_BlendSrcAlpha1", (float) alphaBlendSrc);
        mat.SetFloat("_BlendDstAlpha1", (float) alphaBlendDst);
    }

    void SetBlendOperation2(Material mat, BlendOp blendOp, BlendMode blendSrc, BlendMode blendDst)
    {
        SetBlendOperation2(mat, blendOp, blendSrc, blendDst, blendSrc, blendDst);
    }

    void SetBlendOperation2(Material mat, BlendOp colorBlendOp, BlendMode blendSrc, BlendMode blendDst, BlendMode alphaBlendSrc, BlendMode alphaBlendDst)
    {
        mat.SetFloat("_BlendOp2", (float)colorBlendOp);
        mat.SetFloat("_BlendSrc2", (float)blendSrc);
        mat.SetFloat("_BlendDst2", (float)blendDst);
        mat.SetFloat("_BlendSrcAlpha2", (float)alphaBlendSrc);
        mat.SetFloat("_BlendDstAlpha2", (float)alphaBlendDst);
    }

    // Update is called once per frame
    void Update ()
    {

        Material mat = null;
        if (GetComponent<SpriteRenderer>())
        {
            mat = GetComponent<SpriteRenderer>().sharedMaterial;
        }

        if (GetComponent<MeshRenderer>())
        {
            mat = GetComponent<MeshRenderer>().sharedMaterial;
        }

        if (GetComponent<Image>())
        {
            mat = GetComponent<Image>().material;
        }

        if (mat)
        {
            shader = mat.shader;

            SetBlendOperation2(mat, BlendOp.Add, BlendMode.Zero, BlendMode.One); // Defaults to background passthrough
            mat.SetShaderPassEnabled("ForwardBase", false);

            switch (blendModeOption)
            {
                case BlendModeOption.Blend:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.SrcAlpha, BlendMode.OneMinusSrcAlpha);
                    break;
                case BlendModeOption.Darken:
                    SetBlendOperation1(mat, BlendOp.Min, BlendMode.One, BlendMode.One);
                    break;
                case BlendModeOption.Multiply:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.OneMinusSrcAlpha);
                    break;
                case BlendModeOption.ColorBurn:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.One, BlendMode.OneMinusSrcColor);
                    break;
                case BlendModeOption.LinearBurn:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.One, BlendMode.One);
                    break;
                case BlendModeOption.Lighten:
                    SetBlendOperation1(mat, BlendOp.Max, BlendMode.One, BlendMode.One);
                    break;
                case BlendModeOption.Screen:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.One, BlendMode.OneMinusSrcColor);
                    break;
                case BlendModeOption.ColorDodge:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.Zero);
                    break;
                case BlendModeOption.LinearDodge:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.SrcAlpha, BlendMode.One);
                    break;
                case BlendModeOption.Overlay:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.DstColor);
                    SetBlendOperation2(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.Zero);
                    break;
                case BlendModeOption.BetterOverlay:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.One, BlendMode.Zero);
                    mat.SetShaderPassEnabled("ForwardBase", true);
                    break;
                case BlendModeOption.SoftLight:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.DstColor);
                    SetBlendOperation2(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.Zero);
                    break;
                case BlendModeOption.HardLight:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.One, BlendMode.One);
                    SetBlendOperation2(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.Zero);
                    break;
                case BlendModeOption.VividLight:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.DstColor, BlendMode.Zero);
                    SetBlendOperation2(mat, BlendOp.Add, BlendMode.One, BlendMode.OneMinusSrcColor);
                    break;
                case BlendModeOption.LinearLight:
                    SetBlendOperation1(mat, BlendOp.Add, BlendMode.One, BlendMode.One);
                    //SetBlendOperation2(mat, BlendOp.Add, BlendMode.One, BlendMode.SrcColor);
                    break;

            }

            SetBlendModeHint(mat, blendModeOption);

            mat.DisableKeyword("_ALPHATEST_ON");
            mat.EnableKeyword("_ALPHABLEND_ON");
            mat.DisableKeyword("_ALPHAPREMULTIPLY_ON");
        }
    }
}

```


### 使用定制 shader {#使用定制-shader}

```c
// Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'

// unlit, vertex colour, alpha blended
// cull off

Shader "Custom/PhotoshopBlendEffect"
{
    Properties
    {
        _MainTex("EffectTexture (RGB)", 2D) = "white" {}

        // This here gives hints to the shader to do pixel-time tricks
        _BlendMode("BlendMode", Range(0, 16)) = 0 // 0 is standard blend

        [HideInInspector] _BlendOp1("__op1", Float) = 0.0
        [HideInInspector] _BlendSrc1("__src1", Float) = 1.0
        [HideInInspector] _BlendDst1("__dst1", Float) = 0.0
        [HideInInspector] _BlendSrcAlpha1("__src_alpha1", Float) = 1.0
        [HideInInspector] _BlendDstAlpha1("__dst_alpha1", Float) = 0.0

        [HideInInspector] _BlendOp2("__op2", Float) = 0.0
        [HideInInspector] _BlendSrc2("__src2", Float) = 1.0
        [HideInInspector] _BlendDst2("__dst2", Float) = 0.0
        [HideInInspector] _BlendSrcAlpha2("__src_alpha2", Float) = 1.0
        [HideInInspector] _BlendDstAlpha2("__dst_alpha2", Float) = 0.0
    }

    SubShader
    {
        Tags{ "Queue" = "Transparent" "IgnoreProjector" = "True" "RenderType" = "Transparent" }
        ZWrite Off Lighting Off Cull Off Fog{ Mode Off }
        LOD 110

        // 截取当前背景做底图
        GrabPass
        {
            Tags { "LightMode" = "ForwardBase" }
            "_BackgroundTexture"
        }

        Pass
        {
            BlendOp[_BlendOp1]
            Blend[_BlendSrc1][_BlendDst1],[_BlendSrcAlpha1][_BlendDstAlpha1]

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #pragma fragmentoption ARB_precision_hint_fastest
            #pragma enable_d3d11_debug_symbols
            #include "UnityCG.cginc"

            sampler2D _MainTex;
            sampler2D _BackgroundTexture;
            float _BlendMode;

            struct vin
            {
                float4 vertex : POSITION;
                float2 texcoord : TEXCOORD0;
                float4 color : COLOR;
            };

            struct v2f
            {
                float4 vertex : POSITION;
                float2 texcoord : TEXCOORD0;
                float4 color : COLOR;
                float4 grabPos : TEXCOORD1;
            };

            v2f vert(vin v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.texcoord = v.texcoord;
                o.color = v.color;

                if (_BlendMode == 14)
                {
                    o.grabPos = ComputeGrabScreenPos(o.vertex);
                }

                return o;
            }

            fixed4 frag(v2f i) : COLOR
            {
                float4 color = tex2D(_MainTex, i.texcoord);

                if (_BlendMode == 1) // Darken
                {
                    color.rgb = lerp(float3(1, 1, 1), color.rgb, color.a);
                }
                else if (_BlendMode == 2) // Multiply
                {
                    color.rgb *= color.a;
                }
                else if (_BlendMode == 3) // Color Burn
                {
                    color.rgb = 1.0 - (1.0 / max(0.001, color.rgb * color.a + 1.0 - color.a)); // max to avoid infinity
                }
                else if (_BlendMode == 4) // Linear Burn
                {
                    color.rgb = (color.rgb - 1.0) * color.a;
                }
                else if (_BlendMode == 5) // Lighten
                {
                    color.rgb = lerp(float3(0, 0, 0), color.rgb, color.a);
                }
                else if (_BlendMode == 6) // Screen
                {
                    color.rgb *= color.a;
                }
                else if (_BlendMode == 7) // Color Dodge
                {
                    color.rgb = 1.0 / max(0.01, (1.0 - color.rgb * color.a));
                }
                else if (_BlendMode == 8) // Linear Dodge
                {
                    // Do nothing
                }
                else if (_BlendMode == 9) // Overlay
                {
                    color.rgb *= color.a;
                    float3 desiredValue = (4.0 * color.rgb - 1.0) / (2.0 - 4.0 * color.rgb);
                    float3 backgroundValue = (1.0 - color.a) / ((2.0 - 4.0 * color.rgb) * max(0.001, color.a));
                    color.rgb = desiredValue + backgroundValue;
                }
                else if (_BlendMode == 10) // Soft Light
                {
                    float3 desiredValue = 2.0 * color.rgb * color.a / (1.0 - 2.0 * color.rgb * color.a);
                    float3 backgroundValue = (1.0 - color.a) / ((1.0 - 2.0 * color.rgb * color.a) * max(0.001, color.a));

                    color.rgb = desiredValue + backgroundValue;
                }
                else if (_BlendMode == 11) // Hard Light
                {
                    float3 numerator = (2.0 * color.rgb * color.rgb - color.rgb) * (color.a);
                    float3 denominator = max(0.001, (4.0 * color.rgb - 4.0 * color.rgb * color.rgb) * (color.a) + 1.0 - color.a);
                    color.rgb = numerator / denominator;
                }
                else if (_BlendMode == 12) // Vivid Light
                {
                    color.rgb *= color.a;
                    color.rgb = color.rgb >= float3(0.5, 0.5, 0.5) ? (1.0 / max(0.0001, 2.0 - 2.0 * color.rgb)) : float3(1.0, 1.0, 1.0);
                }
                else if (_BlendMode == 13) // Linear Light
                {
                    color.rgb = (2 * color.rgb - 1.0) * color.a;
                }
                else if (_BlendMode == 14) // Better Overlay
                {
                    half4 A = tex2Dproj(_BackgroundTexture, i.grabPos);
                    half4 B = color;
                    fixed4 flag = step(A, fixed4(0.5, 0.5, 0.5, 0.5));
                    color = flag * A * B * 2 + (1 - flag) * (1 - (1 - A) * (1 - B) * 2);
                }

                return color;
            }

            ENDCG
        }

        Pass
        {
            BlendOp[_BlendOp2]
            Blend[_BlendSrc2][_BlendDst2],[_BlendSrcAlpha2][_BlendDstAlpha2]

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #pragma fragmentoption ARB_precision_hint_fastest
            #pragma enable_d3d11_debug_symbols
            #include "UnityCG.cginc"

            sampler2D _MainTex;
            float _BlendMode;

            struct vin
            {
                float4 vertex : POSITION;
                float2 texcoord : TEXCOORD0;
                float4 color : COLOR;
            };

            struct v2f
            {
                float4 vertex : POSITION;
                float2 texcoord : TEXCOORD0;
                float4 color : COLOR;
            };

            v2f vert(vin v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.texcoord = v.texcoord;
                o.color = v.color;
                return o;
            }

            fixed4 frag(v2f i) : COLOR
            {
                float4 color = tex2D(_MainTex, i.texcoord);
                color.rgb *= i.color.rgb;
                color.a *= i.color.a;

                if (_BlendMode == 9) // Overlay
                {
                    // 模拟的版本效果较差，使用GrabPass代替
                    // color.rgb *= color.a; // For alpha blending
                    // float3 value = (2.0 - 4.0 * color.rgb);
                    // color.rgb = value * max(0.001, color.a);
                }
                else if (_BlendMode == 10) // Soft Light
                {
                    color.rgb = (1.0 - 2.0 * color.rgb * color.a) * max(0.001, color.a);
                }
                else if (_BlendMode == 11) // Hard Light
                {
                    color.rgb = max(0.001, (4.0 * color.rgb - 4.0 * color.rgb * color.rgb) * (color.a) + 1.0 - color.a); // max because 0 goes to infinity
                }
                else if (_BlendMode == 12) // Vivid Light
                {
                    //color.rgb *= color.a;
                    color.rgb = color.rgb < 0.5 ? (color.a - color.a / max(0.0001, 2.0 * color.rgb)) : float3(0.0, 0.0, 0.0);
                }
                else if (_BlendMode == 13) // Linear Light
                {

                }

                return color;
            }

            ENDCG
        }
    }
}
```
