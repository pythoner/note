
首先来看一下ldd的介绍
```
LDD(1)                                                                  Linux Programmer's Manual                                                                 LDD(1)

NAME
       ldd - print shared library dependencies

SYNOPSIS
       ldd [OPTION]... FILE...

DESCRIPTION
       ldd prints the shared libraries required by each program or shared library specified on the command line.

   Security
       In the usual case, ldd invokes the standard dynamic linker (see ld.so(8)) with the LD_TRACE_LOADED_OBJECTS environment variable set to 1, which causes the linker
       to display the library dependencies.  Be aware, however, that in some circumstances, some versions of ldd may attempt to obtain  the  dependency  information  by
       directly executing the program.  Thus, you should never employ ldd on an untrusted executable, since this may result in the execution of arbitrary code.  A safer
       alternative when dealing with untrusted executables is:

           $ objdump -p /path/to/program | grep NEEDED
```



#### **ldd**: 打印程序所需的共享库或者共享库的依赖


###### 举例1： 查看二进制文件的依赖

```
[lee@lee lib]$ ldd /usr/local/bin/ffmpeg
linux-vdso.so.1 =>  (0x00007ffd05fbd000)
libavdevice.so.58 => /usr/local/lib/libavdevice.so.58 (0x00007fa7252b6000)
libavfilter.so.7 => /usr/local/lib/libavfilter.so.7 (0x00007fa724c70000)	libavformat.so.58 => /usr/local/lib/libavformat.so.58 (0x00007fa7247b2000)
libavcodec.so.58 => /usr/local/lib/libavcodec.so.58 (0x00007fa722f49000)
libpostproc.so.55 => /usr/local/lib/libpostproc.so.55 (0x00007fa722d3d000)
libswresample.so.3 =>/usr/local/lib/libswresample.so.3(0x00007fa722b22000)
libswscale.so.5 => /usr/local/lib/libswscale.so.5 (0x00007fa7227c5000)
libavutil.so.56 => /usr/local/lib/libavutil.so.56 (0x00007fa72253f000)
libm.so.6 => /lib64/libm.so.6 (0x00007fa72223d000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fa722021000)
libc.so.6 => /lib64/libc.so.6 (0x00007fa721c5e000)
libxcb.so.1 => /lib64/libxcb.so.1 (0x00007fa721a36000)
libxcb-shm.so.0 => /lib64/libxcb-shm.so.0 (0x00007fa721832000)
libxcb-shape.so.0 => /lib64/libxcb-shape.so.0 (0x00007fa72162e000)
libxcb-xfixes.so.0 => /lib64/libxcb-xfixes.so.0 (0x00007fa721426000)
libasound.so.2 => /lib64/libasound.so.2 (0x00007fa721128000)
libXv.so.1 => /lib64/libXv.so.1 (0x00007fa720f23000)
libX11.so.6 => /lib64/libX11.so.6 (0x00007fa720be5000)
libXext.so.6 => /lib64/libXext.so.6 (0x00007fa7209d3000)
libz.so.1 => /lib64/libz.so.1 (0x00007fa7207bd000)
liblzma.so.5 => /lib64/liblzma.so.5 (0x00007fa720597000)
libx264.so.155 => /usr/local/lib/libx264.so.155 (0x00007fa72008f000)
/lib64/ld-linux-x86-64.so.2 (0x00007fa7254cf000)
libXau.so.6 => /lib64/libXau.so.6 (0x00007fa71fe8b000)
libdl.so.2 => /lib64/libdl.so.2 (0x00007fa71fc87000)
librt.so.1 => /lib64/librt.so.1 (0x00007fa71fa7f000)
```

如果想要看一下更详细的信息， 则可以添加*-v*参数

