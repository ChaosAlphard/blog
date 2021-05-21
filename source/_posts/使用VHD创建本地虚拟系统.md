---
title: 使用VHD创建本地虚拟系统
date: 2018-05-20 10:30:12
tags: [OS]
---

# VHD/VHDX简介
## 什么是VHD
VHD 是Microsoft Virtual Hard Disk format（微软虚拟磁盘文件）的简称。可以由Windows Vista/7/8/10，Windows Server 2008R2 等系统创建。  
一个VHD文件可以被虚拟为一块硬盘，并且可以像操作一块硬盘一样操作VHD文件，例如：读取、写入、创建分区、格式化等等。

## 什么是VHDX
随着虚拟环境的企业工作负荷的增加以及性能要求的提高，虚拟硬盘 (VHD) 格式需要适应这些变化。因此，Windows Server 2012 中的 Hyper-V 引入了一个新版本的VHD格式，称为VHDX。  
与旧的VHD格式相比，VHDX具有更大的存储容量。它优化了动态磁盘和差异磁盘的结构对齐方式，以防止在新的大型扇区物理磁盘上性能降级。同时还能在电源故障期间提供数据损坏保护。

VHDX格式的主要新功能如下：
- 支持的虚拟硬盘的存储容量高达 64 TB。
- 通过记录对VHDX元数据结构的更新，可以在电源发生故障时保护数据不会被损坏。
- 改进了虚拟硬盘格式的对齐方式，可在大型扇区磁盘上更好地工作。

VHDX 格式还提供以下功能：
- 动态磁盘和差异磁盘有较大的数据块大小，可让这些磁盘满足工作负荷的需求。
- 拥有4KB的逻辑扇区，可以在为4KB扇区设计的应用程序和作负荷使用该磁盘时提供较高的性能。
- 能够存储有关用户可能想记录的文件的自定义元数据，如操作系统版本或应用的修补程序。
- 高效地表示数据（也称为“剪裁”），使文件大小更小并且允许基础物理存储设备回收未使用的空间。（剪裁需要直接连接到虚拟机或 SCSI 磁盘的物理磁盘以及与剪裁兼容的硬件。)

<!-- more -->

## VHD/VHDX的优点
- 维护简单
  - 操作VHD就和操作物理硬盘一样，我们可以对其进行分区、格式化、压缩、删除等等操作，并且这些操作并不会影响你的物理分区。

- 加载自如
  - VHD磁盘可以进行分离操作，这样在磁盘管理中就无法找到VHD磁盘分区。如果想要再次挂载，只需对VHD磁盘文件进行挂载操作即可。

- 轻松备份
  - 备份时仅需要将 .vhd/.vhdx 文件进行备份，文件中所包含的所有信息(包括分区表等信息)就被备份下来了。

- 迁移方便
  - 只需要将 .vhd/.vhdx 文件复制到目标计算机上，再进行附加操作即可完成迁移。也可以通过服务器进行分发。也可以在物理机和虚拟机之间进行迁移。

# VHD系统的特点
## 安装简便
VHD系统安装非常简单，到了Windows10系统更是如此，甚至可以从别的电脑上拷一个Windows10的VHD来启动，类似于即插即用。

## 性能强劲
VHD系统如同真实的系统一样，可以直接使用计算机的物理硬件，性能与真实系统相差无几，这与虚拟机有明显的差别。虚拟机使用虚拟出来的硬件，VHD系统直接使用物理硬件。

## 备份方便
直接将 .vhd/.vhdx 文件复制一份，就完成了备份工作。  
此外，VHD还提供了更为方便的备份技术——“差分磁盘”。使用差分磁盘，即可实现系统的“秒备份”，“秒还原”。

## 删除方便
当你不想使用VHD系统时，只需要将对应的 .vhd/.vhdx 文件删除，再将对应的引导条目删除即可。

# 如何部署VHD系统
如果你从网络上或者你朋友那里获得了部署有Windows10系统的vhd/vhdx文件，只需将其拷入你的计算机，然后添加引导项即可完成VHD系统的部署。

如果没有也没关系，下面将会介绍如何从零开始部署VHD系统

## 创建VHD/VHDX文件
**以下命令均在Windows10环境下进行，Windows7可能略有不同**

使用Windows磁盘管理创建

