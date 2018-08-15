```
pkg-config(1)

NAME
       pkg-config - Return metainformation about installed libraries

SYNOPSIS
       pkg-config  [--modversion]  [--version]  [--help] [--atleast-pkgconfig-version=VERSION] [--print-errors] [--short-errors] [--silence-errors] [--errors-to-stdout]
       [--debug] [--cflags] [--libs] [--libs-only-L] [--libs-only-l] [--cflags-only-I] [--libs-only-other]  [--cflags-only-other]  [--variable=VARIABLENAME]  [--define-
       variable=VARIABLENAME=VARIABLEVALUE] [--print-variables] [--uninstalled] [--exists] [--atleast-version=VERSION] [--exact-version=VERSION] [--max-version=VERSION]
       [--list-all] [LIBRARIES...]  [--print-provides] [--print-requires] [--print-requires-private] [LIBRARIES...]

DESCRIPTION
       The pkg-config program is used to retrieve information about installed libraries in the system.  It is typically used to compile and link  against  one  or  more
       libraries.  Here is a typical usage scenario in a Makefile:

       program: program.c
            cc program.c $(pkg-config --cflags --libs gnomeui)

       pkg-config  retrieves information about packages from special metadata files. These files are named after the package, and has a .pc extension.  On most systems,
       pkg-config looks in /usr/lib/pkgconfig, /usr/share/pkgconfig, /usr/local/lib/pkgconfig and /usr/local/share/pkgconfig for these files.  It will additionally look
       in the colon-separated (on Windows, semicolon-separated) list of directories specified by the PKG_CONFIG_PATH environment variable.

       The  package name specified on the pkg-config command line is defined to be the name of the metadata file, minus the .pc extension. If a library can install mul‐
       tiple versions simultaneously, it must give each version its own name (for example, GTK 1.2 might have the package name "gtk+" while GTK 2.0 has "gtk+-2.0").

       In addition to specify
```

##### 说明

* pkg-config 默认的查找路径
pkg-config的默认查找路径，根据上面的说明， 在大多数系统中可以是*/usr/lib/pkgconfig*, */usr/share/pkgconfig*, */usr/local/lib/pkgconfig* 和 */usr/local/share/pkgconfig*， 其实我们可以通过命令，获取他在环境的查找路径
```
[lee@lee ~]$ pkg-config --variable pc_path pkg-config
/usr/lib64/pkgconfig:/usr/share/pkgconfig
```

**pkg-config** 用于获取系统中安装的库的信息。 典型的应用是用于编译和连接多个库的时候。 下面是Makefile中典型的应用场景
```
program: program.c
     cc program.c $(pkg-config --cflags --libs gnomeui)
```

  *pkg-config* 从特定的元数据文件获取包/库的信息。这些文件跟包的名字相同， 多了一个*.pc*的后缀。 在多数系统中， *pkg-config*在*/usr/lib/pkgconfig*， */usr/share/pkgconfig*， */usr/local/lib/pkgconfig*， */usr/local/share/pkgconfig* 目录下找这些文件。 同时也会查找**PKG_CONFIG_PATH**环境变量对应的目录列表。


  *pkg-config* 命令指定的**包/库**的名字， 对应着 **包/库.pc**元数据文件



##### 举例
```
[lee@lee lib]$
[lee@lee lib]$ pkg-config --cflags --libs python
-I/usr/include/python2.7  -lpython2.7  
[lee@lee lib]$ pkg-config --cflags  python
-I/usr/include/python2.7  
[lee@lee lib]$ pkg-config --libs  python
-lpython2.7  

[lee@lee linux]$ pkg-config -h
Usage:
  pkg-config [OPTION?]

Help Options:
  -h, --help                              Show help options

Application Options:
  --version                               output version of pkg-config
  --modversion                            output version for package
  --atleast-pkgconfig-version=VERSION     require given version of pkg-config
  --libs                                  output all linker flags
  --static                                output linker flags for static linking
  --short-errors                          print short errors
  --libs-only-l                           output -l flags
  --libs-only-other                       output other libs (e.g. -pthread)
  --libs-only-L                           output -L flags
  --cflags                                output all pre-processor and compiler flags
  --cflags-only-I                         output -I flags
  --cflags-only-other                     output cflags not covered by the cflags-only-I option
  --variable=NAME                         get the value of variable named NAME
  --define-variable=NAME=VALUE            set variable NAME to VALUE
  --exists                                return 0 if the module(s) exist
  --print-variables                       output list of variables defined by the module
  --uninstalled                           return 0 if the uninstalled version of one or more module(s) or their dependencies will be used
  --atleast-version=VERSION               return 0 if the module is at least version VERSION
  --exact-version=VERSION                 return 0 if the module is at exactly version VERSION
  --max-version=VERSION                   return 0 if the module is at no newer than version VERSION
  --list-all                              list all known packages
  --debug                                 show verbose debug information
  --print-errors                          show verbose information about missing or conflicting packages,default if --cflags or --libs given on the command line
  --silence-errors                        be silent about errors (default unless --cflags or --libsgiven on the command line)
  --errors-to-stdout                      print errors from --print-errors to stdout not stderr
  --print-provides                        print which packages the package provides
  --print-requires                        print which packages the package requires
  --print-requires-private                print which packages the package requires for static linking

```

