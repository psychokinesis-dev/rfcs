- 开始日期： 2016-07-10

# 概述

基于 [Psychokinesis](https://github.com/psychokinesis-dev/js-psychokinesis) 传输实时流媒体数据。通过 [Media Recorder API](https://w3c.github.io/mediacapture-record/MediaRecorder.html) 采集实时数据，并将其缓存至 Psychokinesis 网络，进而接收方定期通过 Psychokinesis 网络获得实时数据。大致流程如下：
![](live-stream.png)

# 目的

通过 Psychokinesis 网络传输实时流媒体数据。

# 实现细节

## MediaRecorder API
MediaRecorder API 是为浏览器处理流媒体而设计的一套简单实用的接口，它能够让开发者获取到直接可用于 Web 渲染的流媒体数据，而无需关注数据采集、编码等细节。此 API 已获得[大部分现代浏览器的支持](http://caniuse.com/#feat=mediarecorder)。

## 实时数据传输
获取到的流媒体数据按一定时间间隔存储于多份数据块中，数据块通过一份索引组织在一起。接收方首先获取索引，然后按时间间隔不断获取最新的数据块，页面则依次渲染已经获取到的完整数据块。

# 类似的设计
[HLS](https://developer.apple.com/streaming/) 一套由苹果公司设计的实时流媒体数据传输方案，目前官方仅提供对苹果设备的支持。

# 缺陷
## 支持的平台有限
MediaRecorder API 目前仅[较新的浏览器内核支持](http://caniuse.com/#feat=mediarecorder)。

## 延迟较大
取决于数据存储的时间间隔，时间间隔越大数据流的延迟则越大。因而此方案较适合于单向数据流传输，如直播等，不适合双向数据流传输，如视频聊天等。