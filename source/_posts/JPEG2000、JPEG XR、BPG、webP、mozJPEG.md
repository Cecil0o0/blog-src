---
title: JPEG2000、JPEG XR、BPG、webP、mozJPEG
tags:
  - BPG
  - JPG
  - JX2
  - JXR
  - mozJPEG
  - webP
url: 612.html
id: 612
categories:
  - technology
date: 2018-04-22 22:56:18
---

常见的图片格式

- JPEG, a lossy format compression, best for photographic and complex images. But the lossy part is means it's awful for some kinds of image,especially simple text
- GIF is lossless, and therefore good for text - but can only show 256 colors per image
- PNG is lossless format created to replace GIF. It can show more than 256 colors in an image

那么以上图片到底有哪些优化呢？

# JPEG2000

相较于 JPEG 压缩率更高，但由于计算复杂度高（其中涉及了小波变换）编解码速率要相对慢 10 倍左右，以及设计专利费问题，所以只是在小范围领域中大规模应用，比如数字影院（DCI 规范要求 Motion JPEG-2000），网络地图等。 目前仅 safari 浏览器支持，其他 Browsers 需要装插件查看。

# BGP

BGP 作为 HEVC（将来可能会取代目前主流的视频编码规范 H.264）的 Intra-Frame，采用先进的图像压缩协议，其有损压缩率是图片编码格式中最高且质量高的。对于业界几大巨头 Mozilla、微软、谷歌、苹果，由于苹果支持 BGP 会增加成本而且其他三家公司都自己的图像压缩技术沉淀，BGP 能不能流行还不好说。

# JPEG-XR

微软图像压缩技术，同样比传统 JPEG 压缩率高，成像质量高。仅 IE 支持

# webP

谷歌图像压缩技术，webM（谷歌的视频格式）的 intra frame。由于谷歌有 Android，相对其他公司更具竞争力，在谷歌生态中已经有 90%以上的覆盖率了。

# mozJPEG

Mozilla 的图像压缩技术，仅 FireFox 支持

# SSIM 值对比

![](https://pic1.zhimg.com/80/22276c2d45117ffbe38dad51f9a20694_hd.jpg)

- 不同压缩技术下每像素点压缩前后的 SSIM 值（结构相似度）多曲线图

# 图片对比（JPEG，webP）

一张 3832 \* 2554 尺寸的高清大图（源自 momentum），有 1.3M，转换为 webp 后是不到 1.1M。webp 相对 jpeg 的压缩率高 15%左右，且压缩前后质量的损耗人眼无感知

# 总结

一个图片格式能否流行基于其压缩率、编解码效率、计算复杂度等，取决于专利。
