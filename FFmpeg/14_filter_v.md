#Filter和SDL（Video）

本文主要来自官方例子`doc/examples/filtering_video.c` 。

- [滤镜官方语法](http://ffmpeg.org/ffmpeg-filters.html) , 推荐参考《FFmpeg从入门到精通》。

##使用滤镜流程

![FFmpeg使用滤镜流程](img/14_filter_v/filter-process.png)


其中，AVFormatContext、AVPacket等重要的结构体请看：[FFmpeg重要结构体](./06_struct.md) 。

##代码实现
```c
/**
 * @author 秦城季
 * @email xhunmon@126.com
 * @Blog https://qincji.gitee.io
 * @date 2021/01/09
 * description: 来自官方例子：doc/examples/filtering_video.c
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

//swapuv
//const char *filter_descr = "scale=78:24,transpose=cclock";
//const char *filter_descr = "movie=logo.png[wm];[in][wm]overlay=30:10[out]";
const char *filter_descr = "scale=iw/2:ih/2[in_tmp];[in_tmp]split=4[in_1][in_2][in_3][in_4];[in_1]pad=iw*2:ih*2[a];[a][in_2]overlay=w[b];[b][in_3]overlay=0:h[d];[d][in_4]overlay=w:h[out]";
/* other way:
   scale=78:24 [scl]; [scl] transpose=cclock // assumes "[in]" and "[out]" to be input output pads respectively
 */

static AVFormatContext *fmt_ctx;
static AVCodecContext *dec_ctx;
AVFilterContext *buffersink_ctx;
AVFilterContext *buffersrc_ctx;
AVFilterGraph *filter_graph;
static int video_stream_index = -1;
static int64_t last_pts = AV_NOPTS_VALUE;

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

    /* select the video stream */
    ret = av_find_best_stream(fmt_ctx, AVMEDIA_TYPE_VIDEO, -1, -1, &dec, 0);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot find a video stream in the input file\n");
        return ret;
    }
    video_stream_index = ret;

    /* create decoding context */
    dec_ctx = avcodec_alloc_context3(dec);
    if (!dec_ctx)
        return AVERROR(ENOMEM);
    avcodec_parameters_to_context(dec_ctx, fmt_ctx->streams[video_stream_index]->codecpar);

    /* init the video decoder */
    if ((ret = avcodec_open2(dec_ctx, dec, NULL)) < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot open video decoder\n");
        return ret;
    }

    return 0;
}

static int init_filters(const char *filters_descr) {
    char args[512];
    int ret = 0;
    const AVFilter *buffersrc = avfilter_get_by_name("buffer");//预缓存帧数据。用于输入
    const AVFilter *buffersink = avfilter_get_by_name("buffersink");//缓冲视频帧，并使它们可用于过滤器图形的末尾。用于输出
    AVFilterInOut *outputs = avfilter_inout_alloc();
    AVFilterInOut *inputs = avfilter_inout_alloc();
    AVRational time_base = fmt_ctx->streams[video_stream_index]->time_base;
    enum AVPixelFormat pix_fmts[] = {AV_PIX_FMT_YUV420P, AV_PIX_FMT_NONE};//注意输入的格式类型pix_fmts[0]

    filter_graph = avfilter_graph_alloc();
    if (!outputs || !inputs || !filter_graph) {
        ret = AVERROR(ENOMEM);
        goto end;
    }

    /* buffer video source: the decoded frames from the decoder will be inserted here. */
    snprintf(args, sizeof(args),
             "video_size=%dx%d:pix_fmt=%d:time_base=%d/%d:pixel_aspect=%d/%d",
             dec_ctx->width, dec_ctx->height, dec_ctx->pix_fmt,
             time_base.num, time_base.den,
             dec_ctx->sample_aspect_ratio.num, dec_ctx->sample_aspect_ratio.den);

    //创建和初始化过滤器实例并将其添加到现有图形中。【输入】
    ret = avfilter_graph_create_filter(&buffersrc_ctx, buffersrc, "in",
                                       args, NULL, filter_graph);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot create buffer source\n");
        goto end;
    }

    //创建和初始化过滤器实例并将其添加到现有图形中。【输出】
    /* buffer video sink: to terminate the filter chain. */
    ret = avfilter_graph_create_filter(&buffersink_ctx, buffersink, "out",
                                       NULL, NULL, filter_graph);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot create buffer sink\n");
        goto end;
    }

    //给buffersink_ctx设置参数
    ret = av_opt_set_int_list(buffersink_ctx, "pix_fmts", pix_fmts,
                              AV_PIX_FMT_NONE, AV_OPT_SEARCH_CHILDREN);
    if (ret < 0) {
        av_log(NULL, AV_LOG_ERROR, "Cannot set output pixel format\n");
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

    //将字符串描述的图形添加到图形中。
    if ((ret = avfilter_graph_parse_ptr(filter_graph, filters_descr,
                                        &inputs, &outputs, NULL)) < 0){
        av_log(NULL, AV_LOG_ERROR, "Error avfilter_graph_parse_ptr\n");
        goto end;
    }

    //检查AVFilterGraph有效性
    if ((ret = avfilter_graph_config(filter_graph, NULL)) < 0){
        av_log(NULL, AV_LOG_ERROR, "Error avfilter_graph_config\n");
        goto end;
    }

    end:
    avfilter_inout_free(&inputs);
    avfilter_inout_free(&outputs);

    return ret;
}


static SDL_Renderer *sdl_renderer;
static SDL_Texture *sdl_texture;
static SDL_Rect sdl_rect;
static int init_sdl(AVCodecContext *dec_ctx) {
    int ret = -1;
    SDL_Window *screen;
    // B1. 初始化SDL子系统：缺省(事件处理、文件IO、线程)、视频、音频、定时器
    if (SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO | SDL_INIT_TIMER)){
        printf("SDL_Init() failed: %s\n", SDL_GetError());
        goto end;
    }

    // B2. 创建SDL窗口，SDL 2.0支持多窗口
    //     SDL_Window即运行程序后弹出的视频窗口，同SDL 1.x中的SDL_Surface
    screen = SDL_CreateWindow("Simplest ffmpeg player's Window",
                              SDL_WINDOWPOS_UNDEFINED,// 不关心窗口X坐标
                              SDL_WINDOWPOS_UNDEFINED,// 不关心窗口Y坐标
                              dec_ctx->width,
                              dec_ctx->height,
                              SDL_WINDOW_OPENGL
    );


    if (!screen){
        printf("SDL_CreateWindow() failed: %s\n", SDL_GetError());
        goto end;
    }

    // B3. 创建SDL_Renderer
    //     SDL_Renderer：渲染器
    sdl_renderer = SDL_CreateRenderer(screen, -1, 0);

    // B4. 创建SDL_Texture
    //     一个SDL_Texture对应一帧YUV数据，同SDL 1.x中的SDL_Overlay
    //     此处第2个参数使用的是SDL中的像素格式，对比参考注释A7
    //     FFmpeg中的像素格式AV_PIX_FMT_YUV420P对应SDL中的像素格式SDL_PIXELFORMAT_IYUV
    sdl_texture = SDL_CreateTexture(sdl_renderer,
                                    SDL_PIXELFORMAT_IYUV,
                                    SDL_TEXTUREACCESS_STREAMING,
                                    dec_ctx->width,
                                    dec_ctx->height);

    sdl_rect.x = 0;
    sdl_rect.y = 0;
    sdl_rect.w = dec_ctx->width;
    sdl_rect.h = dec_ctx->height;
    ret = 1;
    end:
    return ret;
}

static void sdl_play(const AVFrame *frame){
    // B5. 使用新的YUV像素数据更新SDL_Rect
    SDL_UpdateYUVTexture(sdl_texture,                   // sdl texture
                         &sdl_rect,                     // sdl rect &sdl_rect
                         frame->data[0],            // y plane
                         frame->linesize[0],        // y pitch
                         frame->data[1],            // u plane
                         frame->linesize[1],        // u pitch
                         frame->data[2],            // v plane
                         frame->linesize[2]         // v pitch
    );

    // B6. 使用特定颜色清空当前渲染目标
    SDL_RenderClear(sdl_renderer);
    // B7. 使用部分图像数据(texture)更新当前渲染目标
    SDL_RenderCopy(sdl_renderer,                        // sdl renderer
                   sdl_texture,                         // sdl texture
                   NULL,                                // src rect, if NULL copy texture
                   &sdl_rect                            // dst rect
    );
    // B8. 执行渲染，更新屏幕显示
    SDL_RenderPresent(sdl_renderer);

    // B9. 控制帧率为25FPS，此处不够准确，未考虑解码消耗的时间
    //Delay 40ms
    SDL_Event e;
    while (SDL_PollEvent(&e)) {
        SDL_Delay(40);
        if (e.type == SDL_QUIT) {
            break;
        }
    }
}

static void display_frame(const AVFrame *frame, AVRational time_base) {
    int x, y;
    uint8_t *p0, *p;
    int64_t delay;

    if (frame->pts != AV_NOPTS_VALUE) {
        if (last_pts != AV_NOPTS_VALUE) {
            /* sleep roughly the right amount of time;
             * usleep is in microseconds, just like AV_TIME_BASE. */
            delay = av_rescale_q(frame->pts - last_pts,
                                 time_base, AV_TIME_BASE_Q);
            if (delay > 0 && delay < 1000000)
                usleep(delay);
        }
        last_pts = frame->pts;
    }
    sdl_play(frame);
    /* Trivial ASCII grayscale display. */
//    p0 = frame->data[0];
//    puts("\033c");
    /*for (y = 0; y < frame->height; y++) {
        p = p0;
        for (x = 0; x < frame->width; x++)
            putchar(" .-+#"[*(p++) / 52]);
        putchar('\n');
        p0 += frame->linesize[0];
    }
    fflush(stdout);*/
}

int main(int argc, char **argv) {
    int ret;
    AVPacket packet;
    AVFrame *frame;
    AVFrame *filt_frame;

    const char *filename = "source/Kobe.flv";
//    const char *filename = "source/lol.mp4";

    frame = av_frame_alloc();
    filt_frame = av_frame_alloc();
    if (!frame || !filt_frame) {
        perror("Could not allocate frame");
        exit(1);
    }

    //获取解封装上下文（AVFormatContext）和解码器，并查找视频通道
    if ((ret = open_input_file(filename)) < 0)
        goto end;
    //这是本节的重点：滤镜器的使用
    if ((ret = init_filters(filter_descr)) < 0)
        goto end;
    //用来显示播放结果
    if ((ret = init_sdl(dec_ctx)) < 0)
        goto end;

    /* read all packets */
    while (1) {
        if ((ret = av_read_frame(fmt_ctx, &packet)) < 0)
            break;

        if (packet.stream_index == video_stream_index) {
            //送去解码
            ret = avcodec_send_packet(dec_ctx, &packet);
            if (ret < 0) {
                av_log(NULL, AV_LOG_ERROR, "Error while sending a packet to the decoder\n");
                break;
            }

            while (ret >= 0) {
                //获取解码后数据
                ret = avcodec_receive_frame(dec_ctx, frame);
                if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF) {
                    break;
                } else if (ret < 0) {
                    av_log(NULL, AV_LOG_ERROR, "Error while receiving a frame from the decoder\n");
                    goto end;
                }

                frame->pts = frame->best_effort_timestamp;

                /**将解码后得到的数据通过滤镜处理*/
                /* push the decoded frame into the filtergraph */
                if (av_buffersrc_add_frame_flags(buffersrc_ctx, frame, AV_BUFFERSRC_FLAG_KEEP_REF) < 0) {
                    av_log(NULL, AV_LOG_ERROR, "Error while feeding the filtergraph\n");
                    break;
                }

                /* pull filtered frames from the filtergraph */
                while (1) {
                    /**得到滤镜处理后的数据*/
                    ret = av_buffersink_get_frame(buffersink_ctx, filt_frame);
                    if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF)
                        break;
                    if (ret < 0)
                        goto end;
                    //将得到滤镜处理后的数据进行处理，播放、重新封装、转格式或者直接保存到文件等
                    display_frame(filt_frame, buffersink_ctx->inputs[0]->time_base);
                    av_frame_unref(filt_frame);
                }
                av_frame_unref(frame);
            }
        }
        av_packet_unref(&packet);
    }
    end:
    SDL_Quit();
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

### CMakeList配置
```cmake
cmake_minimum_required(VERSION 3.17)
project(VAFFmpeg)

set(CMAKE_CXX_STANDARD 11)
set(SOURCE_FILES main.cpp)

link_directories(lib)
include_directories(include)

#引入sdl2库
find_package(SDL2 REQUIRED)
include_directories(VAFFmpeg ${SDL2_INCLUDE_DIRS})

add_executable(VAFFmpeg ${SOURCE_FILES})

target_link_libraries(
        VAFFmpeg
        ${SDL2_LIBRARIES}
        -lavcodec
        -lavdevice
        -lavfilter
        -lavformat
        -lswresample
        -lswscale
#        -lx264
)
```

##[测试文件下载地址](../MustRead/img/01_flv/Kobe.flv)


参考
- https://blog.csdn.net/leixiaohua1020/article/details/29368911
- https://blog.csdn.net/leixiaohua1020/article/details/40876089
- FFmpeg从入门到精通
- 其他：忘记留地址了[尴尬]