一、编译和连接

  一般来说，如果库的头文件不在 /usr/include 目录中，那么在编译的时候需要用 -I 参数指定其路径。由于同一个库在不同系统上可能位于不同的目录下，用户安装库的时候也可以将库安装在不同的目录下，所以即使使用同一个库，由于库的路径的 不同，造成了用 -I 参数指定的头文件的路径也可能不同，其结果就是造成了编译命令界面的不统一。如果使用 -L 参数，也会造成连接界面的不统一。编译和连接界面不统一会为库的使用带来麻烦。

为了解决编译和连接界面不统一的问题，人们找到了一些解决办法。其基本思想就是：事先把库的位置信息等保存起来，需要的时候再通过特定的工具将其中有用的 信息提取出来供编译和连接使用。这样，就可以做到编译和连接界面的一致性。其中，目前最为常用的库信息提取工具就是下面介绍的 pkg-config。

pkg-config 是通过库提供的一个 .pc 文件获得库的各种必要信息的，包括版本信息、编译和连接需要的参数等。这些信息可以通过 pkg-config 提供的参数单独提取出来直接供编译器和连接器使用。

使用 pkg-config 的 --cflags 参数可以给出在编译时所需要的选项，而 --libs 参数可以给出连接时的选项。例如，假设一个 sample.c 的程序用到了 Glib 库，就可以这样编译：
```
$ gcc -c `pkg-config --cflags glib-2.0` sample.c
```
然后这样连接：
```
$ gcc sample.o -o sample `pkg-config --libs glib-2.0`
```
或者上面两步也可以合并为以下一步：
```
$ gcc sample.c -o sample `pkg-config --cflags --libs glib-2.0`
```
可以看到：由于使用了 pkg-config 工具来获得库的选项，所以不论库安装在什么目录下，都可以使用相同的编译和连接命令，带来了编译和连接界面的统一。

使用 pkg-config 工具提取库的编译和连接参数有两个基本的前提：

库本身在安装的时候必须提供一个相应的 .pc 文件。不这样做的库说明不支持 pkg-config 工具的使用。
pkg-config 必须知道要到哪里去寻找此 .pc 文件。
GTK+ 及其依赖库支持使用 pkg-config 工具，所以剩下的问题就是如何告诉 pkg-config 到哪里去寻找库对应的 .pc 文件，这也是通过设置搜索路径来解决的。

    对于支持 pkg-config 工具的 GTK+ 及其依赖库来说，库的头文件的搜索路径的设置变成了对 .pc 文件搜索路径的设置。.pc 文件的搜索路径是通过环境变量 PKG_CONFIG_PATH 来设置的，pkg-config 将按照设置路径的先后顺序进行搜索，直到找到指定的 .pc 文件为止。

安装完 Glib 后，在 bash 中应该进行如下设置：
```
$ export PKG_CONFIG_PATH=/opt/gtk/lib/pkgconfig:$PKG_CONFIG_PATH
```
可以执行下面的命令检查是否 /opt/gtk/lib/pkgconfig 路径已经设置在 PKG_CONFIG_PATH 环境变量中：
```
$ echo $PKG_CONFIG_PATH
```
这样设置之后，使用 Glib 库的其它程序或库在编译的时候 pkg-config 就知道首先要到 /opt/gtk/lib/pkgconfig 这个目录中去寻找 glib-2.0.pc 了（GTK+ 和其它的依赖库的 .pc 文件也将拷贝到这里，也会首先到这里搜索它们对应的 .pc 文件）。之后，通过 pkg-config 就可以把其中库的编译和连接参数提取出来供程序在编译和连接时使用。

