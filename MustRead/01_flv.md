#手撕FLV协议

##实现效果
纯代码实现分离FLV音视频流，并组装成AAC和H264文件，最后能正常播放。注：本文只对有AAC和H264格式音视频流组成的flv进行分离。

##FLV协议
![FLV协议](img/01_flv/flv-pr.jpg)

注：脚本部分本文未使用，[可前往这查看](https://www.cnblogs.com/chyingp/p/flv-getting-started.html)。

##实现代码
代码注释已经很详细了，其实就是对照协议表进行数据解析，其中：
- (1) H264协议请看[直播推流全过程：视频编码之H.264（3）](../RTMP/3-h264.md);
- (2) AAC协议请看[直播推流全过程：音频编码之AAC（4）](../RTMP/4-aac.md);

```c
#include <cstdlib>

/**
 * @author 秦城季
 * @email xhunmon@126.com
 * @Blog https://qincji.gitee.io
 * @date 2021/01/01
 * description:   <br>
 */
#include <cstdio>
#include <iostream>

using namespace std;

typedef struct Tag {
    int TagType;
    unsigned DataSize;
    unsigned Timestamp;
    unsigned TimestampExtended;
    int StreamID;
    unsigned char *Data;
} Tag;

//从flv中第一帧获取的数据
typedef struct FLV_AAC_HEAD {
    unsigned char audioObjectType: 5;
    unsigned char samplingFrequencyIndex: 4;
    unsigned char channelConfiguration: 4;
    unsigned char other: 3;//占位；这个值不需要的 000
} FLV_AAC_HEAD;

//aac封装 每个帧头结构设定恒为7Byte
typedef struct AAC_ADST_HEAD {
    //1. adts_fixed_header
    unsigned short syncword: 12;           /* 恒为 1111 1111 1111*/
    unsigned char ID: 1;                    /* 0：MPEG-4，1：MPEG-2*/
    unsigned char layer: 2;                  /* 00*/
    unsigned char protection_absent: 1;     /* 是否使用error_check()。0：使用，1：不使用。*/
    unsigned char profile: 2;               /* AAC类型*/
    unsigned char sampling_frequency_index: 4;/* 采样率的数组下标，即：sampling frequeny[sampling_frequency_index] */
    unsigned char private_bit: 1;           /* 0*/
    unsigned char channel_configuration: 3; /* 声道数*/
    unsigned char original_copy: 1;         /* 0*/
    unsigned char home: 1;                  /* 0*/

    //2. adts_variable_header
    unsigned char copyright_identification_bit: 1;  /* 0*/
    unsigned char copyright_identification_start: 1; /* 0*/
    unsigned short frame_length: 13;                 /* 帧的长度，包括head*/
    unsigned short adts_buffer_fullness: 11;        /* 固定0x7FF*/
    unsigned char number_of_raw_data_blocks_in_frame: 2; /*在ADTS帧中有number_of_raw_data_blocks_in_frame + 1个AAC原始数据块。number_of_raw_data_blocks_in_frame == 0 表示说ADTS帧中有一个AAC原始数据块。*/
} AAC_ADST_HEAD;

int checkFLV(FILE *fp_in) {
    int ret = 1;
    char *head = (char *) malloc(9);
    fread(head, 1, 9, fp_in);
    //Signature(FLV)
    if (head[0] != 'F' || head[1] != 'L' || head[2] != 'V') {
        printf("Not FLV File !\n");
        ret = 0;
    }
    //第5个字节为Flags(流信息)，视频标志位：.... ...1；音频标志位：.... .1..
    int vFlags = head[4] & 0x01;
    int aFlags = (head[4] & 0x04) >> 1;
    if (!vFlags) {
        printf("Not Video Data !\n");
        ret = 0;
    }
    if (!aFlags) {
        printf("Not Audio Data !\n");
        ret = 0;
    }
    free(head);
    return ret;
}

int getTagAndTagSize(FILE *fp_in, Tag *tag) {
    int length = -1;
    //Tag表中除了Data之外，其他所有的字节
    unsigned char *buf = (unsigned char *) malloc(11);
    //读取Tag前面11个字节
    fread(buf, 1, 11, fp_in);
    tag->TagType = buf[0];
    tag->DataSize = (buf[1] << 16) + (buf[2] << 8) + buf[3];
    tag->Timestamp = (buf[4] << 16) + (buf[5] << 8) + buf[6];
    tag->TimestampExtended = buf[7];
    tag->StreamID = (buf[8] << 16) + (buf[9] << 8) + buf[10];
    tag->Data = (unsigned char *) calloc(tag->DataSize, sizeof(char));
    //读取Data
    fread(tag->Data, 1, tag->DataSize, fp_in);
    //读取PreviousTagSize的4个字节
    fread(buf, 1, 4, fp_in);
    //这个长度就是 Tag(n)+PreviousTagSize(n)的长度
    length = 11 + tag->DataSize + 4;
    unsigned PreviousTagSize = (buf[0] << 24) + (buf[1] << 16) + (buf[2] << 8) + buf[3];
    free(buf);
    fprintf(stdout, "%9d| %9d| %10d| %18d| %9d| %16d|\n", tag->TagType, tag->DataSize, tag->Timestamp,
            tag->TimestampExtended,
            tag->StreamID, PreviousTagSize);
    return length;
}

unsigned char *sps_nalu = NULL;
unsigned char *pps_nalu = NULL;
char *buf_start = NULL;
unsigned sps_nalu_size = 0;
unsigned pps_nalu_size = 0;

int videoToH264(FILE *fp_video, Tag *tag) {
    int ret = 1;
    //tag->Data = FrameType + CodecID + VideoData ;
    //VideoData = AVCPacketType + CompositionTime + 【Data（注：视频数据）】
    unsigned FrameType = (tag->Data[0] & 0xF0) >> 4;//Data表(视频)：高4bit
    unsigned CodecID = tag->Data[0] & 0x0F;         //Data表(视频)：低4bit
    unsigned AVCPacketType = tag->Data[1];          //VideoData表( AVCVIDEOPACKE)
    unsigned CompositionTime = (tag->Data[2] << 16) + (tag->Data[3] << 8) + tag->Data[4];//VideoData表( AVCVIDEOPACKE)
    if (CodecID != 7) {//7: AVC(高级视频编码)
        ret = 0;
        printf("Not AVC Error\n");
        goto end;
    }
    printf("FrameType=%d\t|CodecID=%d\t|AVCPacketType=%d\t|CompositionTime=%d\t\n", FrameType, CodecID, AVCPacketType,
           CompositionTime);
    //从tag->Data[5]开始至结束为【Data（注：视频数据）】

    //一般FLV第一个视频Tag为sps和pps数据，需要根据AVCDecoderConfigurationRecord进行解析，单独抽出来封装成NALU数据
    if (AVCPacketType == 0) {//0: AVC sequence header
        printf("0: AVC sequence header\n");
        if (sps_nalu) {
            free(sps_nalu);
        }
        if (pps_nalu) {
            free(pps_nalu);
        }
        unsigned Version = tag->Data[5];//sps：版本
        unsigned AVCProfileIndication = tag->Data[6];//sps：编码规格
        unsigned profile_compatibility = tag->Data[7];//sps：编码规格
        unsigned AVCLevelIndication = tag->Data[8];//sps：编码规格
        unsigned reserved_And_lengthSizeMinusOne = tag->Data[9];//sps：应恒为0xFF
        unsigned numOfSequenceParameterSets = tag->Data[10] & 0x1F;//sps：sps个数，取低5位；tag->Data[10]一般为0xE1
        unsigned sps_len = (tag->Data[11] << 8) + tag->Data[12];//sps：sps长度
        sps_nalu_size = sps_len + 4;
        sps_nalu = (unsigned char *) malloc(sps_nalu_size);//加4个为起始位
        memcpy(sps_nalu, buf_start, 4);
        memcpy(sps_nalu + 4, tag->Data + 13, sps_len);
        printf("sps_len=%d\nsps_data=", sps_len);
        for (int i = 4; i < sps_nalu_size; ++i) {
            printf("%.2x", sps_nalu[i]);
        }
        printf("\n");
        fwrite(sps_nalu, 1, sps_nalu_size, fp_video);

        unsigned ppsIndex = 13 + sps_len;
        unsigned numOfPictureParameterSets = tag->Data[ppsIndex];//pps：pps个数，1个字节；
        unsigned pps_len = (tag->Data[ppsIndex + 1] << 8) + tag->Data[ppsIndex + 2];//pps：pps长度
        pps_nalu_size = pps_len + 4;
        pps_nalu = (unsigned char *) malloc(pps_nalu_size);//加4个为起始位
        memcpy(pps_nalu, buf_start, 4);
        memcpy(pps_nalu + 4, tag->Data + ppsIndex + 3, pps_len);
        printf("pps_len=%d\npps_data=", pps_len);
        for (int i = 4; i < pps_nalu_size; ++i) {
            printf("%.2x", pps_nalu[i]);
        }
        printf("\n");
        fwrite(pps_nalu, 1, pps_nalu_size, fp_video);
    } else if (AVCPacketType == 1) {//1: AVC NALU：可能包括多个NALU，需要重新封装
        printf("1: AVC NALU\n");
        //【Data（注：视频数据）】= [size1+pic1]+...+[sizen+picn] 转成 [起始码+pic1]+...+[起始码+picn]
        unsigned int pic_start = 5;
        unsigned int pic_size = 0;
        unsigned int pic_next_index = 0;
        while (1) {
            if (pic_start + 4 >= tag->DataSize) {
                printf("----break-----, pic_start + 4 >= tag->DataSize\n");
                break;
            }
            printf("pic_size=0x%.2x%.2x%.2x%.2x\n", tag->Data[pic_start], tag->Data[pic_start + 1],
                   tag->Data[pic_start + 2],
                   tag->Data[pic_start + 3]);
            pic_size =
                    (tag->Data[pic_start] << 24) + (tag->Data[pic_start + 1] << 16) + (tag->Data[pic_start + 2] << 8) +
                    tag->Data[pic_start + 3];
            pic_next_index = pic_start + 4 + pic_size;
            if (pic_size <= 0 || tag->DataSize < pic_next_index) {
                printf("----break-----, Data Error ! pic_start=%d\tpic_size=%d\tpic_next_index=%d\tDataSize=%d \n",
                       pic_start, pic_size, pic_next_index,
                       tag->DataSize);
                break;
            }
            printf("write pic data,pic_start=%d\tpic_size=%d\tpic_next_index=%d\tDataSize=%d \n", pic_start, pic_size,
                   pic_next_index,
                   tag->DataSize);
            //当前帧数据范围 pic_start + 4 ~ pic_next_index
            fwrite(buf_start, 1, 4, fp_video);
            fwrite(tag->Data + pic_start + 4, 1, pic_size, fp_video);
            pic_start = pic_next_index;
        }
    } else if (AVCPacketType == 2) {//2: AVC end of sequence
        printf("2: AVC end of sequence\n");
    }

    end:
    free(tag->Data);
    return ret;
}

AAC_ADST_HEAD *aacAdstHead = NULL;

int getAACHeadBuf(unsigned char *buf, AAC_ADST_HEAD *aacAdstHead, const unsigned int frame_size) {
    int ret = 0;
    aacAdstHead->frame_length = frame_size;
    //取syncword高位1Byte
    buf[0] = aacAdstHead->syncword >> 4;
    //syncword(低4位)+ID(1)+layer(2)+protection_absent(1)
    buf[1] = ((aacAdstHead->syncword & 0x00F) << 4) + (aacAdstHead->ID >> 3) + (aacAdstHead->layer >> 1) +
             aacAdstHead->protection_absent;
    //profile(2)+sampling_frequency_index(4)+private_bit(1)+channel_configuration(3bit取最高1bit)
    buf[2] = (aacAdstHead->profile << 6) + (aacAdstHead->sampling_frequency_index << 2) + (aacAdstHead->private_bit
            << 1) + (aacAdstHead->channel_configuration
            >> 2);
    // ...00000 00000000
    //channel_configuration(3bit取低2bit)+original_copy(1)+home(1)+copyright_identification_bit(1)+copyright_identification_start(1)+frame_length(13bit取最高2bit)
    buf[3] = (aacAdstHead->channel_configuration << 6) + (aacAdstHead->original_copy << 5) + (aacAdstHead->home << 4)
             + (aacAdstHead->copyright_identification_bit << 3) + (aacAdstHead->copyright_identification_start << 2)
             + (aacAdstHead->frame_length >> 11);
    //frame_length(13bit取：去掉最高2bit开始8位)
    buf[4] = (aacAdstHead->frame_length >> 3) & 0x0FF;
    //frame_length(13bit取最低3bit)+adts_buffer_fullness(11bit取高5bit)
    buf[5] = (aacAdstHead->frame_length << 5) + (aacAdstHead->adts_buffer_fullness >> 6);
    //adts_buffer_fullness(11bit取低6bit) + number_of_raw_data_blocks_in_frame(2)
    buf[6] = (aacAdstHead->adts_buffer_fullness << 2) + aacAdstHead->number_of_raw_data_blocks_in_frame;
    return ret;
}

int audioToAac(FILE *fp_audio, Tag *tag) {
    int ret = 1;
    unsigned SoundFormat = tag->Data[0] >> 4;
    unsigned SoundRate = (tag->Data[0] & 0x0F) >> 2;
    unsigned SoundSize = (tag->Data[0] & 0x02) >> 1;
    unsigned SoundType = tag->Data[0] & 0x01;
    //SoundData = AACPacketType + Data
    unsigned AACPacketType = tag->Data[1];

    if (SoundFormat != 10) {//10 = AAC
        printf("Not AAC Error\n");
        ret = 0;
        goto end;
    }

    if (AACPacketType == 0) {//0: AAC sequence header(参考14496-3 AudioSpecificConfig https://wiki.multimedia.cx/index.php/MPEG-4_Audio#Audio_Specific_Config)
        printf("0: AAC sequence header\n");
        //读取flv文件中的头信息
        FLV_AAC_HEAD *flvAacHead = (FLV_AAC_HEAD *) malloc(sizeof(FLV_AAC_HEAD));
        //只要前面两个字节就行，如0x1390=00010011 10010000
        flvAacHead->audioObjectType = tag->Data[2] >> 3;//UB[5]=00010... ........
        flvAacHead->samplingFrequencyIndex =
                ((tag->Data[2] & 0x07) << 1) + (tag->Data[3] >> 7);//UB[4]=.....011 1.......
        flvAacHead->channelConfiguration = (tag->Data[3] & 0x7F) >> 3;//UB[4]=........ .0010...
        flvAacHead->other = tag->Data[3] & 0x07;//两个字节剩余的bit，为0也行。

        //构造AAC_ADST_HEAD
        if (aacAdstHead) {
            free(aacAdstHead);
        }
        aacAdstHead = (AAC_ADST_HEAD *) malloc(sizeof(AAC_ADST_HEAD));
        aacAdstHead->syncword = 0xFFF;
        aacAdstHead->ID = 1;
        aacAdstHead->layer = 0;
        aacAdstHead->protection_absent = 1;
        aacAdstHead->profile = flvAacHead->audioObjectType;
//        aacAdstHead->profile = 1;
        aacAdstHead->sampling_frequency_index = flvAacHead->samplingFrequencyIndex;
        aacAdstHead->private_bit = 0;
        aacAdstHead->channel_configuration = flvAacHead->channelConfiguration;
        aacAdstHead->original_copy = 0;
        aacAdstHead->home = 0;

        aacAdstHead->copyright_identification_bit = 0;
        aacAdstHead->copyright_identification_start = 0;
//        aacAdstHead->frame_length = xx; //帧的长度动态计算的
        aacAdstHead->adts_buffer_fullness = 0x7FF;
        aacAdstHead->number_of_raw_data_blocks_in_frame = 0;
        printf("audioObjectType=%d\tsamplingFrequencyIndex=%d\t channelConfiguration=%d\n ",
               flvAacHead->audioObjectType, flvAacHead->samplingFrequencyIndex, flvAacHead->channelConfiguration);
        free(flvAacHead);
    } else if (AACPacketType == 1) {//1: AAC raw
        printf("1: AAC raw\n");
        unsigned char *buf = (unsigned char *) malloc(7);;
        if (getAACHeadBuf(buf, aacAdstHead, tag->DataSize - 2 + 7) == -1) {
            printf("get AAC Head Buf Error\n");
            ret = 0;
            goto end;
        }
        fwrite(buf, 1, 7, fp_audio);
        fwrite(tag->Data + 2, 1, tag->DataSize - 2, fp_audio);
        free(buf);
    }

    end:
    free(tag->Data);
    return ret;
}

int test() {
    return 0;
}

int main() {
    if (test()) {//非0位true
        exit(1);
    }
    printf("delete cache video file ? %d\n", remove("output/Kobe.h264"));
    printf("delete cache audio file ? %d\n", remove("output/Kobe.aac"));

    FILE *fp_in = fopen("assets/Kobe.flv", "rb+");
    FILE *fp_video = fopen("output/Kobe.h264", "wb+");
    FILE *fp_audio = fopen("output/Kobe.aac", "wb+");
    Tag *tag;
    if (!fp_in) {
        cout << "源文件不存在！" << endl;
        exit(1);
    }
    if (!fp_video) {
        cout << "输出文件不存在！" << endl;
        exit(1);
    }

    if (!checkFLV(fp_in)) {
        exit(1);
    }

    //跳过PreviousTagSize(0) 4byte
    fseek(fp_in, 4, SEEK_CUR);
    tag = (Tag *) calloc(1, sizeof(Tag));
    //初始化h264中NALU的起始码（0x00 00 00 01或者0x00 00 01）
    buf_start = (char *) malloc(4);
    buf_start[0] = 0x00;
    buf_start[1] = 0x00;
    buf_start[2] = 0x00;
    buf_start[3] = 0x01;
    if (!tag) {
        printf("Alloc Tag Error\n");
        return 0;
    }
    printf("---------+----------+----------Tag------------------+----------+-----------------+\n");
    printf(" TagType | DataSize | Timestamp | TimestampExtended | StreamID | PreviousTagSize |\n");
    printf("---------+----------+-----------+-------------------+----------+-----------------+\n");
    while (!feof(fp_in)) {
        //查找一组：Tag + PreviousTagSize
        int data_lenth;
        int ret;
        data_lenth = getTagAndTagSize(fp_in, tag);

        if (tag->TagType == 0x12) {//脚本数据

        }
        if (tag->TagType == 0x08) {//音频数据
            ret = audioToAac(fp_audio, tag);
        }
        if (tag->TagType == 0x09) {//视频数据
            ret = videoToH264(fp_video, tag);
        }
    }
    if (sps_nalu) {
        free(sps_nalu);
    }
    if (pps_nalu) {
        free(pps_nalu);
    }
    if (buf_start) {
        free(buf_start);
    }
    fclose(fp_video);
    return 0;
}
```

##使用ffplay查看输出文件
h264文件播放：
> ffplay xxx.h264

aac文件播放：
> ffplay xxx.aac

##[测试文件下载地址](07_flvobe.flv)



参考
- https://www.cnblogs.com/chyingp/p/flv-getting-started.html
- ISO/IEC_14496-3

