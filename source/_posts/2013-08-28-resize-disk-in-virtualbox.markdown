---
layout: post
title: "resize_disk_in_virtualbox"
date: 2013-08-28 17:21
comments: true
categories: misc
---

###resize disk in virtualbox
首先我们要在VirtualBox里面扩容我们的镜像。
VirtualBox本身有自带的command tool，利用它们可以很方便的配置镜像。

进入你的镜像目录，找到.vdi文件，执行下列命令：

```bash
VBoxManage modifyhd Centos-64bit-disk.vdi --resize 18432
＃18432 ＝ 18 ＊ 1024, resize to 18G
```

Note： 如果你的文件是.vmdk格式，那么需要先转换成.vdi,
然后替换掉原来的硬盘镜像。
```bash
VBoxManage clonehd Centos-64bit-disk1.vmdk Centos-64bit-disk1.vdi --format vdi
```

至此，你的disk image已经在VirtualBox中被扩展到18G，下一步就是要让Centos扩容。

###apply resize in Centos
要在Centos里面扩容，首先要知道你的分区情况。

Centos 6.4默认的分区方式是两个：
/dev/sda1挂载了/boot，大小约为500M，
剩余的硬盘都在/dev/sda2，由LVM统一管理。

LVM（Logical Volume Manager）建立在硬盘和分区之上的一个逻辑层，来提高磁盘分 区管理的灵活性.

默认的LVM有两个分区，一个是vg_livedvd-lv_root，数据都在这里面，另一个是vg_livedvd-lv_swap，用以缓存。

所以，在Centos扩容，首先要扩展分区容量，然后在扩展LVM的容量。

分区容量可以使用[gparted](http://gparted.sourceforge.net)来扩容。
将分区容量扩大后，就可以开始处理LVM。

打开虚拟机，输入命令：

```bash
lvextend -L15G /dev/mapper/vg_livedvd-lv_root
#extend vg_livedvd-lv_root to 15G

resize2fs vg_livedvd-lv_root
```

这样子vg_livedvd-lv_root就变成了15G大小了。


