# Summary

* [打怪之路总览](README.md)
* [直播推流全过程](RTMP/README.md)
    * [视频数据源之YUV](RTMP/1-yuv.md)
    * [音频数据源之PCM](RTMP/2-pcm.md)
    * [视频编码之H.264](RTMP/3-h264.md)
    * [音频编码之AAC](RTMP/4-aac.md)
    * [直播推流编码之RTMP](RTMP/5-rtmp.md)
    * [其他：H.264符号描述](RTMP/h264-descriptor.md)
    * [其他：直播优化基础](RTMP/6-optimize.md)
* [AFPlayer](AFPlayer/README.md)
    * [OpenSL ES播放PCM](AFPlayer/01_opensl_es.md)
    * [OpenGL ES播放RGB](AFPlayer/02_opengl_es.md)
    * [FFmpeg实现MediaCodec解码](AFPlayer/03_mediacodec.md)
    * [FFmpeg、x264、fdk-aac、openssl的交叉编译](AFPlayer/04_build_for_android.md)
* [FFmpeg](FFmpeg/README.md)
    * [Configure配置选项 (转载)](FFmpeg/01_configure_help.md)
    * [Shell脚本](FFmpeg/02_shell.md)
    * [编译FFmpeg4.2.2](FFmpeg/03_build_ffmpeg.md)
    * [FFmpeg导入到Clion（MacOS）](FFmpeg/04_import_ffmpeg.md)
    * [使用Clion阅读FFmpeg源码（支持跳转）](FFmpeg/05_source.md)
    * [FFmpeg重要结构体（转载）](FFmpeg/06_struct.md)
    * [Demuxing（解封装）](FFmpeg/08_demuxing.md)
    * [Muxing（封装）](FFmpeg/09_muxing.md)
    * [Remuxing（重新封装）](FFmpeg/10_remuxing.md)
    * [Decode（解码）](FFmpeg/11_decode.md)
    * [Encode（编码）](FFmpeg/12_encode.md)
    * [简单实现转码](FFmpeg/13_transfer.md)
    * [Filter和SDL（Video）](FFmpeg/14_filter_v.md)
    * [Filter和SDL（Audio）](FFmpeg/15_filter_a.md)
    * [Transcode(转码)](FFmpeg/16_transcode.md)
    * [音视频同步处理](FFmpeg/17_sync.md)
    * [FFmpeg命令使用指南](FFmpeg/18_command.md)
    * [Swscale（图像转换）](FFmpeg/19_swscale.md)
    * [FFmpeg添加字幕的详细操作](FFmpeg/20_subtitle_ffmpeg.md)
    * [如何深入学习解封装？](FFmpeg/21_deep_demuxing.md)
* [OpenGL](OpenGL/README.md)
    * [OpenGL基础知识](OpenGL/01_opengl.md)
    * [GLSL（着色器语言）中文手册](OpenGL/02_glsl.md)
    * [OpenGL ES播放RGB](AFPlayer/02_opengl_es.md)
* [重点技术](MustRead/README.md)    
    * [手撕FLV协议](MustRead/01_flv.md)
    * [基于H.264看H.265](MustRead/02_h265.md)
    * [面试题整理](MustRead/03_fst.md)
    * [常见的音视频传输协议对比](MustRead/04_transport_protocols.md)
    * [常见的音视频编解码器对比](MustRead/05_codec.md)
    * [常见的音视频封装格式对比](MustRead/06_muxer.md)
* [Null](Other/README.md)
    * [初步认识c/c++编译](Other/01_c_compile.md)
    * [网络基础知识](Other/02_net_base.md)
    * [ADB的使用](Other/03_adb_dy.md)
    * [JNI](Other/04_jni.md)