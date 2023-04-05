#Filter和SDL（Audio）

本文主要来自官方例子`doc/examples/filtering_video.c` 。

- [滤镜官方语法](http://ffmpeg.org/ffmpeg-filters.html) , 推荐参考《FFmpeg从入门到精通》。

##使用滤镜流程
[参考上一篇视频滤镜使用流程](./14_filter_v.md) 。注意以下一点：
- 获取滤镜器的名称
```text
输入：avfilter_get_by_name("buffer")      -> avfilter_get_by_name("abuffer")
输出：avfilter_get_by_name("buffersink") -> avfilter_get_by_name("abuffersink")
```

![视频滤镜使用流程](img/14_filter_v/filter-process.png)

其中，AVFormatContext、AVPacket等重要的结构体请看：[FFmpeg重要结构体](./06_struct.md) 。

##代码实现
```c
/**
 * @author 秦城季
 * @email xhunmon@126.com
 * @Blog https://qincji.gitee.io
 * @date 2021/01/10
 * description: 来自官方例子：doc/examples/filtering_audio.c
 * <br>
 */
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

#define _XOPEN_SOURCE 600 /* for usleep */

#include <unistd.h>

extern "C" {
#include <libavcodec/avcodec.h>
#include <libavformat/avformat.h>
#include <libavfilter/buffersink.h>
#include <libavfilter/buffersrc.h>
#include <libavutil/opt.h>
#include <SDL.h>
};
//fltp
//static const char *filter_descr = "aresample=44100,aformat=sample_fmts=fltp:channel_layouts=mono";
static const char *filter_descr = "aecho=0.8:0.88:60:0.4";//参考：http://ffmpeg.org/ffmpeg-filters.html#aecho
static const char *player = "ffplay -f s16le -ar 8000 -ac 1 -";

static AVFormatContext *fmt_ctx;
static AVCodecContext *dec_ctx;
AVFilterContext *buffersink_ctx;
AVFilterContext *buffersrc_ctx;
AVFilterGraph *filter_graph;
static int audio_stream_index = -1;

static int open_input_file(const char *filename) {
    int ret;
    AVCodec *dec;

    if ((ret = avformat_open_input(&fmt_ctx, filename, NULL, NULL)) < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot open input file\n");
        return ret;
    }

    if ((ret = avformat_find_stream_info(fmt_ctx, NULL)) < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot find stream information\n");
        return ret;
    }

    /* select the audio stream */
    ret = av_find_best_stream(fmt_ctx, AVMEDIA_TYPE_AUDIO, -1, -1, &dec, 0);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot find an audio stream in the input file\n");
        return ret;
    }
    audio_stream_index = ret;

    /* create decoding context */
    dec_ctx = avcodec_alloc_context3(dec);
    if (!dec_ctx)
        return AVERROR(ENOMEM);
    avcodec_parameters_to_context(dec_ctx, fmt_ctx->streams[audio_stream_index]->codecpar);

    /* init the audio decoder */
    if ((ret = avcodec_open2(dec_ctx, dec, NULL)) < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot open audio decoder\n");
        return ret;
    }

    return 0;
}

static int init_filters(const char *filters_descr) {
    char args[512];
    int ret = 0;
    const AVFilter *abuffersrc = avfilter_get_by_name("abuffer");
    const AVFilter *abuffersink = avfilter_get_by_name("abuffersink");
    AVFilterInOut *outputs = avfilter_inout_alloc();
    AVFilterInOut *inputs = avfilter_inout_alloc();
    static const enum AVSampleFormat out_sample_fmts[] = {AV_SAMPLE_FMT_FLTP, AV_SAMPLE_FMT_NONE};
    static const int64_t out_channel_layouts[] = {AV_CH_LAYOUT_STEREO, -1};
    static const int out_sample_rates[] = {44100, -1};
    const AVFilterLink *outlink;
    AVRational time_base = fmt_ctx->streams[audio_stream_index]->time_base;

    filter_graph = avfilter_graph_alloc();
    if (!outputs || !inputs || !filter_graph) {
        ret = AVERROR(ENOMEM);
        goto end;
    }

    /* buffer audio source: the decoded frames from the decoder will be inserted here. */
    if (!dec_ctx->channel_layout)
        dec_ctx->channel_layout = av_get_default_channel_layout(dec_ctx->channels);
    snprintf(args, sizeof(args),
             "time_base=%d/%d:sample_rate=%d:sample_fmt=%s:channel_layout=0x%lld",
             time_base.num, time_base.den, dec_ctx->sample_rate,
             av_get_sample_fmt_name(dec_ctx->sample_fmt), dec_ctx->channel_layout);
    ret = avfilter_graph_create_filter(&buffersrc_ctx, abuffersrc, "in",
                                       args, NULL, filter_graph);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot create audio buffer source\n");
        goto end;
    }

    /* buffer audio sink: to terminate the filter chain. */
    ret = avfilter_graph_create_filter(&buffersink_ctx, abuffersink, "out",
                                       NULL, NULL, filter_graph);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot create audio buffer sink\n");
        goto end;
    }

    ret = av_opt_set_int_list(buffersink_ctx, "sample_fmts", out_sample_fmts, -1,
                              AV_OPT_SEARCH_CHILDREN);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot set output sample format\n");
        goto end;
    }

    ret = av_opt_set_int_list(buffersink_ctx, "channel_layouts", out_channel_layouts, -1,
                              AV_OPT_SEARCH_CHILDREN);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot set output channel layout\n");
        goto end;
    }

    ret = av_opt_set_int_list(buffersink_ctx, "sample_rates", out_sample_rates, -1,
                              AV_OPT_SEARCH_CHILDREN);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot set output sample rate\n");
        goto end;
    }

    /*
     * Set the endpoints for the filter graph. The filter_graph will
     * be linked to the graph described by filters_descr.
     */

    /*
     * The buffer source output must be connected to the input pad of
     * the first filter described by filters_descr; since the first
     * filter input label is not specified, it is set to "in" by
     * default.
     */
    outputs->name = av_strdup("in");
    outputs->filter_ctx = buffersrc_ctx;
    outputs->pad_idx = 0;
    outputs->next = NULL;

    /*
     * The buffer sink input must be connected to the output pad of
     * the last filter described by filters_descr; since the last
     * filter output label is not specified, it is set to "out" by
     * default.
     */
    inputs->name = av_strdup("out");
    inputs->filter_ctx = buffersink_ctx;
    inputs->pad_idx = 0;
    inputs->next = NULL;

    if ((ret = avfilter_graph_parse_ptr(filter_graph, filters_descr,
                                        &inputs, &outputs, NULL)) < 0)
        goto end;

    if ((ret = avfilter_graph_config(filter_graph, NULL)) < 0)
        goto end;

    /* Print summary of the sink buffer
     * Note: args buffer is reused to store channel layout string */
    outlink = buffersink_ctx->inputs[0];
    av_get_channel_layout_string(args, sizeof(args), -1, outlink->channel_layout);
    av_log(NULL, AV_LOG_INFO, "Output: srate:%dHz fmt:%s chlayout:%s\n",
           (int) outlink->sample_rate,
           (char *) av_x_if_null(av_get_sample_fmt_name(static_cast<AVSampleFormat>(outlink->format)), "?"),
           args);

    end:
    avfilter_inout_free(&inputs);
    avfilter_inout_free(&outputs);

    return ret;
}


static Uint8 *audio_chunk;
static Uint32 audio_len;
static Uint8 *audio_pos;

void fill_audio(void *udata, Uint8 *stream, int len) {
    //SDL 2.0
    SDL_memset(stream, 0, len);
    if (audio_len == 0)
        return;
    len = (len > audio_len ? audio_len : len);

    SDL_MixAudio(stream, audio_pos, len, SDL_MIX_MAXVOLUME);
    audio_pos += len;
    audio_len -= len;
}


//https://blog.csdn.net/leixiaohua1020/article/details/40544521
static int init_sdl(AVCodecContext *dec_ctx) {
    int ret = -1;
    // B1. 初始化SDL子系统：缺省(事件处理、文件IO、线程)、视频、音频、定时器
    if (SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO | SDL_INIT_TIMER)) {
        printf("SDL_Init() failed: %s\n", SDL_GetError());
        goto end;
    }
    //注意：这里设置的参数会算出 audio_chunk 所使用的长度
    //audio_chunk = 采样数 * 通道数 * 位宽
    SDL_AudioSpec wanted_spec;
    wanted_spec.freq = dec_ctx->sample_rate;
//    wanted_spec.format = dec_ctx->sample_fmt;
    wanted_spec.format = AUDIO_F32SYS;//位宽=4
    wanted_spec.channels = dec_ctx->channels;//通道数
    wanted_spec.silence = 0;
    wanted_spec.samples = dec_ctx->frame_size;//采样数
    wanted_spec.callback = fill_audio;


    if (SDL_OpenAudio(&wanted_spec, NULL) < 0) {
        printf("can't open audio.\n");
        goto end;
    }
    ret = 1;
    //Play
    SDL_PauseAudio(0);
    end:
    return ret;
}

static void sdl_play(const AVFrame *frame) {
    if (frame->data[0][0] == '\0') {//没有数据？
        return;
    }
    int i, ch, data_size;
    data_size = av_get_bytes_per_sample(dec_ctx->sample_fmt);//每一个采样点所占的字节数
    Uint32 len = data_size * frame->nb_samples * dec_ctx->channels;//所有通道采样数所占字节长度（一帧大小）
    Uint8 *all_channels_buf = (Uint8 *) malloc(len);
    int index = 0;
    //把所有通道采样数据重新排列
    for (i = 0; i < frame->nb_samples; i++) {
        for (ch = 0; ch < dec_ctx->channels; ch++) {
            memcpy(all_channels_buf + index * data_size, frame->data[ch] + data_size * i, data_size);
            ++index;
        }
    }

    //把一帧数据设置给SDL播放器
    audio_chunk = all_channels_buf;
    audio_len = len;
    audio_pos = audio_chunk;
    while (audio_len > 0)//Wait until finish
        SDL_Delay(1);
    free(all_channels_buf);
}

static void print_frame(const AVFrame *frame) {
    sdl_play(frame);
    /*const int n = frame->nb_samples * av_get_channel_layout_nb_channels(frame->channel_layout);
    const uint16_t *p = (uint16_t *) frame->data[0];
    const uint16_t *p_end = p + n;

    while (p < p_end) {
         fputc(*p & 0xff, stdout);
         fputc(*p >> 8 & 0xff, stdout);
         p++;
     }
     fflush(stdout);*/
}

int main(int argc, char **argv) {
    int ret;
    AVPacket packet;
    AVFrame *frame = av_frame_alloc();
    AVFrame *filt_frame = av_frame_alloc();

    if (!frame || !filt_frame) {
        perror("Could not allocate frame");
        exit(1);
    }

    const char *filename = "source/Kobe.flv";

    if ((ret = open_input_file(filename)) < 0)
        goto end;
    if ((ret = init_filters(filter_descr)) < 0)
        goto end;
    if ((ret = init_sdl(dec_ctx)) < 0)
        goto end;

    /* read all packets */
    while (1) {
        if ((ret = av_read_frame(fmt_ctx, &packet)) < 0)
            break;

        if (packet.stream_index == audio_stream_index) {
            ret = avcodec_send_packet(dec_ctx, &packet);
            if (ret < 0) {
                av_log(NULL, AV_LOG_ERROR, "Error while sending a packet to the decoder\n");
                break;
            }

            while (ret >= 0) {
                ret = avcodec_receive_frame(dec_ctx, frame);
                if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF) {
                    break;
                } else if (ret < 0) {
                    av_log(NULL, AV_LOG_ERROR, "Error while receiving a frame from the decoder\n");
                    goto end;
                }

                if (ret >= 0) {
                    /* push the audio data from decoded frame into the filtergraph */
                    if (av_buffersrc_add_frame_flags(buffersrc_ctx, frame, AV_BUFFERSRC_FLAG_KEEP_REF) < 0) {
                        av_log(NULL, AV_LOG_ERROR, "Error while feeding the audio filtergraph\n");
                        break;
                    }

                    /* pull filtered audio from the filtergraph */
                    while (1) {
                        ret = av_buffersink_get_frame(buffersink_ctx, filt_frame);
                        if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF)
                            break;
                        if (ret < 0)
                            goto end;
                        //切换查看与原来的效果差异
//                        print_frame(frame);
                        print_frame(filt_frame);
                        av_frame_unref(filt_frame);
                    }
                    av_frame_unref(frame);
                }
            }
        }
        av_packet_unref(&packet);
    }
    end:
    avfilter_graph_free(&filter_graph);
    avcodec_free_context(&dec_ctx);
    avformat_close_input(&fmt_ctx);
    av_frame_free(&frame);
    av_frame_free(&filt_frame);

    if (ret < 0 && ret != AVERROR_EOF) {
        fprintf(stderr, "Error occurred: %s\n", av_err2str(ret));
        exit(1);
    }

    exit(0);
}
```

##[测试文件下载地址](../MustRead/img/01_flv/Kobe.flv)

参考
- https://blog.csdn.net/leixiaohua1020/article/details/40544521