```
[lee@lee lib]$ ldd -v /usr/local/bin/ffmpeg
linux-vdso.so.1 =>  (0x00007ffebddce000)
libavdevice.so.58 => /usr/local/lib/libavdevice.so.58 (0x00007fec4b0d7000)
libavfilter.so.7 => /usr/local/lib/libavfilter.so.7 (0x00007fec4aa91000)
libavformat.so.58 => /usr/local/lib/libavformat.so.58 (0x00007fec4a5d3000)
libavcodec.so.58 => /usr/local/lib/libavcodec.so.58 (0x00007fec48d6a000)
libpostproc.so.55 => /usr/local/lib/libpostproc.so.55 (0x00007fec48b5e000)
libswresample.so.3 => /usr/local/lib/libswresample.so.3 (0x00007fec48943000)
libswscale.so.5 => /usr/local/lib/libswscale.so.5 (0x00007fec485e6000)
libavutil.so.56 => /usr/local/lib/libavutil.so.56 (0x00007fec48360000)
libm.so.6 => /lib64/libm.so.6 (0x00007fec4805e000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fec47e42000)
libc.so.6 => /lib64/libc.so.6 (0x00007fec47a7f000)
libxcb.so.1 => /lib64/libxcb.so.1 (0x00007fec47857000)
libxcb-shm.so.0 => /lib64/libxcb-shm.so.0 (0x00007fec47653000)
libxcb-shape.so.0 => /lib64/libxcb-shape.so.0 (0x00007fec4744f000)
libxcb-xfixes.so.0 => /lib64/libxcb-xfixes.so.0 (0x00007fec47247000)
libasound.so.2 => /lib64/libasound.so.2 (0x00007fec46f49000)
libXv.so.1 => /lib64/libXv.so.1 (0x00007fec46d44000)
libX11.so.6 => /lib64/libX11.so.6 (0x00007fec46a06000)
libXext.so.6 => /lib64/libXext.so.6 (0x00007fec467f4000)
libz.so.1 => /lib64/libz.so.1 (0x00007fec465de000)
liblzma.so.5 => /lib64/liblzma.so.5 (0x00007fec463b8000)
libx264.so.155 => /usr/local/lib/libx264.so.155 (0x00007fec45eb0000)
/lib64/ld-linux-x86-64.so.2 (0x00007fec4b2f0000)
libXau.so.6 => /lib64/libXau.so.6 (0x00007fec45cac000)
libdl.so.2 => /lib64/libdl.so.2 (0x00007fec45aa8000)
ibrt.so.1 => /lib64/librt.so.1 (0x00007fec458a0000)

Version information:
/usr/local/bin/ffmpeg:
	libpostproc.so.55 (LIBPOSTPROC_55) => /usr/local/lib/libpostproc.so.55
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libswscale.so.5 (LIBSWSCALE_5) => /usr/local/lib/libswscale.so.5
	libavdevice.so.58 (LIBAVDEVICE_58) => /usr/local/lib/libavdevice.so.58
	libswresample.so.3 (LIBSWRESAMPLE_3) => /usr/local/lib/libswresample.so.3
	libavfilter.so.7 (LIBAVFILTER_7) => /usr/local/lib/libavfilter.so.7
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.7) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libavformat.so.58 (LIBAVFORMAT_58) => /usr/local/lib/libavformat.so.58
	libavcodec.so.58 (LIBAVCODEC_58) => /usr/local/lib/libavcodec.so.58
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
/usr/local/lib/libavdevice.so.58:
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.7) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libavcodec.so.58 (LIBAVCODEC_58) => /usr/local/lib/libavcodec.so.58
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
	libavformat.so.58 (LIBAVFORMAT_58) => /usr/local/lib/libavformat.so.58
	libavfilter.so.7 (LIBAVFILTER_7) => /usr/local/lib/libavfilter.so.7
	libasound.so.2 (ALSA_0.9.0rc4) => /lib64/libasound.so.2
	libasound.so.2 (ALSA_0.9) => /lib64/libasound.so.2
/usr/local/lib/libavfilter.so.7:
	libavformat.so.58 (LIBAVFORMAT_58) => /usr/local/lib/libavformat.so.58
	libswresample.so.3 (LIBSWRESAMPLE_3) => /usr/local/lib/libswresample.so.3
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libc.so.6 (GLIBC_2.7) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libavcodec.so.58 (LIBAVCODEC_58) => /usr/local/lib/libavcodec.so.58
	libpostproc.so.55 (LIBPOSTPROC_55) => /usr/local/lib/libpostproc.so.55
	libswscale.so.5 (LIBSWSCALE_5) => /usr/local/lib/libswscale.so.5
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
/usr/local/lib/libavformat.so.58:
	libz.so.1 (ZLIB_1.2.0.2) => /lib64/libz.so.1
	libpthread.so.0 (GLIBC_2.3.2) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libavcodec.so.58 (LIBAVCODEC_58) => /usr/local/lib/libavcodec.so.58
	libc.so.6 (GLIBC_2.7) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
/usr/local/lib/libavcodec.so.58:
	libz.so.1 (ZLIB_1.2.0) => /lib64/libz.so.1
	liblzma.so.5 (XZ_5.0) => /lib64/liblzma.so.5
	libswresample.so.3 (LIBSWRESAMPLE_3) => /usr/local/lib/libswresample.so.3
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.7) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.3.2) => /lib64/libpthread.so.0
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
/usr/local/lib/libpostproc.so.55:
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/usr/local/lib/libswresample.so.3:
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
/usr/local/lib/libswscale.so.5:
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
	libavutil.so.56 (LIBAVUTIL_56) => /usr/local/lib/libavutil.so.56
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
  /usr/local/lib/libavutil.so.56:
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
	libpthread.so.0 (GLIBC_2.3.2) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libc.so.6 (GLIBC_2.7) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.17) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/lib64/libm.so.6:
	ld-linux-x86-64.so.2 (GLIBC_PRIVATE) => /lib64/ld-linux-x86-64.so.2
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libc.so.6 (GLIBC_PRIVATE) => /lib64/libc.so.6
/lib64/libpthread.so.0:
	ld-linux-x86-64.so.2 (GLIBC_2.2.5) => /lib64/ld-linux-x86-64.so.2
	ld-linux-x86-64.so.2 (GLIBC_2.3) => /lib64/ld-linux-x86-64.so.2
	ld-linux-x86-64.so.2 (GLIBC_PRIVATE) => /lib64/ld-linux-x86-64.so.2
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.2) => /lib64/libc.so.6
	libc.so.6 (GLIBC_PRIVATE) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/lib64/libc.so.6:
	ld-linux-x86-64.so.2 (GLIBC_2.3) => /lib64/ld-linux-x86-64.so.2
	ld-linux-x86-64.so.2 (GLIBC_PRIVATE) => /lib64/ld-linux-x86-64.so.2
/lib64/libxcb.so.1:
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.2) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/lib64/libxcb-shm.so.0:
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
/lib64/libxcb-shape.so.0:
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
/lib64/libxcb-xfixes.so.0:
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
/lib64/libasound.so.2:
	librt.so.1 (GLIBC_2.2.5) => /lib64/librt.so.1
	ld-linux-x86-64.so.2 (GLIBC_2.3) => /lib64/ld-linux-x86-64.so.2
	libdl.so.2 (GLIBC_2.2.5) => /lib64/libdl.so.2
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
	libpthread.so.0 (GLIBC_2.3.2) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.16) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.7) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/lib64/libXv.so.1:
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
/lib64/libX11.so.6:
	libdl.so.2 (GLIBC_2.2.5) => /lib64/libdl.so.2
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.15) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.2) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3) => /lib64/libc.so.6
/lib64/libXext.so.6:
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
/lib64/libz.so.1:
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/lib64/liblzma.so.5:
	libpthread.so.0 (GLIBC_2.3.3) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.3.2) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.17) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/usr/local/lib/libx264.so.155:
	libdl.so.2 (GLIBC_2.2.5) => /lib64/libdl.so.2
	libpthread.so.0 (GLIBC_2.3.2) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libc.so.6 (GLIBC_2.3) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.6) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
	libm.so.6 (GLIBC_2.2.5) => /lib64/libm.so.6
	libm.so.6 (GLIBC_2.15) => /lib64/libm.so.6
/lib64/libXau.so.6:
	libc.so.6 (GLIBC_2.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.4) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/lib64/libdl.so.2:
	ld-linux-x86-64.so.2 (GLIBC_PRIVATE) => /lib64/ld-linux-x86-64.so.2
	libc.so.6 (GLIBC_PRIVATE) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6
/lib64/librt.so.1:
	libpthread.so.0 (GLIBC_2.3.2) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_PRIVATE) => /lib64/libpthread.so.0
	libpthread.so.0 (GLIBC_2.2.5) => /lib64/libpthread.so.0
	libc.so.6 (GLIBC_2.14) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.3.2) => /lib64/libc.so.6
	libc.so.6 (GLIBC_PRIVATE) => /lib64/libc.so.6
	libc.so.6 (GLIBC_2.2.5) => /lib64/libc.so.6

```

