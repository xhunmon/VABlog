## 一、前言
正所谓源于开源，回馈开源！以下是我个人的学习建议。丰富的**音视频资料**往最后翻。

## 二、学习技能

| 技能        | 重要度 | 作用                                                         | 学习建议                                 |
| ----------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| c/c++       | ★★★★☆  | 音视频开源库基本都是用c/c++写的，如：FFmpeg库用C语言写的，Webrtc底层是用c\+\+写的。 | 1. 看动脑或网易c/c++视频；2. 看书：c\+\+ primer 第5版；3. 看FFmpeg源码；4. 最重要自己动手敲。 |
| cmake       | ★★★☆☆  | 跨平台引导编译的重要语言。在CMakeList.txt文件体现。          | 1. 看动脑CMake中视频；2. [CMake 入门实战](https://www.hahack.com/codes/cmake/) |
| shell       | ★★☆☆☆  | 很多开源库都是通过shell脚本进行编译的。如ffmpeg和x264中configure。 |  [Shell脚本](https://qincji.gitee.io/2020/12/13/ffmpeg/02_shell/) |
| Android NDK | ★★☆☆☆  | 在android平台上使用，需要掌握NDK的一些知识。如：交叉编译，JNI的接入。 | 看动脑NDK中JNI和交叉编译视频；                            |
| IOS         | ★★☆☆☆  | （略）                                                       | （略）                                                       |

## 三、学习音视频理论知识
- [书：音视频开发进阶指南：基于Android与iOS平台的实践(京东)](https://item.jd.com/12292642.html) ：**第1章　音视频基础概念**；电子书往最后翻。
- [书：Android 音视频开发_何俊林(京东)](https://item.jd.com/12467530.html) ：**第1章　音视频基础知识**；电子书往最后翻。
- 也可以在[这里1](https://www.dalipan.com/) 或者[这里2](https://ebook2.lorefree.com/) 搜索。


## 四、学习音视频基本原理
### 1）视音频数据处理入门
[[总结]视音频编解码技术零基础学习方法](https://blog.csdn.net/leixiaohua1020/article/details/18893769) 系列文章，介绍了视音频编解码技术大体上原理和流程，通俗易懂。包括以下文章：
- [视音频数据处理入门：RGB、YUV像素数据处理](https://blog.csdn.net/leixiaohua1020/article/details/50534150) ：视频就是由它们组成的。
- [视音频数据处理入门：PCM音频采样数据处理](https://blog.csdn.net/leixiaohua1020/article/details/50534316) ：音频就是由它们组成的。
- [视音频数据处理入门：H.264视频码流解析](https://blog.csdn.net/leixiaohua1020/article/details/50534369) ：视频编码技术的一种（现代音视频开发必须掌握）。
- [视音频数据处理入门：AAC音频码流解析](https://blog.csdn.net/leixiaohua1020/article/details/50535042) ：音频编码技术的一种（现代音视频开发必须掌握）。
- [视音频数据处理入门：FLV封装格式解析](https://blog.csdn.net/leixiaohua1020/article/details/50535082) ：音视频封装格式的一种。具体一点看：[手撕FLV协议](https://qincji.gitee.io/2021/01/02/ffmpeg/07_flv/) 。
- [视音频数据处理入门：UDP-RTP协议解析](https://blog.csdn.net/leixiaohua1020/article/details/50535230) ：音视频协议的一种。

### 2）完整RTMP推送小项目 
[这个项目简单介绍音视频相关知识，以及实现的原理，总共分五章：](https://github.com/xhunmon/RtmpPush)
- [第一章：直播推流全过程：视频数据源之YUV（1）](https://qincji.gitee.io/2020/12/03/rtmppush/1-yuv/) 
- [第二章：直播推流全过程：音频数据源之PCM（2）](https://qincji.gitee.io/2020/12/04/rtmppush/2-pcm/)
- [第三章：直播推流全过程：视频编码之H.264（3）](https://qincji.gitee.io/2020/12/06/rtmppush/3-h264/)
- [第四章：直播推流全过程：音频编码之AAC（4）](https://qincji.gitee.io/2020/12/07/rtmppush/4-aac/) 
- [第五章：直播推流全过程：直播推流编码之RTMP（5）](https://qincji.gitee.io/2020/12/08/rtmppush/5-rtmp/)
- 福利：博主买了个一年的服务器搭建了rtmp接收服务端，地址在项目里面，大家可以拿来测试。

## 五、学习FFmpeg
音视频开发是绕不开FFmpeg的，因为它是一个"集大成者"，里面已经包含或可集成现代几乎所有的音视频技术（库）。

### 1）学习途径
- [阅读官方文档](http://ffmpeg.org/ffmpeg.html)
- 学习官方例子（源码中`doc/examples/xxx`）
- [[总结]FFMPEG视音频编解码零基础学习方法](https://blog.csdn.net/leixiaohua1020/article/details/15811977)
- 书籍（推荐，电子书往最后翻）
> 1. FFmpeg从入门到精通
> 2. FFMPEG_FFPLAY源码剖析
> 3. 音视频开发进阶指南：基于Android与iOS平台的实践
> 4. Android 音视频开发

### 2）学习路线
[这里不推荐直接学习雷神的[总结]FFMPEG视音频编解码零基础学习方法，建议是通过在学习FFmpeg官方例子中进行学习，避免先入为主使用了过时的API。](https://blog.csdn.net/leixiaohua1020/article/details/15811977)

#### a) 源码编译
[编译ffmpeg4.2.2通过这篇文章我们基本可以编译出我们想要的FFmpeg库](https://qincji.gitee.io/2020/12/17/ffmpeg/03_build_ffmpeg/)

#### b) 源码阅读
- 源码导入：[FFmpeg导入到Clion（MacOS）](https://qincji.gitee.io/2020/12/24/ffmpeg/04_import_ffmpeg/) 、 [使用Clion阅读FFmpeg源码（支持跳转）](https://qincji.gitee.io/2020/12/27/ffmpeg/05_source/) 
- 阅读参考：[FFMPEG/FFPLAY源码剖析(CSDN下载)](https://download.csdn.net/detail/leixiaohua1020/6377803) ，我也放到最下方资料里面了，没条件的朋友们可以从这里下载。 

#### c) 学习官方例子
- [FFmpeg重要结构体（转自雷神）](https://qincji.gitee.io/2020/12/28/ffmpeg/06_struct/) ，因为在学习FFmpeg中，必须得知道结构体中重要参数的含义，否则举步维艰。
- [FFmpeg Demuxing（解封装）](https://qincji.gitee.io/2021/01/03/ffmpeg/08_demuxing/) 对应 `doc/examples/demuxing_decoding.c` 中的解封装部分。 
- [FFmpeg Muxing（封装）](https://qincji.gitee.io/2021/01/04/ffmpeg/09_muxing/) 对应 `doc/examples/muxing.c` 。
- [FFmpeg Remuxing（重新封装）](https://qincji.gitee.io/2021/01/04/ffmpeg/10_remuxing/) 对应 `doc/examples/remuxing.c` 。
- [FFmpeg Decode（解码）](https://qincji.gitee.io/2021/01/06/ffmpeg/11_decode/) 对应 `doc/examples/decode_audio.c` 和 `doc/examples/decode_video.c` 。
- [FFmpeg Encode（编码）](https://qincji.gitee.io/2021/01/07/ffmpeg/12_encode/) 对应 `doc/examples/decode_audio.c` 和 `doc/examples/decode_video.c` 。
- [FFmpeg 简单实现转码](https://qincji.gitee.io/2021/01/08/ffmpeg/13_transfer/) 汇总解封装、解码、编码、封装放到一起方便理解 。
- [FFmpeg Filter和SDL（Video）](https://qincji.gitee.io/2021/01/09/ffmpeg/14_filter_v/) 对应 `doc/examples/filtering_video.c` 。
- [FFmpeg Filter和SDL（Audio）](https://qincji.gitee.io/2021/01/10/ffmpeg/15_filter_a/) 对应 `doc/examples/filtering_video.c` 。
- [FFmpeg Transcode(转码)](https://qincji.gitee.io/2021/01/12/ffmpeg/16_transcode/) 对应 `doc/examples/transcoding.c` 。

## 六、待更...

## [音视频资料](https://pan.baidu.com/s/1Y5PFgbVu3W0ELBgQnrHNYA)
- 密码:lqi9
- 动脑视频
- 网易视频
- Advanced C and C++ Compiling.pdf
- Android 音视频开发_何俊林.pdf
- C Primer中文版 第五版 .pdf
- C++ Primer Plus（第6版）中文版.azw3
- C++ Primer(第5版)中文版.pdf
- FFMPEG_FFPLAY源码剖析.7z
- H.264-AVC-ISO_IEC_14496-10.pdf
- H.264-AVC-ISO_IEC_14496-15.pdf
- H.264_MPEG-4-Part-10-White-Paper.pdf
- H.264官方中文版.pdf
- ISO_IEC-14496-3-2009.pdf
- ISO_IEC_14496-14_2003-11-15.pdf
- SDL2-API手册.doc
- aac-iso-13818-7.pdf
- STL源码剖析简体中文完整版(清晰扫描带目录).pdf
- amf0_spec_121207.pdf
- amf3_spec_121207.pdf
- hls-m3u8-draft-pantos-http-live-streaming-12.txt
- hls-mpeg-ts-VB_WhitePaper_TransportStreamVSProgramStream_rd2.pdf
- hls-mpeg-ts-iso13818-1.pdf
- rtmp.part1.Chunk-Stream.pdf
- rtmp.part2.Message-Formats.pdf
- rtmp.part3.Commands-Messages.pdf
- rtmp规范翻译1.0.docx
- rtmp_specification_1.0.pdf
- video_file_format_spec_v10_1.pdf
- 《FFmpeg从入门到精通》.pdf
- 数字信号处理教程（第四版）.pdf
- 新一代视频压缩编码标准-H.264_AVC(第二版).pdf
- 音视频开发进阶指南：基于Android与iOS平台的实践.pdf

## 作者有话说
若有帮助就Star一下呗，您的鼓励是我开源的动力！

此外：[欢迎光临我的博客](https://qincji.gitee.io/) && [这个导航网页内容也很丰富哦](https://qincji.gitee.io/life/)

---------------------------------------------
本文一切皆从网络而来，如有侵权请联系我（邮箱：xhunmon@126.com）进行处理。