创建vhdx文件  
![](https://s2.ax1x.com/2019/06/16/V7kaxH.jpg)

设置大小  
![](https://s2.ax1x.com/2019/06/16/V7kwMd.jpg)

初始化磁盘，GPT还是MBR看个人情况，推荐选GPT格式  
![](https://s2.ax1x.com/2019/06/16/V7k0sA.jpg)

新建分区  
![](https://s2.ax1x.com/2019/06/16/V7kBqI.jpg)

完成


使用命令行创建
```bash
#启动diskpart工具
diskpart

#创建一个大小为25600MB的固定大小的vhdx文件
#如果想创建动态拓展大小的vhdx文件，换成type=expandable即可
create vdisk file=E:\vhd.vhdx maximum=25600 type=fixed

#选中刚才创建的vhdx文件
select vdisk file=E:\vhd.vhdx

#挂载硬盘
attach vdisk

#创建主分区
create partition primary

#设置卷标
assign letter=V

#格式化分区
format quick label=vhdx

exit
```
完成

## 部署Windows系统到VHD虚拟磁盘
**首先将vhdx文件挂载到系统上，如果已挂载则可跳过这一步**
```bash
diskpart

select vdisk file=E:\vhd.vhdx

attach vdisk

exit
```

**将系统部署到vhdx文件中**

下载.iso镜像文件或者.wim映像文件。  
如果是.iso镜像文件则将其解压或者挂载到虚拟光驱。
```bash
#获得镜像信息
Dism /Get-ImageInfo /imagefile:D:\Windows10\sources\install.wim

#部署镜像到vhdx中
Dism /apply-image /imagefile:D:\Windows10\sources\install.wim /index:1 /ApplyDir:V:\
```
等待部署完成

参数解释：  
imagefile: install.wim文件所在的位置  
index: 系统版本所对应的索引路径  
ApplyDir: 要部署镜像的驱动器盘符

## 添加引导项
```bash
#将V盘的系统添加到引导项，V即为你vhd虚拟磁盘的盘符
bcdboot V:\Windows /l zh-CN /d
```
如果你是在PE下进行的，则需要为ESP分区分配盘符
```bash
bcdboot V:\Windows /s Z: /f uefi /l zh-cn /d
```
参数解释：  
/l: 语言  
/s: 要将引导文件复制到的磁盘的盘符，通常是ESP分区所在位置  
/f: 引导方式  
/d: 保留现在的默认启动条目

使用bcdedit指令来查看引导项，或者用msconfig来查看引导项。  
```bash
\>bcdedit

Windows 启动管理器
--------------------
标识符                  {bootmgr}
device                  partition=\Device\HarddiskVolume1
path                    \EFI\Microsoft\Boot\bootmgfw.efi
description             Windows Boot Manager
locale                  zh-CN
inherit                 {globalsettings}
default                 {default}
resumeobject            {3fa12b79-202e-11e8-8e30-ddc8fe2935d0}
displayorder            {default}
                        {current}
toolsdisplayorder       {memdiag}
timeout                 5

Windows 启动加载器
-------------------
标识符                  {current}
device                  partition=C:
path                    \Windows\system32\winload.efi
description             SSD Win10
locale                  zh-CN
inherit                 {bootloadersettings}
recoverysequence        {3fa12b6f-202e-11e8-8e30-ddc8fe2935d0}
recoveryenabled         Yes
isolatedcontext         Yes
allowedinmemorysettings 0x15000075
osdevice                partition=C:
systemroot              \Windows
resumeobject            {3fa12b79-202e-11e8-8e30-ddc8fe2935d0}
nx                      OptIn
bootmenupolicy          Standard

Windows 启动加载器
-------------------
标识符                  {3fa12b75-202e-11e8-8e30-ddc8fe2935d0}
device                  partition=E:
path                    \Windows\system32\winload.efi
description             Virtual Win10
locale                  zh-CN
inherit                 {bootloadersettings}
isolatedcontext         Yes
allowedinmemorysettings 0x15000075
osdevice                partition=E:
systemroot              \Windows
resumeobject            {3fa12b75-202e-11e8-8e30-ddc8fe2935d0}
nx                      OptIn
bootmenupolicy          Standard
```

使用`bcdedit /set`指令来为启动项命名
```bash
#bcdedit /set {标识符} description "要更改的启动项名称"
bcdedit /set {3fa12b75-202e-11e8-8e30-ddc8fe2935d0} description "VW10"
```

取消挂载，重启电脑，引导进入VHD系统即可
```bash
diskpart

select vdisk file=E:\vhd.vhdx

detach vdisk

exit
```

# 使用差分磁盘进行备份和还原
## 差分磁盘如何实现“秒备份”，“秒还原”
差分磁盘是指基于父盘的子盘，子盘能读取父盘的所有数据，同时对子盘的所有改动不会影响到父盘。  
因此，一旦建立了子盘，就相当于为父盘建立了一个还原点，由于子盘自身不带数据，所以创建非常快，大小非常小(4MB左右)，子盘的大小会随着子盘的使用逐渐增大。  
同时，如果想要还原，只需要再次为父盘创建一个子盘即可，原来的子盘同样可以使用或删除。

基于此，使用差分磁盘可以使用最少的空间，创建多个系统，供不同的人使用。并且备份和还原对硬盘的损伤也降到了最低。

**创建差分磁盘**
```bash
diskpart
create vdisk file=E:\VDisk\child01.vhdx parent=E:\VDisk\vhd.vhdx
exit
```
参数解释：  
file: 创建的子盘的位置和名称  
parent: 父盘的位置

**将子盘添加到启动项**
```bash
diskpart
select vdisk file=E:\VDisk\child01.vhdx
attach vdisk
exit
bcdboot V:\Windows /l zh-CN /d
```
子盘的启动项名称同样可以通过`bcdedit /set`指令更改。

由于子盘是基于父盘的数据创建的，创建完毕的子盘和父盘的数据是一模一样的，但是父盘内的数据就无法修改，否则会导致子盘数据错误，无法打开。  
因此，将父盘的vhdx文件设为“只读”属性，同时在Win+R的运行框中输入msconfig，进入启动项管理，将父盘的启动项删除。

以后进行还原操作时，只需要将原本的子盘删除，创建一个新子盘，再将其添加到启动项即可。你也可以将指令写为bat批处理，双击执行。

```bash
del E:\VDisk\child01.vhdx
diskpart
    create vdisk file=E:\VDisk\child01.vhdx parent=E:\VDisk\vhd.vhdx
    select vdisk file=E:\VDisk\child01.vhdx
    attach vdisk
exit
bcdboot V:\Windows /l zh-CN /d
```