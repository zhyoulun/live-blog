# JNI编译

## 准备

代码根路径：`/Users/zhangyoulun/codes/java`

## 编写java脚本

```
cd /Users/zhangyoulun/codes/java
mkdir -p src/com/zhyoulun/
cd src
```

编写文件`com/zhyoulun/HelloJni.java`

```
package com.zhyoulun;

public class HelloJni {
    static {
        // System.setProperty("java.library.path", "/Users/zhangyoulun/codes/java/src");
        // System.out.println(System.getProperty("java.library.path"));
        System.loadLibrary("hello"); // Load native library at runtime
        // hello.dll (Windows) or libhello.so (Unixes) or libhello.bylib （Mac）
    }

    // Declare a native method sayHello() that receives nothing and returns void
    private native void sayHello();

    // Test Driver
    public static void main(String[] args) {
        new HelloJni().sayHello();  // invoke the native method
    }
}
```

编译java脚本，`javac com/zhyoulun/HelloJni.java`，生成`com/zhyoulun/HelloJni.class`

使用`com/zhyoulun/HelloJni.class`生成头文件`com_zhyoulun_HelloJni.h`，命令是`javah com.zhyoulun.HelloJni`

## 编写CPP脚本

编写文件`com_zhyoulun_HelloJni.cpp`

```
#include "com_zhyoulun_HelloJni.h"
#include <iostream>

JNIEXPORT void JNICALL Java_com_zhyoulun_HelloJni_sayHello(JNIEnv *env, jobject obj)
{
    std::cout << "Hello world!" << std::endl;
}
```

编译生成`libhello.dylib`

```
g++ -fPIC \
    -shared \
    -I /System/Library/Frameworks/JavaVM.framework/Versions/Current/Headers \
    -o libhello.dylib com_zhyoulun_HelloJni.cpp
```

其中

* `/System/Library/Frameworks/JavaVM.framework/Versions/Current/Headers`含有`jni.h`文件
* `-I`后面跟的是jni所需要的头文件路径
* `-shared `编译器生成共享链接库
* `-fPIC` 选项使编译器在编译阶段生成与位置无关的代码，以使共享库能够在内存中被正确加载，`PIC`即`Position, Independent Code`。使用`-shared`选项时必须有该选项，否则编译期会出错
* 如果是C代码，使用gcc编译时，需要通过`-Wl,--add-stdcall-alias`向链接器传递链接选项，以避免出现`UnsatisfiedLinkError`错误

## 运行java程序

`java com.zhyoulun.HelloJni`

```
➜  src ls
com com_zhyoulun_HelloJni.cpp com_zhyoulun_HelloJni.h libhello.dylib
➜  src java com.zhyoulun.HelloJni
Hello world!
```

## 参考

* \[Java通过JNI调用C/C++动态链接库之HelloWorld\]\(http://notes.maxwi.com/2017/04/19/java-jni-cpp-hello/\)
* 


