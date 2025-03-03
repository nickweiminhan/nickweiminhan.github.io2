---
title: "Arch Linux 无图形化操作系统安装指南"
subtitle: "Arch Linux 无图形化操作系统安装指南"
layout: post
author: "Hux"
header-style: text
hidden: true
tags:
  - SF (软件基础)
  - QC (Quickcheck)
  - Coq
  - 笔记
---



在本文开始之前，首先允许我提前声明一点，Arch Linux的安装并不算难，但是绝对也算不上简单，中间的安装可能会遇到很多问题，本篇文章不能保证完全贴合你的真实机器环境，但是我会在我安装过程中遇到的一点点小问题都说出来，给大家避个坑，那么接下来，介绍一下本文用到的安装环境：

- Windows
- VMware虚拟机
- Vmware的NAT网络连接模式

至于在个人电脑上安装 Arch Linux 与虚拟机安装不同的地方，我会当前步骤前后标注出来。

毕竟是安装操作系统，第一步肯定获得Arch Linux操作系统的镜像包，关于其镜像包，由于官网给我们的是磁链和BT文件，所以网络环境和软件环境好的同学可以使用官网的下载方式。条件不允许的同志，可以选择使用镜像站来下载；这里贴出官网的下载方式和镜像站：

- 官网BT：https://archlinux.org/releng/releases/2025.03.01/torrent/
- 清华源镜像站：https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/2025.03.01/

下载完成后，还需要在 archlinux 下载页面（https://archlinux.org/iso/2025.03.01/archlinux-2025.03.01-x86_64.iso.sig）下载`PGP signature`签名文件(不要从镜像源下载签名文件)，将签名文件和 iso 镜像置于同一文件夹，随后进行对镜像的签名校验，以保证下载的镜像是完整，无错误的，未被篡改的。若你使用 Linux，执行以下命令，确保输出完好的签名。具体镜像名根据名字自行修改。

```
gpg --keyserver-options auto-key-retrieve --verify archlinux-2025.03.01-x86_64.iso.sig

BASH
```

如果你使用其他系统，请自行搜索验证签名的方式。注意，这里的签名校验**非常重要**，这可以保证你的安装镜像是未被篡改的，同时可以保证你在使用安装盘安装系统时，用正确的公钥校验安装包。

## 01. 新建虚拟机

新建虚拟机的过程很简单，这里不过多赘述，主要注意的几个点已经在下图中标注，这里磁盘的大小仅为参考，一般来说个人日常使用的 linux 分配 100G 已经够用了。如果你的存储资源有限，那么最小建议不小于 50G，磁盘过小会造成无法更新系统软件包等问题。

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171153348.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171153348.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171312094.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171312094.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171341272.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171341272.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171412011.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171412011.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171624083.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171624083.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171703559.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171703559.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171755021.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171755021.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302171851824.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302171851824.png)

注意：在安装之前，首先不要急着启动 Arch，我们先到 Arch 虚拟机的设置中修改一下，使用UEFI来引导系统启动。（由于当前 UEFI 已普及十余年，安装将全部以 UEFI+GPT 的形式进行，传统 BIOS 方式不再赘述。）

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302180856032.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302180856032.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302181110500.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302181110500.png)

看到这里，使用虚拟机的同学就可以准备启动 Arch 进行接下来的安装了。而对于需要在 PC 端安装的同学还需要以下准备：

1. 确保网络环境
   如果你可以使用路由器分接出来的网线，以 dhcp 的方式直接上网，那么不用准备什么。如果你的环境只能使用无线网络安装，需要事先把自己所用的 wifi 名称改成自己能记住的英文名称。因为安装时无法显示和输入中文名的 wifi，你会看到一堆不知道是什么的方块，并且在安装过程中你将没有办法输入中文的无线名称进行连接。虽然通过一些繁琐的步骤可以解决终端中文的问题，但是显然这么做在安装 Arch Linux 时毫无必要。
   其次，有些笔记本电脑上存在无线网卡的硬件开关或者键盘控制，开机后安装前需要确保你的无线网卡硬件开关处于打开状态。
2. 刻录启动优盘
   准备一个 2G 以上的优盘，刻录一个安装启动盘。Windows 下推荐使用ventoy或者Rufus或者etcher进行优盘刻录。三者皆为自由软件。Linux 下可以直接用 dd 命令进行刻录。注意 of 的参数为 sdx,不是 sdx1 sdx2 等。

```
sudo dd bs=4M if=/path/to/archlinux.iso of=/dev/sdx status=progress oflag=sync

BASH
```

