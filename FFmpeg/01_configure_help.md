#Configure配置选项 (转载)

<html>
 <head></head>
 <body>
  <article class="baidu_pl"> 
   <div id="article_content" class="article_content clearfix"> 
    <link rel="stylesheet" href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/ck_htmledit_views-b5506197d8.css" /> 
    <div id="content_views" class="markdown_views"> 
     <svg xmlns="http://www.w3.org/2000/svg" style="display: none;"> 
      <path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path> 
     </svg> 
     <p></p> 
     <div class="toc"> 
      <div class="toc"> 
       <ul>
        <li> 
         <ul>
          <li><a href="#帮助选项help-options" target="_self">帮助选项Help options</a></li>
          <li><a href="#标准选项standard-options" target="_self">标准选项Standard options</a></li>
          <li><a href="#许可证选项licensing-options" target="_self">许可证选项Licensing options</a></li>
          <li><a href="#配置选项configuration-options" target="_self">配置选项Configuration options</a></li>
          <li><a href="#程序选项program-options" target="_self">程序选项Program options</a></li>
          <li><a href="#文档选项documentation-options" target="_self">文档选项Documentation options</a></li>
          <li><a href="#组件选项component-options" target="_self">组件选项Component options</a></li>
          <li><a href="#个别组件选项individual-component-options" target="_self">个别组件选项Individual component options</a></li>
          <li><a href="#扩展库支持external-library-support" target="_self">扩展库支持External library support</a> 
           <ul>
            <li><a href="#硬件加速功能hardware-acceleration-features" target="_self">硬件加速功能hardware acceleration features</a></li>
           </ul> </li>
          <li><a href="#工具链选项toolchain-options" target="_self">工具链选项Toolchain options</a></li>
          <li><a href="#高级选项advanced-options" target="_self">高级选项Advanced options</a></li>
          <li><a href="#优化选项optimization-options" target="_self">优化选项Optimization options</a></li>
          <li><a href="#开发者选项developer-options" target="_self">开发者选项Developer options</a></li>
         </ul> </li>
       </ul> 
      </div> 
     </div> 
     <p></p> 
     <h2 id="帮助选项help-options"><a name="t0"></a><a name="t0"></a>帮助选项（Help options）</h2> 
     <p>用于查看ffmpeg的能力</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–help</td>
         <td>print this message</td>
        </tr>
        <tr>
         <td>–list-decoders</td>
         <td>显示所有可用的解码器（h264/mjpeg等）</td>
        </tr>
        <tr>
         <td>–list-encoders</td>
         <td>显示所有可用的编码器（h264/mjpeg等）</td>
        </tr>
        <tr>
         <td>–list-hwaccels</td>
         <td>显示所有支持的硬编解码器（h264_videotoolbox/h264_mediacodec等）</td>
        </tr>
        <tr>
         <td>–list-demuxers</td>
         <td>显示所有支持解复用的容器（mp4/h264等）</td>
        </tr>
        <tr>
         <td>–list-muxers</td>
         <td>显示所有支持复用的容器（mp4/h264等）</td>
        </tr>
        <tr>
         <td>–list-parsers</td>
         <td>show all available parsers</td>
        </tr>
        <tr>
         <td>–list-protocols</td>
         <td>显示所有支持的传输协议（rtmp/rtp等）</td>
        </tr>
        <tr>
         <td>–list-bsfs</td>
         <td>显示所有可用的格式转换（h264_mp4toannexb/aac_adtstoasc等）</td>
        </tr>
        <tr>
         <td>–list-indevs</td>
         <td>显示所有支持的输入设备（alsa/v4l2等）</td>
        </tr>
        <tr>
         <td>–list-outdevs</td>
         <td>显示所有支持的输出设备（alsa/opengl等）</td>
        </tr>
        <tr>
         <td>–list-filters</td>
         <td>显示支持的所有过滤器（scale/volume/fps/allyuv等）</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="标准选项standard-options"><a name="t1"></a><a name="t1"></a>标准选项（Standard options）</h2> 
     <p>编译配置</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–logfile=FILE</td>
         <td>配置过程中的log输出文件，默认输出到当前位置的ffbuild/config.log文件</td>
        </tr>
        <tr>
         <td>–disable-logging</td>
         <td>配置过程中不输出日志</td>
        </tr>
        <tr>
         <td>–fatal-warnings</td>
         <td>把配置过程中的任何警告当做致命的错误处理</td>
        </tr>
        <tr>
         <td>–prefix=PREFIX</td>
         <td>设定安装的跟目录，如果不指定默认是/usr</td>
        </tr>
        <tr>
         <td>–bindir=DIR</td>
         <td>设置可执行程序的安装位置，默认是[PREFIX/bin]</td>
        </tr>
        <tr>
         <td>–datadir=DIR</td>
         <td>设置测试程序以及数据的安装位置，默认是[PREFIX/share/ffmpeg]</td>
        </tr>
        <tr>
         <td>–docdir=DIR</td>
         <td>设置文档的安装目录，默认是[PREFIX/share/doc/ffmpeg]</td>
        </tr>
        <tr>
         <td>–libdir=DIR</td>
         <td>设置静态库的安装位置，默认是[PREFIX/lib]</td>
        </tr>
        <tr>
         <td>–shlibdir=DIR</td>
         <td>设置动态库的安装位置，默认是[LIBDIR]</td>
        </tr>
        <tr>
         <td>–incdir=DIR</td>
         <td>设置头文件的安装位置，默认是[PREFIX/include]；一般来说用于依赖此头文件来开发就够了</td>
        </tr>
        <tr>
         <td>–mandir=DIR</td>
         <td>设置man文件的安装目录，默认是[PREFIX/share/man]</td>
        </tr>
        <tr>
         <td>–pkgconfigdir=DIR</td>
         <td>设置pkgconfig的安装目录，默认是[LIBDIR/pkgconfig]，只有<code>--enable-shared</code>使能的时候这个选项才有效</td>
        </tr>
        <tr>
         <td>–enable-rpath</td>
         <td>use rpath to allow installing libraries in paths not part of the dynamic linker search path use rpath when linking programs [USE WITH CARE]</td>
        </tr>
        <tr>
         <td>–install-name-dir=DIR</td>
         <td>Darwin directory name for installed targets</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="许可证选项licensing-options"><a name="t2"></a><a name="t2"></a>许可证选项（Licensing options）</h2> 
     <p>选择许可证，FFMPEG默认许可证LGPL 2.1，如果需要加gpl的库需要使用gpl的许可证，例如libx264就是gpl的，如果需要加入libx264则需要<code>--enable-gpl</code>。</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–enable-gpl</td>
         <td>allow use of GPL code, the resulting libs and binaries will be under GPL [no]</td>
        </tr>
        <tr>
         <td>–enable-version3</td>
         <td>upgrade (L)GPL to version 3 [no]</td>
        </tr>
        <tr>
         <td>–enable-nonfree</td>
         <td>allow use of nonfree code, the resulting libs and binaries will be unredistributable [no]</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="配置选项configuration-options"><a name="t3"></a><a name="t3"></a>配置选项（Configuration options）</h2> 
     <p>编译选项的配置</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-static</td>
         <td>不生产静态库，默认生成静态库</td>
        </tr>
        <tr>
         <td>–enable-shared</td>
         <td>生成动态库，默认不生成动态库</td>
        </tr>
        <tr>
         <td>–enable-small</td>
         <td>optimize for size instead of speed，默认开启</td>
        </tr>
        <tr>
         <td>–disable-runtime-cpudetect</td>
         <td>disable detecting cpu capabilities at runtime (smaller binary)，默认开启</td>
        </tr>
        <tr>
         <td>–enable-gray</td>
         <td>enable full grayscale support (slower color)</td>
        </tr>
        <tr>
         <td>–disable-swscale-alpha</td>
         <td>disable alpha channel support in swscale</td>
        </tr>
        <tr>
         <td>–disable-all</td>
         <td>禁止编译所有库和可执行程序</td>
        </tr>
        <tr>
         <td>–enable-raise-major</td>
         <td>增加主版本号</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="程序选项program-options"><a name="t4"></a><a name="t4"></a>程序选项（Program options）</h2> 
     <p>可执行程序开启选项，默认编译ffmpeg中的所有可执行程序，包括ffmpeg、ffplay、ffprobe、ffserver，不过Mac平台默认情况下不生成ffplay，目前暂未知道啥原因。</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-programs</td>
         <td>do not build command line programs</td>
        </tr>
        <tr>
         <td>–disable-ffmpeg</td>
         <td>disable ffmpeg build</td>
        </tr>
        <tr>
         <td>–disable-ffplay</td>
         <td>disable ffplay build</td>
        </tr>
        <tr>
         <td>–disable-ffprobe</td>
         <td>disable ffprobe build</td>
        </tr>
        <tr>
         <td>–disable-ffserver</td>
         <td>disable ffserver build</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="文档选项documentation-options"><a name="t5"></a><a name="t5"></a>文档选项（Documentation options）</h2> 
     <p>离线文档选择</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-doc</td>
         <td>do not build documentation</td>
        </tr>
        <tr>
         <td>–disable-htmlpages</td>
         <td>do not build HTML documentation pages</td>
        </tr>
        <tr>
         <td>–disable-manpages</td>
         <td>do not build man documentation pages</td>
        </tr>
        <tr>
         <td>–disable-podpages</td>
         <td>do not build POD documentation pages</td>
        </tr>
        <tr>
         <td>–disable-txtpages</td>
         <td>do not build text documentation pages</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="组件选项component-options"><a name="t6"></a><a name="t6"></a>组件选项（Component options）</h2> 
     <p>除了avresample模块，默认编译所有模块。一般来说用于轻量化ffmpeg库的大小，可以仅仅开启指定某些组件的某些功能。</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-avdevice</td>
         <td>disable libavdevice build</td>
        </tr>
        <tr>
         <td>–disable-avcodec</td>
         <td>disable libavcodec build</td>
        </tr>
        <tr>
         <td>–disable-avformat</td>
         <td>disable libavformat build</td>
        </tr>
        <tr>
         <td>–disable-swresample</td>
         <td>disable libswresample build</td>
        </tr>
        <tr>
         <td>–disable-swscale</td>
         <td>disable libswscale build</td>
        </tr>
        <tr>
         <td>–disable-postproc</td>
         <td>disable libpostproc build</td>
        </tr>
        <tr>
         <td>–disable-avfilter</td>
         <td>disable libavfilter build</td>
        </tr>
        <tr>
         <td>–enable-avresample</td>
         <td>enable libavresample build [no]</td>
        </tr>
        <tr>
         <td>–disable-pthreads</td>
         <td>disable pthreads [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-w32threads</td>
         <td>disable Win32 threads [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-os2threads</td>
         <td>disable OS/2 threads [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-network</td>
         <td>disable network support [no]</td>
        </tr>
        <tr>
         <td>–disable-dct</td>
         <td>disable DCT code</td>
        </tr>
        <tr>
         <td>–disable-dwt</td>
         <td>disable DWT code</td>
        </tr>
        <tr>
         <td>–disable-error-resilience</td>
         <td>disable error resilience code</td>
        </tr>
        <tr>
         <td>–disable-lsp</td>
         <td>disable LSP code</td>
        </tr>
        <tr>
         <td>–disable-lzo</td>
         <td>disable LZO decoder code</td>
        </tr>
        <tr>
         <td>–disable-mdct</td>
         <td>disable MDCT code</td>
        </tr>
        <tr>
         <td>–disable-rdft</td>
         <td>disable RDFT code</td>
        </tr>
        <tr>
         <td>–disable-fft</td>
         <td>disable FFT code</td>
        </tr>
        <tr>
         <td>–disable-faan</td>
         <td>disable floating point AAN (I)DCT code</td>
        </tr>
        <tr>
         <td>–disable-pixelutils</td>
         <td>disable pixel utils in libavutil</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="个别组件选项individual-component-options"><a name="t7"></a><a name="t7"></a>个别组件选项（Individual component options）</h2> 
     <p>可以用于设定开启指定功能，例如禁止所有encoders，在这里可以开启特定的encoders（x264、aac等）</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-everything</td>
         <td>disable all components listed below</td>
        </tr>
        <tr>
         <td>–disable-encoder=NAME</td>
         <td>disable encoder NAME</td>
        </tr>
        <tr>
         <td>–enable-encoder=NAME</td>
         <td>enable encoder NAME</td>
        </tr>
        <tr>
         <td>–disable-encoders</td>
         <td>disable all encoders</td>
        </tr>
        <tr>
         <td>–disable-decoder=NAME</td>
         <td>disable decoder NAME</td>
        </tr>
        <tr>
         <td>–enable-decoder=NAME</td>
         <td>enable decoder NAME</td>
        </tr>
        <tr>
         <td>–disable-decoders</td>
         <td>disable all decoders</td>
        </tr>
        <tr>
         <td>–disable-hwaccel=NAME</td>
         <td>disable hwaccel NAME</td>
        </tr>
        <tr>
         <td>–enable-hwaccel=NAME</td>
         <td>enable hwaccel NAME</td>
        </tr>
        <tr>
         <td>–disable-hwaccels</td>
         <td>disable all hwaccels</td>
        </tr>
        <tr>
         <td>–disable-muxer=NAME</td>
         <td>disable muxer NAME</td>
        </tr>
        <tr>
         <td>–enable-muxer=NAME</td>
         <td>enable muxer NAME</td>
        </tr>
        <tr>
         <td>–disable-muxers</td>
         <td>disable all muxers</td>
        </tr>
        <tr>
         <td>–disable-demuxer=NAME</td>
         <td>disable demuxer NAME</td>
        </tr>
        <tr>
         <td>–enable-demuxer=NAME</td>
         <td>enable demuxer NAME</td>
        </tr>
        <tr>
         <td>–disable-demuxers</td>
         <td>disable all demuxers</td>
        </tr>
        <tr>
         <td>–enable-parser=NAME</td>
         <td>enable parser NAME</td>
        </tr>
        <tr>
         <td>–disable-parser=NAME</td>
         <td>disable parser NAME</td>
        </tr>
        <tr>
         <td>–disable-parsers</td>
         <td>disable all parsers</td>
        </tr>
        <tr>
         <td>–enable-bsf=NAME</td>
         <td>enable bitstream filter NAME</td>
        </tr>
        <tr>
         <td>–disable-bsf=NAME</td>
         <td>disable bitstream filter NAME</td>
        </tr>
        <tr>
         <td>–disable-bsfs</td>
         <td>disable all bitstream filters</td>
        </tr>
        <tr>
         <td>–enable-protocol=NAME</td>
         <td>enable protocol NAME</td>
        </tr>
        <tr>
         <td>–disable-protocol=NAME</td>
         <td>disable protocol NAME</td>
        </tr>
        <tr>
         <td>–disable-protocols</td>
         <td>disable all protocols</td>
        </tr>
        <tr>
         <td>–enable-indev=NAME</td>
         <td>enable input device NAME</td>
        </tr>
        <tr>
         <td>–disable-indev=NAME</td>
         <td>disable input device NAME</td>
        </tr>
        <tr>
         <td>–disable-indevs</td>
         <td>disable input devices</td>
        </tr>
        <tr>
         <td>–enable-outdev=NAME</td>
         <td>enable output device NAME</td>
        </tr>
        <tr>
         <td>–disable-outdev=NAME</td>
         <td>disable output device NAME</td>
        </tr>
        <tr>
         <td>–disable-outdevs</td>
         <td>disable output devices</td>
        </tr>
        <tr>
         <td>–disable-devices</td>
         <td>disable all devices</td>
        </tr>
        <tr>
         <td>–enable-filter=NAME</td>
         <td>enable filter NAME</td>
        </tr>
        <tr>
         <td>–disable-filter=NAME</td>
         <td>disable filter NAME</td>
        </tr>
        <tr>
         <td>–disable-filters</td>
         <td>disable all filters</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="扩展库支持external-library-support"><a name="t8"></a><a name="t8"></a>扩展库支持（External library support）</h2> 
     <p>ffmpeg提供的一些功能是由其他扩展库支持的，如果需要使用需要明确声明，确定编译的第三方库的目标架构<code>--arch</code>相同就好了，在编译ffmpeg的时候需加入第三方库的头文和库搜索路径（通过<code>extra-cflags</code>和<code>extra-ldflags</code>指定即可），剩下的事ffmpeg都给你做好了。</p> 
     <ul>
      <li>libx264例子 <br /> <a href="https://trac.ffmpeg.org/wiki/CompilationGuide/Quick/libx264" target="_blank" rel="noopener noreferrer">ffmpeg集成libx264官方文档</a></li>
     </ul> 
     <pre class="prettyprint" name="code"><code class="hljs smalltalk has-numbering" onclick="mdcp.copyCode(event)" style="position: unset;"><span class="hljs-char">$ </span>git clone <span class="hljs-method">http:</span>//source.ffmpeg.org/git/ffmpeg.git
<span class="hljs-char">$ </span>cd x264
<span class="hljs-char">$ </span>./configure --prefix=<span class="hljs-char">$F</span>FMPEG_PREFIX --enable-static --enable-shared
<span class="hljs-char">$ </span>make -j8 &amp;&amp; make install
       <div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}"></div></code>
      </pre> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–enable-avisynth</td>
         <td>enable reading of AviSynth script files [no]</td>
        </tr>
        <tr>
         <td>–disable-bzlib</td>
         <td>disable bzlib [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-chromaprint</td>
         <td>enable audio fingerprinting with chromaprint [no]</td>
        </tr>
        <tr>
         <td>–enable-frei0r</td>
         <td>enable frei0r video filtering [no]</td>
        </tr>
        <tr>
         <td>–enable-gcrypt</td>
         <td>enable gcrypt, needed for rtmp(t)e support if openssl, librtmp or gmp is not used [no]</td>
        </tr>
        <tr>
         <td>–enable-gmp</td>
         <td>enable gmp, needed for rtmp(t)e support if openssl or librtmp is not used [no]</td>
        </tr>
        <tr>
         <td>–enable-gnutls</td>
         <td>enable gnutls, needed for https support if openssl is not used [no]</td>
        </tr>
        <tr>
         <td>–disable-iconv</td>
         <td>disable iconv [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-jni</td>
         <td>enable JNI support [no]</td>
        </tr>
        <tr>
         <td>–enable-ladspa</td>
         <td>enable LADSPA audio filtering [no]</td>
        </tr>
        <tr>
         <td>–enable-libass</td>
         <td>enable libass subtitles rendering, needed for subtitles and ass filter [no]</td>
        </tr>
        <tr>
         <td>–enable-libbluray</td>
         <td>enable BluRay reading using libbluray [no]</td>
        </tr>
        <tr>
         <td>–enable-libbs2b</td>
         <td>enable bs2b DSP library [no]</td>
        </tr>
        <tr>
         <td>–enable-libcaca</td>
         <td>enable textual display using libcaca [no]</td>
        </tr>
        <tr>
         <td>–enable-libcelt</td>
         <td>enable CELT decoding via libcelt [no]</td>
        </tr>
        <tr>
         <td>–enable-libcdio</td>
         <td>enable audio CD grabbing with libcdio [no]</td>
        </tr>
        <tr>
         <td>–enable-libdc1394</td>
         <td>enable IIDC-1394 grabbing using libdc1394 and libraw1394 [no]</td>
        </tr>
        <tr>
         <td>–enable-libebur128</td>
         <td>enable libebur128 for EBU R128 measurement, needed for loudnorm filter [no]</td>
        </tr>
        <tr>
         <td>–enable-libfdk-aac</td>
         <td>enable AAC de/encoding via libfdk-aac [no]</td>
        </tr>
        <tr>
         <td>–enable-libflite</td>
         <td>enable flite (voice synthesis) support via libflite [no]</td>
        </tr>
        <tr>
         <td>–enable-libfontconfig</td>
         <td>enable libfontconfig, useful for drawtext filter [no]</td>
        </tr>
        <tr>
         <td>–enable-libfreetype</td>
         <td>enable libfreetype, needed for drawtext filter [no]</td>
        </tr>
        <tr>
         <td>–enable-libfribidi</td>
         <td>enable libfribidi, improves drawtext filter [no]</td>
        </tr>
        <tr>
         <td>–enable-libgme</td>
         <td>enable Game Music Emu via libgme [no]</td>
        </tr>
        <tr>
         <td>–enable-libgsm</td>
         <td>enable GSM de/encoding via libgsm [no]</td>
        </tr>
        <tr>
         <td>–enable-libiec61883</td>
         <td>enable iec61883 via libiec61883 [no]</td>
        </tr>
        <tr>
         <td>–enable-libilbc</td>
         <td>enable iLBC de/encoding via libilbc [no]</td>
        </tr>
        <tr>
         <td>–enable-libkvazaar</td>
         <td>enable HEVC encoding via libkvazaar [no]</td>
        </tr>
        <tr>
         <td>–enable-libmodplug</td>
         <td>enable ModPlug via libmodplug [no]</td>
        </tr>
        <tr>
         <td>–enable-libmp3lame</td>
         <td>enable MP3 encoding via libmp3lame [no]</td>
        </tr>
        <tr>
         <td>–enable-libnut</td>
         <td>enable NUT (de)muxing via libnut, native (de)muxer exists [no]</td>
        </tr>
        <tr>
         <td>–enable-libopencore-amrnb</td>
         <td>enable AMR-NB de/encoding via libopencore-amrnb [no]</td>
        </tr>
        <tr>
         <td>–enable-libopencore-amrwb</td>
         <td>enable AMR-WB decoding via libopencore-amrwb [no]</td>
        </tr>
        <tr>
         <td>–enable-libopencv</td>
         <td>enable video filtering via libopencv [no]</td>
        </tr>
        <tr>
         <td>–enable-libopenh264</td>
         <td>enable H.264 encoding via OpenH264 [no]</td>
        </tr>
        <tr>
         <td>–enable-libopenjpeg</td>
         <td>enable JPEG 2000 de/encoding via OpenJPEG [no]</td>
        </tr>
        <tr>
         <td>–enable-libopenmpt</td>
         <td>enable decoding tracked files via libopenmpt [no]</td>
        </tr>
        <tr>
         <td>–enable-libopus</td>
         <td>enable Opus de/encoding via libopus [no]</td>
        </tr>
        <tr>
         <td>–enable-libpulse</td>
         <td>enable Pulseaudio input via libpulse [no]</td>
        </tr>
        <tr>
         <td>–enable-librubberband</td>
         <td>enable rubberband needed for rubberband filter [no]</td>
        </tr>
        <tr>
         <td>–enable-librtmp</td>
         <td>enable RTMP[E] support via librtmp [no]</td>
        </tr>
        <tr>
         <td>–enable-libschroedinger</td>
         <td>enable Dirac de/encoding via libschroedinger [no]</td>
        </tr>
        <tr>
         <td>–enable-libshine</td>
         <td>enable fixed-point MP3 encoding via libshine [no]</td>
        </tr>
        <tr>
         <td>–enable-libsmbclient</td>
         <td>enable Samba protocol via libsmbclient [no]</td>
        </tr>
        <tr>
         <td>–enable-libsnappy</td>
         <td>enable Snappy compression, needed for hap encoding [no]</td>
        </tr>
        <tr>
         <td>–enable-libsoxr</td>
         <td>enable Include libsoxr resampling [no]</td>
        </tr>
        <tr>
         <td>–enable-libspeex</td>
         <td>enable Speex de/encoding via libspeex [no]</td>
        </tr>
        <tr>
         <td>–enable-libssh</td>
         <td>enable SFTP protocol via libssh [no]</td>
        </tr>
        <tr>
         <td>–enable-libtesseract</td>
         <td>enable Tesseract, needed for ocr filter [no]</td>
        </tr>
        <tr>
         <td>–enable-libtheora</td>
         <td>enable Theora encoding via libtheora [no]</td>
        </tr>
        <tr>
         <td>–enable-libtwolame</td>
         <td>enable MP2 encoding via libtwolame [no]</td>
        </tr>
        <tr>
         <td>–enable-libv4l2</td>
         <td>enable libv4l2/v4l-utils [no]</td>
        </tr>
        <tr>
         <td>–enable-libvidstab</td>
         <td>enable video stabilization using vid.stab [no]</td>
        </tr>
        <tr>
         <td>–enable-libvo-amrwbenc</td>
         <td>enable AMR-WB encoding via libvo-amrwbenc [no]</td>
        </tr>
        <tr>
         <td>–enable-libvorbis</td>
         <td>enable Vorbis en/decoding via libvorbis, native implementation exists [no]</td>
        </tr>
        <tr>
         <td>–enable-libvpx</td>
         <td>enable VP8 and VP9 de/encoding via libvpx [no]</td>
        </tr>
        <tr>
         <td>–enable-libwavpack</td>
         <td>enable wavpack encoding via libwavpack [no]</td>
        </tr>
        <tr>
         <td>–enable-libwebp</td>
         <td>enable WebP encoding via libwebp [no]</td>
        </tr>
        <tr>
         <td>–enable-libx264</td>
         <td>enable H.264 encoding via x264 [no]</td>
        </tr>
        <tr>
         <td>–enable-libx265</td>
         <td>enable HEVC encoding via x265 [no]</td>
        </tr>
        <tr>
         <td>–enable-libxavs</td>
         <td>enable AVS encoding via xavs [no]</td>
        </tr>
        <tr>
         <td>–enable-libxcb</td>
         <td>enable X11 grabbing using XCB [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-libxcb-shm</td>
         <td>enable X11 grabbing shm communication [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-libxcb-xfixes</td>
         <td>enable X11 grabbing mouse rendering [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-libxcb-shape</td>
         <td>enable X11 grabbing shape rendering [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-libxvid</td>
         <td>enable Xvid encoding via xvidcore, native MPEG-4/Xvid encoder exists [no]</td>
        </tr>
        <tr>
         <td>–enable-libzimg</td>
         <td>enable z.lib, needed for zscale filter [no]</td>
        </tr>
        <tr>
         <td>–enable-libzmq</td>
         <td>enable message passing via libzmq [no]</td>
        </tr>
        <tr>
         <td>–enable-libzvbi</td>
         <td>enable teletext support via libzvbi [no]</td>
        </tr>
        <tr>
         <td>–disable-lzma</td>
         <td>disable lzma [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-decklink</td>
         <td>enable Blackmagic DeckLink I/O support [no]</td>
        </tr>
        <tr>
         <td>–enable-mediacodec</td>
         <td>enable Android MediaCodec support [no]</td>
        </tr>
        <tr>
         <td>–enable-netcdf</td>
         <td>enable NetCDF, needed for sofalizer filter [no]</td>
        </tr>
        <tr>
         <td>–enable-openal</td>
         <td>enable OpenAL 1.1 capture support [no]</td>
        </tr>
        <tr>
         <td>–enable-opencl</td>
         <td>enable OpenCL code</td>
        </tr>
        <tr>
         <td>–enable-opengl</td>
         <td>enable OpenGL rendering [no]</td>
        </tr>
        <tr>
         <td>–enable-openssl</td>
         <td>enable openssl, needed for https support if gnutls is not used [no]</td>
        </tr>
        <tr>
         <td>–disable-schannel</td>
         <td>disable SChannel SSP, needed for TLS support on Windows if openssl and gnutls are not used [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-sdl2</td>
         <td>disable sdl2 [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-securetransport</td>
         <td>disable Secure Transport, needed for TLS support on OSX if openssl and gnutls are not used [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-x11grab</td>
         <td>enable X11 grabbing (legacy) [no]</td>
        </tr>
        <tr>
         <td>–disable-xlib</td>
         <td>disable xlib [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-zlib</td>
         <td>disable zlib [autodetect]</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h3 id="硬件加速功能hardware-acceleration-features"><a name="t9"></a><a name="t9"></a>硬件加速功能（hardware acceleration features）</h3> 
     <p>ffmpeg默认实现了移动端（Android和IOS）的硬编解码，可以选择disable的都是默认开启的，可以关闭，可以选择enable的都是需要自己解决依赖的。</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-audiotoolbox</td>
         <td>disable Apple AudioToolbox code [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-cuda</td>
         <td>enable dynamically linked Nvidia CUDA code [no]</td>
        </tr>
        <tr>
         <td>–enable-cuvid</td>
         <td>enable Nvidia CUVID support [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-d3d11va</td>
         <td>disable Microsoft Direct3D 11 video acceleration code [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-dxva2</td>
         <td>disable Microsoft DirectX 9 video acceleration code [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-libmfx</td>
         <td>enable Intel MediaSDK (AKA Quick Sync Video) code via libmfx [no]</td>
        </tr>
        <tr>
         <td>–enable-libnpp</td>
         <td>enable Nvidia Performance Primitives-based code [no]</td>
        </tr>
        <tr>
         <td>–enable-mmal</td>
         <td>enable Broadcom Multi-Media Abstraction Layer (Raspberry Pi) via MMAL [no]</td>
        </tr>
        <tr>
         <td>–disable-nvenc</td>
         <td>disable Nvidia video encoding code [autodetect]</td>
        </tr>
        <tr>
         <td>–enable-omx</td>
         <td>enable OpenMAX IL code [no]</td>
        </tr>
        <tr>
         <td>–enable-omx-rpi</td>
         <td>enable OpenMAX IL code for Raspberry Pi [no]</td>
        </tr>
        <tr>
         <td>–disable-vaapi</td>
         <td>disable Video Acceleration API (mainly Unix/Intel) code [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-vda</td>
         <td>disable Apple Video Decode Acceleration code [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-vdpau</td>
         <td>disable Nvidia Video Decode and Presentation API for Unix code [autodetect]</td>
        </tr>
        <tr>
         <td>–disable-videotoolbox</td>
         <td>disable VideoToolbox code [autodetect]</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="工具链选项toolchain-options"><a name="t10"></a><a name="t10"></a>工具链选项（Toolchain options）</h2> 
     <p>ffmpeg代码本身是支持跨平台的，要编译不同的平台需要配置不同平台的交叉编译工具链。ffmpeg都是c代码，所以不需要配置c++的sysroot。常用的就几个<code>arch</code>,<code>cpu</code>,<code>cross-prefix</code>,<code>enable-cross-compile</code>,<code>sysroot</code>,<code>target-os</code>,<code>extra-cflags</code>,<code>extra-ldflags</code>,<code>enable-pic</code>。现在Android和IOS几乎没有armv5的设备了，所以如果编译这两个平台配置armv7和armv8就好了。</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–arch=ARCH</td>
         <td>选择目标架构[armv7a/aarch64/x86/x86_64等]</td>
        </tr>
        <tr>
         <td>–cpu=CPU</td>
         <td>选择目标cpu[armv7-a/armv8-a/x86/x86_64]</td>
        </tr>
        <tr>
         <td>–cross-prefix=PREFIX</td>
         <td>设定交叉编译工具链的前缀,不算gcc/nm/as命令，例如android 32位的交叉编译链<code>$ndk_dir/toolchains/arm-linux-androideabi-$toolchain_version/prebuilt/linux-$host_arch/bin/arm-linux-androideabi-</code></td>
        </tr>
        <tr>
         <td>–progs-suffix=SUFFIX</td>
         <td>program name suffix []</td>
        </tr>
        <tr>
         <td>–enable-cross-compile</td>
         <td>如果目标平台和编译平台不同则需要使能它</td>
        </tr>
        <tr>
         <td>–sysroot=PATH</td>
         <td>交叉工具链的头文件和库位，例如Android 32位位置<code>$ndk_dir/platforms/android-14/arch-arm</code></td>
        </tr>
        <tr>
         <td>–sysinclude=PATH</td>
         <td>location of cross-build system headers</td>
        </tr>
        <tr>
         <td>–target-os=OS</td>
         <td>设置目标系统</td>
        </tr>
        <tr>
         <td>–target-exec=CMD</td>
         <td>command to run executables on target</td>
        </tr>
        <tr>
         <td>–target-path=DIR</td>
         <td>path to view of build directory on target</td>
        </tr>
        <tr>
         <td>–target-samples=DIR</td>
         <td>path to samples directory on target</td>
        </tr>
        <tr>
         <td>–tempprefix=PATH</td>
         <td>force fixed dir/prefix instead of mktemp for checks</td>
        </tr>
        <tr>
         <td>–toolchain=NAME</td>
         <td>set tool defaults according to NAME</td>
        </tr>
        <tr>
         <td>–nm=NM</td>
         <td>use nm tool NM [nm -g]</td>
        </tr>
        <tr>
         <td>–ar=AR</td>
         <td>use archive tool AR [ar]</td>
        </tr>
        <tr>
         <td>–as=AS</td>
         <td>use assembler AS []</td>
        </tr>
        <tr>
         <td>–ln_s=LN_S</td>
         <td>use symbolic link tool LN_S [ln -s -f]</td>
        </tr>
        <tr>
         <td>–strip=STRIP</td>
         <td>use strip tool STRIP [strip]</td>
        </tr>
        <tr>
         <td>–windres=WINDRES</td>
         <td>use windows resource compiler WINDRES [windres]</td>
        </tr>
        <tr>
         <td>–yasmexe=EXE</td>
         <td>use yasm-compatible assembler EXE [yasm]</td>
        </tr>
        <tr>
         <td>–cc=CC</td>
         <td>use C compiler CC [gcc]</td>
        </tr>
        <tr>
         <td>–cxx=CXX</td>
         <td>use C compiler CXX [g++]</td>
        </tr>
        <tr>
         <td>–objcc=OCC</td>
         <td>use ObjC compiler OCC [gcc]</td>
        </tr>
        <tr>
         <td>–dep-cc=DEPCC</td>
         <td>use dependency generator DEPCC [gcc]</td>
        </tr>
        <tr>
         <td>–ld=LD</td>
         <td>use linker LD []</td>
        </tr>
        <tr>
         <td>–pkg-config=PKGCONFIG</td>
         <td>use pkg-config tool PKGCONFIG [pkg-config]</td>
        </tr>
        <tr>
         <td>–pkg-config-flags=FLAGS</td>
         <td>pass additional flags to pkgconf []</td>
        </tr>
        <tr>
         <td>–ranlib=RANLIB</td>
         <td>use ranlib RANLIB [ranlib]</td>
        </tr>
        <tr>
         <td>–doxygen=DOXYGEN</td>
         <td>use DOXYGEN to generate API doc [doxygen]</td>
        </tr>
        <tr>
         <td>–host-cc=HOSTCC</td>
         <td>use host C compiler HOSTCC</td>
        </tr>
        <tr>
         <td>–host-cflags=HCFLAGS</td>
         <td>use HCFLAGS when compiling for host</td>
        </tr>
        <tr>
         <td>–host-cppflags=HCPPFLAGS</td>
         <td>use HCPPFLAGS when compiling for host</td>
        </tr>
        <tr>
         <td>–host-ld=HOSTLD</td>
         <td>use host linker HOSTLD</td>
        </tr>
        <tr>
         <td>–host-ldflags=HLDFLAGS</td>
         <td>use HLDFLAGS when linking for host</td>
        </tr>
        <tr>
         <td>–host-libs=HLIBS</td>
         <td>use libs HLIBS when linking for host</td>
        </tr>
        <tr>
         <td>–host-os=OS</td>
         <td>compiler host OS []</td>
        </tr>
        <tr>
         <td>–extra-cflags=ECFLAGS</td>
         <td>设置cflags，如果是Android平台可以根据ndk内的设定,<a href="https://chromium.googlesource.com/android_tools/+/master-backup/ndk/toolchains/arm-linux-androideabi-4.6/setup.mk" target="_blank" rel="noopener noreferrer">arm-linux-androideabi-4.6/setup.mk</a>，建议参考你当前的setup来配置</td>
        </tr>
        <tr>
         <td>–extra-cxxflags=ECFLAGS</td>
         <td>add ECFLAGS to CXXFLAGS []</td>
        </tr>
        <tr>
         <td>–extra-objcflags=FLAGS</td>
         <td>add FLAGS to OBJCFLAGS []</td>
        </tr>
        <tr>
         <td>–extra-ldflags=ELDFLAGS</td>
         <td>参考cflags</td>
        </tr>
        <tr>
         <td>–extra-ldexeflags=ELDFLAGS</td>
         <td>add ELDFLAGS to LDEXEFLAGS []</td>
        </tr>
        <tr>
         <td>–extra-ldlibflags=ELDFLAGS</td>
         <td>add ELDFLAGS to LDLIBFLAGS []</td>
        </tr>
        <tr>
         <td>–extra-libs=ELIBS</td>
         <td>add ELIBS []</td>
        </tr>
        <tr>
         <td>–extra-version=STRING</td>
         <td>version string suffix []</td>
        </tr>
        <tr>
         <td>–optflags=OPTFLAGS</td>
         <td>override optimization-related compiler flags</td>
        </tr>
        <tr>
         <td>–build-suffix=SUFFIX</td>
         <td>library name suffix []</td>
        </tr>
        <tr>
         <td>–enable-pic</td>
         <td>build position-independent code</td>
        </tr>
        <tr>
         <td>–enable-thumb</td>
         <td>compile for Thumb instruction set</td>
        </tr>
        <tr>
         <td>–enable-lto</td>
         <td>use link-time optimization</td>
        </tr>
        <tr>
         <td>–env=”ENV=override”</td>
         <td>override the environment variables</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="高级选项advanced-options"><a name="t11"></a><a name="t11"></a>高级选项（Advanced options）</h2> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–malloc-prefix=PREFIX</td>
         <td>prefix malloc and related names with PREFIX</td>
        </tr>
        <tr>
         <td>–custom-allocator=NAME</td>
         <td>use a supported custom allocator</td>
        </tr>
        <tr>
         <td>–disable-symver</td>
         <td>disable symbol versioning</td>
        </tr>
        <tr>
         <td>–enable-hardcoded-tables</td>
         <td>use hardcoded tables instead of runtime generation</td>
        </tr>
        <tr>
         <td>–disable-safe-bitstream-reader</td>
         <td>disable buffer boundary checking in bitreaders (faster, but may crash)</td>
        </tr>
        <tr>
         <td>–enable-memalign-hack</td>
         <td>emulate memalign, interferes with memory debuggers</td>
        </tr>
        <tr>
         <td>–sws-max-filter-size=N</td>
         <td>the max filter size swscale uses [256]</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="优化选项optimization-options"><a name="t12"></a><a name="t12"></a>优化选项（Optimization options）</h2> 
     <p>默认开启各个平台的汇编优化，有些嵌入式平台可能并不能完整的支持架构的所有汇编指令，所以需要关闭。（自己理解的，没有实战）</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-asm</td>
         <td>disable all assembly optimizations</td>
        </tr>
        <tr>
         <td>–disable-altivec</td>
         <td>disable AltiVec optimizations</td>
        </tr>
        <tr>
         <td>–disable-vsx</td>
         <td>disable VSX optimizations</td>
        </tr>
        <tr>
         <td>–disable-power8</td>
         <td>disable POWER8 optimizations</td>
        </tr>
        <tr>
         <td>–disable-amd3dnow</td>
         <td>disable 3DNow! optimizations</td>
        </tr>
        <tr>
         <td>–disable-amd3dnowext</td>
         <td>disable 3DNow! extended optimizations</td>
        </tr>
        <tr>
         <td>–disable-mmx</td>
         <td>disable MMX optimizations</td>
        </tr>
        <tr>
         <td>–disable-mmxext</td>
         <td>disable MMXEXT optimizations</td>
        </tr>
        <tr>
         <td>–disable-sse</td>
         <td>disable SSE optimizations</td>
        </tr>
        <tr>
         <td>–disable-sse2</td>
         <td>disable SSE2 optimizations</td>
        </tr>
        <tr>
         <td>–disable-sse3</td>
         <td>disable SSE3 optimizations</td>
        </tr>
        <tr>
         <td>–disable-ssse3</td>
         <td>disable SSSE3 optimizations</td>
        </tr>
        <tr>
         <td>–disable-sse4</td>
         <td>disable SSE4 optimizations</td>
        </tr>
        <tr>
         <td>–disable-sse42</td>
         <td>disable SSE4.2 optimizations</td>
        </tr>
        <tr>
         <td>–disable-avx</td>
         <td>disable AVX optimizations</td>
        </tr>
        <tr>
         <td>–disable-xop</td>
         <td>disable XOP optimizations</td>
        </tr>
        <tr>
         <td>–disable-fma3</td>
         <td>disable FMA3 optimizations</td>
        </tr>
        <tr>
         <td>–disable-fma4</td>
         <td>disable FMA4 optimizations</td>
        </tr>
        <tr>
         <td>–disable-avx2</td>
         <td>disable AVX2 optimizations</td>
        </tr>
        <tr>
         <td>–disable-aesni</td>
         <td>disable AESNI optimizations</td>
        </tr>
        <tr>
         <td>–disable-armv5te</td>
         <td>disable armv5te optimizations</td>
        </tr>
        <tr>
         <td>–disable-armv6</td>
         <td>disable armv6 optimizations</td>
        </tr>
        <tr>
         <td>–disable-armv6t2</td>
         <td>disable armv6t2 optimizations</td>
        </tr>
        <tr>
         <td>–disable-vfp</td>
         <td>disable VFP optimizations</td>
        </tr>
        <tr>
         <td>–disable-neon</td>
         <td>disable NEON optimizations</td>
        </tr>
        <tr>
         <td>–disable-inline-asm</td>
         <td>disable use of inline assembly</td>
        </tr>
        <tr>
         <td>–disable-yasm</td>
         <td>disable use of nasm/yasm assembly</td>
        </tr>
        <tr>
         <td>–disable-mipsdsp</td>
         <td>disable MIPS DSP ASE R1 optimizations</td>
        </tr>
        <tr>
         <td>–disable-mipsdspr2</td>
         <td>disable MIPS DSP ASE R2 optimizations</td>
        </tr>
        <tr>
         <td>–disable-msa</td>
         <td>disable MSA optimizations</td>
        </tr>
        <tr>
         <td>–disable-mipsfpu</td>
         <td>disable floating point MIPS optimizations</td>
        </tr>
        <tr>
         <td>–disable-mmi</td>
         <td>disable Loongson SIMD optimizations</td>
        </tr>
        <tr>
         <td>–disable-fast-unaligned</td>
         <td>consider unaligned accesses slow</td>
        </tr>
       </tbody>
      </table>
     </div> 
     <h2 id="开发者选项developer-options"><a name="t13"></a><a name="t13"></a>开发者选项（Developer options）</h2> 
     <p>调试用的一些开关</p> 
     <div class="table-box">
      <table>
       <thead>
        <tr>
         <th>选项</th>
         <th>说明</th>
        </tr>
       </thead>
       <tbody>
        <tr>
         <td>–disable-debug</td>
         <td>disable debugging symbols</td>
        </tr>
        <tr>
         <td>–enable-debug=LEVEL</td>
         <td>set the debug level []</td>
        </tr>
        <tr>
         <td>–disable-optimizations</td>
         <td>disable compiler optimizations</td>
        </tr>
        <tr>
         <td>–enable-extra-warnings</td>
         <td>enable more compiler warnings</td>
        </tr>
        <tr>
         <td>–disable-stripping</td>
         <td>disable stripping of executables and shared libraries</td>
        </tr>
        <tr>
         <td>–assert-level=level</td>
         <td>0(default), 1 or 2, amount of assertion testing, 2 causes a slowdown at runtime.</td>
        </tr>
        <tr>
         <td>–enable-memory-poisoning</td>
         <td>fill heap uninitialized allocated space with arbitrary data</td>
        </tr>
        <tr>
         <td>–valgrind=VALGRIND</td>
         <td>run “make fate” tests through valgrind to detect memory leaks and errors, using the specified valgrind binary. Cannot be combined with –target-exec</td>
        </tr>
        <tr>
         <td>–enable-ftrapv</td>
         <td>Trap arithmetic overflows</td>
        </tr>
        <tr>
         <td>–samples=PATH</td>
         <td>location of test samples for FATE, if not set use $FATE_SAMPLES at make invocation time.</td>
        </tr>
        <tr>
         <td>–enable-neon-clobber-test</td>
         <td>check NEON registers for clobbering (should be used only for debugging purposes)</td>
        </tr>
        <tr>
         <td>–enable-xmm-clobber-test</td>
         <td>check XMM registers for clobbering (Win64-only; should be used only for debugging purposes)</td>
        </tr>
        <tr>
         <td>–enable-random</td>
         <td>randomly enable/disable components</td>
        </tr>
        <tr>
         <td>–disable-random</td>
         <td></td>
        </tr>
        <tr>
         <td>–enable-random=LIST</td>
         <td>randomly enable/disable specific components or</td>
        </tr>
        <tr>
         <td>–disable-random=LIST</td>
         <td>component groups. LIST is a comma-separated list of NAME[:PROB] entries where NAME is a component (group) and PROB the probability associated with</td>
        </tr>
        <tr>
         <td>–random-seed=VALUE</td>
         <td>seed value for –enable/disable-random</td>
        </tr>
        <tr>
         <td>–disable-valgrind-backtrace</td>
         <td>do not print a backtrace under Valgrind (only applies to –disable-optimizations builds)</td>
        </tr>
       </tbody>
      </table>
     </div> 
    </div>
    <div>
     <div></div>
    </div> 
    <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/markdown_views-d7a94ec6ab.css" rel="stylesheet" /> 
    <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/style-ba784fbaf8.css" rel="stylesheet" /> 
   </div> 
  </article>
 </body>
</html>


> 转自：[https://blog.csdn.net/momo0853/article/details/78043903](https://blog.csdn.net/momo0853/article/details/78043903)