另外还需要注意的是：环境变量的设置只对当前的终端窗口有效。如果到了没有进行上述设置的终端窗口中，pkg-config 将找不到新安装的 glib-2.0.pc 文件、从而可能使后面进行的安装（如 Glib 之后的 Atk 的安装）无法进行。

    在我们采用的安装方案中，由于是使用环境变量对 GTK+ 及其依赖库进行的设置，所以当系统重新启动、或者新开一个终端窗口之后，如果想使用新安装的 GTK+ 库，需要如上面那样重新设置 PKG_CONFIG_PATH 和 LD_LIBRARY_PATH 环境。

这种使用 GTK+ 的方法，在使用之前多了一个对库进行设置的过程。虽然显得稍微繁琐了一些，但却是一种最安全的使用 GTK+ 库的方式，不会对系统上已经存在的使用了 GTK+ 库的程序（比如 GNOME 桌面）带来任何冲击。

为了使库的设置变得简单一些，可以把下面的这两句设置保存到一个文件中（比如 set_gtk-2.10 文件）:
```
export PKG_CONFIG_PATH=/opt/gtk/lib/pkgconfig:$PKG_CONFIG_PATH
export LD_LIBRARY_PATH=/opt/gtk/lib:$LD_LIBRARY_PATH
```
之后，就可以用下面的方法进行库的设置了（其中的 source 命令也可以用 . 代替）：
```
$ source set_gtk-2.10
```
 只有在用新版的 GTK+ 库开发应用程序、或者运行使用了新版 GTK+ 库的程序的时候，才有必要进行上述设置。

   如果想避免使用 GTK+ 库之前上述设置的麻烦，可以把上面两个环境变量的设置在系统的配置文件中（如 /etc/profile）或者自己的用户配置文件中（如 ~/.bash_profile） ；库的搜索路径也可以设置在 /etc/ld.so.conf 文件中，等等。这种设置在系统启动时会生效，从而会导致使用 GTK+ 的程序使用新版的 GTK+ 运行库，这有可能会带来一些问题。当然，如果你发现用新版的 GTK+ 代替旧版没有什么问题的话，使用这种设置方式是比较方便的。加入到~/.bashrc中，例如：
```
PKG_CONFIG_PATH=/opt/gtk/lib/pkgconfig
```
 重启之后：
```
 [root@localhost ~]# echo $PKG_CONFIG_PATH
 /opt/gtk/lib/pkgconfig
```

 二、运行时

库文件在连接（静态库和共享库）和运行（仅限于使用共享库的程序）时被使用，其搜索路径是在系统中进行设置的。一般 Linux 系统把 /lib 和 /usr/lib 两个目录作为默认的库搜索路径，所以使用这两个目录中的库时不需要进行设置搜索路径即可直接使用。对于处于默认库搜索路径之外的库，需要将库的位置添加到 库的搜索路径之中。设置库文件的搜索路径有下列两种方式，可任选其一使用：

 在环境变量 LD_LIBRARY_PATH 中指明库的搜索路径。
```
 在 /etc/ld.so.conf 文件中添加库的搜索路径。
         将自己可能存放库文件的路径都加入到/etc/ld.so.conf中是明智的选择 ^_^
```
 添加方法也极其简单，将库文件的绝对路径直接写进去就OK了，一行一个。例如：
```
 /usr/X11R6/lib

 /usr/local/lib

 /opt/lib
```
需要注意的是：第二种搜索路径的设置方式对于程序连接时的库（包括共享库和静态库）的定位已经足够了，但是对于使用了共享库的程序的执行还是不够的。这是 因为为了加快程序执行时对共享库的定位速度，避免使用搜索路径查找共享库的低效率，所以是直接读取库列表文件 /etc/ld.so.cache 从中进行搜索的。/etc/ld.so.cache 是一个非文本的数据文件，不能直接编辑，它是根据 /etc/ld.so.conf 中设置的搜索路径由 /sbin/ldconfig 命令将这些搜索路径下的共享库文件集中在一起而生成的（ldconfig 命令要以 root 权限执行）。因此，为了保证程序执行时对库的定位，在 /etc/ld.so.conf 中进行了库搜索路径的设置之后，还必须要运行 /sbin/ldconfig 命令更新 /etc/ld.so.cache 文件之后才可以。ldconfig ,简单的说，它的作用就是将/etc/ld.so.conf列出的路径下的库文件 缓存到/etc/ld.so.cache 以供使用。因此当安装完一些库文件，(例如刚安装好glib)，或者修改ld.so.conf增加新的库路径后，需要运行一下 /sbin/ldconfig使所有的库文件都被缓存到ld.so.cache中，如果没做，即使库文件明明就在/usr/lib下的，也是不会被使用 的，结果编译过程中抱错，缺少xxx库，去查看发现明明就在那放着，搞的想大骂computer蠢猪一个。 ^_^

