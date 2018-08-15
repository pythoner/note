昨天发现linux下的netbeans无法正常启动， 查看/var/log/messages log发现错误信息如下

```
[lee@lee log]$ sudo tail -f /var/log/messages
[sudo] password for lee:
Aug 10 09:16:42 lee gnome-software-service.desktop: 01:16:42:0711 Gs  no app for changed window-list@gnome-shell-extensions.gcampax.github.com
Aug 10 09:17:06 lee journal: No devices in use, exit
Aug 10 09:17:34 lee netbeans-8.2.desktop: Cannot find java. Please use the --jdkhome switch.
Aug 10 09:17:48 lee dbus[854]: [system] Activating via systemd: service name='net.reactivated.Fprint' unit='fprintd.service'
Aug 10 09:17:48 lee systemd: Starting Fingerprint Authentication Daemon...
Aug 10 09:17:48 lee dbus[854]: [system] Successfully activated service 'net.reactivated.Fprint'
Aug 10 09:17:48 lee fprintd: Launching FprintObject
Aug 10 09:17:48 lee journal: D-Bus service launched with name: net.reactivated.Fprint
Aug 10 09:17:48 lee journal: entering main loop
Aug 10 09:17:48 lee systemd: Started Fingerprint Authentication Daemon.
Aug 10 09:18:01 lee netbeans-8.2.desktop: Cannot find java. Please use the --jdkhome switch.
Aug 10 09:18:04 lee nautilus-classic.desktop: [2973:2973:0810/091804.118834:ERROR:gcm_channel_status_request.cc(127)] GCM channel request failed.

```

**Cannot find java. Please use the --jdkhome switch.**, 这里肯定跟我们的java有关。
查看我们的netbeans.conf配置文件
```
#netbeans_jdkhome="/home/lee/netbeans-8.2/bin/jre"

netbeans_jdkhome="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.171-8.b10.el7_5.x86_64/jre"

```

查看/usr/lib/jvm目录下，安装的java.
```
[lee@lee jvm]$ ls -l
total 0
drwxr-xr-x. 3 root root 16 Feb 15  2017 java-1.7.0-openjdk-1.7.0.121-2.6.8.0.el7_3.x86_64
drwxr-xr-x. 3 root root 16 Mar 13 17:32 java-1.7.0-openjdk-1.7.0.131-2.6.9.0.el7_3.x86_64
drwxr-xr-x. 3 root root 16 Aug  9 14:59 java-1.7.0-openjdk-1.7.0.171-2.6.13.0.el7_4.x86_64
drwxr-xr-x. 4 root root 95 Aug  9 14:51 java-1.7.0-openjdk-1.7.0.191-2.6.15.4.el7_5.x86_64
drwxr-xr-x. 3 root root 16 Feb  8  2017 java-1.8.0-openjdk-1.8.0.121-0.b13.el7_3.x86_64
drwxr-xr-x. 3 lee  lee  16 Jan 18  2018 java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64
drwxr-xr-x. 3 lee  lee  16 Aug  9 14:58 java-1.8.0-openjdk-1.8.0.171-8.b10.el7_5.x86_64
drwxr-xr-x. 3 root root 30 Aug  9 14:54 java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64
lrwxrwxrwx. 1 root root 21 Aug  9 14:59 jre -> /etc/alternatives/jre
lrwxrwxrwx. 1 root root 27 Aug  9 14:59 jre-1.7.0 -> /etc/alternatives/jre_1.7.0
lrwxrwxrwx. 1 root root 35 Aug  9 14:59 jre-1.7.0-openjdk -> /etc/alternatives/jre_1.7.0_openjdk
lrwxrwxrwx. 1 root root 54 Aug  9 14:52 jre-1.7.0-openjdk-1.7.0.191-2.6.15.4.el7_5.x86_64 -> java-1.7.0-openjdk-1.7.0.191-2.6.15.4.el7_5.x86_64/jre
lrwxrwxrwx. 1 root root 27 Aug  9 14:59 jre-1.8.0 -> /etc/alternatives/jre_1.8.0
lrwxrwxrwx. 1 root root 35 Aug  9 14:59 jre-1.8.0-openjdk -> /etc/alternatives/jre_1.8.0_openjdk
lrwxrwxrwx. 1 root root 51 Feb  8  2017 jre-1.8.0-openjdk-1.8.0.121-0.b13.el7_3.x86_64 -> java-1.8.0-openjdk-1.8.0.121-0.b13.el7_3.x86_64/jre
lrwxrwxrwx. 1 root root 51 Aug  9 14:52 jre-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64 -> java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
lrwxrwxrwx. 1 root root 29 Aug  9 14:59 jre-openjdk -> /etc/alternatives/jre_openjdk
[lee@lee jvm]$ pwd
/usr/lib/jvm

```
查看我们环境中配置的java-1.8.0-openjdk-1.8.0.171-8.b10.el7_5.x86_64
```
[lee@lee jvm]$ tree java-1.8.0-openjdk-1.8.0.171-8.b10.el7_5.x86_64
java-1.8.0-openjdk-1.8.0.171-8.b10.el7_5.x86_64
└── jre
    └── lib
        └── security
            ├── local_policy.jar
            ├── policy
            │   ├── limited
            │   └── unlimited
            └── US_export_policy.jar

6 directories, 2 files

```
发现我们这个目录下的好多文件都被删除的。

我们在查看一下对应最新版本*java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64*的目录
```
[lee@lee jvm]$ tree java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64 -L 3
java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64
├── jre
│   ├── bin
│   │   ├── java
│   │   ├── jjs
│   │   ├── keytool
│   │   ├── orbd
│   │   ├── pack200
│   │   ├── policytool
│   │   ├── rmid
│   │   ├── rmiregistry
│   │   ├── servertool
│   │   ├── tnameserv
│   │   └── unpack200
│   └── lib
│       ├── amd64
│       ├── applet
│       ├── calendars.properties
│       ├── charsets.jar
│       ├── classlist
│       ├── cmm
│       ├── content-types.properties
│       ├── currency.data
│       ├── ext
│       ├── flavormap.properties
│       ├── hijrah-config-umalqura.properties
│       ├── images
│       ├── jce.jar
│       ├── jexec
│       ├── jsse.jar
│       ├── jvm.hprof.txt
│       ├── logging.properties
│       ├── management
│       ├── management-agent.jar
│       ├── meta-index
│       ├── net.properties
│       ├── psfontj2d.properties
│       ├── psfont.properties.ja
│       ├── resources.jar
│       ├── rt.jar
│       ├── security
│       ├── sound.properties
│       └── tzdb.dat -> /usr/share/javazi-1.8/tzdb.dat
└── src.zip
```

这个才是我们需要的， 更改netbeans.conf文件， 设置jdkhome为*java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64*
```
netbeans_jdkhome="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre"
```

再次启动netbeans, 可以正常启动了。 问题解决



###### 是什么造成了java升级了呢？

查询history log， 发现执行了一次"yum update"...
