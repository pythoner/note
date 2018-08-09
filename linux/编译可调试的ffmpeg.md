前面我们的已经写过了ffmpeg的源码的编译安装。 编译可调试的ffmpeg跟之前是一样的。 只不过是configure需要知名enanble-debug参数

在ffmpeg源码目录下执行以下命令
```
[root@lee ffmpeg-3.4.2]# ./configure --enable-shared --enable-gpl  --enable-libx264 --enable-debug --extra-cflags=-g --extra-ldflags=-g --disable-asm --disable-optimizations --disable-stripping 
[root@lee ffmpeg-3.4.2]# make -j && make install
```


拷贝编译的库文件到/usr/local/lib

```
[root@lee ffmpeg-3.4.2]# pwd
/root/ffmpeg-3.4.2

[root@lee ffmpeg-3.4.2]# cp -f ./*/*.so* /usr/local/lib
```
执行**ffmpeg_g* 试试:
```
[root@lee ffmpeg-3.4.2]# ./ffmpeg_g
./ffmpeg_g: error while loading shared libraries: libavdevice.so.57: cannot open shared object file: No such file or directory
[root@lee ffmpeg-3.4.2]# find / -name libavdevice.so.57
find: ‘/run/user/1000/gvfs’: Permission denied
/root/ffmpeg-3.4.2/libavdevice/libavdevice.so.57
/usr/local/lib/libavdevice.so.57
/usr/local/lib64/libavdevice.so.57
```

这里报找不到libavdevice.so.57. 这个在我们的lib目录下。

* 解决：

1. 编辑/etc/ld.so.conf, 添加/usr/local/lib目录
```
include ld.so.conf.d/*.conf
/usr/local/lib
```
2. 然后执行 ldconfig命令, 看效果
```
[root@lee ffmpeg-3.4.2]# ldconfig
[root@lee ffmpeg-3.4.2]#
[root@lee ffmpeg-3.4.2]# ./ffmpeg_g
ffmpeg version 3.4.2 Copyright (c) 2000-2018 the FFmpeg developers
  built with gcc 4.8.5 (GCC) 20150623 (Red Hat 4.8.5-16)
  configuration: --enable-shared --enable-gpl --enable-libx264 --enable-debug --extra-cflags=-g --extra-ldflags=-g
  libavutil      55. 78.100 / 55. 78.100
  libavcodec     57.107.100 / 57.107.100
  libavformat    57. 83.100 / 57. 83.100
  libavdevice    57. 10.100 / 57. 10.100
  libavfilter     6.107.100 /  6.107.100
  libswscale      4.  8.100 /  4.  8.100
  libswresample   2.  9.100 /  2.  9.100
  libpostproc    54.  7.100 / 54.  7.100
Hyper fast Audio and Video encoder
usage: ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...

Use -h to get full help or, even better, run 'man ffmpeg'

```
