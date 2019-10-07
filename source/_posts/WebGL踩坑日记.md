---
title: WebGL踩坑日记
url: 1102.html
id: 1102
categories:
  - technology
date: 2019-07-27 20:56:47
tags:
---

## 记录学习webgl踩坑

## 当gl.drawElements报警告时，请check未出现以下情况，如出现请先解决

- 索引数组中的最大值 \* 每个顶点所占浮点数个数（包含图元所需顶点数和Color）> 顶点数组长度

## 当gl.drawArrays报警告时，请check未出现以下情况，如出现请先解决

- 第二个参数Count数 * 每个顶点所占浮点数个数（包含图元所需顶点数和Color） > 顶点数组长度

## 当gl.drawElements和gl.drawArrays都没报错，但仍没有画面时，请check

- Color值从生成直到FragmentShader使用到该Color值前有没有做归一化或？换句话说就是可能被裁剪了。顶点值也是如此。
- 如果是用gl.TRIANGLE画几何体，注意是否画的是正面（FRONT\_FACE）即逆时针绘制。换句话说可能是gl.CULL\_FACE。

## 其他注意点

- attribute属性限制符只适用于顶点着色器而不适用于片元着色器
- engine内部代码注意不要出现循环依赖，如出现需及时修改避免留坑

## 照相机

- 正交照相机必须要使用aspect，否则渲染出来的图形会变形。

## snowy框架使用注意点

- 自定义geometry类中的positions属性必须归一化
- indices一般都要UInt8Array类型并且必须和drawElements的第三个参数对齐
- 光源颜色无需归一化且目前仅支持RGB，不支持HSL等
