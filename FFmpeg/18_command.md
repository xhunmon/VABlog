#FFmpeg命令使用指南

本文目的主要为了探讨如何使用命令，以及该如何去查找我们需要的命令，最后抛砖引玉示范几个案例。

## FFmpeg主要工具
FFmpeg主要工具有`ffmpeg`、`ffprobe`和`ffplay`，它们分别用作编解码、内容分析和播放器。它们均可通过编译或者直接下载安装获得的应用程序。

## ffmpeg
- **[官方文档](https://ffmpeg.org/ffmpeg.html)** ，如果英文不太好，推荐从《FFmpeg从入门到精通》书中查找中文含义。
- **源码所在：`fftools/ffmpeg.c`**

以下分析ffmpeg工具帮助文档，以便掌握使用命令的技巧
### 1）帮助文档
通常拿到工具的第一步看帮助文档：
> ffmpeg --help

当内容太多时，我们需要把打印的信息保存到文件中，再拿工具看：
> ffmpeg --help > ffmpeg_help.txt

内容如下：
```c
Hyper fast Audio and Video encoder
usage: ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...

Getting help:
    -h      -- print basic options
    -h long -- print more options
    -h full -- print all options (including all format and codec specific options, very long)
    -h type=name -- print all options for the named decoder/encoder/demuxer/muxer/filter/bsf
    See man ffmpeg for detailed description of the options.

Print help / information / capabilities:
-L                  show license
-h topic            show help
-? topic            show help
-help topic         show help
--help topic        show help
-version            show version
-buildconf          show build configuration
-formats            show available formats
-muxers             show available muxers
-demuxers           show available demuxers
-devices            show available devices
-codecs             show available codecs
-decoders           show available decoders
-encoders           show available encoders
-bsfs               show available bit stream filters
-protocols          show available protocols
-filters            show available filters
-pix_fmts           show available pixel formats
-layouts            show standard channel layouts
-sample_fmts        show available audio sample formats
-colors             show available color names
-sources device     list sources of the input device
-sinks device       list sinks of the output device
-hwaccels           show available HW acceleration methods

Global options (affect whole program instead of just one file:
-loglevel loglevel  set logging level
-v loglevel         set logging level
-report             generate a report
-max_alloc bytes    set maximum size of a single allocated block
-y                  overwrite output files
-n                  never overwrite output files
-ignore_unknown     Ignore unknown stream types
-filter_threads     number of non-complex filter threads
-filter_complex_threads  number of threads for -filter_complex
-stats              print progress report during encoding
-max_error_rate maximum error rate  ratio of errors (0.0: no errors, 1.0: 100% errors) above which ffmpeg returns an error instead of success.
-bits_per_raw_sample number  set the number of bits per raw sample
-vol volume         change audio volume (256=normal)

Per-file main options:
-f fmt              force format
-c codec            codec name
-codec codec        codec name
-pre preset         preset name
-map_metadata outfile[,metadata]:infile[,metadata]  set metadata information of outfile from infile
-t duration         record or transcode "duration" seconds of audio/video
-to time_stop       record or transcode stop time
-fs limit_size      set the limit file size in bytes
-ss time_off        set the start time offset
-sseof time_off     set the start time offset relative to EOF
-seek_timestamp     enable/disable seeking by timestamp with -ss
-timestamp time     set the recording timestamp ('now' to set the current time)
-metadata string=string  add metadata
-program title=string:st=number...  add program with specified streams
-target type        specify target file type ("vcd", "svcd", "dvd", "dv" or "dv50" with optional prefixes "pal-", "ntsc-" or "film-")
-apad               audio pad
-frames number      set the number of frames to output
-filter filter_graph  set stream filtergraph
-filter_script filename  read stream filtergraph description from a file
-reinit_filter      reinit filtergraph on input parameter changes
-discard            discard
-disposition        disposition

Video options:
-vframes number     set the number of video frames to output
-r rate             set frame rate (Hz value, fraction or abbreviation)
-s size             set frame size (WxH or abbreviation)
-aspect aspect      set aspect ratio (4:3, 16:9 or 1.3333, 1.7777)
-bits_per_raw_sample number  set the number of bits per raw sample
-vn                 disable video
-vcodec codec       force video codec ('copy' to copy stream)
-timecode hh:mm:ss[:;.]ff  set initial TimeCode value.
-pass n             select the pass number (1 to 3)
-vf filter_graph    set video filters
-ab bitrate         audio bitrate (please use -b:a)
-b bitrate          video bitrate (please use -b:v)
-dn                 disable data

Audio options:
-aframes number     set the number of audio frames to output
-aq quality         set audio quality (codec-specific)
-ar rate            set audio sampling rate (in Hz)
-ac channels        set number of audio channels
-an                 disable audio
-acodec codec       force audio codec ('copy' to copy stream)
-vol volume         change audio volume (256=normal)
-af filter_graph    set audio filters

Subtitle options:
-s size             set frame size (WxH or abbreviation)
-sn                 disable subtitle
-scodec codec       force subtitle codec ('copy' to copy stream)
-stag fourcc/tag    force subtitle tag/fourcc
-fix_sub_duration   fix subtitles duration
-canvas_size size   set canvas size (WxH or abbreviation)
-spre preset        set the subtitle options to the indicated preset
```

### 2）指令格式
`usage: ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...`

这就是ffmpeg指令格式，`-i`前面的`options`(选择)指定输入文件，`[outfile options]`指定输出文件。举个例子：（官方文档的）将输入文件的帧速率强制为1fps，将输出文件的帧速率强制为24fps，进行转换：
> ffmpeg -r 1 -i Kobe_in.flv -r 24 Kobe_out.avi

其中`-r`是作用转换帧率，描述：`-r rate             set frame rate (Hz value, fraction or abbreviation)`。

输入和输出对比一下：
```commandline
这是输入：
 Duration: 00:00:30.00, start: 0.000000, bitrate: 221 kb/s
    Stream #0:0: Video: h264 (Main), yuv420p(progressive), 384x216 [SAR 1:1 DAR 16:9], 182 kb/s, 15.07 fps, 15 tbr, 1k tbn, 30 tbc
这是输出：
Input #0, avi, from 'Kobe_out.avi':
  Metadata:
    encoder         : Lavf58.29.100
  Duration: 00:07:30.04, start: 0.000000, bitrate: 62 kb/s
    Stream #0:0: Video: mpeg4 (Simple Profile) (FMP4 / 0x34504D46), yuv420p, 384x216 [SAR 1:1 DAR 16:9], 44 kb/s, 24 fps, 24 tbr, 24 tbn, 24 tbc
```

### 3）打印某个协议的具体信息（如h264）
`-h type=name -- print all options for the named decoder/encoder/demuxer/muxer/filter/bsf`
就是这一行有点难理解，`-h type=name`，这是指定对类型`decoder/encoder/...`中某个类型的"选项"信息，举一个例子就知道，如：
> ffmpeg -h decoder=h264

打印信息如下：
```commandline
Decoder h264 [H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10]:
    General capabilities: dr1 delay threads 
    Threading capabilities: frame and slice
    Supported hardware devices: videotoolbox 
H264 Decoder AVOptions:
  -enable_er         <boolean>    .D.V..... Enable error resilience on damaged frames (unsafe) (default auto)
  -x264_build        <int>        .D.V..... Assume this x264 version if no x264 version found in any SEI (from -1 to INT_MAX) (default -1)
```
而从哪里知道有哪些`decoder`呢？`ffmpeg -help`时显示出`-decoders           show available decoders`，也就是可以通过这个命令打印的：
> ffmpeg -decoders

其他`encoder、...、filter`也是一样的操作，先通过找有哪些存在的，然后再打印，又如`filter`:
> ffmpeg -filters 找到有 `acopy`，然后查看它的具体用法：
> ffmpeg -h filter=acopy


### 4）滤镜命令
- **[官方文档](http://ffmpeg.org/ffmpeg-filters.html)**

1）我们从上面的帮助文档(`ffmpeg -h`)中找到有关滤镜输出的，分别是视频滤镜输出，以及音频滤镜输出：
`-vf filter_graph    set video filters` 和 `-af filter_graph    set audio filters` ，而通过上一步`ffmpeg -filters`找到处理视频的`boxblur`（动态模糊）以及处理音频的`aecho`（回声处理）两条，然后分别打印两个的用法如下：
> ffmpeg -h filter=boxblur

输出如下：
```commandline
Filter boxblur
  Blur the input.
    Inputs:
       #0: default (video)
    Outputs:
       #0: default (video)
boxblur AVOptions:
  luma_radius       <string>     ..FV..... Radius of the luma blurring box (default "2")
  lr                <string>     ..FV..... Radius of the luma blurring box (default "2")
  luma_power        <int>        ..FV..... How many times should the boxblur be applied to luma (from 0 to INT_MAX) (default 2)
  lp                <int>        ..FV..... How many times should the boxblur be applied to luma (from 0 to INT_MAX) (default 2)
  chroma_radius     <string>     ..FV..... Radius of the chroma blurring box
  cr                <string>     ..FV..... Radius of the chroma blurring box
  chroma_power      <int>        ..FV..... How many times should the boxblur be applied to chroma (from -1 to INT_MAX) (default -1)
  cp                <int>        ..FV..... How many times should the boxblur be applied to chroma (from -1 to INT_MAX) (default -1)
  alpha_radius      <string>     ..FV..... Radius of the alpha blurring box
  ar                <string>     ..FV..... Radius of the alpha blurring box
  alpha_power       <int>        ..FV..... How many times should the boxblur be applied to alpha (from -1 to INT_MAX) (default -1)
  ap                <int>        ..FV..... How many times should the boxblur be applied to alpha (from -1 to INT_MAX) (default -1)

This filter has support for timeline through the 'enable' option.
```
通过以上说明，我们设置视频滤镜处理为：`boxblur=luma_radius=3:luma_power=8`。

以及音频：
> ffmpeg -h filter=aecho

输出如下：
```commandline
Filter aecho
  Add echoing to the audio.
    Inputs:
       #0: default (audio)
    Outputs:
       #0: default (audio)
aecho AVOptions:
  in_gain           <float>      ..F.A.... set signal input gain (from 0 to 1) (default 0.6)
  out_gain          <float>      ..F.A.... set signal output gain (from 0 to 1) (default 0.3)
  delays            <string>     ..F.A.... set list of signal delays (default "1000")
  decays            <string>     ..F.A.... set list of signal decays (default "0.5")
```
通过上面说明我们设置音频滤镜处理为：`aecho=0.8:0.88:60:0.4` 或者 `aecho=in_gain=0.8:out_gain=0.88:delays=60:decays=0.4` 。

2）合并两条处理命令如下：
> ffmpeg -i Kobe_in.flv -vf boxblur=luma_radius=3:luma_power=8 -af aecho=0.8:0.88:60:0.4 Kobe_out.flv

3）如果使用多条滤镜指令则使用`,`进行分割，如添加视频水平翻转(hflip)如下：
> ffmpeg -i Kobe_in.flv -vf boxblur=luma_radius=1:luma_power=8,hflip Kobe_out.flv

4）其他：滤镜处理是非常非常丰富的，例如：高斯模糊、3D降噪、添加logo、视频截切、视频反转、滤镜以及各种各样的特性（近200种处理）。


### 5）协议命令
- **[官方文档](http://ffmpeg.org/ffmpeg-protocols.html)**

首先我们得知道在FFmpeg中所有的输入源都是一种协议(`protocol`)，如`file`、`http`、`rtmp`、`concat`等等，这都是一种协议格式，所以网络资源等同于本地文件一样处理就好了。

通过开始时的帮助信息，我们使用`ffmpeg -h full`输出所有帮助信息，然后我们对一下进行测试：

1）将flv格式转换成fps为60的git格式进行输出，我们先找到git的封装信息：
```commandline
GIF muxer AVOptions:
  -loop              <int>        E........ Number of times to loop the output: -1 - no loop, 0 - infinite loop (from -1 to 65535) (default 0)
  -final_delay       <int>        E........ Force delay (in centiseconds) after the last frame (from -1 to 65535) (default -1)
```
注：`AVOptions`是针对该类型的"选项"参数，上面打印的帮助日志包含了非常多的`AVOptions`，自己多尝试两边就知道用法了。

2）通过上面`AVOptions`指定封装成GIF的"选项"进行输出，如下：
> ffmpeg -r 60 -i Kobe_in.flv -r 60 -loop 0 output.GIF

3）同样，当我们把网络数据进行转换到本地文件，便可以这样：
> ffmpeg -r 60 -i rtmp://58.200.131.2:1935/livetv/hunantv -r 60 -loop 0 output.GIF

4.1）推送道理也一样，只是将本地协议的格式转为远程协议的格式（将mp4转换成flv格式推送到rtmp服务器）：
> ffmpeg -r 60 -i lol.mp4 -r 60 -f flv rtmp://8.129.163.125:1935/myapp/testpush

注：这里特意说一下`-f`的使用，强制使用格式，如这里的`flv`是`flv muxer AVOptions:`

4.2）然后可以通过`ffplay`进行拉流播放：
> ffplay rtmp://8.129.163.125:1935/myapp/testpush

### 6）把多个图片合并视频命令
使用`-f`指定`image2`解封装格式，解封装`-pattern_type`"选项"的值`glob_sequence`、从下表为5开始，最多200张作为输入。输入fps为15，用x264进行编码输出mp4：
> ffmpeg -f image2 -pattern_type glob_sequence -start_number 5 -start_number_range 200 -framerate 12 -i 'loading/loading%d.png' -r 15 -vcodec libx264 out.mp4


### 7）合并视频命令
首先要明确合并的效果，以及合并的输入流。明确是否需要重新编解码、滤镜处理等等。这就是需要看输入的类型了。比如当两中尺寸不等，编码格式不同的两个输入文件，如果合并时，是否需要把尺寸统一、编码统一，然后才能进行合并。
````commandline
 _________
|         |
| input 0 |\                   
|_________| \              
             \   _________ 
              \ |         |
 _________     \| decode? |     _________
|         |     |         |    |         |
| input 1 |---->| filter? |--->| output  |
|_________|     |         |    |_________|
               /| encode? |     
              / |   ...   |
 _________   /  |_________|
|         | /
| input 2 |/
|_________|
````
指令使用看：[FFMpeg无损合并视频的多种方法](https://blog.csdn.net/doublefi123/article/details/47276739) （原文没找到），为了方便看，搬过来：
1）方法一：[FFmpeg concat协议](http://ffmpeg.org/ffmpeg-protocols.html#concat)
对于 MPEG 格式的视频，可以直接连接：
> ffmpeg -i "concat:input1.mpg|input2.mpg|input3.mpg" -c copy output.mpg

对于非 MPEG 格式容器，但是是 MPEG 编码器（H.264、DivX、XviD、MPEG4、MPEG2、AAC、MP2、MP3 等），可以包装进 TS 格式的容器再合并。在新浪视频，有很多视频使用 H.264 编码器，可以采用这个方法
> ffmpeg -i input1.flv -c copy -bsf:v h264_mp4toannexb -f mpegts input1.ts
> ffmpeg -i input2.flv -c copy -bsf:v h264_mp4toannexb -f mpegts input2.ts
> ffmpeg -i input3.flv -c copy -bsf:v h264_mp4toannexb -f mpegts input3.ts
> ffmpeg -i "concat:input1.ts|input2.ts|input3.ts" -c copy -bsf:a aac_adtstoasc -movflags +faststart output.mp4

保存 QuickTime/MP4 格式容器的时候，建议加上  -movflags +faststart。这样分享文件给别人的时候可以边下边看。

2）方法二：FFmpeg concat 分离器
> 这种方法成功率很高，也是最好的，但是需要 FFmpeg 1.1 以上版本。先创建一个文本文件 filelist.txt：
> file 'input1.mkv'
> file 'input2.mkv'
> file 'input3.mkv'

然后：
> ffmpeg -f concat -i filelist.txt -c copy output.mkv

注意：使用 FFmpeg concat 分离器时，如果文件名有奇怪的字符，要在  filelist.txt 中转义。

3）方法三：Mencoder 连接文件并重建索引
这种方法只对很少的视频格式生效。幸运的是，新浪视频使用的 FLV 格式是可以这样连接的。对于没有使用 MPEG 编码器的视频（如 FLV1 编码器），可以尝试这种方法，或许能够成功。
> mencoder -forceidx -of lavf -oac copy -ovc copy -o output.flv input1.flv input2.flv input3.flv

4）方法四：使用 FFmpeg concat 过滤器重新编码（有损）
语法有点复杂，但是其实不难。这个方法可以合并不同编码器的视频片段，也可以作为其他方法失效的后备措施。
> ffmpeg -i input1.mp4 -i input2.webm -i input3.avi -filter_complex '[0:0] [0:1] [1:0] [1:1] [2:0] [2:1] concat=n=3:v=1:a=1 [v] [a]' -map '[v]' -map '[a]' <编码器选项> output.mkv

如你所见，上面的命令合并了三种不同格式的文件，FFmpeg concat 过滤器会重新编码它们。 注意这是有损压缩。
[0:0] [0:1] [1:0] [1:1] [2:0] [2:1] 分别表示第一个输入文件的视频、音频、第二个输入文件的视频、音频、第三个输入文件的视频、音频。 concat=n=3:v=1:a=1 表示有三个输入文件，输出一条视频流和一条音频流。 [v] [a] 就是得到的视频流和音频流的名字，注意在 bash 等 shell 中需要用引号，防止通配符扩展。
- 提示:
以上三种方法，在可能的情况下，最好使用第二种。第一种次之，第三种更次。第四种是后备方案，尽量避免。规格不同的视频合并后可能会有无法预测的结果。有些媒体需要先分离视频和音频，合并完成后再封装回去。对于 Packed B-Frames 的视频，如果封装成 MKV 格式的时候提示 Can't write packet with unknown timestamp，尝试在 FFmpeg 命令的 ffmpeg 后面加上 -fflags +genpts

5）了解了上面情况下，我们合并两个尺寸不一样的视频，`lol_640x360.mp4`与`Kobe_384x216.flv`合并成`out_640x360.avi`。思路：我们通过把两个尺寸统一成一致（处理方案很多），然后转码成统一格式`.ts`，使用`concat`协议输出到一个文件。


a. 使用`scale`滤镜把`Kobe_384x216.flv`放大成`lol_640x360.mp4`尺寸，setsar(像素比)&[setdar](http://ffmpeg.org/ffmpeg-filters.html#setdar_002c-setsar) （宽高比），`vcodec`（h264编码），`acodec`（aac编码），不然默认其他编码（还应该制定码率什么的，这里从简了）：
> ffmpeg -i Kobe_384x216.flv -vf scale=w=640:h=360,setsar=sar=1/1,setdar=dar=16/9 -vcodec libx264 -acodec aac Kobe_640x360.flv

注：setsar等时通过使用ffprobe查看`lol_640x360.mp4`文件所知道的，如：
```commandline
Stream #0:0(und): Video: h264 (Main) (avc1 / 0x31637661), yuv420p, 640x360 [SAR 1:1 DAR 16:9], 256 kb/s, 15 fps, 15 tbr, 15360 tbn, 30 tbc (default)
```

b. 统一转码成`.ts`格式：
> ffmpeg -i Kobe_640x360.flv -c copy -bsf:v h264_mp4toannexb -f mpegts input1.ts
> ffmpeg -i lol_640x360.mp4 -c copy -bsf:v h264_mp4toannexb -f mpegts input2.ts

c. 使用`concat`协议输出到一个文件：
> ffmpeg -i "concat:input1.ts|input2.ts" -c copy -bsf:a aac_adtstoasc -movflags +faststart out_640x360.avi 



## ffprobe
- [官方文档](https://ffmpeg.org/ffprobe.html) 
- 源码所在：`fftools/ffprobe.c`

ffprobe作为FFmpeg的分析工具，内容非常强大，可以说是能分析`输入源`一切的信息。

### 1）帮助文档
同上操作，先看帮助文档：
> ffprobe --help

这里就不贴出来了，而且很多跟`ffmpeg`都是一样的。

### 2）指令格式
`usage: ffprobe [OPTIONS] [INPUT_FILE]`

最简单使用就是直接忽略`[OPTIONS]`，如：
> ffprobe Kobe.flv

输出结果如下：
```commandline
Input #0, flv, from 'Kobe.flv':
  Metadata:
    metadatacreator : iku
    hasKeyframes    : true
    hasVideo        : true
    hasAudio        : true
    hasMetadata     : true
    canSeekToEnd    : false
    datasize        : 828648
    videosize       : 690936
    audiosize       : 133316
    lasttimestamp   : 30
    lastkeyframetimestamp: 25
    lastkeyframelocation: 696106
  Duration: 00:00:30.00, start: 0.000000, bitrate: 221 kb/s
    Stream #0:0: Video: h264 (Main), yuv420p(progressive), 384x216 [SAR 1:1 DAR 16:9], 182 kb/s, 15.07 fps, 15 tbr, 1k tbn, 30 tbc
    Stream #0:1: Audio: aac (HE-AAC), 44100 Hz, stereo, fltp, 33 kb/s
```

其他也非常简单明了，如查看`packets`信息`-show_packets       show packets info`：
> ffprobe -show_packets Kobe.flv

输出将是所有packet信息，这里拿出一小段：
```commandline
[PACKET]
codec_type=video
stream_index=0
pts=29533
pts_time=29.533000
dts=29533
dts_time=29.533000
duration=66
duration_time=0.066000
convergence_duration=N/A
convergence_duration_time=N/A
size=777
pos=818689
flags=__
[/PACKET]
```


## ffplay
[官方文档](https://ffmpeg.org/ffplay.html) 
- 源码所在：`fftools/ffplay.c`
这是一个非常非常强大的播放器，市面上的视频格式都支持播放，而且播放裸流数据一样是可以的，如pcm音频，yuv视频、rgb视频等。


### 1）帮助文档
同上操作，先看帮助文档：
> ffplay --help

这里就不贴出来了，而且很多跟`ffmpeg`都是一样的。

### 2）指令格式
`usage: ffplay [options] input_file`

最简单使用就是直接忽略`[OPTIONS]`，如：
> ffplay Kobe.flv

### 3）播放pcm数据
关于裸数据的播放，就必须要设置这几个参数：`采样率`、`声道数`、`位宽`、`值的范围(fmt)`和`排序方式(大端/小端)`。原因就是：一个存pcm裸流数据的文件是不包含任何参数的，只是按顺序存放一个个采样点的值，具体请看：[直播推流全过程：音频数据源之PCM（2）](../RTMP/2-pcm.md) 。

1）我们先从`Kobe.flv`解码出pcm裸流数据，存储到文件中，我们通过`ffprobe Kobe.flv`命令参看解码前的pcm信息如下：
```commandline
Stream #0:1: Audio: aac (HE-AAC), 44100 Hz, stereo, fltp, 33 kb/s
```
注：可以直接通过[FFmpeg Decode（解码）](./11_decode.md) 生成pcm裸流数据。

2）所以解码后得到的pcm数据信息为：采样率=44100，声道数=2，位宽=4，值的范围(fmt)=fltp（32位浮点类型），排序方式=小端。根据这几个英文意思，从帮助文档里面找到先关的几条"选项"如下：
```commandline
-ar                <int>        ED..A.... set audio sampling rate (in Hz) (from 0 to INT_MAX) (default 0)
-f fmt              force format
f32be demuxer AVOptions:
  -sample_rate       <int>        .D.......  (from 0 to INT_MAX) (default 44100)
  -channels          <int>        .D.......  (from 0 to INT_MAX) (default 1)

f32le demuxer AVOptions:
  -sample_rate       <int>        .D.......  (from 0 to INT_MAX) (default 44100)
  -channels          <int>        .D.......  (from 0 to INT_MAX) (default 1)

s32be demuxer AVOptions:
  -sample_rate       <int>        .D.......  (from 0 to INT_MAX) (default 44100)
  -channels          <int>        .D.......  (from 0 to INT_MAX) (default 1)
u32be demuxer AVOptions:
  -sample_rate       <int>        .D.......  (from 0 to INT_MAX) (default 44100)
  -channels          <int>        .D.......  (from 0 to INT_MAX) (default 1)
```
3）这里特意多取几个进行介绍一下：
`f32be`=浮点类型32位大端排序；`f32le`=浮点类型32位小端排序；`s32be`=带符号整形32位大端排序；`u32be`=无符号整形32位大端排序。相信你一下子就能找到规律了。
第一个字母：`f`为浮点类型，`s`为带符号整形，`u`为无符号整形；
中间数字：表示占位，有8、16、24、32、64可选。

4）所以，最后播放pcm命令为：
> ffplay -ar 44100 -channels 2 -f f32le Kobe-44100-2-32-33ks.pcm


### 4）播放yuv数据
跟pcm一样，分析过程并无多大区别。而且yuv相关更少，只有跟像素、yuv类型以及fps相关。

1）我们先从`Kobe.flv`解码出yuv裸流数据，存储到文件中，我们通过`ffprobe Kobe.flv`命令参看解码前的yuv信息如下：
```commandline
Stream #0:0: Video: h264 (Main), yuv420p(progressive), 384x216 [SAR 1:1 DAR 16:9], 182 kb/s, 15.07 fps, 15 tbr, 1k tbn, 30 tbc
```
2）所以解码后得到的yuv数据信息为：像素=384x216，yuv类型=yuv420p，fps=15。根据这几个英文意思，从帮助文档里面找到先关的几条"选项"如下：
```commandline
rawvideo demuxer AVOptions:
  -video_size        <image_size> .D....... set frame size
  -pixel_format      <string>     .D....... set pixel format (default "yuv420p")
  -framerate         <video_rate> .D....... set frame rate (default "25")
colorspace AVOptions:
 format            <int>        ..FV..... Output pixel format (from -1 to 164) (default -1)
     yuv420p                      ..FV..... 
     yuv420p10                    ..FV..... 
     yuv420p12                    ..FV..... 
     yuv422p                      ..FV..... 
     yuv422p10                    ..FV..... 
     yuv422p12                    ..FV..... 
     yuv444p                      ..FV..... 
     yuv444p10                    ..FV..... 
     yuv444p12                    ..FV..... 
```
3）所以，最后播放yuv命令为：
> ffplay -f rawvideo -video_size 384x216 -pixel_format yuv420p -framerate 15 Kobe-384x216-15fps.yuv


### 5）播放网络资源
上面我们叫的输入源都被称为一种协议格式，跟本地文件（如.flv等）处理是一样的，所以播放网络资源后面直接跟地址就行了，如：
>rtmp://58.200.131.2:1935/livetv/hunantv


### 6）播放的其他重要参数
1）打印日志，这有助于我们分析问题，或者阅读源码：
`-v loglevel         set logging level`
参数"选项"：
```commandline
"quiet"
"panic"
"fatal"
"error"
"warning"
"info"
"verbose"
"debug"
"trace"
```
使用： ffplay -v "debug" lol.mp4

2）生成日志
`-report             generate a report`
使用： ffplay -v "debug" `-report lol.mp4

3）一些常规的播放设置相关
```commandline
-x width            force displayed width                //指定宽度
-y height           force displayed height               //指定高度
-s size             set frame size (WxH or abbreviation) //设置帧的大小（过时？），使用：video_size？
-fs                 force full screen                    //全屏播放
-an                 disable audio                        //禁用声音
-vn                 disable video                        //禁用视频
-sn                 disable subtitling                   //禁用字幕
-ss pos             seek to a given position in seconds  //从指定开始播放（跳过多少秒）
-t duration         play  "duration" seconds of audio/video //播放多长时间（单位秒）
-bytes val          seek by bytes 0=off 1=on -1=auto
-seek_interval seconds  set seek interval for left/right keys, in seconds  //设置按下左右键时快退/快进多少秒
-nodisp             disable graphical display            //播放时无显示（可用于调试）
-noborder           borderless window                   //无边框播放
-alwaysontop        window always on top                //窗口总在最上面
-volume volume      set startup volume 0=min 100=max    //设置音量（0——100），0为静音
-f fmt              force format                        //强制播放的格式
-window_title window title  set window title            //播放器窗口添加标题显示
-af filter_graph    set audio filters                   //播放前添加音频滤镜处理后再播放
-vf filter_graph    set video filters                   //播放前添加视频滤镜处理后再播放
-showmode mode      select show mode (0 = video, 1 = waves, 2 = RDFT) //画面显示的模式
-i input_file       read specified file                 //指定输入文件（可以忽略）
-codec decoder_name  force decoder                       //指定解码器
```

其中播放前的滤镜处理跟ffmpeg使用一样，只用替换成ffplay即可，如：
> ffplay -vf boxblur=luma_radius=3:luma_power=8 -af aecho=0.8:0.88:60:0.4 Kobe.flv

- [Kobi.flv下载](../MustRead/img/01_flv/Kobe.flv)
