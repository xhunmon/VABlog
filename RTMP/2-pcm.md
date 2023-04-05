#音频数据源之PCM（2）


### 声音与音频

声音是波，成为声波，而声波的三要素是频率、振幅和波形。频率代表音阶的高低（女高音、男低音）单位赫兹（Hz），人耳能听到的声波范围：频率在20Hz~20kHz之间；振幅代表响度（音量）；波形代表音色。而我们音频处理就是对声波采集成数字信号后进行处理。

### 音频采集与关键名词

音频采集的过程主要是通过设备设置采样率、采样数，将音频信号采集为**pcm**（Pulse-code modulation，脉冲编码调制）编码的原始数据（无损压缩），然后编码压缩成mp3、aac等封装格式的数据。音频关键知识：

- **采样率：** 一段音频数据中单位时间内（每秒）采样的个数。
- **位宽：** 一次最大能传递数据的宽度，可以理解成放单个采集数据的内存。常有8位和16位，而8位：代表着每个采集点的数据都使用8位（1字节）来存储；16位：代表着每个采集点的数据都使用16位（2字节）来存储。
- **声道数：** 扬声器的个数，单声道、双声道等。每一个声道都占一个位宽。

来一张图来描述一下：

![pcm-数据采集](img/2-pcm/pcm-数据采集.png)

一段时间内的数据大小如何计算？

> 采样率 x (位宽 / 8) x 声道数 x 时间  =  数据大小（单位：字节）

比如 2分钟的CD（采样率为：44100，位宽：16，声道数：2）的数据大小：44100 x (16 / 8) x 2 x 120  = 20671.875 Byte 约为 20.18M。

### PCM数据的基本使用

我们采集到的pcm原始数据要怎么玩？首先得知道怎么这些数据都代表啥意思，然后才能入手处理。

#### 1、pcm数据时如何组成（存储）？

举个例子，分别使用不同的方式存储一段采集数据 0x11 0x22 0x33 0x44 0x55 0x66 0x77 0x88 总共8个字节。

- **8位单声道：** 按照数据采集时间顺序存储，即：0x11 0x22 0x33 0x44 0x55 0x66 0x77 0x88

- **8位双声道：** L声道-R声道-L声道-R声道形式存储，即：0x11(L) 0x22(R) 0x33(L) 0x44(R) ……

