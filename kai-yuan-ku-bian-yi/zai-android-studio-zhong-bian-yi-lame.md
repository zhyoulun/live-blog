# 在Android Studio中编译lame

新建项目，设置ndk路径

![](/assets/201806-001.png)

新建jni目录

![](/assets/201806-002.png)

新建类Mp3Encoder.java

```
package com.zhyoulun.mp3encoder;

/**
 * Created by zhangyoulun on 2018/6/10.
 */

public class Mp3Encoder {
    public native int init(String pcmPath, int audioChannels, int bitRate, int sampleRate, String mp3Path);
    public native void encode();
    public native void destroy();
}
```

编译

```
cd /app/src/main/java
javac com/zhyoulun/mp3encoder/Mp3Encoder.java
javah com.zhyoulun.mp3encoder.Mp3Encoder
```

这时会生成文件com\_zhyoulun\_mp3encoder\_Mp3Encoder.h

移动到src/main/jni文件夹中

编写com\_zhyoulun\_mp3encoder\_Mp3Encoder.cpp

```

```





