#ADB的使用

##ADB的命令使用大全

[awesome-adb](https://github.com/mzlogin/awesome-adb)

##其他简单实用

###打开应用，在命令行输入该命令来查看当前在前台的activity：

```c
qincji:Downloads mac$ adb shell dumpsys activity activities | grep mResumedActivity
    mResumedActivity: ActivityRecord{e3c2d05 u0 com.ss.android.ugc.aweme.lite/com.ss.android.ugc.aweme.splash.SplashActivity t122}
```

###[编写shell脚本](./../FFmpeg/02_shell.md)，然后执行：

```c
#!/bin/bash
adb shell am start -n  com.ss.android.ugc.aweme.lite/com.ss.android.ugc.aweme.splash.SplashActivity
sleep 10s
a=1
while (($a < 500)); do
  adb shell input swipe 300 1000 300 500
  echo "while语句，滑动屏幕：$a"
  let "a++"
  sleep 10s
done
```

