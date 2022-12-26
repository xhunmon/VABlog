#总纲

## 简述
`AFPlayer`是指在Android上使用FFmpeg做的播放器。Android 上使用FFmpeg、OpenSL ES、OpenGL SE、MediaCodec等，实现简单的播放器，主要体现出相关知识点的使用。

- **[项目地址：AFPlayer](https://github.com/xhunmon/AFPlayer)**


- 第一章：[OpenSL ES播放PCM](./01_opensl_es.md)

  OpenSL ES全称 Open Sound Library for Embedded Systems，即嵌入式系统的开放音频库。是无授权费、跨平台、硬件加速的C语言音频API，用于2D和3D音频。
  

- 第二章：[OpenGL ES播放RGB](./02_opengl_es.md)

  OpenGL（英语：Open Graphics Library，译名：开放图形库或者“开放式图形库”）是用于渲染2D、3D矢量图形的跨语言、跨平台的应用程序编程接口（API）。而OpenGL ES （OpenGL for Embedded Systems）是三维图形应用程序接口OpenGL的子集，针对手机、PDA和游戏主机等嵌入式设备而设计，如Android 。

  

- 第三章：[FFmpeg实现MediaCodec解码](./03_mediacodec.md)

  `MediaCodec`是Android平台的硬件编解码，`FFmpeg`从3.1版本开始支持，但是，目前支持实现解码功能。[官网硬件编解码支持结束](https://trac.ffmpeg.org/wiki/HWAccelIntro)
  
  