###### 举例2: 查看共享库的依赖

```
[root@lee lib]# ldd /home/lee/Downloads/opencv/lib/cv2.so
linux-vdso.so.1 =>  (0x00007ffcdfba1000)
libopencv_core.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_core.so.3.4 (0x00007f5156d44000)
libopencv_flann.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_flann.so.3.4 (0x00007f5156aa4000)
libopencv_imgproc.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_imgproc.so.3.4 (0x00007f515408e000)
libopencv_ml.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_ml.so.3.4 (0x00007f5153d98000)
libopencv_objdetect.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_objdetect.so.3.4 (0x00007f5153ad7000)
libopencv_photo.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_photo.so.3.4 (0x00007f51537e6000)
libopencv_video.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_video.so.3.4 (0x00007f5153565000)
libopencv_dnn.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_dnn.so.3.4 (0x00007f5152d9e000)
libopencv_imgcodecs.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_imgcodecs.so.3.4 (0x00007f5152898000)
libopencv_shape.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_shape.so.3.4 (0x00007f5152644000)
libopencv_videoio.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_videoio.so.3.4 (0x00007f5152402000)
libopencv_highgui.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_highgui.so.3.4 (0x00007f51521e6000)
libopencv_superres.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_superres.so.3.4 (0x00007f5151faf000)
libopencv_features2d.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_features2d.so.3.4 (0x00007f5151c58000)
libopencv_calib3d.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_calib3d.so.3.4 (0x00007f515184a000)
libopencv_stitching.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_stitching.so.3.4 (0x00007f5151541000)
libopencv_videostab.so.3.4 => /home/lee/Downloads/opencv/lib/libopencv_videostab.so.3.4 (0x00007f51512d6000)
libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f5150fce000)
libm.so.6 => /lib64/libm.so.6 (0x00007f5150ccc000)
libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f5150ab6000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f515089a000)
libc.so.6 => /lib64/libc.so.6 (0x00007f51504d7000)
libdl.so.2 => /lib64/libdl.so.2 (0x00007f51502d3000)
librt.so.1 => /lib64/librt.so.1 (0x00007f51500cb000)
libz.so.1 => /lib64/libz.so.1 (0x00007f514feb5000)
/lib64/ld-linux-x86-64.so.2 (0x00007f5158226000)
libjpeg.so.62 => /lib64/libjpeg.so.62 (0x00007f514fc60000)
libpng15.so.15 => /lib64/libpng15.so.15 (0x00007f514fa35000)
libgtk-3.so.0 => /lib64/libgtk-3.so.0 (0x00007f514f122000)
libgdk-3.so.0 => /lib64/libgdk-3.so.0 (0x00007f514ee36000)
libatk-1.0.so.0 => /lib64/libatk-1.0.so.0 (0x00007f514ec10000)
libgio-2.0.so.0 => /lib64/libgio-2.0.so.0 (0x00007f514e875000)
libpangocairo-1.0.so.0 => /lib64/libpangocairo-1.0.so.0 (0x00007f514e668000)
libgdk_pixbuf-2.0.so.0 => /lib64/libgdk_pixbuf-2.0.so.0 (0x00007f514e440000)
libcairo-gobject.so.2 => /lib64/libcairo-gobject.so.2 (0x00007f514e237000)
libpango-1.0.so.0 => /lib64/libpango-1.0.so.0 (0x00007f514dfec000)
libcairo.so.2 => /lib64/libcairo.so.2 (0x00007f514dcc4000)
ibgobject-2.0.so.0 => /lib64/libgobject-2.0.so.0 (0x00007f514da73000)
libglib-2.0.so.0 => /lib64/libglib-2.0.so.0 (0x00007f514d75f000)
libgthread-2.0.so.0 => /lib64/libgthread-2.0.so.0 (0x00007f514d55d000)
libgmodule-2.0.so.0 => /lib64/libgmodule-2.0.so.0 (0x00007f514d359000)
libX11.so.6 => /lib64/libX11.so.6 (0x00007f514d01b000)
libXi.so.6 => /lib64/libXi.so.6 (0x00007f514ce0b000)
libXfixes.so.3 => /lib64/libXfixes.so.3 (0x00007f514cc05000)
libatk-bridge-2.0.so.0 => /lib64/libatk-bridge-2.0.so.0 (0x00007f514c9d6000)
libepoxy.so.0 => /lib64/libepoxy.so.0 (0x00007f514c6e1000)
libpangoft2-1.0.so.0 => /lib64/libpangoft2-1.0.so.0 (0x00007f514c4cb000)
libfontconfig.so.1 => /lib64/libfontconfig.so.1 (0x00007f514c28e000)
libXinerama.so.1 => /lib64/libXinerama.so.1 (0x00007f514c08b000)
libXrandr.so.2 => /lib64/libXrandr.so.2 (0x00007f514be80000)
libXcursor.so.1 => /lib64/libXcursor.so.1 (0x00007f514bc75000)
libXcomposite.so.1 => /lib64/libXcomposite.so.1 (0x00007f514ba72000)
libXdamage.so.1 => /lib64/libXdamage.so.1 (0x00007f514b86f000)
libxkbcommon.so.0 => /lib64/libxkbcommon.so.0 (0x00007f514b62f000)
libwayland-cursor.so.0 => /lib64/libwayland-cursor.so.0 (0x00007f514b427000)
libwayland-egl.so.1 => /lib64/libwayland-egl.so.1 (0x00007f514b225000)
libwayland-client.so.0 => /lib64/libwayland-client.so.0 (0x00007f514b016000)
libXext.so.6 => /lib64/libXext.so.6 (0x00007f514ae04000)
libffi.so.6 => /lib64/libffi.so.6 (0x00007f514abfc000)
libpcre.so.1 => /lib64/libpcre.so.1 (0x00007f514a99a000)
libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f514a773000)
libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f514a559000)
libmount.so.1 => /lib64/libmount.so.1 (0x00007f514a317000)
libthai.so.0 => /lib64/libthai.so.0 (0x00007f514a10b000)
libharfbuzz.so.0 => /lib64/libharfbuzz.so.0 (0x00007f5149ea8000)
libfreetype.so.6 => /lib64/libfreetype.so.6 (0x00007f5149c02000)
libpixman-1.so.0 => /lib64/libpixman-1.so.0 (0x00007f5149959000)
libEGL.so.1 => /lib64/libEGL.so.1 (0x00007f5149747000)
libxcb-shm.so.0 => /lib64/libxcb-shm.so.0 (0x00007f5149543000)
libxcb.so.1 => /lib64/libxcb.so.1 (0x00007f514931b000)
libxcb-render.so.0 => /lib64/libxcb-render.so.0 (0x00007f514910d000)
libXrender.so.1 => /lib64/libXrender.so.1 (0x00007f5148f02000)
libGL.so.1 => /lib64/libGL.so.1 (0x00007f5148c5e000)
libatspi.so.0 => /lib64/libatspi.so.0 (0x00007f5148a2e000)
libdbus-1.so.3 => /lib64/libdbus-1.so.3 (0x00007f51487de000)
libexpat.so.1 => /lib64/libexpat.so.1 (0x00007f51485b4000)
libblkid.so.1 => /lib64/libblkid.so.1 (0x00007f5148375000)
libuuid.so.1 => /lib64/libuuid.so.1 (0x00007f5148170000)
libgraphite2.so.3 => /lib64/libgraphite2.so.3 (0x00007f5147f42000)
libGLdispatch.so.0 => /lib64/libGLdispatch.so.0 (0x00007f5147c74000)
li帮助bXau.so.6 => /lib64/libXau.so.6 (0x00007f5147a70000)
libGLX.so.0 => /lib64/libGLX.so.0 (0x00007f5147840000)
libsystemd.so.0 => /lib64/libsystemd.so.0 (0x00007f514760f000)
libcap.so.2 => /lib64/libcap.so.2 (0x00007f514740a000)
liblzma.so.5 => /lib64/liblzma.so.5 (0x00007f51471e4000)
liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f5146fcf000)
libgcrypt.so.11 => /lib64/libgcrypt.so.11 (0x00007f5146d4e000)
libgpg-error.so.0 => /lib64/libgpg-error.so.0 (0x00007f5146b49000)
libdw.so.1 => /lib64/libdw.so.1 (0x00007f5146902000)
libattr.so.1 => /lib64/libattr.so.1 (0x00007f51466fd000)
libelf.so.1 => /lib64/libelf.so.1 (0x00007f51464e5000)
libbz2.so.1 => /lib64/libbz2.so.1 (0x00007f51462d5000)

```

###### 当然还有一些命令可能有帮助
```
ldd : 列出一个可执行文件在运行时需要的共享库信息
ar : 创建静态库，插入，删除，列出和提取成员
strings : 列出文件中的所有可打印字符串
strip : 从目标文件中删除符号表信息
nm : 列出目标文件中符号表中定义的符号
size : 列出目标文件中节的名字和大小
readelf : 显示一个木边文件完整结构，包括elf头中的编码的所有信息，包含size和nm的功能
objdump : 所有二进制工具之母。能够显示一个目标文件中的所有信息，它最有用的功能是反汇编.text节中的二进制指令
```
