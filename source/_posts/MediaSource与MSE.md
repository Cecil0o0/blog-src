---
title: MediaSource 与 MSE
url: 1111.html
id: 1111
categories:
  - technology
date: 2019-08-24 13:57:40
tags:
---

*   **适用群体**

> 有一定前端知识的用户群体

*   **MSE概述**

> 结论是MSE提供了实现无插件且基于web的流媒体的能力。可以支持比如自适应码率，精确控制时间的H5直播和点播功能。业界比较出名的有DASH以及HLS协议，这两种协议都基于http协议，有较好的防火墙穿透功能(只要防火墙不拦截80与443端口流量，而一般来说都不会拦截)，HLS对苹果设备极度友好而DASH跨平台兼容性更好，不过这两者劣势都是实时性不好通常延迟有好几秒之长，取而代之的一般是基于web技术有webRTC以及非web技术RTP。

*   **MediaSource概述/含义**

> 属于Media Source Extensions(MSE)标准中的一个接口，每一个MediaSource实例代表了一个媒体源数据应用于HTML媒体元素比如`<video/>`或`<audio/>`，通常附着于HTML媒体元素在用户代理中播放。

*   **本质**

> MediaSource = EventTarget + SourceBuffer + 其他属性比如说duration和readyState + 其他方法比如说isTypeSupported

*   **用处**

> 高级的音视频媒体流播放方式代替传统URL类型

*   **用法**

    import React, { Component } from "react";
    import webmUrl from './3.webm';
    
    class VideoPlayer extends Component {
      videoDom;
      getUserMedia() {
        return navigator.mediaDevices.getUserMedia({ video: true, audio: true });
      }
      play = (url, mimeType = "webm") => {
        let mimeCodec, mediaSource;
        if (mimeType === "webm") {
          mimeCodec = 'video/webm; codecs="vorbis, vp8"';
        }
    
        const sourceOpen = () => {
          // 新增mediaSource对象
          let sourceBuffer = mediaSource.addSourceBuffer(mimeCodec);
          fetch(url, {
            responseType: 'arraybuffer'
          }).then(data => data.arrayBuffer()).then(buffer => {
            sourceBuffer.addEventListener("updateend", () => {
              if (mediaSource.readyState === "open") {
                mediaSource.endOfStream();
              }
              this.videoDom.play();
            });
            sourceBuffer.appendBuffer(buffer);
          });
        };
    
        // 检查可用性
        if ("MediaSource" in window && MediaSource.isTypeSupported(mimeCodec)) {
          mediaSource = new MediaSource();
          this.videoDom.src = URL.createObjectURL(mediaSource);
          mediaSource.addEventListener("sourceopen", sourceOpen);
        } else {
          console.error("Unsupported MIME type or codec: ", mimeCodec);
        }
      };
      componentDidMount = () => {
        this.play(webmUrl);
      };
      render() {
        return (
          <div className="recorder-wrapper">
            <canvas style={ { width: 0, height: 0 }} />
            <video
              ref={dom => (this.videoDom = dom)}
              style={ { width: 400, height: 400 }}
            />
          </div>
        );
      }
    }
    
    export default VideoPlayer;
    

*   坑

    index.js:1509 Uncaught Error: The error you provided does not contain a stack trace.
        at B (index.js:1509)
        at G (index.js:1816)
        at index.js:1831
        at index.js:1850
        at index.js:1343
    B @ index.js:1509
    G @ index.js:1816
    (anonymous) @ index.js:1831
    (anonymous) @ index.js:1850
    (anonymous) @ index.js:1343
    stream-media.js:25 Uncaught (in promise) DOMException
    

> 根因是该媒体文件不兼容MSE标准（可能原因是容器格式不兼容也有可能是音视频编码器不兼容），请check mimeCodec和真实视频完全一致，要查看视频容器的源信息需要借助ffmpeg等三方工具。

*   Tips

> ffmpeg -i test.webm