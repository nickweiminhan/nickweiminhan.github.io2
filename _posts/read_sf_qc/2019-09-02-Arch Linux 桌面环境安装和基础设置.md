---
title: "Arch Linux 桌面环境安装和基础设置"
subtitle: "Arch Linux 桌面环境安装和基础设置"
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



本文将介绍 Arch Linux 桌面环境和一些必要应用的安装，直入正题，在这一切之前，我们需要先确认NetworkManager 服务和 OpenSSH 服务的安装和开启：

```
pacman -S NetworkManager		#安装NetworkManager服务
pacman -S OpenSSH				#安装OpenSSH服务
systemctl start NetworkManager	#开启NetworkManager服务
systemctl start OpenSSH			#开启OpenSSH服务
systemctl enable NetworkManager	#设置NetworkManager服务开机自启
systemctl enable OpenSSH		#设置OpenSSH服务开机自启

BASH
```

## 01. 确保系统为最新

```
pacman -Syyu    #升级系统中全部包

BASH
```

## 02. 准备非 root 用户

添加用户，比如新增加的用户叫 testuser

```
useradd -m -G wheel -s /bin/bash testuser  #wheel附加组可sudo，以root用户执行命令 -m同时创建用户家目录

BASH
```

设置新用户 testuser 的密码

```
passwd testuser

BASH
```

编辑 sudoers 配置文件

```
EDITOR=vim visudo  # 需要以 root 用户运行 visudo 命令

BASH
```

找到下面这样的一行，把前面的注释符号 `#` 去掉，`:wq` 保存并退出即可

```
#%wheel ALL=(ALL:ALL) NOPASSWD: ALL

BASH
```

这里稍微解释一下 %wheel 代表是 wheel 组，百分号是前缀 ALL= 代表在所有主机上都生效(如果把同样的`sudoers`文件下发到了多个主机上) (ALL) 代表可以成为任意目标用户 ALL 代表可以执行任意命令 一个更详细的例子:

```
%mailadmin   snow,rain=(root) /usr/sbin/postfix, /usr/sbin/postsuper, /usr/bin/doveadm
nobody       ALL=(root) NOPASSWD: /usr/sbin/rndc reload

BASH
```

组 mailadmin 可以作为 root 用户，执行一些邮件服务器控制命令。可以在 “snow” 和 “rain”这两台主机上执行 用户 nobody 可以以 root 用户执行`rndc reload`命令。可以在所有主机上执行。同时可以不输入密码。（正常来说 sudo 都是要求输入调用方的密码的）

## 03. 安装 KDE Plasma 桌面环境

```
pacman -S plasma-meta konsole dolphin  #安装plasma-meta元软件包以及终端和文件管理器

BASH
```

## 04. 配置 greeter sddm

```
systemctl enable sddm

BASH
```

## 05. 设置交换文件 swap （可选）

在桌面环境中，交换分区或文件用来实现休眠(hibernate)的功能，即将当前环境保存在磁盘的交换文件或分区部分。除此之外，某些特定软件需要 swap 才可以正确运行。交换文件与分区性能相同，且交换文件更为灵活，可随时变更大小，增加与删除。

```
dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress #创建4G的交换空间 大小根据需要自定
chmod 600 /swapfile #设置正确的权限
mkswap /swapfile #格式化swap文件
swapon /swapfile #启用swap文件

BASH
```

最后，向/etc/fstab 中**追加**如下内容：

```
/swapfile none swap defaults 0 0

BASH
```

KDE 自身提供开箱即用的睡眠功能(suspend)，即将系统挂起到内存，消耗少量的电量。休眠(hibernate)会将系统挂起到交换分区或文件，几乎不消耗电量。睡眠功能已可满足绝大多数人的需求，如果你一定需要休眠功能，可以参考官方文档（https://wiki.archlinux.org/title/Power_management/Suspend_and_hibernate）设置休眠相关步骤。

## 06. 开启 32 位支持库

```
vim /etc/pacman.conf

BASH
```

去掉[multilib]一节中两行的注释，来开启 32 位库支持。

最后:wq 保存退出，刷新 pacman 数据库

```
pacman -Syyu

BASH
```

重启电脑，即可看到欢迎界面，输入新用户的密码即可登录桌面

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302220930345.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302220930345.png)