> bs=4M 指定一个较为合理的文件输入输出块大小。
> status=progress 用来输出刻录过程总的信息。
> oflag=sync 用来控制写入数据时的行为特征。确保命令结束时数据及元数据真正写入磁盘，而不是刚写入缓存就返回。

1. 进入主板 BIOS 进行设置
   插入优盘并开机。在开机的时候，按下 F2/F8/F10/DEL 等(取决与你的主板型号，具体请查阅你主板的相关信息)按键，进入主板的 BIOS 设置界面。
2. 关闭主板设置中的 Secure Boot
   在类似名为 security 的选项卡中，找到一项名为 Secure Boot(名称可能略有差异)的选项，选择 Disable 将其禁用。
3. 调整启动方式为 UEFI
   在某些旧的主板里，需要调整启动模式为 UEFI,而非传统的 BIOS/CSM。在类似名为 boot 的选项卡中，找到类似名为 Boot Mode 的选项，确保将其调整为 UEFI only，而非 legacy/CSM。
4. 调整硬盘启动顺序
   在类似名为 boot 的选项卡中，找到类似名为 Boot Options(名称可能略有差异)的设置选项，将 USB 优盘的启动顺序调至首位。
5. 准备安装
   最后保存 BIOS 设置并退出，一般的按键是 F10。此时系统重启，不出意外你应该已经进入 archlinux 的安装界面。

## 02. Arch Linux 基础安装

本节从安装最基础的无图形化 ArchLinux 系统开始。（官方指南：https://wiki.archlinux.org/title/Installation_guide）

开机后应显示如下界面：

