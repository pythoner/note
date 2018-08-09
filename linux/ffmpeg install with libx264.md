
1. get source code of x264
```
[root@lee ~]# git clone http://git.videolan.org/git/x264.git
Cloning into 'x264'...
remote: Counting objects: 21253, done.
remote: Compressing objects: 100% (4652/4652), done.
remote: Total 21253 (delta 17597), reused 20107 (delta 16558)
Receiving objects: 100% (21253/21253), 5.02 MiB | 1.43 MiB/s, done.
Resolving deltas: 100% (17597/17597), done.
[root@lee ~]#
```


2. compile, install
```
[root@lee x264]# ./configure --enable-shared --enable-static
Found no assembler
Minimum version is nasm-2.13
If you really want to compile without asm, configure with --disable-asm.

```

the version is lower than 2.13 in repo
```
[root@lee x264]# yum info nasm
Loaded plugins: auto-update-debuginfo, fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * epel: mirrors.huaweicloud.com
 * epel-debuginfo: mirrors.huaweicloud.com
 * rpmfusion-free-updates: mirrors.ustc.edu.cn
 * rpmfusion-free-updates-debuginfo: mirrors.ustc.edu.cn
Available Packages
Name        : nasm
Arch        : x86_64
Version     : 2.10.07
Release     : 7.el7
Size        : 402 k
Repo        : base/7/x86_64
Summary     : A portable x86 assembler which uses Intel-like syntax
URL         : http://www.nasm.us
License     : BSD
Description : NASM is the Netwide Assembler, a free portable assembler for the Intel
            : 80x86 microprocessor series, using primarily the traditional Intel
            : instruction mnemonics and syntax.
```

download nasm 2.1.3 package, install
> https://nasm.us/
```
[root@lee ~]# wget https://www.nasm.us/pub/nasm/releasebuilds/2.13.03/linux/nasm-2.13.03-0.fc24.x86_64.rpm
[root@lee ~]# rpm -ivh ./nasm-2.13.03-0.fc24.x86_64.rpm

```


compile, install x264 again
```
[root@lee x264]# ./configure --enable-shared --enable-static
[root@lee x264]# make && make install
```


3, unzip ffmpeg source code, compile, install
```
[root@lee ~]# tar -zxvf ./ffmpeg-3.4.2.tar.gz
[root@lee ~]# ./configure --enable-libx264 --enable-gpl
[root@lee ~]# make && make install
```

issues when install ffmpeg

* issue 1
```
libavcodec/libavcodec.a(libx264.o): In function `X264_init':
/root/ffmpeg-3.4.2/libavcodec/libx264.c:818: undefined reference to `x264_encoder_open_155'
collect2: error: ld returned 1 exit status
```

remove all install x264 by yum install
```
[root@lee lee]# yum remove x264-devel.x86_64 x264-libs.x86_64 x264.x86_64 x264-debuginfo.x86_64
```

* issue 2
```
[root@lee ffmpeg-3.4.2]# ffmpeg
ffmpeg: error while loading shared libraries: libx264.so.155: cannot open shared object file: No such file or directory
```


```
[root@lee ffmpeg-3.4.2]# ldd $(which ffmpeg)
linux-vdso.so.1 =>  (0x00007ffee00fc000)
libXv.so.1 => /lib64/libXv.so.1 (0x00007f6e2e418000)
libX11.so.6 => /lib64/libX11.so.6 (0x00007f6e2e0da000)
libXext.so.6 => /lib64/libXext.so.6 (0x00007f6e2dec7000)
libxcb.so.1 => /lib64/libxcb.so.1 (0x00007f6e2dc9f000)
libxcb-shm.so.0 => /lib64/libxcb-shm.so.0 (0x00007f6e2da9b000)
libxcb-xfixes.so.0 => /lib64/libxcb-xfixes.so.0 (0x00007f6e2d892000)
libxcb-shape.so.0 => /lib64/libxcb-shape.so.0 (0x00007f6e2d68e000)
libasound.so.2 => /lib64/libasound.so.2 (0x00007f6e2d390000)
libx264.so.155 => not found
libm.so.6 => /lib64/libm.so.6 (0x00007f6e2d08d000)
libdl.so.2 => /lib64/libdl.so.2 (0x00007f6e2ce89000)
liblzma.so.5 => /lib64/liblzma.so.5 (0x00007f6e2cc62000)
libz.so.1 => /lib64/libz.so.1 (0x00007f6e2ca4c000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f6e2c830000)
libc.so.6 => /lib64/libc.so.6 (0x00007f6e2c46c000)
libXau.so.6 => /lib64/libXau.so.6 (0x00007f6e2c268000)
librt.so.1 => /lib64/librt.so.1 (0x00007f6e2c05f000)
/lib64/ld-linux-x86-64.so.2 (0x00005626e3daa000)

[root@lee ffmpeg-3.4.2]# cd /lib64/
[root@lee lib64]# ln -s /usr/local/lib/libx264.so.155
```