- **16位单声道：** 首先从 [维基多媒体：pcm](https://wiki.multimedia.cx/index.php/PCM)了解到位宽大于8位时，字节的排序方式是有差别的，描述如下：

  > When more than one byte is used to represent a PCM sample, the byte order (big endian vs. little endian) must be known. Due to the widespread use of little-endian Intel CPUs, little-endian PCM tends to be the most common byte orientation.
  >
  > 当使用一个以上的字节表示PCM样本时，必须知道字节顺序（大端与小端）。由于低端字节Intel CPU的广泛使用，低端字节PCM往往是最常见的字节方向。
  >
  > 
  >
  > 举个栗子：当位宽为16位（2字节）存储一个采集数据时，如：`0x12ab`，大端和小端分别是：
  >
  > > big-endian: `0x12` `0xab`；
  > > little-endian: `0xab` `0x12`。

  所以：
  big-endian存储方式：0x1122  0x3344 0x5566  0x7788；
  little-endian存储方式：0x2211 0x4433 0x6655  0x8877。

- **16位双声道：** L声道-R声道-L声道-R声道形式存储：

  big-endian：0x1122(L) 0x3344(R) 0x5566(L)  0x7788(R)

  little-endian: 0x2211(L) 0x4433(R) 0x66550(L)  0x8877(R)

#### 2、pcm原始数据可以怎么玩？

- 将little-endian_2_44100_16.pcm采样数据进行切割，只保留后面5秒的数据

  ```c
  /**
   * 将little-endian_2_44100_16.pcm采样数据进行切割，只保留后面5秒的数据
   * 1、该类型数据5秒有多长？
   * 2、从哪里开始截取？
   */
  int cut5second(const char *url){
      FILE *in = fopen(url, "rb+");
      FILE *out = fopen("./output/spit5second.pcm", "wb+");
      long long  data5Length = 44100 * (16/8) * 2 * 5;
      struct stat statbuf;
      stat(url,&statbuf);
      long long fileLength = statbuf.st_size;
      long long  start = fileLength - data5Length;
      char *simple = (char *)malloc(data5Length);
      //把指针位置移动到start位置开始读取
      fseek(in,start,1);
      //每次从in文件中读取1组data5Length个长度数据的到simple中
      fread(simple,data5Length,1,in);
      fwrite(simple,data5Length,1,out);
      fclose(in);
      fclose(out);
      return 0;
  }
  
  ```

- 分离各声道的数据：把各个声道的采集点数据分开存储。

  ```c
  /**
   * 将little-endian_2_44100_16.pcm 分离各声道的数据，即把各个声道的采集点数据分开存储。
   */
  int separateLR(const char *url){
      FILE *in = fopen(url, "rb+");
      FILE *outL = fopen("./output/l.pcm", "wb+");
      FILE *outR = fopen("./output/r.pcm", "wb+");
      int simpleLength = 16 / 8 * 2;
      char *simple = (char *)malloc(simpleLength);
      while (1){
          //每次从in文件中读取1组4个长度数据的到simple中
          fread(simple,4,1,in);
          if(feof(in)){
              break;
          }
          //l(0声道)：1-2
          fwrite(simple,2,1,outL);
          //r(1声道)：3-4
          fwrite(simple+2,2,1,outR);
      }
      fclose(in);
      fclose(outL);
      fclose(outR);
      return 0;
  }
  ```

  

- 音量调节：把每个采集点数据的值 x 调节比例。注意：需要注意的是可调节范围，如：8位有无符号时最大是多少。

  ```c
  /**
   * 将little-endian_2_44100_16.pcm 调节音量 比例
   * 1、little-endian排序的值是如何排序的？真正的值是多少？
   * 2、little-endian转成真正的值之后再进行计算，得到的结果再反转little-endian。
   * 如：原始pcm数据：0xaa 0x01(左声道采样点数据)，当scale=2：
   * -> 值：0x01aa * 2 = 0x0354
   * -> 转回little-endian再进行存储：0x5403（缩放后的值）
   */
  int volumeAdjustment(const char *url, float scale){
      FILE *in = fopen(url, "rb+");
      FILE *out = fopen("./output/volume_adjustment.pcm", "wb+");
      char *simple = (char *)malloc(4);
      while (1){
          //每次从in文件中读取1组4个长度数据的到simple中
          fread(simple, 4, 1, in);
          if(feof(in)){
              break;
          }
          short *simple8bitTemp = (short *)malloc(2);
  
          //l(0声道)：
          simple8bitTemp[0] = (simple[0] + (simple[1] << 8)) * scale;
          //r(1声道)：
          simple8bitTemp[1] = (simple[2] + (simple[3] << 8)) * scale;
  
          simple[0] = simple8bitTemp[0] & 0x00FF;
          simple[1] = simple8bitTemp[0] >> 8;
  
          simple[2] = simple8bitTemp[1] & 0x00FF;
          simple[3] = simple8bitTemp[1] >> 8;
          for(int i=0; i<4; i++){
              printf("simple[%d]=%d\n",i,simple[i]);
          }
          fwrite(simple, 4, 1, out);
      }
      fclose(in);
      fclose(out);
      return 0;
  }
  ```

  

- 播放速度：按照比例丢弃（或插入0）采集点的数据即可。（涉及到不是整数倍不单单是这么处理，我也不懂）

- 参照雷神的必看项目： [视音频数据处理入门：PCM音频采样数据处理](http://blog.csdn.net/leixiaohua1020/article/details/50534316) 。

### Android终端音频采样介绍

- 1、关于采集的主要api介绍

```java
/** 
* @param audioSource 音频来源{@link MediaRecorder.AudioSource}；如指定麦克风：MediaRecorder.AudioSource.MIC
* @param sampleRateInHz 采样率{@link AudioFormat#SAMPLE_RATE_UNSPECIFIED}，单位Hz；安卓支持所有的设备是：44100Hz
* @param channelConfig 声道数{@link AudioFormat#CHANNEL_IN_MONO}；
* @param audioFormat 位宽{@link AudioFormat#ENCODING_PCM_8BIT}
* @param bufferSizeInBytes 采集期间缓存区的大小
*/
public AudioRecord(int audioSource, int sampleRateInHz, int channelConfig, int audioFormat,
            int bufferSizeInBytes)
  
//获取最小缓存区，参数跟AudioRecord保持一致
int getMinBufferSize(int sampleRateInHz, int channelConfig, int audioFormat) {}
```

- 2、实现采集的伪代码

```java
//1、申请权限

//2、获取最小缓存大小（根据api介绍，应该取要比预期大的缓冲区大小），这个大小其实也可以取①和②计算得来的大小
int minBufferSize = AudioRecord.getMinBufferSize(44100, AudioFormat.CHANNEL_IN_STEREO, AudioFormat.ENCODING_PCM_16BIT) * 2;

//3、初始化AudioRecord对象
AudioRecord audioRecord = new AudioRecord(MediaRecorder.AudioSource.MIC, 44100, AudioFormat.CHANNEL_IN_STEREO, AudioFormat.ENCODING_PCM_16BIT, minBufferSize);

//4、开始在子线程中进行采集数据
new Thread(new Runnable() {
  @Override
  public void run() {
    audioRecord.startRecording();
    while(isRunning){
      byte[] bytes = new byte[minBufferSize];
    	int len = audioRecord.read(bytes, 0, bytes.length);
      //这里就可以把数据直接写入到sdcard了，如：xxx.pcm；输出排序方式为：little endian。
    }
     //5、停止录音机
		audioRecord.stop();
  }
}).start();


//6、最后释放资源
audioRecord.release();
```

### 播放pcm原始数据

- ffmpeg：

> ffplay -f s16le -sample_rate 44100 -channels 2 -i xxx.pcm

- 其他：

> Adobe Audition

### 参考

-  [维基多媒体：pcm](https://wiki.multimedia.cx/index.php/PCM)
-  [视音频数据处理入门：PCM音频采样数据处理](http://blog.csdn.net/leixiaohua1020/article/details/50534316) 
- Android 音视频开发_何俊林（书）