在程序连接时，对于库文件（静态库和共享库）的搜索路径，除了上面的设置方式之外，还可以通过 -L 参数显式指定。因为用 -L 设置的路径将被优先搜索，所以在连接的时候通常都会以这种方式直接指定要连接的库的路径。



前面已经说明过了，库搜索路径的设置有两种方式：在环境变量 LD_LIBRARY_PATH 中设置以及在 /etc/ld.so.conf 文件中设置。其中，第二种设置方式需要 root 权限，以改变 /etc/ld.so.conf 文件并执行 /sbin/ldconfig 命令。而且，当系统重新启动后，所有的基于 GTK2 的程序在运行时都将使用新安装的 GTK+ 库。不幸的是，由于 GTK+ 版本的改变，这有时会给应用程序带来兼容性的问题，造成某些程序运行不正常。为了避免出现上面的这些情况，在 GTK+ 及其依赖库的安装过程中对于库的搜索路径的设置将采用第一种方式进行。这种设置方式不需要 root 权限，设置也简单：
```
 $ export LD_LIBRARY_PATH=/opt/gtk/lib:$LD_LIBRARY_PATH
```
 可以用下面的命令查看 LD_LIBRAY_PATH 的设置内容：
```
 $ echo $LD_LIBRARY_PATH
```
 至此，库的两种设置就完成了。


##### 对于源码编译安装的如何制作使用pkg_config

这里我们以opencv为例

opencv我们从源码编译安装后， 默认安装与/usr/local目录下， libs安装与/usr/local/lib64.

```
[lee@lee pkgconfig]$ pwd
/usr/local/lib64/pkgconfig
[lee@lee pkgconfig]$ ls -l
total 4
-rw-r--r--. 1 root root 653 Jul 13 17:17 opencv.pc
[lee@lee pkgconfig]$
[lee@lee pkgconfig]$
[lee@lee pkgconfig]$ cat opencv.pc
# Package Information for pkg-config

prefix=/usr/local
exec_prefix=${prefix}
libdir=${exec_prefix}/lib64
includedir_old=${prefix}/include/opencv
includedir_new=${prefix}/include

Name: OpenCV
Description: Open Source Computer Vision Library
Version: 3.4.1
Libs: -L${exec_prefix}/lib64 -lopencv_dnn -lopencv_ml -lopencv_objdetect -lopencv_shape -lopencv_stitching -lopencv_superres -lopencv_videostab -lopencv_calib3d -lopencv_features2d -lopencv_highgui -lopencv_videoio -lopencv_imgcodecs -lopencv_video -lopencv_photo -lopencv_imgproc -lopencv_flann -lopencv_core
Libs.private: -ldl -lm -lpthread -lrt
Cflags: -I${includedir_old} -I${includedir_new}

```

执行pkg-config
```
[lee@lee ~]$ pkg-config --libs opencv
Package opencv was not found in the pkg-config search path.
Perhaps you should add the directory containing `opencv.pc'
to the PKG_CONFIG_PATH environment variable
No package 'opencv' found
```

解决： 更新PKG_CONFIG_PATH环境变量
```
[lee@lee ~]$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib64/pkgconfig
[lee@lee ~]$
[lee@lee ~]$ pkg-config --libs opencv
-L/usr/local/lib64 -lopencv_dnn -lopencv_ml -lopencv_objdetect -lopencv_shape -lopencv_stitching -lopencv_superres -lopencv_videostab -lopencv_calib3d -lopencv_features2d -lopencv_highgui -lopencv_videoio -lopencv_imgcodecs -lopencv_video -lopencv_photo -lopencv_imgproc -lopencv_flann -lopencv_core
```


##### 参考

* [pkg-config的使用](http://www.cnblogs.com/feisky/archive/2010/03/29/1699880.html)
