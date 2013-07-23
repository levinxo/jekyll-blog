---
author: levin
comments: true
date: 2012-12-16 16:12:53+00:00
layout: post
slug: windows-xp-works-with-centos
title: Windows XP下硬盘安装第二系统CentOS
categories:
- Unix/Linux
tags:
- linux
---

老是在虚拟机下玩linux感觉不够畅快，碰到问题经常没先自己想想，立刻返回Windows下google之，感觉这样几乎没进步，总是抱着无所谓的态度，是学不到东西的。于是打算把E盘删了，将CentOS安装为第二系统(不知道是不是因为ATI显卡的原因在安装完毕以后启动系统直接花屏，此问题尚未解决，后来按照下面的方法(稍稍有点出入)安装Debian成功了)。<!-- more -->

先进行准备工作：
1.删除一个不要的分区，此处我删的是E盘，一定要记得先转移备份数据哦
2.CentOS-6.3-i386-minimal.iso
3.grub4dos-0.4.4.zip (此项目已从sourceforge转移到了gna.org：http://download.gna.org/grub4dos/)
4.一个FAT32文件系统的分区，一般是C盘(我的C盘是NTFS，只能找个U盘来替代了)，用于存放安装的镜像文件。

然后工作开始：

将CentOS-6.3-i386-minimal.iso的isolinux文件夹里的文件解压到C盘下的CentOS文件夹里

把grub4dos-0.4.4.zip解压后将下面的grldr、grldr.mbr、menu.lst这三个文件复制到C盘根目录下。打开menu.lst文件编辑加入下面这三行代码：

        title Install CentOS
        kernel (hd0,0)/CentOS/vmlinuz
        initrd (hd0,0)/CentOS/initrd.img

取消C盘下的boot.ini文件的只读属性，在最后加上

        C:\grldr="Start GRUB4DOS"

保存文件，修改只读属性回去，如果这几步出错(比如将grldr、grldr.mbr、menu.lst这三个文件放入文件夹内，或者在boot.ini的代码写错文件名字)，可能导致启动grub时发生hal.dll missing的错误

将CentOS-6.3-i386-minimal.iso解压了，整个文件夹拷到任何一个FAT32文件系统的分区下，到此注意，还应将CentOS-6.3-i386-minimal.iso这个文件放进这个解压好的文件夹里(不然安装的时候会提示：Missing ISO 9660 Image 的错误)，我是放在了U盘(C盘是NTFS格式的，囧)。

准备工作搞定，重启电脑吧。开机以后会提示进入XP和grub4dos，选择grub4dos，再选择Install CentOS选项。然后就是选择语言，地区等等，此时会提示需要安装文件包，即之前我们解压安装包的那个FAT32分区和文件夹所在的位置，例如C盘一般就是sda1，sda2就是D盘了，sdb1一般是u盘，选好分区，再键入安装包文件夹的名字CentOS-6.3-i386-minimal，点确定，就出现熟悉的安装界面了。

关于分区，因为要装2个系统，所以分区要特别谨慎，选择最后一项自定义布局。在进入界面以后的Hard Drives下的sda里应该会看到free，确认好此处就是之前删除的E盘，在其上面新建分区，挂载/boot，100M大小即可，ext2和ext3文件系统均可。

接着在剩余的free处建立一个LVM类型的分区。File System Type选择physical volume (LVM)。接着在此上创建一个LVM Volume Group，其Physical Extent设置为4MB(一般默认就是4MB)。完成以后在此LVM Volume Group上建立并挂载各种需要的分区：swap、/、/home、/var。更详细的分区可参照：
http://share.blog.51cto.com/278008/174774

分区完毕，就可以开始安装了，选择默认的MBR记录，XP或者CentOS均可，接着就等待安装完成吧。

//end.