[![image-20250302183935600](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302183935600.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302183935600.png)

image-20250302183935600



1. 检查 SSH 服务是否开启
   我们安装Arch的时候是完全被允许使用SSH来远程连接的，关于我为什么要提这个问题，因为我们安装的时候是无法在虚拟机中粘贴的，如果我们能够使用物理机Shell工具远程连接到Arch的安装进程上，就可以正常的来粘贴命令了，这样的话将会起到事半功倍的效果。如果你没有相关的工具，那么也不影响后续的操作；如果你有，请使用[root@xxx.xxx.xxx.xxx](mailto:root@xxx.xxx.xxx.xxx)来进行连接，ip a命令可以查看 Arch 的 IP，passwd 可以修改 root 的密码。闲话少说，基本的配置完成，我们还是步入正轨来安装Arch吧。

```
systemctl status sshd
#若没有开启，便将其开启
systemctl start sshd
systemctl enable sshd
#查看 IP
ip a
#修改 root 密码
passwd root
#此时便可以远程登陆了

BASH
```

1. 禁用 reflector

   reflector 会为你选择速度合适的镜像源，但其结果并不准确，同时会清空配置文件中的内容，对于新人来讲并不适用，我们首先对其进行禁用。

   ```
   systemctl stop reflector.service
   
   BASH
   ```

2. 再次确保是否为 UEFI 模式

   在一系列的信息刷屏后，可以看到已经以 root 登陆安装系统了，此时可以执行的命令：

   ```
   ls /sys/firmware/efi/efivars
   
   BASH
   ```

   若输出了一堆东西，即 efi 变量，则说明已在 UEFI 模式。否则请确认你的启动方式是否为 UEFI。

[![image-20250302185140436](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302185140436.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302185140436.png)

image-20250302185140436



1. 连接网络

   对于使用虚拟机的同学，curl 百度进行测试，如果能够输出东西，有一堆HTML标签等等，说明网络无异常。对于在 PC 端安装的同学，还需要以下步骤：

   一般来说，你连接的网络几乎均可以通过 DHCP 的方式来进行 IP 地址和 DNS 的相关设置，你无需进行额外操作。在没有合适网络的情况下，使用手机的移动热点也是很方便的选择。如果你的网络环境需要配置静态 IP 和 DNS,请自行参考 Arch Wiki。

   对于有线连接来说，直接插入网线即可。

   对于无线连接，则需进行如下操作进行网络连接。

   无线连接使用 iwctl 命令进行，按照如下步骤进行网络连接：

   ```
   iwctl                           #执行iwctl命令，进入交互式命令行
   device list                     #列出设备名，比如无线网卡看到叫 wlan0
   station wlan0 scan              #扫描网络
   station wlan0 get-networks      #列出网络 比如想连接YOUR-WIRELESS-NAME这个无线
   station wlan0 connect YOUR-WIRELESS-NAME #进行连接 输入密码即可
   exit                            #成功后exit退出
   
   BASH
   ```

   可以等待几秒等网络建立链接后再进行下面测试网络的操作。

   ```
   ping www.baidu.com
   
   BASH
   ```

   **如果**你不能正常连接网络，首先确认系统已经启用网络接口

   ```
   ip link  #列出网络接口信息，如不能联网的设备叫wlan0
   ip link set wlan0 up #比如无线网卡看到叫 wlan0
   
   BASH
   ```

   **如果**随后看到类似`Operation not possible due to RF-kill`的报错，继续尝试`rfkill`命令来解锁无线网卡。

   ```
   rfkill unblock wifi
   
   BASH
   ```

2. 更新系统时钟

   系统时间的准确是十分重要的，我们的 SSL 证书等等以及例如 OTP 等等很多服务，都是要建立在时间准确的前提下的，所以这一步是至关重要的。使用命令同步系统时间：

   ```
   timedatectl set-ntp true    #将系统时间与网络时间进行同步
   timedatectl status          #检查服务状态
   
   BASH
   ```

   [![image-20250302190147555](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302190147555.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302190147555.png)
   由于我们国家处于东八区，这里的Time zone给出的是UTC+0，所以只要将其时间加上8H即可得出我们的时间，如果计算后的时间和我们的实际时间基本相吻合，那么这里就可以继续操作了

3. 分区

   由于这里我们是使用新开虚拟机操作，顾虑不用很多，不会刷写到我们物理机的磁盘。但是在这里我还是提一嘴，数据无价！

   这里总共设置三个分区，是一个**我们认为**较为通用的方案。此步骤会清除磁盘中全部内容，请事先确认。

   - EFI 分区： `/efi` 800M
   - Swap分区：2.4GB（这个分区的大小可以是你运行内存大小的60%左右，这里我是分了4G的运存给虚拟机，所以就是4G*0.6=2.4G）
   - 根分区： `/` 剩余全部

   首先将磁盘转换为 gpt 类型，这里假设比如你想安装的磁盘名称为 sdx。如果你使用 NVME 的固态硬盘，你看到的磁盘名称可能为 nvme0n1。

   ```
   lsblk                       #显示分区情况 找到你想安装的磁盘名称
   parted /dev/sdx             #执行parted，进入交互式命令行，进行磁盘类型变更
   (parted)mktable             #输入mktable
   New disk label type? gpt    #输入gpt 将磁盘类型转换为gpt 如磁盘有数据会警告，输入yes即可
   (parted)quit                        #最后quit退出parted命令行交互
   
   BASH
   ```

   接下来使用 cfdisk 命令对磁盘分区。进入 cfdisk 后的操作很直观，用键盘的方向键、Tab 键、回车键配合即可操作分配各个分区的大小与格式。一般建议将 EFI 分区设置为磁盘的第一个分区，据说有些主板如果不将 EFI 设置为第一个分区，可能有不兼容的问题。其中 EFI 分区选择`EFI System`类型，其余两个分区分别选择`Linux swap` 和`Linux filesystem`类型。

   ```
   cfdisk /dev/sdx #来执行分区操作,分配各个分区大小，类型
   fdisk -l #分区结束后， 复查磁盘情况
   
   BASH
   ```

   注意：最后一定要点击Write，以更改分区表，当下方提示“The partition table has been altered.”后，说明修改成功，然后点击Quit退出。

   [![image-20250302191902542](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302191902542.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302191902542.png)

4. 格式化

   建立好分区后，需要对分区用合适的文件系统进行格式化。这里用`mkfs.vfat`命令格式化 EFI 分区，使用`mkswap`命令格式化 swap 分区，用`mkfs.xfs`命令格式化根分区，。如下命令中的 sdax 中，x 代表分区的序号。格式化命令要与上一步分区中生成的分区名字对应才可以。

   磁盘若事先有数据，会提示你: ‘proceed any way?’ 按 y 回车继续即可。

   ```
   mkfs.vfat  /dev/sda1            #格式化efi分区
   mkswap  /dev/sda2            	#格式化swap分区
   mkfs.xfs -f /dev/sda3           #格式化根分区
   lsblk -f						#查看分区结果
   
   BASH
   ```

   [![image-20250302193124154](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302193124154.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302193124154.png)

5. 挂载分区

   在挂载时，挂载是有顺序的，先挂载根分区，再挂载 EFI 分区。 这里的 sdax 只是例子，具体根据你自身的实际分区情况来。

   ```
   mount /dev/sdax /mnt
   mkdir /mnt/efi     			#创建efi目录
   mount /dev/sdax /mnt/efi
   swapon /dev/sdax
   
   BASH
   ```

   [![image-20250302193704601](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302193704601.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302193704601.png)

6. 镜像源选择

   使用如下命令编辑镜像列表：

   ```
   vim /etc/pacman.d/mirrorlist
   
   BASH
   ```

   其中的首行是将会使用的镜像源。添加中科大或者清华的放在最上面即可。

   ```
   Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
   Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
   
   BASH
   ```

   如果其速度不佳，可以手动指定其他镜像源。完整的镜像源列表可参考官方[镜像源生成器](https://archlinux.org/mirrorlist/)。

   > 这里使用中国境内的镜像源以提高访问速度。然而这存在问题，镜像源(如 arch linux 清华镜像源)以及第三方源(如 archlinux-cn)可以知道你的 ip 是什么，什么时候更新了系统，什么时候检查了系统，什么时候更新了什么软件，你安装的软件列表是什么。在威权国家的镜像源维护者完全有可能根据威权当局的要求提供这些数据，很多维护者在网络上几乎是实名上网的，他们没有任何抵抗能力，进一步的，威权国家可以根据这些元数据与你产生的其他元数据进行比对，从而对你进行进一步的定位与辨识。简单举一个例子，要求维护者提供或监视安装了 v2ray/qv2ray 等软件包的使用者的 ip,以及安装时间，以及其全部软件列表。
   >
   > 如果你在安装 arch linux 时的网络已经处于代理模式下，可以选择一个与你代理位置较近的，非威权国家的镜像源来使用。如果你在安装 arch linux 时的网络环境没有代理，那么在安装结束后，需要尽快更换一个非威权国家的镜像源来使用。如下列举一些较为优质的国际源。
   >
   > ```
   > Server = https://mirror.archlinux.tw/ArchLinux/$repo/os/$arch   #东亚地区:中华民国
   > Server = https://mirror.0xem.ma/arch/$repo/os/$arch    #北美洲地区:加拿大
   > Server = https://mirror.aktkn.sg/archlinux/$repo/os/$arch    #东南亚地区:新加坡
   > Server = https://archlinux.uk.mirror.allworldit.com/archlinux/$repo/os/$arch    #欧洲地区:英国
   > Server = https://mirrors.cat.net/archlinux/$repo/os/$arch    #东亚地区:日本
   > 
   > BASH
   > ```

7. 安装系统

   必须的基础包

   ```
   pacstrap /mnt base base-devel linux linux-headers linux-firmware  #base-devel在AUR包的安装是必须的
   
   BASH
   ```

   > 注意，目前需要首先确保等待 pacman-init.service 服务启动后，才能执行 pacstrap 或 pacman 命令安装包，否则会引发错误使得安装过程无法进行。使用`systemctl status pacman-init.service`命令来检查当前服务状态。

   必须的功能性软件

   ```
   pacstrap /mnt dhcpcd iwd vim bash-completion   #一个有线所需(iwd也需要dhcpcd) 一个无线所需 一个编辑器 一个补全工具
   
   BASH
   ```

8. 生成 fstab 文件

fstab 用来定义磁盘分区

```
genfstab -U /mnt >> /mnt/etc/fstab

BASH
```

复查一下 /mnt/etc/fstab 确保没有错误

```
cat /mnt/etc/fstab

BASH
```

1. change root

   把环境切换到新系统的/mnt 下

   ```
   arch-chroot /mnt
   
   BASH
   ```

2. 时区设置

   设置时区，在/etc/localtime 下用/usr 中合适的时区创建符号连接。如下设置上海时区

   ```
   ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   
   BASH
   ```

   随后进行硬件时间设置，将当前的正确 UTC 时间写入硬件时间

   ```
   hwclock --systohc
   
   BASH
   ```

3. 设置 Locale 进行本地化

   Locale 决定了地域、货币、时区日期的格式、字符排列方式和其他本地化标准。

   首先使用 vim 编辑 /etc/locale.gen，去掉 en_US.UTF-8 所在行以及 zh_CN.UTF-8 所在行的注释符号（#）。

   ```
   vim /etc/locale.gen
   
   BASH
   ```

   然后使用如下命令生成 locale

   ```
   locale-gen
   
   BASH
   ```

   最后向 /etc/locale.conf 导入内容

   ```
   echo 'LANG=en_US.UTF-8'  > /etc/locale.conf
   
   BASH
   ```

4. 设置主机名

   首先在`/etc/hostname`设置主机名

   ```
   vim /etc/hostname
   
   BASH
   ```

   加入你想为主机取的主机名，这里比如叫 myarch。

   接下来在`/etc/hosts`设置与其匹配的条目。

   ```
   vim /etc/hosts
   
   BASH
   ```

   加入如下内容

   ```
   127.0.0.1   localhost
   ::1         localhost
   127.0.1.1   myarch
   
   BASH
   ```

   > 某些情况下如不设置主机名，在 KDE 下可能会存在网络情况变更时无法启动 GUI 应用的问题，在终端中出现形如`No protocol specified qt.qpa.xcb: could not connect to display`的错误，这种情况较为少见。

5. 安装微码

   ```
   pacman -S intel-ucode   #Intel
   pacman -S amd-ucode     #AMD
   
   BASH
   ```

6. 安装引导程序

   ```
   pacman -S grub efibootmgr   #grub是启动引导器，efibootmgr被 grub 脚本用来将启动项写入 NVRAM。
   grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
   
   BASH
   ```

   接下来编辑/etc/default/grub 文件，去掉`GRUB_CMDLINE_LINUX_DEFAULT`一行中最后的 quiet 参数，同时把 log level 的数值从 3 改成 5。这样是为了后续如果出现系统错误，方便排错。同时在同一行加入 nowatchdog 参数，这可以显著提高开关机速度。

   ```
   vim /etc/default/grub
   
   BASH
   ```

   [![image-20250302201304960](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302201304960.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302201304960.png)

   最后生成 GRUB 所需的配置文件

   ```
   grub-mkconfig -o /boot/grub/grub.cfg
   
   BASH
   ```

   > 对于使用 PC 端安装的同学：
   >
   > 使用 N 卡的同学需要注意，KDE6 默认使用 wayland session 为默认，如果你需要使用 wayland,则需开启 DRM。同样编辑/etc/default/grub 文件，在`GRUB_CMDLINE_LINUX_DEFAULT`一行中最后的加入参数：nvidia_drm.modeset=1
   >
   > 我们在之前的命令中指定了 bootloader-id 为 GRUB，这一般不会出现问题。然而在某些主板安装完成后，你会发现没有 nvme 启动条目。这是因为某些主板的 UEFI 固件在显示 UEFI NVRAM 引导条目之前，需要在特定的位置存放可引导文件，不支持自定义存放 efi 文件[[6\]](https://wiki.archlinux.org/index.php/GRUB#Default/fallback_boot_path)。解决方式是使用`--removable` 参数解决一些主板 NVRAM 的兼容性问题。
   >
   > ```
   > grub-install --target=x86_64-efi --efi-directory=/efi --removable
   > grub-mkconfig -o /boot/grub/grub.cfg
   > 
   > BASH
   > ```

   > 除此之外，如果你的主板是一些较老的型号，如 intel 9 系列以下或者较老 AMD 的主板，它们很可能不支持从 nvme 启动系统，虽然可以通过修改 BIOS 加入 NVME 支持模块来解决，但这不在本文讨论范围内。

7. 完成安装

   ```
   exit                # 退回安装环境#
   umount -R  /mnt     # 卸载新分区
   reboot              # 重启
   
   BASH
   ```

   使用虚拟机安装的同学，到此，无图形化 ArchLinux 系统安装完成。

   使用 PC 端安装的同学请往下看：

   注意，重启前要先拔掉优盘，否则你重启后还是进安装程序而不是安装好的系统。重启后，开启 dhcp 服务，即可连接网络

   ```
   systemctl start dhcpcd  #立即启动dhcp
   ping www.gnu.org      #测试网络连接
   
   BASH
   ```

   若为无线链接，则还需要启动 iwd 才可以使用 iwctl 连接网络

   ```
   systemctl start iwd #立即启动iwd
   iwctl               #和之前的方式一样，连接无线网络
   
   BASH
   ```

   > archlinux 在 2021 年 4 月在安装镜像中内置了一个[安装脚本](https://archlinux.org/packages/extra/any/archinstall/)，提供一些选项，即可快速安装。其和所有一键安装脚本类似，提供自动化，且不灵活的安装过程。不建议使用这种安装脚本，除了不灵活的原因，初学者也无法在这种安装过程中学到任何东西。如果你因为任何原因需要快速启动一个基础的 archlinux 环境，那么可以尝试此脚本。

最后的最后，既然是Arch，怎么能不运行fastfetch命令呢，我们使用pacman安装一下，安装之前，我们先来启动一下 Arch 的 NetworkManager 服务，否则Arch是没有网络的

```
systemctl start NetworkManager 	#启动NetworkManager
systemctl enable NetworkManager #将NetworkManager设置为开机自启
systemctl status NetworkManager	#查看NetworkManager服务状态

NSIS
```

安装fastfetch，使用pacman命令

```
pacman -S fastfetch	#安装
fastfetch  			#运行

NGINX
```

[![image-20250302203130971](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302203130971.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302203130971.png)

image-20250302203130971



到此为止，一个基础的，无 UI 界面的 Arch Linux 已经安装完成了。





