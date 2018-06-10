# Lame3.99.5编译

下载地址：[http://lame.sourceforge.net/](http://lame.sourceforge.net/)

编译

```
./configure --prefix "/Users/zhangyoulun/codes/c/lame-3.99.5/output/build" \
    --disable-shared \
    --disable-frontend
make
make install
```

生成了`bin/` `lib/` `include/` `share/`四个文件夹

其中：

* `--disable-shared`: 通常是GNU标准中关闭动态链接库的选项，一般是在编译出命令行工具的时候，期望命令行工具可以单独使用，而不需要动态链接库的设置
* `--disable-frontend`: 不编译出LAME的可执行文件

测试lame：

```
g++ -o main \
    -I /Users/zhangyoulun/codes/c/lame-3.99.5/output/build/include/lame \
    -L /Users/zhangyoulun/codes/c/lame-3.99.5/output/build/lib \
    -lmp3lame \
    main.cpp mp3_encoder.cpp
```

如果使用CLION编译，CMakeLists.txt文件的内容是：

```
cmake_minimum_required(VERSION 3.9)
project(lame_study)

set(CMAKE_CXX_STANDARD 11)

include_directories(/Users/zhangyoulun/codes/c/lame-3.99.5/output/build/include/lame)
link_directories(/Users/zhangyoulun/codes/c/lame-3.99.5/output/build/lib)
link_libraries(/Users/zhangyoulun/codes/c/lame-3.99.5/output/build/lib/libmp3lame.a)

add_executable(lame_study main.cpp mp3_encoder.cpp mp3_encoder.h)
```

其中：

`main.cpp`

```
#include "mp3_encoder.h"
#include <iostream>

int main() {
    Mp3Encoder encoder = Mp3Encoder();
    int ret;
    ret = encoder.Init("/Users/zhangyoulun/Downloads/audio.pcm", "audio.mp3", 48000, 2, 1536000);
    std::cout << "ret: " << ret << std::endl;
    encoder.Encode();
    encoder.Destroy();

    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

`mp3_encoder.cpp`

```
//
// Created by zhangyoulun on 2018/6/10.
//

#include "mp3_encoder.h"

Mp3Encoder::Mp3Encoder() {}

Mp3Encoder::~Mp3Encoder() {}

int Mp3Encoder::Init(const char *pcmFilePath, const char *mp3FilePath, int sampleRate, int channels, int bitRate) {
    int ret = -1;
    pcmFile = fopen(pcmFilePath, "rb");
    if (pcmFile) {
        mp3File = fopen(mp3FilePath, "wb");
        if (mp3File) {
            lameClient = lame_init();
            lame_set_in_samplerate(lameClient, sampleRate);
            lame_set_out_samplerate(lameClient, sampleRate);
            lame_set_num_channels(lameClient, channels);
            lame_set_brate(lameClient, bitRate / 1000);
            lame_init_params(lameClient);
            ret = 0;
        }
    }
    return ret;
}

void Mp3Encoder::Encode() {
    int bufferSize = 1024 * 256;
    short *buffer = new short[bufferSize / 2];
    short *leftBuffer = new short[bufferSize / 4];
    short *rightBuffer = new short[bufferSize / 4];
    unsigned char *mp3_buffer = new unsigned char[bufferSize];
    size_t readBufferSize = 0;
    while ((readBufferSize = fread(buffer, 2, bufferSize / 2, pcmFile)) > 0) {
        for (int i = 0; i < readBufferSize; i++) {
            if (i % 2 == 0) {
                leftBuffer[i / 2] = buffer[i];
            } else {
                rightBuffer[i / 2] = buffer[i];
            }
        }
        size_t wroteSize = lame_encode_buffer(lameClient, (short int *) leftBuffer, (short int *) rightBuffer,
                                              (int) (readBufferSize / 2), mp3_buffer, bufferSize);
        fwrite(mp3_buffer, 1, wroteSize, mp3File);
    }
    delete[] buffer;
    delete[] leftBuffer;
    delete[] rightBuffer;
    delete[] mp3_buffer;
}

void Mp3Encoder::Destroy() {
    if (pcmFile) {
        fclose(pcmFile);
    }
    if (mp3File) {
        fclose(mp3File);
        lame_close(lameClient);
    }
}
```

`mp3_encoder.h`

```
//
// Created by zhangyoulun on 2018/6/10.
//

#ifndef LAME_STUDY_MP3_ENCODER_H
#define LAME_STUDY_MP3_ENCODER_H

#include "lame.h"

class Mp3Encoder {
private:
    FILE *pcmFile;
    FILE *mp3File;
    lame_t lameClient;
public:
    Mp3Encoder();
    ~Mp3Encoder();
    int Init(const char *pcmFilePath, const char *mp3FilePath, int sampleRate, int channels, int bitRate);
    void Encode();
    void Destroy();
};


#endif //LAME_STUDY_MP3_ENCODER_H
```



