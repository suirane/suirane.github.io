# WebRTC视频JitterBuffer详解

>  WebRTC版本：m74
>
> 参考文档
>
> [WebRTC视频JitterBuffer详解](https://blog.csdn.net/sonysuqin/article/details/106629343?spm=1001.2014.3001.5501)

## 概要

旧版的视频JitterBuffer实现在VCMJitterBuffer类中，目前已经不用，新版的JitterBuffer的功能被分散到多个模块中，主要包括：

* PacketBuffer：负责帧的完整性，保证组成帧的每个包序列号连续，并且有一个包标识帧的开始，有一个包标识帧的结束；
* RtpFrameReferenceFinder：负责给每个帧设置好参考帧，同时兼顾GOP内各帧的连续性；
* FrameBuffer：负责帧的连续性和可解码性，这里帧的连续性是指某帧的所有参考帧都已经收到，帧的可解码性是指某帧的所有参考帧都已经被解码；
* VCMJitterEstimator：计算抖动(googJitterbufferMS)，用于计算目标延迟(googTargetDelayMs)，用于音视频同步；
* VCMTiming：计算当前延迟(googCurrentDelayMs)，用于计算渲染时间。

本文对照代码描述上述模块的主要工作过程。
