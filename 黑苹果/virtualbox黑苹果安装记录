### virtualbox黑苹果安装记录


##### 环境情况

* centos 7.4
```
[lee@lee ~]$ uname -a
Linux lee 3.10.0-693.21.1.el7.x86_64 #1 SMP Wed Mar 7 19:03:37 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
[lee@lee ~]$ cat /etc/redhat-release
CentOS Linux release 7.4.1708 (Core)
```

* virtualbox 5.1
```
[lee@lee ~]$ vboxmanage -v
5.1.14r112924
```

* macos  **Mac OSX 10.9.5.cdr**
```
[lee@lee iso]$ ls -l
total 17179040
-rw-r--r--. 1 qemu qemu 4148166656 Feb  9  2017 CentOS-7.0-1406-x86_64-DVD.iso
-rw-rw-r--. 1 lee  lee  6237523658 Apr 12 10:32 Clover USB install EI Capitan 10.11.4[15E65].dmg
-rw-rw-r--. 1 lee  lee     7626752 Apr 18 17:46 HackBoot_Mav.iso
-rw-rw-r--. 1 lee  lee  6574112768 Apr 18 16:41 Mac OSX 10.9.5.cdr
-rw-rw-r--. 1 qemu qemu  623902720 Feb  9  2017 ubuntu-14.04.2-server-amd64.iso
```

##### 安装中需要注意的事项

* 在virtualbox的system中要将Chipset设置为**PIIX3**
* 需要勾选**Enable EFI(special OSes only)**
![](./images/system settings.png)

* 需要对象应的虚拟机进行如下设置.
```
vboxmanage modifyvm <vmname> --cpuidset 00000001 000106e5 00100800 0098e3fd bfebfbff
vboxmanage setextradata <vmname>  "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1
```
上述中的第二条是为了解决下图中的问题
![](./images/lpc device initialization failed.png)

> 1. 第一条修改cpuidset的不确定是否有效， 没有验证。
> 2. 可以通过 VBoxManage setextradata <vmname> "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC"来取消key的设置。 即把后面的value去掉

##### 备注
网上源于修改这个还有很多， 目前这是验证第二条是有效的.
```
VBoxManage.exe modifyvm <vmname> --cpuidset 00000001 000106e5 00100800 0098e3fd bfebfbff
VBoxManage setextradata <vmname> "VBoxInternal/Devices/efi/0/Config/DmiSystemProduct" "iMac11,3"
VBoxManage setextradata <vmname> "VBoxInternal/Devices/efi/0/Config/DmiSystemVersion" "1.0"
VBoxManage setextradata <vmname> "VBoxInternal/Devices/efi/0/Config/DmiBoardProduct" "Iloveapple"
VBoxManage setextradata <vmname> "VBoxInternal/Devices/smc/0/Config/DeviceKey" "ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc"
VBoxManage setextradata <vmname> "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1

```
