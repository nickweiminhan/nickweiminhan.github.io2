---
title: "Shell 编程快速入门（Fedora 41）"
subtitle: "Shell 编程快速入门（Fedora 41）"
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

- 

## 1.概述

Shell是一个命令行解释器，它接收应用程序（用户）命令，然后调用操作系统内核。Shell还是一个功能相当强大的编程语言，易编写，易调试，灵活性强。

[![image-20250226161555192](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250226161555192.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250226161555192.png)

image-20250226161555192



### 1.1 Fedora Linux 提供的Shell解释器有

```
[root@hadoop100 ~]# cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
/usr/bin/tmux
/bin/tmux

BASH
```

### 1.2 bash 和 sh 的关系

```
[root@hadoop100 ~]# cd /usr/bin; ll | grep bash
-rwxr-xr-x. 1 root root     1414688 Aug 11  2024 bash
lrwxrwxrwx. 1 root root           4 Aug 11  2024 sh -> bash

BASH
```

### 1.3 Fedora 默认的解释器是 bash

```
[root@hadoop100 ~]# echo $SHELL
/bin/bash

BASH
```

## 2. Shell脚本入门

### 2.1 脚本格式

脚本以 `#!/bin/bash` 开头（指定解释器）

### 2.2 第一个 Shell 脚本: helloworld.sh

（1）需求：创建一个 Shell 脚本，输出 Hello World

（2）案例实操：

```
[root@hadoop100 ~]# mkdir scripts
[root@hadoop100 ~]# cd scripts/
[root@hadoop100 scripts]# touch helloworld.sh
[root@hadoop100 scripts]# vim helloworld.sh 
#------------------------------
#在 helloworld.sh 中输入如下内容:
#!/bin/bash
echo "Hello World"
#-------------------------------

BASH
```

（3）脚本的常用执行方式

第一种：采用 bash 或 sh + 脚本的相对路径或绝对路径（不用赋予脚本 +x 权限）

 sh + 脚本的相对路径

```
[root@hadoop100 scripts]# sh ./helloworld.sh 
Hello World

BASH
```

 sh + 脚本的绝对路径

```
[root@hadoop100 scripts]# sh /root/scripts/helloworld.sh 
Hello World

BASH
```

 bash + 脚本的相对路径

```
[root@hadoop100 scripts]# bash ./helloworld.sh 
Hello World

BASH
```

 bash + 脚本的绝对路径

```
[root@hadoop100 scripts]# bash /root/scripts/helloworld.sh 
Hello World

BASH
```

第二种：采用输入脚本的绝对路径或相对路径执行脚本（必须具有 +x 权限）

```
		1. 首先要赋予 helloworld.sh 脚本的 +x 权限
TEXT
[root@hadoop100 scripts]# chmod +x helloworld.sh 

BASH
2. 执行脚本
TEXT
```

 相对路径

```
[root@hadoop100 scripts]# ./helloworld.sh 
Hello World

BASH
```

 绝对路径

```
[root@hadoop100 scripts]# /root/scripts/helloworld.sh 
Hello World

BASH
```

> 注意：第一种执行方法，本质是bash解释器帮你执行脚本，所以脚本本身不需要执行权限。第二种执行方法，本质是脚本需要自己执行，所以需要执行权限。

第三种：在脚本的路径前加上 “.” 或者 source

```
1. 创建以下脚本：
TEXT
[root@hadoop100 scripts]# vim test.sh
[root@hadoop100 scripts]# cat test.sh 
#!bin/bash
A=5
echo $A

BASH
2. 分别使用 sh, bash, ./ 和 "." 的方式来执行，结果如下：
TEXT
[root@hadoop100 scripts]# bash test.sh 
5
[root@hadoop100 scripts]# echo $A

[root@hadoop100 scripts]# sh test.sh 
5
[root@hadoop100 scripts]# echo $A

[root@hadoop100 scripts]# ./test.sh 
5
[root@hadoop100 scripts]# echo $A

[root@hadoop100 scripts]# . test.sh 
5
[root@hadoop100 scripts]# echo $A
5      <--使脚本内容在当前shell中执行，而不是子shell

BASH
```

原因：

 前三种方式都是在当前shell中打开一个子shell来执行脚本内容，当脚本内容结束，则子shell关闭，回到父shell中。

 第四种，也就是在脚本路径前加 “.” 或 source 的方式，可以使脚本内容在当前 shell 中执行，而无需打开子 shell ，这也是为什么我们每次要修改完 `etc/profile` 文件后，需要 source 一下的原因。

 开子 shell 和不开子 shell 的区别就在于，环境变量的继承关系，如在子 shell 中设置的当前变量，父 shell 是不可见的。

## 3. 变量

### 3.1 系统预定义变量

（1）常用系统变量

```
		`$HOME` `$PWD` `$SHELL` `$USER` 等
TEXT
```

（2）案例实操

 1）查看系统变量的值

```
[root@hadoop100 scripts]# echo $HOME
/root
[root@hadoop100 scripts]# echo $PWD
/root/scripts
[root@hadoop100 scripts]# echo $SHELL
/bin/bash
[root@hadoop100 scripts]# echo $USER
root

BASH
```

 2）显示系统环境变量： `env`

```
[root@hadoop100 scripts]# env
SHELL=/bin/bash
HISTCONTROL=ignoredups
HISTSIZE=1000
HOSTNAME=hadoop100
GPG_TTY=/dev/pts/0
EDITOR=/usr/bin/nano
PWD=/root/scripts
LOGNAME=root
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/root
SSH_ASKPASS=/usr/bin/ksshaskpass
LANG=en_US.UTF-8
...

BASH
```

 3）显示当前 Shell 中所有变量：`set`

```
[root@hadoop100 scripts]# set | head
A=5
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extglob:extquote:force_fignore:globasciiranges:globskipdots:histappend:interactive_comments:login_shell:patsub_replacement:progcomp:promptvars:sourcepath
BASHRCSOURCED=Y
BASH_ALIASES=()
BASH_ARGC=([0]="0")
BASH_ARGV=()
BASH_CMDS=()
BASH_COMPLETION_VERSINFO=([0]="2" [1]="13" [2]="0")
BASH_LINENO=()

BASH
```

### 3.2 自定义变量

 （1）基本语法

 1）定义变量：变量名=变量值，注意：=号前后不能有空格

 2）撤销变量：unset 变量名

 3）声明静态变量：readonly 变量，注意：不能unset

 （2）变量定义规则

 1）变量名称可以由字母，数字和下划线组成，但是不能以数字开头，环境变量名建议大写

 2）等号两侧不能由空格

 3）在 bash 中，变量默认类型都是字符串类型，无法直接进行数值计算

 4）变量的值如果有空格，需要使用双引号或单引号括起来

 （3）案例实操

 1）定义变量 A

```
[root@hadoop100 scripts]# A=5
[root@hadoop100 scripts]# echo $A
5

BASH
```

 2）给变量 A 重新赋值

```
[root@hadoop100 scripts]# A=8
[root@hadoop100 scripts]# echo $A
8

BASH
```

 3）撤销变量 A

```
[root@hadoop100 scripts]# unset A
[root@hadoop100 scripts]# echo $A


BASH
```

 4）声明静态变量 B=2，不能 unset

```
[root@hadoop100 scripts]# readonly B=2
[root@hadoop100 scripts]# echo $B
2
[root@hadoop100 scripts]# B=9
-bash: B: readonly variable
[root@hadoop100 scripts]# unset B
-bash: unset: B: cannot unset: readonly variable

BASH
```

 5）在 bash 中，变量默认类型都是字符串类型，无法直接进行数值计算

```
[root@hadoop100 scripts]# C=1+2
[root@hadoop100 scripts]# echo $C
1+2

BASH
```

 6）变量的值如果有空格，需要使用双引号或单引号括起来

```
[root@hadoop100 scripts]# D=I love shell
bash: love: command not found...
[root@hadoop100 scripts]# D="I love shell"
[root@hadoop100 scripts]# echo $D
I love shell

BASH
```

 7）可以把变量提升为全局环境变量，可供其他 Shell 程序使用

```
[root@hadoop100 scripts]# E=11
[root@hadoop100 scripts]# vim ./helloworld.sh 
#--------------------------
#在helloworld中添加如下内容
#!/bin/bash
echo "Hello World"
echo $E
#--------------------------
[root@hadoop100 scripts]# ./helloworld.sh 
Hello World

[root@hadoop100 scripts]# export E
[root@hadoop100 scripts]# ./helloworld.sh 
Hello World
11

BASH
```

### 3.3 特殊变量

#### 3.3.1 $n

 （1）基本语法

 `$n`（功能描述：n 为数字，$0 代表该脚本名称，$1 - $9 代表第一到第九个参数，十以上的参数需要用大括号包含，如 ${10}）

 （2）案例实操

```
[root@hadoop100 scripts]# touch paremeter.sh
[root@hadoop100 scripts]# vim paremeter.sh 
#------------------------
#!/bin/bash
echo '=========$n==========='
echo $0
echo $1
echo $2
#-------------------------
[root@hadoop100 scripts]# chmod 755 paremeter.sh 
[root@hadoop100 scripts]# ./paremeter.sh 
=========$n===========
./paremeter.sh


[root@hadoop100 scripts]# ./paremeter.sh cls top
=========$n===========
./paremeter.sh
cls
top

BASH
```

#### 3.3.2 $#

 （1）基本语法

 `$#` （功能描述：获取所有输入参数个数，常用于循环，判断参数的个数是否正确以及加强脚本的健壮性）

 （2）案例实操

```
[root@hadoop100 scripts]# vim paremeter.sh 
#------------------------
#!/bin/bash
echo '=========$n==========='
echo $0
echo $1
echo $2
echo '=========$#==========='
echo $#
#-------------------------
[root@hadoop100 scripts]# ./paremeter.sh cls top
=========$n===========
./paremeter.sh
cls
top
=========$#===========
2

BASH
```

### 3.3.3 $*、$@

 （1）基本语法

 `$*` （功能描述：这个变量代表命令行中所有的参数，$* 把所有的参数看成一个整体）

 `$@` （功能描述：这个变量也代表命令行中所有的参数，不过 $@ 把每个参数区分对待）

 （2）案例实操

```
[root@hadoop100 scripts]# vim paremeter.sh 
#------------------------
#!/bin/bash
echo '=========$n==========='
echo $0
echo $1
echo $2
echo '=========$#==========='
echo $#
echo '=========$*==========='
echo $*
echo '=========$@==========='
echo $@
#-------------------------
[root@hadoop100 scripts]# ./paremeter.sh a b c d e f g
=========$n===========
./paremeter.sh
a
b
=========$#===========
7
=========$*===========
a b c d e f g
=========$@===========
a b c d e f g

BASH
```

#### 3.3.4 $?

 （1）基本语法

 `$?` （功能描述：最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如果这个变量的值为非0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确）

 （2）案例实操

 判断 helloworld.sh 脚本是否正确执行

```
[root@hadoop100 scripts]# ./helloworld.sh 
Hello World
11
[root@hadoop100 scripts]# echo $?
0
[root@hadoop100 scripts]# abc
bash: abc: command not found...
[root@hadoop100 scripts]# echo $?
127

BASH
```

## 4. 运算符

 （1）基本语法

 `$((运算式)) 或 $[运算式]`

 （2）案例实操：

 计算（2+3）*4 的值

```
[root@hadoop100 scripts]# S=$(((2+3)*4))
[root@hadoop100 scripts]# echo $S
20
[root@hadoop100 scripts]# S=$[(2+3)*4]
[root@hadoop100 scripts]# echo $S
20

BASH
```

## 5. 条件判断

 （1）基本语法

 1）test condition

 2）[ condition ] （注意 condition 前后要有空格）

 注意：条件非空即为 true，如 [ abc ] 返回 true，[ ] 返回false

 （2）常用判断条件

 1）两个整数之间比较

 -eq 等于（equal） -ne 不等于（not equal）

 -lt 小于（less than） -le 小于等于（less equal）

 -gt 大于（greater than） -ge 大于等于（greater equal）

 注：如果是字符串之间的比较，用 “=” 判断相等，用 “!=” 判断不等

 2）按照文件权限进行判断

 -r 有读的权限（read）

 -w 有写的权限（write）

 -x 有执行的权限（execute）

 3）按照文件类型进行判断

 -e 文件存在（existence）

 -f 文件存在并且是一个常规文件（file）

 -d 文件存在并且是一个目录（directory）

 （3）案例实操

 1）23 是否大于等于 22

```
[root@hadoop100 scripts]# [ 23 -ge 22 ]
[root@hadoop100 scripts]# echo $?
0

BASH
```

 2）helloworld 是否具有写权限

```
[root@hadoop100 scripts]# [ -w helloworld.sh ]
[root@hadoop100 scripts]# echo $?
0

BASH
```

 3）`/home/nailclipper/cls.txt` 目录中的文件是否存在

```
[root@hadoop100 scripts]# [ -e /home/nailclipper/cls.txt ]
[root@hadoop100 scripts]# echo $?
0

BASH
```

 4）多条件判断（&& 表示前一条命令执行成功时，才执行后一条命令，|| 表示上一条命令执行失败后，才执行下一条命令）

```
[root@hadoop100 scripts]# [ abc ] && echo OK || echo notOK
OK
[root@hadoop100 scripts]# [   ] && echo OK || echo notOK
notOK

BASH
```

## 6. 流程控制

### 6.1 if 判断

 （1）基本语法

 1）单分支

```
if [ 条件判断式 ];then
	程序
fi

BASH
```

 或者

```
if [ 条件判断式 ]
then
	程序
fi

BASH
```

 2）多分支

```
if [ 条件判断式 ]
then
	程序
elif [ 条件判断式 ]
then
	程序
else
	程序
fi 

BASH
```

> 注意事项：
>
> ```
> 			1. [ 条件判断式 ]，中括号和条件判断式之间必须有空格
> 			2. fi 后要有空格
> TEXT
> ```

 （2）案例实操

 输入一个数字，如果是1，则输出 shuai，如果是2，则输出 mei，如果是其他，什么也不输出

```
[root@hadoop100 scripts]# vim if.sh
#-------------------------------
#!/bin/bash
if [ $1 -eq 1 ]
then    
        echo "shuai"
elif [ $1 -eq 2 ]
then
        echo "mei"
else    
        echo
fi  
#--------------------------------
[root@hadoop100 scripts]# chmod 755 if.sh 
[root@hadoop100 scripts]# ./if.sh 1
shuai
[root@hadoop100 scripts]# ./if.sh 2
mei
[root@hadoop100 scripts]# ./if.sh 3


BASH
```

### 6.2 case 语句

 （1）基本语法

```
case $变量名 in
"值 1")
	如果变量的值等于值 1 ，则执行程序 1 
;;
"值 2")
	如果变量的值等于值 2 ，则执行程序 2 
;;
	...省略其他分支
*)
	如果变量的值都不是以上的值，则执行此程序
;;
esac

BASH
```

> 注意事项：
>
> ```
> 	1. case行尾必须为单词 "in" ，每一个模式匹配必须以右括号 ")" 结束
> 	1. 双分号 ";;" 表示命令序列结束，相当于 java 中的 break
> 	1. 最后的 "*)" 表示默认模式，相当于 java 中的 default
> TEXT
> ```

 （2）案例实操

 输入一个数字，如果是 1，则输出 one ，如果是 2 ，则输出 two ，如果是其他，则输出 big

```
[root@hadoop100 scripts]# vim case.sh
#------------------------------
#!/bin/bash

case $1 in
"1")
        echo "one"
;;

"2")
        echo "two"
;;

*)
        echo "big"
;;
esac
#------------------------------
[root@hadoop100 scripts]# chmod 755 case.sh 
[root@hadoop100 scripts]# ./case.sh 1
one
[root@hadoop100 scripts]# ./case.sh 2
two
[root@hadoop100 scripts]# ./case.sh 3
big

BASH
```

### 6.3 for 循环

 （1）基本语法1

```
for ((初始值;循环控制条件;变量变化))
do
	程序
done

BASH
```

 （2）案例实操

 从 1 加到 100

```
[root@hadoop100 scripts]# vim for1.sh
#----------------------
#!/bin/bash

for ((i=0;i<=100;i++))
do
        sum=$[ $sum+$i ]
done

echo $sum
#----------------------
[root@hadoop100 scripts]# chmod 755 for1.sh 
[root@hadoop100 scripts]# ./for1.sh 
5050

BASH
```

 （3）基本语法2

```
for 变量 in 值1 值2 值3...
do
	程序
done

BASH
```

 （4）案例实操

 1）打印所有输入参数

```
[root@hadoop100 scripts]# vim for2.sh
#---------------------------
#!/bin/bash

for i in java python shell
do
        echo "I love $i"
done  
#----------------------------
[root@hadoop100 scripts]# chmod 755 for2.sh 
[root@hadoop100 scripts]# ./for2.sh
I love java
I love python
I love shell

BASH
```

 2）比较 “$*” 和 “$@” 的区别

 “$*” 和 “$@” 都表示传递给函数或脚本的所有参数，不被双引号 “” 包含时，都以 $1 $2 … $n 的形式输出所有参数

```
[root@hadoop100 scripts]# vim for3.sh
#----------------------------------
#!/bin/bash

echo '=========$*==========='
for i in $*
do      
        echo "I love $i"
done    

echo '=========$@==========='
for j in $@
do      
         echo "I love $j"
done   
#-----------------------------------
[root@hadoop100 scripts]# chmod 755 for3.sh 
[root@hadoop100 scripts]# ./for3.sh java python shell
=========$*===========
I love java
I love python
I love shell
=========$@===========
I love java
I love python
I love shell

BASH
```

 当它们被双引号 “” 包含时，$* 会将所有的参数作为一个整体，以 “$1 $2 $3 … $n” 的形式输出所有参数；$@ 会将各个参数分开，以 “$1” “$2” “$3” … “$n” 的形式输出所有参数

```
[root@hadoop100 scripts]# vim for4.sh
#-------------------------------------
#!/bin/bash

echo '=========$*==========='
for i in "$*"
do     
        echo "I love $i"
done    

echo '=========$@==========='
for j in "$@"
do      
        echo "I love $j"
done  
#-------------------------------------
[root@hadoop100 scripts]# chmod 755 for4.sh 
[root@hadoop100 scripts]# ./for4.sh java python shell
=========$*===========
I love java python shell
=========$@===========
I love java
I love python
I love shell

BASH
```

### 6.4 while 循环

 （1）基本语法

```
while [ 条件判断式 ]
do
	程序
done

BASH
```

 （2）案例实操

 从 1 加到 100

```
[root@hadoop100 scripts]# vim while.sh
#---------------------------
"while.sh" [New]                                0,0-1         All
#!/bin/bash


sum=0
i=1

while [ $i -le 100 ]
do
        sum=$[ $sum+$i ]
        i=$[ $1+1 ]
done    

echo $sum
#---------------------------
[root@hadoop100 scripts]# chmod 755 while.sh 
[root@hadoop100 scripts]# ./while.sh 
5050

BASH
```

## 7. read 读取控制台输入

 （1）基本语法

 `read （选项） （参数）`

 1）选项：

 -p：指定读取值时的提示符

 -t：指定读取值时等待的时间（秒），如果 -t 不加表示一直等待

 2）参数：

 变量：指定读取值的变量名

 （2）案例实操

 提示 7 秒内，读取控制台输入的名称

```
[root@hadoop100 scripts]# vim read.sh
#-----------------------------
#!/bin/bash

read -t 7 -p "Enter your name in 7 seconds:" NN
echo $NN
#-----------------------------
[root@hadoop100 scripts]# chmod 755 read.sh 
[root@hadoop100 scripts]# ./read.sh
Enter your name in 7 seconds:nailclipper
nailclipper

BASH
```

## 8. 函数

### 8.1 系统函数

#### 8.1.1 basename

 （1）基本语法

 `basename [string / pathname] [suffix]` （功能描述：basename 命令会删掉所有的前缀，包括最后一个（’/‘）字符，然后将字符串显示出来）

 basename 可以理解为取路径里的文件名称

 选项：

 suffix 为后缀，如果 suffix 被指定了，basename 会将 pathname 或 string 中的 suffix 去掉

 （2）案例实操

 截取该 `/home/nailclipper/xuexi.txt` 路径的文件名称

```
[root@hadoop100 scripts]# touch /home/nailclipper/xuexi.txt
[root@hadoop100 scripts]# basename /home/nailclipper/xuexi.txt
xuexi.txt
[root@hadoop100 scripts]# basename /home/nailclipper/
nailclipper
[root@hadoop100 scripts]# basename /home/nailclipper/xuexi.txt .txt
xuexi

BASH
```

#### 8.1.2 dirname

 （1）基本语法

 `dirname 文件绝对路径` （功能描述：从给定的包含绝对路径的文件名中去除文件名（非目录部分），然后返回剩下的路径（目录的部分））

 diename 可以理解为取文件路径的绝对路径名称

 （2）案例实操

 获取 xuexi.txt 文件的路径

```
[root@hadoop100 scripts]# dirname /home/nailclipper/xuexi.txt
/home/nailclipper
[root@hadoop100 scripts]# dirname ../../home/nailclipper/xuexi.txt
../../home/nailclipper

BASH
```

### 8.2 自定义函数

 （1）基本语法

```
[ function ]funname[()]
{
	Action;
	[return int;]
}
#[]表示可选项

BASH
```

 （2）经验技巧

 1）必须在调用函数地方之前声明函数，shell 脚本是逐行运行，不会像其他语言一样先编译

 2）函数返回值只能通过 $? 系统变量获得，可以显示加：return 返回，如果不加，将以最后一条命令运行结果作为返回值，作为返回值 return 后跟数值 n （0-255）

 （3）案例实操

 计算两个输入参数的和

```
[root@hadoop100 scripts]# vim fun.sh
#---------------------------
"fun.sh" 8L, 62B                                6,12-19       All
#!/bin/bash

function sum()
{
        s=0
        s=$[ $1+$2 ]
        echo "$s"
}

read -p "Please input the number1:" n1;
read -p "Please input the number2:" n2;

sum $n1 $n2;
#----------------------------
[root@hadoop100 scripts]# chmod 755 fun.sh 
[root@hadoop100 scripts]# ./fun.sh 
Please input the number1:345
Please input the number2:678
1023

BASH
```

## 9. 正则表达式入门

 正则表达式是使用单个字符串来描述，匹配一系列符合某个语法规则的字符串。在很多文本编辑器里，正则表达式通常被用来检索、替换那些符合某个模式的文本。在 Linux 中，grep，sed，awk 等文本处理工具都支持通过正则表达式进行模式匹配

### 9.1 常规匹配

 一串不包含特殊字符的正则表达式匹配它自己，如

```
[root@hadoop100 scripts]# cat /etc/passwd | grep nailclipper
nailclipper:x:1000:1000:nailclipper:/home/nailclipper:/bin/bash

BASH
```

 就会匹配所有包含 nailclipper 的行

### 9.2 常用特殊字符

 （1）特殊字符：`^`

 ^ 匹配一行的开头，例如

```
[root@hadoop100 scripts]# cat /etc/passwd | grep ^na
nailclipper:x:1000:1000:nailclipper:/home/nailclipper:/bin/bash

BASH
```

 会匹配出所有以 na 开头的行

 （2）特殊字符：`$`

 “$” 匹配一行的结束，例如

```
[root@hadoop100 scripts]# cat /etc/passwd | grep bash$
root:x:0:0:Super User:/root:/bin/bash
nailclipper:x:1000:1000:nailclipper:/home/nailclipper:/bin/bash

BASH
```

 会匹配出所有以 bash 结尾的行

> ^$ 会匹配所有的空行

 （3）特殊字符：`.`

 “.” 匹配一个任意的字符，例如

```
[root@hadoop100 scripts]# cat /etc/passwd | grep r..t
root:x:0:0:Super User:/root:/bin/bash
operator:x:11:0:operator:/root:/usr/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/usr/sbin/nologin

BASH
```

 （4）特殊字符：`*`

 “*” 不单独使用，它和上一个字符连用，表示匹配上一个字符 0 次或多次，例如

```
[root@hadoop100 scripts]# cat /etc/passwd | grep ro*t
root:x:0:0:Super User:/root:/bin/bash
operator:x:11:0:operator:/root:/usr/sbin/nologin
rtkit:x:172:172:RealtimeKit:/:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin

BASH
```

 会匹配 rt，rot，root，rooor 等所有行

> .* 会匹配所有的行

 （5）字符区间（中括号）：`[]`

 [ ] 表示匹配某个范围内的一个字符，例如

 [6,8]——匹配 6 或者 8

 [0-9]——匹配一个 0-9 的数字

 [0-9]*——匹配任意长度的数字字符串

 [a-z]——匹配一个 a-z 之间的字符

 [a-z]* ——匹配任意长度的字母字符串

 [a-c, e-f]-匹配一个 a-c 或者 e-f 之间的任意字符

```
[root@hadoop100 scripts]# cat /etc/passwd | grep r[a,b,c,o]*t
root:x:0:0:Super User:/root:/bin/bash
operator:x:11:0:operator:/root:/usr/sbin/nologin
rtkit:x:172:172:RealtimeKit:/:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/usr/sbin/nologin

BASH
```

 会匹配 rt，rat，rbt，rabt，rabct，root，rabcbcoat 等等所有行

 （6）特殊字符：`\`

 “\“ 表示转义，并不会单独使用。由于所有特殊字符都有其特定匹配模式，当我们想匹配某一特殊字符本身时（例如，找出所有包含 $ 的行），就会碰到困难，此时我们就要将转义字符和特殊字符连用，来表示特殊字符本身，例如

```
[root@hadoop100 scripts]# cat /etc/passwd | grep '\:'
grep: warning: stray \ before :
root:x:0:0:Super User:/root:/bin/bash
bin:x:1:1:bin:/bin:/usr/sbin/nologin

BASH
```

## 10. 文本处理工具

### 10.1 cut

 cut 的工作就是”剪”，具体的说就是在文件中负责剪切数据用的。cut 命令从文件的每一行剪切字节、字符和字段并将这些字节、字符和字段输出。

 （1）基本用法

 `cut [选项参数] filename`

 说明：默认分隔符是制表符

 （2）选项参数说明

 -f 列号，提取第几列

 -d 分隔符 ，按照指定分隔符分隔列，默认是制表符 “\t”

 -c 按字符进行切割，后面加 n 表示取第几列，比如 -c 1

 （3）案例实操

 1）数据准备

```
[root@hadoop100 scripts]# vim cut.txt
#------------------------------
dong shen
guan zhen
wo   wo
lai  lai
le   le
#------------------------------

BASH
```

 2）切割 cut.txt 第一、二列

```
[root@hadoop100 scripts]# cut -d " " -f 1 cut.txt
dong
guan
wo
lai
le
[root@hadoop100 scripts]# cut -d " " -f 2 cut.txt
shen
zhen
wo
lai
le
[root@hadoop100 scripts]# cut -d " " -f 1,2 cut.txt
dong shen
guan zhen
wo wo
lai lai
le le

BASH
```

 3）在 cut.txt 文件中切割出 guan

```
[root@hadoop100 scripts]# cat cut.txt | grep guan | cut -d " " -f 1
guan

BASH
```

 4）选取系统 PATH 变量值第 2 个 “:” 开始后的所有路径

```
[root@hadoop100 scripts]# echo $PATH
/root/.local/bin:/root/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
[root@hadoop100 scripts]# echo $PATH | cut -d ":" -f 3-
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin

BASH
```

 5）切割 nmcli 后打印的 IP 地址

```
[root@hadoop100 scripts]# nmcli
ens160: connected to Wired connection 1
        "VMware VMXNET3"
        ethernet (vmxnet3), 00:0C:29:F3:6E:24, hw, mtu 1500
        ip4 default
        inet4 192.168.26.100/24
        route4 192.168.26.0/24 metric 100
        route4 default via 192.168.26.2 metric 100
        inet6 fe80::30ab:e710:aa2c:3ab0/64
        route6 fe80::/64 metric 1024

lo: connected (externally) to lo
        "lo"
        loopback (unknown), 00:00:00:00:00:00, sw, mtu 65536
        inet4 127.0.0.1/8
        inet6 ::1/128

DNS configuration:
        servers: 192.168.26.2
        interface: ens160

Use "nmcli device show" to get complete information about known d>
"nmcli connection show" to get an overview on active connection p>

Consult nmcli(1) and nmcli-examples(7) manual pages for complete >
[root@hadoop100 scripts]# nmcli | grep -m 1  inet4 | cut -d "/" -f1 | cut -d " " -f 2
192.168.26.100

BASH
```

### 10.2 awk

 一个强大的文本分析工具，把文件逐行的输入，以空格为默认分隔符将每行切片，切开的部分再进行分析处理

 （1）基本用法

 `awk [选项参数] '/pattern1/{action} /pattern2/{action}...' filename`

 pattern：表示 awk 在数据中查找的内容，就是匹配模式

 action：在找到匹配内容时所执行的一系列命令

 （2）选项参数说明

 -F 指定输入文件分隔符

 -v 赋值一个用户定义变量

 （3）案例实操

 1）数据准备

```
[root@hadoop100 scripts]# cp /etc/passwd /root/scripts/
#文件内容
#用户名:密码(加密过后的):用户 id:组 id:注释:用户家目录:shell 解析器

BASH
```

 2）搜索 passwd 文件以 root 关键字开头的所有行，并输出该行的第 7 列

```
[root@hadoop100 scripts]# awk -F ":" '/^root/{print $7}' passwd
/bin/bash

BASH
```

 3）搜索 passwd 文件以 root 关键字开头的所有行，并输出该行的第 1 列和第 7 列，中间以 “,” 号分隔

```
[root@hadoop100 scripts]# awk -F ":" '/^root/{print $1","$7}' passwd
root,/bin/bash

BASH
```

 注意：只有匹配了 pattern 的行才会执行 action

 4）只显示 passwd 的第一列和第七列，以逗号 “,” 分隔，且在所有行前面添加列名 user,shell，在最后一行添加 “hanli, /bin/hanpaopao”

```
[root@hadoop100 scripts]# awk -F ":" 'BEGIN{print "user,shell"} {print $1","$7} END{print "hanli,/bin/hanpaopao"}' passwd
user,shell
root,/bin/bash
bin,/usr/sbin/nologin
daemon,/usr/sbin/nologin
...
user,/bin/bash
hanli,/bin/hanpaopao

BASH
```

 注意：BEGIN 在所有数据读取行之前执行；END 在所有数据执行之后执行

 5）将 passwd 文件中的用户 id 增加数值 1 并输出

```
[root@hadoop100 scripts]# cut -d ":" -f 3 passwd
0
1
2
3
4
...
[root@hadoop100 scripts]# awk -v i=1 -F ":" '{print $3+i}' passwd
1
2
3
4
5

BASH
```

 （4）awk 的内置变量

 FILENAME 文件名

 NR 已读的记录数（行号）

 NF 浏览记录的域的个数（切割后，列的个数）

 （5）案例实操

 1）统计 passwd 文件名，每行的行号，每行的列数

```
[root@hadoop100 scripts]# awk -F ":" '{print "filename:"FILENAME "
,linenum:"NR ",col:"NF}' passwd
filename:passwd,linenum:1,col:7
filename:passwd,linenum:2,col:7
filename:passwd,linenum:3,col:7

BASH
```

 2）查询 nmcli 命令输出结果中的空行所在的行号

```
[root@hadoop100 scripts]# nmcli
ens160: connected to Wired connection 1
        "VMware VMXNET3"
        ethernet (vmxnet3), 00:0C:29:F3:6E:24, hw, mtu 1500
        ip4 default
        inet4 192.168.26.100/24
        route4 192.168.26.0/24 metric 100
        route4 default via 192.168.26.2 metric 100
        inet6 fe80::30ab:e710:aa2c:3ab0/64
        route6 fe80::/64 metric 1024

lo: connected (externally) to lo
        "lo"
        loopback (unknown), 00:00:00:00:00:00, sw, mtu 65536
        inet4 127.0.0.1/8
        inet6 ::1/128

DNS configuration:
        servers: 192.168.26.2
        interface: ens160

Use "nmcli device show" to get complete information about known d>
"nmcli connection show" to get an overview on active connection p>

Consult nmcli(1) and nmcli-examples(7) manual pages for complete >
[root@hadoop100 scripts]# nmcli | awk '/^$/{print NR}'
10
16
20
23

BASH
```

 3）切割 IP

```
[root@hadoop100 scripts]# nmcli | grep -m 1 inet4 | awk '{print $2}' | awk -F "/" '{print $1}'
192.168.26.100

BASH
```

## 11. 综合应用案例

### 11.1 归档文件

 实际生产应用中，往往需要对重要数据进行归档备份

 需求：实现一个每天对指定目录归档备份的脚本，输入一个目录名称（末尾不带 / ），将目录下所有文件按天归档保存，并将归档日期附加在归档文件名上，放在 `/root/archive` 下

 这里用到了归档命令：tar

 后面可以加上 -c 选项表示归档，加上 -z 选项表示同时进行压缩，得到的文件后缀名为 tar.gz

 脚本实现如下：

```
[root@hadoop100 scripts]# mkdir /root/archive
#--------------------------------------------
#!/bin/bash

#首先判断输入参数个数是否为1
if [ $# -ne 1 ]
then    
        echo "参数个数错误！应输入一个参数，作为归档目录名"
        exit
fi      

#从参数中获取目录名称
if [ -d $1 ]
then
        echo $1
else    
        echo
        echo "目录不存在！"
        echo
        exit
fi      

DIR_NAME=$(basename $1)
DIR_PATH=$(cd $(dirname $1);pwd)

#获取当前日期
DATE=$(date +%y%m%d)

#定义生成的归档文件名称
FILE=archive_${DIR_NAME}_$DATE.tar.gz
DEST=/root/archive/$FILE

#开始归档目录文件

echo "开始归档..."
echo

tar -czf $DEST $DIR_PATH/$DIR_NAME

if [ $? -eq 0 ]
then
        echo
        echo "归档成功！"
        echo "归档文件为：$DEST"
        echo
else
        echo "归档出现问题！"
        echo
fi

exit
#-------------------------------------------

BASH
```

### 11.2 发送消息

 我们可以利用 Linux 自带的 mesg 和 write 工具，向其他用户发送消息

 需求：实现一个向某个用户快速发送消息的脚本，输入用户名作为第一个参数，后面直接跟要发送的消息。脚本需要检测用户是否登陆在系统中、是否打开消息功能，以及当前发送消息是否为空

 脚本实现如下：

```
#!/bin/bash
login_user=$(who | grep -i -m 1 $1 | awk '{print $1}')
if [ -z $login_user ]
then
		echo "$1 不在线！"
		echo "脚本退出.."
exit
fi
is_allowed=$(who -T | grep -i -m 1 $1 | awk '{print $2}')
if [ $is_allowed != "+" ]
then
		echo "$1 没有开启消息功能"
		echo "脚本退出.."
exit
fi
if [ -z $2 ]
then
		echo "没有消息发出"
		echo "脚本退出.."
exit
fi
whole_msg=$(echo $* | cut -d " " -f 2- )
user_terminal=$(who | grep -i -m 1 $1 | awk '{print $2}')
echo $whole_msg | write $login_user $user_terminal
if [ $? != 0 ]
then
		echo "发送失败！"
else
		echo "发送成功！"
fi
exit

BASH
```