[![img](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302221345917.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302221345917.png)

## 07. 安装基础功能包

进入桌面后，搜索 konsole。它是 KDE 桌面环境默认的命令行终端。

首先检查桌面环境中的网络设置：

[![image-20250302222412385](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250302222412385.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250302222412385.png)

image-20250302222412385



接下来安装一些基础功能包

```
sudo pacman -S sof-firmware alsa-firmware alsa-ucm-conf                     #一些可能需要的声音固件
sudo pacman -S ntfs-3g                                                      #识别NTFS格式的硬盘
sudo pacman -S adobe-source-han-serif-cn-fonts wqy-zenhei                   #安装几个开源中文字体 一般装上文泉驿就能解决大多wine应用中文方块的问题
sudo pacman -S noto-fonts-cjk noto-fonts-emoji noto-fonts-extra             #安装谷歌开源字体及表情
sudo pacman -S firefox chromium                                             #安装常用的火狐、谷歌浏览器
sudo pacman -S ark                                                          #与dolphin同用右键解压
sudo pacman -S p7zip unrar unarchiver lzop lrzip                            #安装ark可选依赖
sudo pacman -S packagekit-qt5 packagekit appstream-qt appstream             #确保Discover(软件中心）可用 需重启
sudo pacman -S gwenview                                                     #图片查看器
sudo pacman -S git wget kate bind                                           #一些工具

BASH
```

> 不要安装过多字体：在字体超过 255 种时，某些 QT 程序可能无法正确显示某些表情和符号

## 08. 设置 DNS

一般来说，如今大多电脑连接的路由器是可以自动处理 DNS 的，如果你的路由器不能处理，则需要额外进行 DNS 的设置。同时，如果使用 ISP 提供的默认 DNS,你的网络访问记录将存在更大的，被泄露或被当局存储记录的风险。除此之外，使用 ISP 提供的 DNS 还有可能将某些服务解析至一些已经失效或劣化的服务器。即使你的网络环境可以自动处理 DNS 设置，我们还是建议你使用可信的国际通用 DNS 设置。如下的配置将固定使用谷歌的 DNS,但是网络访问延迟可能增加。在阅读完随后的代理设置一节后，你的 DNS 请求将均通过代理发送，这将在 DNS 发送方面最大限度的保障你的隐私和安全。

vim 编辑`/etc/resolv.conf`，删除已有条目，并将如下内容加入其中

```
nameserver 8.8.8.8
nameserver 2001:4860:4860::8888
nameserver 8.8.4.4
nameserver 2001:4860:4860::8844

BASH
```

如果你的路由器可以自动处理 DNS,resolvconf 会在每次网络连接时用路由器的设置覆盖本机/etc/resolv.conf 中的设置，执行如下命令加入不可变标志，使其不能覆盖如上加入的配置

```
sudo chattr +i /etc/resolv.conf

BASH
```

## 09. 设置系统为中文

打开 *System Settings* > _Regional Settings 在语言中添加中文加入，应用即可

接下来编辑 `~/.config/plasma-localerc` ,将其中的 LANG 值更改为 zh_CN.UTF-8

## 10. 安装yay

AUR 为 archlinux user repository。任何用户都可以上传自己制作的 AUR 包，这也是 Arch Linux 可用软件众多的原因。由于任何人都可上传，也存在对应的风险，一般选用大众认可的包即可。

可以使用 [yay](https://github.com/Jguer/yay) 或 [paru](https://github.com/Morganamilo/paru) 安装 AUR 中的包。首先切换至普通用户：

```
su - nailclipper

BASH
```

刷新包缓存并更新系统

```
sudo pacman -Syu

BASH
```

安装所需的 `base-devel`（包含 `makepkg` 等工具）和 `git`（克隆 yay 的 Git 仓库所需的）

```
sudo pacman -S --needed base-devel git

BASH
```

使用 `--needed` 标志，它不会重新安装已经安装的软件包

使用 git 命令克隆 Yay 仓库。你可以在系统中的任何位置执行此操作，无论是主目录还是其他目录。

```
git clone https://aur.archlinux.org/yay.git

BASH
```

完成后，切换到克隆的目录

```
cd yay

BASH
```

事实上，你是在构建它。ls 后你将在此处看到 `PKGBUILD` 文件。使用以下命令从此处构建包

```
makepkg -si

BASH
```

按照屏幕上的说明进行操作。当系统要求你确认时，按 `Y`

该过程完成后，通过检查其版本来验证 Yay 是否已成功安装

```
yay --version

BASH
```

> 使用 Yay 进行包管理:
>
> 搜索软件包：`yay search_term`
>
> 安装软件包：`yay -S package_name`
>
> 删除软件包：`yay -R package_name`
>
> 要删除包及其依赖项：`yay -Rns package_name`
>
> 仅升级 AUR 包：`yay -Sua`
>
> 将 Yay 升级到新版本：`yay -Sua`
>
> 从 Arch 系统中删除 Yay：`sudo pacman -Rs yay`

## 11. 安装输入法

[Fcitx5 官方文档](https://wiki.archlinux.org/index.php/Fcitx5_(简体中文)) 中文及日文输入法均体验良好

```
sudo pacman -S fcitx5-im #基础包组
sudo pacman -S fcitx5-chinese-addons #官方中文输入引擎
sudo pacman -S fcitx5-anthy #日文输入引擎
yay -S fcitx5-pinyin-moegirl #萌娘百科词库 由于中国大陆政府对github封锁，你可能在此卡住。如卡住，可根据后文设置好代理后再安装
sudo pacman -S fcitx5-pinyin-zhwiki #中文维基百科词库
sudo pacman -S fcitx5-material-color #主题

BASH
```

设置环境变量：编辑文件 `EDITOR=vim sudoedit /etc/environment` 加入以下内容。konsole 以及 dolphin 都需要这些环境变量，倒是 chrome 和 firefox 都不需要就可以输入中文

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx

BASH
```

先点击`运行Fcitx`，打开 *系统设置* > *区域设置* > *输入法*，拼音为默认添加项。如你还需要更多输入法如五笔，则再点击`添加输入法`，找到简体中文下的五笔 ，点击添加即可加入五笔输入法。

接下来点击 *拼音* 右侧的配置按钮，点选`云拼音`和`在程序中显示预编辑文本` 最后应用。

回到输入法设置，点击`配置附加组件`，找到 *经典用户界面* 在主题里选择一个你喜欢的颜色 最后应用。

注销，重新登陆，就可以发现已经可以在各个软件中输入中文了

> RIME 中州韵输入法引擎（英语：Rime Input Method Engine，又称 Rime 输入法）是由佛振编写的开源中文输入法，目前项目网站、源代码均托管在 GitHub。基于同一个核心架构，该输入法分为三个官方发行版：Linux 发行版中州韵（ibus-rime）、Windows 发行版小狼毫（Weasel）、macOS 发行版鼠须管（Squirrel）。另有数个第三方发行版：Linux 发行版 fcitx-rime、Windows 发行版 PRIME、macOS 发行版 XIME、Android 发行版同文输入法（Trime）、iOS 发行版 iRime。

这里介绍一下 fcitx5-rime 的安装方式 ‘’’bash sudo pacman -S fcitx5-rime #不是 fcitx-rime，因为 fcitx 与 fcitx5 有版本冲突 ‘’’ 然后打开 打开 *系统设置* > *区域设置* > *输入法* >,点击添加输入法，找到 RIME，即可添加 RIME 输入法。 RIME 输入法默认是繁体字按 Ctrl+~,即可打开选单，使用键盘上的←→，选择朙月拼音·简化字,回车。

## 12. 配置系统默认编辑器

默认情况下，Arch Linux 在一些终端编辑场景使用 vi 编辑器，但是我们使用 vim。如果不做一个额外配置，在 git 等场景下，在终端调用编辑器会出错。编辑 `EDITOR=vim sudoedit /etc/profile` 文件，加入如下内容，将 vim 设置为默认 EDITOR

```
export EDITOR='vim'

BASH
```

这样就不用在每次执行命令时都指定一遍 `EDITOR=vim` 了

## 13. 启用蓝牙相关功能

对于蓝牙及无线网卡，在 Linux 下推荐使用英特尔产品。博通以及瑞昱产品在兼容性，稳定性方面在 Linux 中表现很差，会带来很多不必要的麻烦，如在驱动，BLE 方面的支持很差或者没有。

如果你有蓝牙设备，需要安装蓝牙软件包并启用蓝牙服务。随后在系统设置中进行添加设备与连接即可。注意，文件传输功能现在需要额外安装包`bluez-obex`，其功能与 2024 年已从 `bluez` 包中分离出来。

```
sudo pacman -S bluez bluez-utils bluez-obex
sudo systemctl enable --now bluetooth

BASH
```

设置开机自动启用蓝牙设备，编辑`/etc/bluetooth/main.conf`文件，将`AutoEnable`设置为 true 即可。如果一些设备依然无法实现开机自动连接，尝试继续将`Experimental`以及`KernelExperimental`两项的值设置为 true。

博通（Broadcom）网卡在 Linux 下的支持度很差，无论是无线还是蓝牙模块。对于无线功能来说，一般直接安装 broadcom-wl 包即可。

```
sudo pacman -S broadcom-wl

BASH
```

对于蓝牙模块，需要查阅[broadcom-bt-firmware](https://github.com/winterheart/broadcom-bt-firmware)仓库，在 brcm 文件夹中找到适合于自己网卡型号的 hcd 文件，将其下载并置于 `/lib/firmware/brcm` 文件夹中后重启即可。如果你无法确认自己的网卡型号对应哪一个 hcd 文件，那就全部下载并置于文件夹中。



