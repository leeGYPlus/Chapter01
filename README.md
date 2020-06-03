
[原 Sample 的 README.md](https://github.com/leeGYPlus/Chapter01/blob/master/READMELog.md)


## 转换文件

> ./minidump_satckwalk **.dmp > crashLog.txt

在将 *.dmp v转换为 txt 文件时，报出以下错误：



```
dyld: Symbol not found: __ZTTNSt7__cxx1118basic_stringstreamIcSt11char_traitsIcESaIcEEE
  Referenced from: xxx/Chapter01/tools/mac/./minidump_stackwalk
  Expected in: /usr/lib/libstdc++.6.dylib
 in xxx/Chapter01/tools/mac/./minidump_stackwalk
Abort trap: 6
```

需要重新编译 BreadPad，使用其中的 minidump_satckwalk 来解析文件。

```
./configure
make
make install
```

此时的 minidump_satckwalk 全局可用，可以直接使用以下命令进行解析文件：

```
minidump_satckwalk **.dmp > crashLog.txt
```



## 获得信息

在 crashLog.txt 中可以得出 crash 的原因、crash 出现的线程以及发生 crash 的位置和寄存器信息：

```
Crash reason:  SIGSEGV /SEGV_MAPERR // crash 的原因
Crash address: 0x0
Process uptime: not available

Thread 0 (crashed)// crash 出现的线程
 0  libcrash-lib.so + 0x600 //发生 crash 的位置和寄存器信息
```

`SIGSEGV` 为 Linux 系统中的一个信号量，常见的信号量类型：

![](http://mmbiz.qpic.cn/mmbiz_png/tnZGrhTk4dcsX0ukLriaOfqkZbdJanumaJ7ehcKw5EeGytsSAy4tm3aqicTxvWDdmACkdKWw36Vp5RQgCvVYBwrg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

具体可以参见: [Android 平台 Native 代码的崩溃捕获机制及实现
](https://mp.weixin.qq.com/s/g-WzYF3wWAljok1XjPoo7w?)。

## 符号解析：

使用以下命令行：
```
/Users/xxx/ndk-bundle/toolchains/aarch64-linux-android-4.9/prebuilt/darwin-x86_64/bin/aarch64-linux-android-addr2line -f -C -e
sample/build/intermediates/transforms/mergeJniLibs/debug/0/lib/arm64-v8a/libcrash-lib.so 0x600

```

00x600 即为 crash 发生位置的寄存器位置。

则可以得到发生 crash 的位置：

```
Crash()
/Users/xxx/Chapter01/sample/.externalNativeBuild/cmake/debug/arm64-v8a/../../../../src/main/cpp/crash.cpp:10

```

从上面可以得出 crash 的位置为 crash.cpp 的第 10 -- Crash() 方法。