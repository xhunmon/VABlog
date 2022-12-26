#OpenGL基础知识

## OpenGL简述
OpenGL（英语：Open Graphics Library，译名：开放图形库或者“开放式图形库”）是用于渲染2D、3D矢量图形的跨语言、跨平台的应用程序编程接口（API）。对于我们音视频开发最大的好处是可以使用OpenGL操控显卡（GPU）播放（渲染）视频，实现硬件加速，并且释放了宝贵的CPU资源。

OpenGL只提供了渲染的功能，核心API没有窗口系统、音频、打印、键盘／鼠标或其他输入设备的概念。然而，有些集成于原生窗口系统的东西需要允许和宿主系统交互，并且为了减轻繁重的编程工作，封装了OpenGL函数，为开发者提供相对简单的用法，实现一些较为复杂的操作。

1）以下包可以用来创建并管理 OpenGL 窗口，也可以管理输入，但几乎没有除此以外的其它功能：
- [GLFW](https://zh.wikipedia.org/wiki/GLFW) ——跨平台窗口和键盘、鼠标、手柄处理；偏向游戏。
- [freeglut](https://sourceforge.net/projects/freeglut/) ——跨平台窗口和键盘、鼠标处理；API 是 GLUT API 的超集，同时也比 GLUT 更新、更稳定。
- [GLUT](https://zh.wikipedia.org/wiki/GLUT) ——早期的窗口处理库，已不再维护。

2）支持创建 OpenGL 窗口的还有一些“多媒体库”，同时还支持输入、声音等类似游戏的程序所需要的功能：
- Allegro 5——跨平台多媒体库，提供针对游戏开发的 C API
- SDL——跨平台多媒体库，提供 C API
- SFML——跨平台多媒体库，提供 C++ API；同时也提供 C#、Java、Haskell、Go 等语言的绑定

3）窗口包：
- FLTK——小型的跨平台 C++ 窗口组件库
- Qt——跨平台 C++ 窗口组件库，提供许多OpenGL辅助对象
- wxWidgets——跨平台 C++ 窗口组件库

## OpenGL ES
[OpenGL ES](https://zh.wikipedia.org/wiki/OpenGL_ES) （OpenGL for Embedded Systems）是三维图形应用程序接口OpenGL的子集，针对手机、PDA和游戏主机等嵌入式设备而设计，如[Android](https://developer.android.google.cn/reference/android/opengl/package-summary?hl=en) 。


## OpenGL播放YUV
```c
/**
 * @author 秦城季
 * @email xhunmon@126.com
 * @Blog https://qincji.gitee.io
 * @date 2021/01/21
 * description: MacOS使用OpenGL播放YUV
 * <br>
 */
#define GLFW_INCLUDE_GLU

#define NDEBUG
extern "C" {
#include <GLUT/GLUT.h> //MacOS上引入
}

//set '1' to choose a type of file to play
#define LOAD_RGB24   0
#define LOAD_BGR24   0
#define LOAD_BGRA    0
#define LOAD_YUV420P 1

int screen_w = 384, screen_h = 216;
const int pixel_w = 384, pixel_h = 216;
//Bit per Pixel
#if LOAD_BGRA
const int bpp=32;
#elif LOAD_RGB24 | LOAD_BGR24
const int bpp = 24;
#elif LOAD_YUV420P
const int bpp = 12;
#endif
//YUV file
FILE *fp = NULL;
unsigned char buffer[pixel_w * pixel_h * bpp / 8];
unsigned char buffer_convert[pixel_w * pixel_h * 3];

inline unsigned char CONVERT_ADJUST(double tmp) {
    return (unsigned char) ((tmp >= 0 && tmp <= 255) ? tmp : (tmp < 0 ? 0 : 255));
}

//YUV420P to RGB24
void CONVERT_YUV420PtoRGB24(unsigned char *yuv_src, unsigned char *rgb_dst, int nWidth, int nHeight) {
    unsigned char *tmpbuf = (unsigned char *) malloc(nWidth * nHeight * 3);
    unsigned char Y, U, V, R, G, B;
    unsigned char *y_planar, *u_planar, *v_planar;
    int rgb_width, u_width;
    rgb_width = nWidth * 3;
    u_width = (nWidth >> 1);
    int ypSize = nWidth * nHeight;
    int upSize = (ypSize >> 2);
    int offSet = 0;

    y_planar = yuv_src;
    u_planar = yuv_src + ypSize;
    v_planar = u_planar + upSize;

    for (int i = 0; i < nHeight; i++) {
        for (int j = 0; j < nWidth; j++) {
            // Get the Y value from the y planar
            Y = *(y_planar + nWidth * i + j);
            // Get the V value from the u planar
            offSet = (i >> 1) * (u_width) + (j >> 1);
            V = *(u_planar + offSet);
            // Get the U value from the v planar
            U = *(v_planar + offSet);

            // Cacular the R,G,B values
            // Method 1
            R = CONVERT_ADJUST((Y + (1.4075 * (V - 128))));
            G = CONVERT_ADJUST((Y - (0.3455 * (U - 128) - 0.7169 * (V - 128))));
            B = CONVERT_ADJUST((Y + (1.7790 * (U - 128))));
            offSet = rgb_width * i + j * 3;

            rgb_dst[offSet] = B;
            rgb_dst[offSet + 1] = G;
            rgb_dst[offSet + 2] = R;
        }
    }
    free(tmpbuf);
}

void display(void) {
    if (fread(buffer, 1, pixel_w * pixel_h * bpp / 8, fp) != pixel_w * pixel_h * bpp / 8) {
        // Loop
        fseek(fp, 0, SEEK_SET);
        fread(buffer, 1, pixel_w * pixel_h * bpp / 8, fp);
    }

    //Make picture full of window
    //Move to(-1.0,1.0)
    glRasterPos3f(-1.0f, 1.0f, 0);
    //Zoom, Flip
    glPixelZoom((float) screen_w / (float) pixel_w, -(float) screen_h / (float) pixel_h);


#if LOAD_BGRA
    glDrawPixels(pixel_w, pixel_h,GL_BGRA, GL_UNSIGNED_BYTE, buffer);
#elif LOAD_RGB24
    glDrawPixels(pixel_w, pixel_h, GL_RGB, GL_UNSIGNED_BYTE, buffer);
#elif LOAD_BGR24
    glDrawPixels(pixel_w, pixel_h,GL_BGR_EXT, GL_UNSIGNED_BYTE, buffer);
#elif LOAD_YUV420P
    CONVERT_YUV420PtoRGB24(buffer, buffer_convert, pixel_w, pixel_h);
    glDrawPixels(pixel_w, pixel_h, GL_RGB, GL_UNSIGNED_BYTE, buffer_convert);
#endif
    //GLUT_DOUBLE
    glutSwapBuffers();

    //GLUT_SINGLE
    //glFlush();
}

void timeFunc(int value) {
    display();
    // Present frame every 40 ms
    glutTimerFunc(40, timeFunc, 0);
}

void processSpecialKeys(int key, int x, int y) {
    printf("OpenGL processSpecialKeys: %d\n", key);
    switch (key) {
        case GLUT_KEY_UP:
            break;
        case GLUT_KEY_DOWN:
            break;
        case GLUT_KEY_LEFT:
            break;
        case GLUT_KEY_RIGHT:
            break;
    }
}

int main(int argc, char **argv) {
    const char *src_filename = "source/Kobe-384x216-yuv420p.yuv";
    fp = fopen(src_filename, "rb+");

    // GLUT init
    glutInit(&argc, argv);
    //Double, Use glutSwapBuffers() to show
    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB);
    //Single, Use glFlush() to show
//    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB );

    //在屏幕中显示的位置
    glutInitWindowPosition(0, 0);
    //窗口的大小
    glutInitWindowSize(screen_w, screen_h);
    //创建窗口
    glutCreateWindow("Simplest Video Play OpenGL");
    printf("OpenGL Version: %s\n", glGetString(GL_VERSION));
    //显示的函数
    glutDisplayFunc(&display);
    //轮询函数
    glutTimerFunc(40, timeFunc, 0);
    //监听按键
    glutSpecialFunc(processSpecialKeys);

    // Start!
    glutMainLoop();

    return 0;
}
```


## 学习资料

- [OpenGL介绍，和相关程序库](https://zh.wikipedia.org/wiki/OpenGL)
- [纹理有关的基础知识](https://blog.csdn.net/leixiaohua1020/article/details/40301179) 。
- [最简单的视音频播放示例5：OpenGL播放RGB/YUV](https://blog.csdn.net/leixiaohua1020/article/details/40333583)
- [最简单的视音频播放示例6：OpenGL播放YUV420P（通过Texture，使用Shader）](https://blog.csdn.net/leixiaohua1020/article/details/40379845)
- [Android OpenGL ES官方文档](https://developer.android.google.cn/guide/topics/graphics/opengl?hl=zh_cn)
- [LearnOpenGL-CN](https://github.com/LearnOpenGL-CN/LearnOpenGL-CN)
- [电子书](https://github.com/Canber/OpenGL-ES-for-android)
