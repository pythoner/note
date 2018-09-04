有一些类型的bug在python内很难调试， 如
* 段错误（segfaults）
* 进程的挂起（hung processes）（在这种情况下我们在pdb中不能获取调用堆栈信息）
* 在daemon进程之外的（out of control daemon processes）

在这种情况下，我们可以使用gdb

### 前置条件

在我们使用gdb调试python之前，我们首先需要在环境中安装gdb以及python debug的扩展包(python debugging extensions).  这些扩展包包括调试符号（debugging symbols）以及在gdb中添加的python相关的命令。 在不同的平台上，安装略有区别。

* Fedora

```
sudo yum install gdb python-debuginfo

```

* ubuntu

```
sudo apt-get install gdb python2.7-dbg

```
* centos

```
sudo yum install yum-utils
sudo debuginfo-install glibc
sudo yum install gdb python-debuginfo
```
> 在centos 7测试过， 执行前两个命令后，就已经安装了pyton-debuginfo


### 使用

使用gdb调试python，有两种方式

1. 一种是我们的python程序还没有开始运行， 这种情况，我们可以直接在gdb下运行python程序

  对于这种情况， 我们有两种实现：

  * 交互式
  ```
  $ gdb python
  ...
  (gdb) run <programname>.py <arguments>
  ```
  * 非交互式
  ```
  $ gdb -ex r --args python <programname>.py <arguments>
  ```

  这将运行程序直至正常结束、 段错误或者使用ctrl+c手动中止

2. 一种是已经在运行中的python程序

  对于这种情况， 我们可以attach已经running的进程的ip
  ```
  $ gdb python <pid of running process>
  ```
  Attaching to a running process like this will cause it to stop. You can tell it to continue running with c command.


### 调试进程

如果程序是段错误（segfaulted）， gdb会自动暂停进程， 这时，我们就可以切换到gdb控制台来查看进程的状态。 当然也可以通过ctrl+c来手动的中止程序的运行


### 获取C的堆栈

如果调试的是一个段错误(segfaults), 这可能是第一个想做的事情

在gdb的提示下， 运行下面的命令：

```
(gdb) bt
#0  0x0000002a95b3b705 in raise () from /lib/libc.so.6
#1  0x0000002a95b3ce8e in abort () from /lib/libc.so.6
#2  0x00000000004c164f in posix_abort (self=0x0, noargs=0x0)
    at ../Modules/posixmodule.c:7158
#3  0x0000000000489fac in call_function (pp_stack=0x7fbffff110, oparg=0)
    at ../Python/ceval.c:3531
#4  0x0000000000485fc2 in PyEval_EvalFrame (f=0x66ccd8)
    at ../Python/ceval.c:2163
```

### 获取python的堆栈

如果已经安装了python的扩展， 则可以用*py-bt*命令
```
(gdb) py-bt
```

### 挂起进程

如果进程显示是挂起状态（hung）, 进程要么是在等待某些东西（锁， io等）， 要么是在busy loop中。不管什么情况，我们将进程attaching 到gdb查看堆栈信息都有帮助。

If the process is in a busy loop, you may want to continue execution for a bit (using the cont command), then break (Ctrl+C) again and bring up a stack trace.

如果进程是busy loop，

如果是在线程中发生了挂起（hang）, 下面的命令会有帮助
```
(gdb) info threads
  Id   Target Id         Frame
  37   Thread 0xa29feb40 (LWP 17914) "NotificationThr" 0xb7fdd424 in __kernel_vsyscall ()
  36   Thread 0xa03fcb40 (LWP 17913) "python2.7" 0xb7fdd424 in __kernel_vsyscall ()
  35   Thread 0xa0bfdb40 (LWP 17911) "QProcessManager" 0xb7fdd424 in __kernel_vsyscall ()
  34   Thread 0xa13feb40 (LWP 17910) "python2.7" 0xb7fdd424 in __kernel_vsyscall ()
  33   Thread 0xa1bffb40 (LWP 17909) "python2.7" 0xb7fdd424 in __kernel_vsyscall ()
  31   Thread 0xa31ffb40 (LWP 17907) "QFileInfoGather" 0xb7fdd424 in __kernel_vsyscall ()
  30   Thread 0xa3fdfb40 (LWP 17906) "QInotifyFileSys" 0xb7fdd424 in __kernel_vsyscall ()
  29   Thread 0xa481cb40 (LWP 17905) "QFileInfoGather" 0xb7fdd424 in __kernel_vsyscall ()
  7    Thread 0xa508db40 (LWP 17883) "QThread" 0xb7fdd424 in __kernel_vsyscall ()
  6    Thread 0xa5cebb40 (LWP 17882) "python2.7" 0xb7fdd424 in __kernel_vsyscall ()
  5    Thread 0xa660cb40 (LWP 17881) "python2.7" 0xb7fdd424 in __kernel_vsyscall ()
  3    Thread 0xabdffb40 (LWP 17876) "gdbus" 0xb7fdd424 in __kernel_vsyscall ()
  2    Thread 0xac7b7b40 (LWP 17875) "dconf worker" 0xb7fdd424 in __kernel_vsyscall ()
* 1    Thread 0xb7d876c0 (LWP 17863) "python2.7" 0xb7fdd424 in __kernel_vsyscall ()
```

当前的线程使用"\*"标记了出来。看我们python目前在那里执行，使用*py-list*
```
(gdb) py-list
2025        # Open external files with our Mac app
2026        if sys.platform == "darwin" and 'Spyder.app' in __file__:
2027            main.connect(app, SIGNAL('open_external_file(QString)'),
2028                         lambda fname: main.open_external_file(fname))
2029
>2030        app.exec_()
2031        return main
2032
2033
2034    def __remove_temp_session():
2035        if osp.isfile(TEMP_SESSION_PATH):
```
看代码在所有线程当中的为值， 使用
```
gdb) thread apply all py-list
...
 200
 201        def accept(self):
>202            sock, addr = self._sock.accept()
 203            return _socketobject(_sock=sock), addr
 204        accept.__doc__ = _realsocket.accept.__doc__
 205
 206        def dup(self):
 207            """dup() -> socket object

Thread 35 (Thread 0xa0bfdb40 (LWP 17911)):
Unable to locate python frame

Thread 34 (Thread 0xa13feb40 (LWP 17910)):
 197            for method in _delegate_methods:
 198                setattr(self, method, dummy)
 199        close.__doc__ = _realsocket.close.__doc__
 200
 201        def accept(self):
>202            sock, addr = self._sock.accept()
 203            return _socketobject(_sock=sock), addr
...
```



[原文地址](https://wiki.python.org/moin/DebuggingWithGdb)

[EasierPythonDebugging](https://fedoraproject.org/wiki/Features/EasierPythonDebugging)
