# shell简介

## 初始shell

```shell
程序 语言 编程
----------------------------------
语言
自然语言:汉语、英语 
计算机语言:c语言、c++、(java php python go shell) 
编译型语言 c c++ java 
解释型语言  php python  bashshell

编译型语言:编译型语言的首先将源代码编译生成机器语言，再由机器运行机器码(二进制)。像C/C++等都是编译型语言。   一次编译，重复使用。
解释型语言:源代码不是直接翻译成机器语言，而是先翻译成中间代码，再由解释器对中间代码进行解释运行。比如Python/JavaScript/Shell等都是解释型语言  每次使用都需要解释
    c 编译型执行代码需要编译成cpu能认识的二进制码 x86指令集
    java 编译型执行编译-->字节码，cpu不能直接运行，只能被Java虚拟机执行 
    shell 解释型语言执行
```

## shell定义

```shell
Shell 也是一种程序设计语言，它有变量，关键字，各种控制语句，有自己的语法结构，利用shell程序设计语 言可以编写功能很强、代码简短的程序。
shell是外壳的意思，就是系统的外壳，我们可以通过shell的命令来控制和操作操作系统，比如linux中的shell命令就包括ls、cd、pwd等等，总结来说shell就是一个命令解释器，他通过接收用户输入的shell命令来启动、停止程序的运行或者对计算机进行控制。
```

## shell的分类和切换

```shell
[root@linux-server ~]# cat /etc/shells 
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin

#默认shell： bash shell
#centos中脚本使用的默认shell 为/usr/bin/sh  

查看当前正在使用的shell
[root@linux-server ~]# echo $SHELL
/bin/bash

shell的修改
# vim /etc/passwd  编辑登录shell
```

## 使用场景

```shell
Shell 能做什么?
1. 自动化批量系统初始化程序 (update，软件安装，时区设置，安全策略...)---初始化脚本
2. 自动化批量软件部署程序 (LAMP，LNMP，Tomcat，LVS，Nginx)---自动化安装脚本
3. 应用管理程序 (KVM)---批量创建虚拟机、管理虚拟机。
4. 日志分析处理程序(PV, UV, 200, !200,grep/awk)----akw、sed、grep
5. 自动化备份恢复程序(MySQL完全备份/增量 + Crond)-----数据备份恢复脚本
6. 自动化信息采集及监控程序(收集系统/应用状态信息，CPU,Mem,Disk,Net,TCP Status,Apache,MySQL)--监控脚本
7. 配合Zabbix信息采集(收集系统/应用状态信息，CPU,Mem,Disk,Net,Apache,MySQL)
8. 9*9乘法表、俄罗斯方块，打印三角形，打印圣诞树，打印五角星，运行小火车，坦克大战，排序实现 
9. Shell可以做任何运维的事情(一切取决于业务需求)

shell 和ansible---批量自动化工具--自动化运维--ansible
```

## shell特性回顾

```shell
文件描述符与输出重定向:
在 shell程序中，最常使用的FD (file descriptor) 大概有三个, 分别是: 
0: Standard Input (STDIN)
1: Standard Output (STDOUT)
2: Standard Error Output (STDERR)
在标准情况下, 这些FD分别跟如下设备关联:
stdin(0): keyboard 键盘输入,并返回在前端
stdout(1): monitor 正确返回值 输出到前端
stderr(2): monitor 错误返回值 输出到前端
>a.txt
1>a.txt
2>a.txt
&>a.txt
1>&2
2>&1
一般来说, "1>" 通常可以省略成 ">".
1>&2 正确返回值传递给2输出通道 &2表示2输出通道，之前如果有定义标准错误重定向到某log文件,那么标准输出也重定向到这个log文件，如果此处错写成 1>2, 就表示把1输出重定向到文件2中.
2>&1 错误返回值传递给1输出通道, 同样&1表示1输出通道.

例子. 当前目录下只有a.txt,没有b.txt
[root@linux-server ~]# touch a.txt
[root@linux-server ~]# ls a.txt b.txt 1>file.out 2>&1
[root@linux-server ~]# cat file.out 
ls: cannot access b.txt: No such file or directory
a.txt
现在, 正确的输出和错误的输出都定向到了file.out这个文件中, 而不显示在前端
```

## bash初始化

```shell
用户登录时相关的bash配置文件 (登录脚本)属于全局配置文件:
/etc/profile 
/etc/profile.d/*.sh 
/etc/bashrc

个人配置文件，每个用户的配置文件
  ~/.bsah_profile
  ~/.bashrc
#profile类的文件: 设定环境变量，运行命令或脚本，用户在登录的时候会自动生效
#bashrc类的文件: 定义命令别名

用户登录时加载bash配置文件的过程:
登录式shell加载配置文件过程
	   ~/.bash_profile --> ~/.bashrc --> /etc/bashrc ---> /etc/profile --> /etc/profile.d/*.sh

#这里的优先级是影响范围最小的优先级最高

下面的文件为系统的每个用户设置环境信息Shell设置文件:
		/etc/profile(系统级)启动时执行 
		这是系统最主要的shell设置文件，也是用户登陆时系统最先检查的文件，有关重要的环境变量都定义在此，其中包括 PATH,USER,MAIL,HOSTNAME,HISTSIZE,INPUTRC等。而在文件的最后，它会检查并执行/etc/profile.d/*.sh的脚本。

		
		~/.bash_login(用户级)登录时执行 
		如果~.bash_profile文件不存在，则系统会转而读取.bash_login这个文件内容。这是用户的登陆文件，在每次用户登陆系统时，bash都会读此内容，所以通常都会将登陆后必须执行的命令放在这个文件中。
		~/.bash_logout 离开时执行如果想在注销shell前执行一些工作，都可以在此文件中设置。 例如:

# vi ~/.bash_logout
clear
仅执行一个clear命令在你注销的时候

~/.bash_history(用户级) #这个文件会记录用户先前使用的历史命令。
```

# bash shell特性

```shell
补全  tab键   #yum -y install bash-completion
历史--history
别名---alias
快捷键---ctrl+c+l
前后台作业---jobs fg bg &
重定向 > >> <
管道 ---|
#命令排序执行: ; && ||
&&：逻辑与，前面执行成功，后面才执行。前面命令执行失败，后面命令也不执行
||：逻辑或，前面执行失败，后面执行，前面命令执行成功，后面不执行。
;：从左往右按顺序执行，不管前面执行成功与否，后面都执行

通配符:[] {} ? *
正则表达式 脚本
```

# 历史命令

```shell
查看历史命令
[root@linux-server ~]# history
调用历史命令 
    上下健
    !关键字
    !历史命令行号
    !! 执行上一条命令
    !$ 上一条命令的最后一个参数
	esc . 上一条命令的最后一个参数
	Ctrl+r 在历史命令中查找，输入关键字调出之前的命令 
别名
查看别名
[root@linux-server ~]# alias

永久设置
# vim /root/.bashrc
# source /root/.bashrc  #让文件生效

小小技巧:显示历史命令执行时间 
1.设置变量:
[root@linux-server ~]# vim /etc/profile    #在最后添加
HISTTIMEFORMAT="%Y-%m-%d %H:%M:%S" 
[root@linux-server ~]# source /etc/profile  #让设置的环境变量生效
2.再次执行history查看结果
```

# bash部分快捷键

```shell
    Ctrl+a 切换到命令行开始(跟home一样，但是home在某些unix环境下无法使用) 
    Ctrl+u 清除剪切光标之前的内容
    Ctrl+k 清除剪切光标之后的内容
    ctrl+y 粘贴刚才所删除的字符
    Ctrl+r 在历史命令中查找，输入关键字调出之前的命令
    Ctrl+l 清屏
    Ctrl+c 终止
    Ctrl+e 切换到命令行末尾
```

# 通配符置换

```ini
#在 Shell命令中，通常会使用通配符表达式来匹配一些文件
*，?,[]，{}

例:
字符          含义                     实例
*       匹配 0 或多个字符               a*b a与b之间可以有任意长度的任意字符, 也可以一个也没有, 如aabcb, axyzb, a012b, ab。
?       匹配任意一个字符                a?b a与b之间必须也只能有一个字符, 可以是任意字符, 如aab, abb, acb, a0b 。
[list]  匹配 list 中的任意单一字符       a[xyz]b a与b之间必须也只能有一个字符, 但只能是 x 或 y 或 z, 如: axb, ayb, azb 。
[!list] 匹配 除list 中的任意单一字符      a[!0-9]b a与b之间必须也只能有一个字符, 但不能是阿拉伯数字, 如axb, aab, a-b。
[c1-c2] 匹配 c1-c2 中的任意单一字符      如:[0-9] [a-z] a[0-9]b 0与9之间必须也只能有一个字符 如a0b, a1b... a9b。
{string1,string2,...} 匹配 sring1 或 string2 (或更多)其一字符串 a{abc,xyz,123}b a与b之间只 能是abc或xyz或123这三个字符串之一。
```

```shell
[root@linux-server tmp]# rm -rf ./*
[root@linux-server tmp]# touch aabcb axyzb a012b ab acb
[root@linux-server tmp]# ls
a012b  aabcb  ab  acb  axyzb
[root@linux-server tmp]# ls a*b
a012b  aabcb  ab  acb  axyzb
[root@linux-server tmp]# ls a?b
acb

[root@linux-server tmp]# rm -rf ./*
[root@linux-server tmp]# touch axb ayb azb axyb
[root@linux-server tmp]# ls
axb  axyb  ayb  azb
[root@linux-server tmp]# ls a[xy]b
axb  ayb
[root@linux-server tmp]# ls a[!xy]b
azb
[root@linux-server tmp]# ls a[!x]b
ayb  azb

[root@linux-server tmp]# rm -rf ./*
[root@linux-server tmp]# touch a0b a1b a9b
[root@linux-server tmp]# ls a[0-9]b
a0b  a1b  a9b

[root@linux-server tmp]# rm -rf ./*
[root@linux-server tmp]# touch aabcb axyzb a012b ab
[root@linux-server tmp]# ls a{abc}b
ls: cannot access a{abc}b: No such file or directory
[root@linux-server tmp]# ls a{abc,xyz}b
aabcb  axyzb
[root@linux-server tmp]# ls a{abc,xyz,012}b
a012b  aabcb  axyzb
```

# shell 脚本规范

```shell
[root@linux-server ~]# vim helloworld.sh   ---.sh代表这个文件是个shell脚本,
拓展名后缀,如果省略.sh则不易判断该文件是否为shell脚本
1. #!/bin/bash ---shebang蛇棒, 解释器, 翻译 2. #
2. #
3. # Author: soso666
4. # Email: soso666@163.com           ---这就是注释, 你没看错
5. # Github: https:github.com/soso666
6. # Date: 2019/12/24
7. printf "hello world\n"

功能说明:打印hello world
[root@linux-server ~]# sh helloworld.sh 
hello world
[root@linux-server ~]# chmod +x helloworld.sh 
[root@linux-server ~]# ./helloworld.sh 
[root@linux-server ~]# /root/helloworld.sh 
hello world

第一行: “#!/usr/bin/env bash”叫做shebang, shell语法规定shell脚本文件第一行为整个文件的解释器
第二行: 为“#”开头的行为注释行默认不会被程序所读取, 用来说明文件及标定所属人员使用, 也可用来解释程序
第七行: 为格式化打印语句printf, printf可以把后面的“hello world”打印到指定的终端中, \n 为换行符

bash 脚本测试：
1.这将执行该脚本并显示所有变量的值
[root@linux-server ~]# sh -x /root/helloworld.sh
+ printf 'hello world\n'
hello world
2.不执行脚本只是检查语法模式，将返回所有错误语法
[root@linux-server ~]# sh -n /root/helloworld.sh
3.执行脚本前把脚本内容显示在屏幕上
[root@linux-server ~]# sh -v /root/helloworld.sh
#!/usr/bin/env bash
#
# Author: soso666
# Email: soso666@163.com
# Github: https:github.com/soso666
# Date: 2019/12/24
printf "hello world\n"
hello world
```

# 变量类型

```shell
变量：bash作为程序设计语言和其它高级语言一样也提供使用和定义变量的功能，简单说就是让一个特定的字符串代表不固定的内容 a=123,echo $a
#分为：预定义变量、环境变量、自定义变量、位置变量
```

```shell
预定义变量:预定义的特殊变量有着特殊的含义，用户不可以更改，所有的预定义变量都由$符号和另外一个符号组成，常用的预定义特殊变量如下：
    $$ 当前进程PID
    $? 命令执行后的返回状态：0 为执行正确，非 0 为执行错误 
    $# 位置参数的数量
    $* 所有位置参数的内容   
    $@ 显示所有的参数
    $! 上一个后台进程的PID (wait命令中使用,后面讲)

[root@linux-server ~]# echo $? 最后一次执行的命令的返回状态。如果这个变量的值为 0，则证明上一条命令正确执行;如果这个变量的值为非 0 ，则 证明上一条命令执行错误
[root@linux-server ~]# echo $$ 当前进程的进程号(PID)
[root@linux-server ~]# echo $! 后台运行的最后一个进程的进程号(PID)

[root@linux-server ~]# ls
anaconda-ks.cfg  a.txt  b.txt  file.out  helloworld.sh  #ls命令正确执行
[root@linux-server ~]# echo $?
0
#预定义变量"$?"的值是0，证明上一条命令正确执

[root@linux-server ~]# sleep 3000 &
[1] 1418 
#符号"&"的意思是把命令放入后台执行 
[root@linux-server ~]# echo $!
1418
```

```shell
自定义变量:就是自己设置的变量只能在当前终端和脚本中使用
变量名称=值  #定义变量
#变量名称:只能由字母，数字，下划线组成，不能以数字开头；
#注意:应该让变量名称有意义；
= 赋值符号 前后不能有空格 ；
值: 所有的字符串和数字都可以；
引用变量: $变量名 或 ${变量名}。
示例:
[root@linux-server ~]# a=100
[root@linux-server ~]# echo $a
100
[root@linux-server ~]# echo $aa     # 这里输出为空，因为解释器认为$aa是变量
[root@linux-server ~]# echo ${a}a
100a

查看变量: echo $变量名
取消变量: unset 变量名，仅在当前shell中有效
```

```shell
环境变量:shell在开始执行时已经定义好的，就是系统执行环境的一些设置
# env  #env是 environment (环境) 的简写，所有的环境变量（包含自定义的环境变量）
# set #列出系统中所有的变量，包括自定义的变量 
export   变量名 #使自定义的变量成为环境变量，才使用这个参数。环境变量拥有可继承性:export之后就拥有继承性环境变量可以被向下继承

临时生效
[root@linux-server ~]# IPADDR=192.168.1.1
[root@linux-server ~]# echo $IPADDR
192.168.1.1
永久生效
写到4个登陆脚本中 ~/.bashrc ~/profile 更好放在/etc/profile.d/目录下建立独立的环境变量配置文件
[root@linux-server ~]# vim /etc/profile.d/test.sh
IPADDT=192.168.1.1
[root@linux-server ~]# source /etc/profile.d/test.sh  #让环境变量生效
[root@linux-server ~]# echo $IPADDT
192.168.1.1

常用环境变量:USER UID HOME HOSTNAME PWD PS1 PATH
PATH:存储所有命令所在的路径
```

```shell
#子进程 仅会继承父 shell 的环境变量， 不会继承父 shell 的自定义变量
[root@localhost ~]# bash           # 打开一个子 shell
[root@localhost ~]# export a=hello # 在 子 shell 声明一个环境变量
[root@localhost ~]# bash           # 在子 shell 中再打开一个 子 shell
[root@localhost ~]# echo $a        # 变量可以生效
hello
[root@localhost ~]# exit           # 退出 子 shell 的 子 shell
exit
[root@localhost ~]# exit           # 退出 子 shell
exit
[root@localhost ~]# echo $a        # 在 当前 shell 中， 其子 shell 声明的环境变量是无效的

[root@localhost ~]# 
```

```shell
位置变量也叫位置参数:在脚本代码中调用通过命令行传递给脚本的参数
$1 $2 $3 $...   #分别对应传递给脚本内容里面的第1、第2等参数

例子：
# /test.sh start   #start是第1个位置参数
#/test.sh 2 3 5 hello #2是第1个位置参数，3是第2个位置参数...依次类推

例子:
[root@linux-server ~]# cd /opt/test/script/
[root@linux-server script]# vim weizhi.sh
#!/usr/bin/bash
echo 我的第一个位置参数是:$1 
echo 我的第二个位置参数是:$2 
echo 我的第三个位置参数是:$3 
echo 我的第四个位置参数是:$4 
echo 一共有 $# 个位置参数 
echo 你输入的参数分别是:$*
[root@linux-server script]# chmod +x weizhi.sh 
[root@linux-server script]# ./weizhi.sh 1 3 4 6 
我的第一个位置参数是:1
我的第二个位置参数是:3
我的第三个位置参数是:4
我的第四个位置参数是:6
一共有 4 个位置参数
你输入的参数分别是:1 3 4 6
```

### 实战

```shell
编写一个shell脚本，用于搜集其执行主机的信息，打印结果如下: 
[root@linux-server ~]# mkdir /opt/test/script
[root@linux-server ~]# cd /opt/test/script
[root@linux-server script]# vim test.sh
[root@linux-server script]# chmod +x test.sh
[root@linux-server script]# ./test.sh 
现在的时间是: 2020-08-22-17:34:03
当前的用户是: root
当前的用户标识: 0
当前的主机名称是: linux-server
当前可用网卡IP是: 192.168.246.148/24

##脚本源码如下
#!/usr/bin/bash
# 获取主机基本信息
#!/bin/bash
A=`date +%F-%X`
B=`whoami`
C=`grep "^$B" /etc/passwd | awk -F ":" '{print $3}'`
D=`hostname`
E=`ip a | grep "ens33" | awk -F " " 'NR==2{print $2}'`
echo "现在时间：$A"
echo "现在用户：$B"
echo "当前用户标识:$C"
echo "主机名称:$D"
echo "当前IP:$E"

这段 Shell 脚本的含义是：首先通过 `ip a` 命令获取系统的网络配置信息，然后使用管道符将输出传递给 `grep` 进行过滤，只保留包含字符串 "ens33" 的行。接着，使用 `awk -F " "` 指定分隔符为空格，对 `grep` 的输出进行进一步处理，`NR==2{print $2}` 表示仅对第二行进行处理，并输出该行的第二个字段，也就是 `ens33` 的 IP 地址。

综上所述，这段脚本的实际作用是获取系统中名为 `ens33` 的网卡的 IP 地址，方便用户快速地查看网络配置信息。

现在时间：2023-04-29-14时39分59秒
现在用户：root
当前用户标识:0
主机名称:cainana
当前IP:192.168.174.40/24

取当前系统分区剩余空间:
[root@linux-server script]# df -Th | awk 'NR==6 {print $5}'
489M
取当前系统剩余内存:
[root@linux-server script]# echo "现在的剩余内存是:"`free -m |awk 'NR==2{print $4}'`
现在的剩余内存是:16G

取当前cpu平均负载:
[root@linux-server script]# echo 现在cpu的`uptime | cut -d, -f3-` #-d指定分隔符，-f指定显示区域，3-第三列以后(包括第三列)
现在cpu的 load average: 0.00, 0.01, 0.05


方式二：
[root@linux-server script]# echo 现在cpu的`uptime | awk -F "," '{print $3,$4,$5}'`
现在cpu的 load average: 0.00 0.01 0.05
```

```shell
编写一个脚本实现收集主机的基本信息，最后脚本还会将这些信息写入一个日志文件.
[root@linux-server script]# vim xinxi.sh
#!/bin/bash
#获取主机基本信息
centime=`date '+%Y-%m-%d %H:%M:%S'`
nowtime=`uptime |awk '{print $1}'`  #默认以空格为分隔符
username=$USER
verage=`uptime |awk -F',' '{print $3,$4,$5}'`
myname=xuange
cat >>file1.txt <<EOF
echo "时间:$centime"
echo "系统的当前时间是: $nowtime"
echo "系统当前负载: $verage"
echo "当前的用户是: $username"
echo "我的名字是: $myname"
EOF
[root@linux-server script]# chmod +x xinxi.sh 
[root@linux-server script]# ./xinxi.sh 
[root@linux-server script]# cat file1.txt
===========================================================
```

```shell
变量运算
算式运算符: +、-、*、/、()、%取余(取模)
如：(5+3)*2
运算方式:$(()) $[] expr 
$(())：例子
# echo $(( 5+2-(3*2)/5 )) 
6
# echo $(((3*2)/5))
1

$[]：例子
# echo $[ 5 + 2 - (3*2)/5 ]
6 
expr：例子
# expr 5 + 3
8
#注意:运算符号两边的空格必须写

乘法运算:
[root@linux-server script]# expr 5 \* 3
15
[root@linux-server script]# expr 5 '*' 3
15

#脚本中常用的方式
echo "$a和$b的和是: `expr $a '+' $b`"
=================================================================
取1到6之间的随机数:
# echo $RANDOM
# echo $(($RANDOM % 6 + 1))
5
取1-10之间的随机数:
# echo $(($RANDOM % 10 + 1)
5

这串代码特别简单，就是利用RANDOM这个随机数生成器进行取余就能够实现，至于为什么取余时需要+1是因为在取余时如果被整除那么余数会是0，这样就不在限定范围内了
```

```shell
浮点运算
bash本身不能做小数计算:需要bc命令转换 
# yum install -y bc
# echo "2.6*4" | bc
# echo "2^4" | bc
```

```shell
变量引用
#转义:\
1.当一个字符被引用时，其特殊含义被禁止，把有意义的变的没意义，把没意义的变的有意义
转义案例：
[root@linux-server script]# echo you now $1250
you now 250
[root@linux-server script]# echo you now \$1250
you now $1250

完全引用:'' #强引 硬引  #指的是被引号包围起来的变量名不会进行不会进行解析，原样变量名原样输出，这种方式比较适合定义显示纯字符串的情况，不希望解析变量、命令等的场景。
部分引用:"" #弱引 软引  #指的是被引号包围起来的变量名会先进行解析，然后将变量的解析结果输出来。这种方式适合字符串中附带有变量和命令并且想将其解析后再输出的变量定义。

例子:
[root@linux-server script]# num=1
[root@linux-server script]# echo 1901班有$num个女生
1901班有1个女生
[root@linux-server script]# echo "1901班有$num个女生"
1901班有1个女生
[root@linux-server script]# echo '1901班有$num个女生'
1901班有$num个女生
======================================================
#读取用户标准输入:read 
read:功能就是读取键盘输入的值，并赋给变量 
#read -t 5 var
#read -p "提示信息" var
read后面的变量var可以只有一个，也可以有多个，这时如果输入多个数据，则第一个数据给第一个变量，第二个数据给第二 个变量，如果输入数据个数过多，则最后所有的值都给最后一个变量

#!/usr/bin/bash
read -p "请输入你的用户名和密码还有你的年龄: " name pass age
echo "你的名字是 $name"
echo "你的密码是 $pass"
echo "你的年龄是 $age"


案例1：
[root@linux-server script]# vim readtest.sh
#!/bin/bash
# read test
read -p "请输入你的银行卡帐号" num 
read -p "请在五秒内输入密码" -t 5 pass 
echo "你的密码错误!"
[root@linux-server script]# ./readtest.sh

案例2：  #-s 选项 能够使read命令中输入的数据不显示在监视器上
[root@linux-server script]# vim readtest3.sh
#!/bin/bash
read -s -p "Enter your password: " pass 
echo "your password is $pass"
exit 1
[root@linux-server script]# chmod +x readtest3.sh
[root@linux-server script]# ./readtest3.sh
========================================================
取消屏幕回显
[root@linux-server script]# stty -echo #回车测试
[root@linux-server script]# stty echo  #恢复回显

显示变量长度
[root@linux-server script]# a=123
[root@linux-server script]# echo ${#a}   #表示$var的长度
3
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
练习：
[root@linux-server script]# cat d.sh
echo 1.配置yum客户端
echo 2.添加A记录
echo 3.一键安装lamp环境
echo 4.一键配置静态IP
read -p "请选择你想使用的功能(1/2/3/4):" num
con_ip(){
echo 这是配置IP地址的小工具
}
case $num in
        1):
        ;;
        2):;;
        3):
        ;;
        4)con_ip
        ;;
        *)
        echo "你输入的不正确！请按提示输入"
        ;;
esac
[root@linux-server script]# chmod +x d.sh 
[root@linux-server script]# ./d.sh
```

# 变量置换

## 命令替换

```shell
取命令结果用。把命令的结果拿出来
[root@linux-server ~]# a=`date +%m%d`
[root@linux-server ~]# echo $a
1225
[root@linux-server ~]# a=$(date +%m-%d)
[root@linux-server ~]# echo $a
12-25

#反引号亦可用$() 代替
```

## 变量替换

```shell
一 ${parameter:-word}
若 parameter 为空或未设置，则用 word 代替 parameter 进行替换，parameter 的值不变,若 parameter 不为空，则不替换，parameter 的值不变  
# unset b
# echo ${b:-3} 
-3
# echo $b
#

二 ${parameter:=word}
若 parameter 为空或未设置，则用 word 代替 parameter 进行替换，parameter 的值改变,若 parameter设置了，则不替换,parameter的值不变 
# unset b
# echo ${b:=3}
3
# echo $b
3
#

三 ${parameter:+word}
若 parameter 设置了值，则用 word 代替 parameter 进行替换，parameter 的值不变 
# unset b
# echo ${b:+3}
#
# echo $b
#

举个例子：
#!/bin/bash
echo "======================="
echo "  目前的版本有：  "
echo "                  mysql-8.0"
echo "                  mysql-5.7"
echo "  默认版本为： MySQL-8.0"
echo "======================="
read -p "请输入你要选择的版本"  Num
Num=`echo ${Num:=8.0}`
echo "您选择的版本为$Num,稍后给你安装......"
```

## 变量替换-匹配截取

```shell
索引及切片
[root@linux-server ~]# a=12345678
[root@linux-server ~]# echo ${a:5}  #从左往右第5位开始截取，留下后三位 
678
[root@linux-server ~]# echo ${a:3:4} #从第3位开始截取，留下后四位的，剩下的都不要。
4567
[root@linux-server ~]# echo ${a:2:-1} #从左往右第2位开始截取，从右往左截取第一位
34567
[root@linux-server ~]# echo ${a:2:-2}
3456

参数解释：
用冒号截取：echo $a: : :
              从哪里截取留那里。

脚本案例：
[root@localhost ~]# vim test11.sh
#!/usr/bin/bash
read -s -p "请输入您的11位手机号  " phone
echo
echo "你的手机号是 $phone"
echo "手机号后四位是 ${phone:7}"


匹配截取
语法：
${变量#关键词}  若变量内容从头开始的数据符合『关键词』，则将符合的最短数据切除
${变量##关键词} 若变量内容从头开始的数据符合『关键词』，则将符合的最长数据切除
${变量%关键词}  若变量内容从尾向前的数据符合『关键词』，则将符合的最短数据切除
${变量%%关键词} 若变量内容从尾向前的数据符合『关键词』，则将符合的最长数据切除

参数解释：
*：表示全部字符。
%：最短尾匹配；
%%：最大尾匹配
%：从右往左
#：从左往右

实战
[root@linux-server ~]# url=www.sina.com.cn 
[root@linux-server ~]# echo ${#url} #获取变量的长度 
15
[root@linux-server ~]# echo ${url}  #正常显示变量 
www.sina.com.cn

匹配截取
[root@newrain ~]# echo ${url#*.}   从前往后匹配到“.”最短匹配
sina.com.cn
[root@newrain ~]# echo ${url##*.}  从前往后匹配到“.”，最长匹配
cn
[root@newrain ~]# echo ${url%.*}   从后往前匹配到“.”，最短匹配
www.sina.com
[root@newrain ~]# echo ${url%%.*}  从后往前匹配到“.”，最长匹配
www
[root@newrain ~]# echo ${url#a.}    #不加*
www.sina.com.cn
[root@newrain ~]# echo ${url#*a.}   #加*
com.cn

[root@localhost ~]# vim mail.sh
脚本案例：
#!/usr/bin/bash
read -p "请输入你的邮箱 " mail
echo "你的邮箱是$mail"
echo "你的邮箱服务器是${mail#*@}"
mail_host=${mail#*@}
case $mail_host in
        163.com)
        echo "网易服务器"
        ;;
        126.com)
        echo "126服务器"
        ;;
        qq.com)
        echo "qq邮箱"
        ;;
        *)
        echo "您输入的邮箱不正确"
        exit 2
esac

变量内容的替换
语法
${变量/旧字符串/新字符串} 若变量内容符合『旧字符串』则『第一个旧字符串会被新字符串替代』
${变量//旧字符串/新字符串} 若变量内容符合『旧字符串』则『全部的旧字符串会被新字符串替代』

实战
[root@linux-server ~]# a=123456123789
[root@linux-server ~]# echo ${a/1/}    #第一次匹配的被替换
23456123789
[root@linux-server ~]# echo ${a/1/0}  #第一次匹配到1替换成0
023456123789
[root@linux-server ~]# echo ${a//1/}  #全局的匹配被替换
2345623789                 
[root@linux-server ~]# echo ${a//1/x} #全局匹配到1替换成x
x23456x23789

例:
file=/dir1/dir2/dir3/my.file.txt
${file#*/}:  拿掉第一条 / 及其左边的字符串:dir1/dir2/dir3/my.file.txt 
${file##*/}: 拿掉最后一条 / 及其左边的字符串:my.file.txt 
${file#*.}:  拿掉第一个 . 及其左边的字符串:file.txt
${file##*.}: 拿掉最后一个 . 及其左边的字符串:txt
${file%/*}:  拿掉最后条 / 及其右边的字符串:/dir1/dir2/dir3
${file%%/*}: 拿掉第一条 / 及其右边的字符串:(空值)
${file%.*}:  拿掉最后一个 . 及其右边的字符串:/dir1/dir2/dir3/my.file
${file%%.*}: 拿掉第一个 . 及其右边的字符串:/dir1/dir2/dir3/my
记忆的方法为:
# 是去掉左边(在键盘上 # 在 $ 之左边)
% 是去掉右边(在键盘上 % 在 $ 之右边) 
单一符号是最小匹配;两个符号是最大匹配(贪婪匹配)。
```

## basename & dirname

```shell
basename 命令
basename 是去除目录后剩下的名字,取文件名
例: 
[root@linux-server ~]# temp=/home/temp/1.test
[root@linux-server ~]# base=`basename $temp`
[root@linux-server ~]# echo $base
1.test

dirname 是获取目录名 
例:
[root@linux-server ~]# temp=/home/temp/1.test
[root@linux-server ~]# dir=`dirname $temp`
[root@linux-server ~]# echo $dir
/home/temp
```

# 流程控制

## 条件结构

```shell
测试-----test 条件
#条件为真返回 0，条件为假返回 1 #语法------[ 条件 ]
test 能够理解3中类型的表达式 
1.文件测试
2.字符串比较
3.数字比较
字符串
 -n STRING
    the length of STRING is nonzero
    -n  字符串的长度 不是零成功。
 -z STRING
    the length of STRING is zero
     -z   字符串长度。是零成功 #对于未定义或赋予空值的变量将是为空串。
 STRING1 = STRING2  （等于）
           the strings are equal
 STRING1 != STRING2  （不等于）
           the strings are not equal
           
           [-n "a"]

# vim string.sh
#!/usr/bin/bash
while : #:默认值为真
do
read -p "请输入你的密码: " a
pass=123456
if [ -z $a ];then
        echo "您输入的密码不能为空"
        exit 1
else
        if [ $a = $pass ];then
                echo "登录成功"
                break
        else
                echo "您的密码输入有误，请重新输入"
        fi
fi
done


#判断数字类型的内容
    -eq(equal) 等于
    -ne(not equal) 不等于
    -ge(Greater than or equal to) 大于等于 
    -le(Less than or equal to) 小于等于 
    -gt(greater than) 大于
    -lt(less than) 小于 

#判断字符串类型的内容
==  "$a"=="1"
！=
>=
<=
>
<





#文件
test
    -f 存在且是正规文件 
    -d 存在且是目录
    -h 存在且是符号链接 
    -b 块设备
    -c 字符设备
    -e 只判断是否存在
```

## if语句

```shell
流控制:
•在一个shell脚本中的命令执行顺序称作脚本的流。大多数脚本会根据一个或多个条件来改变它们的流。 
•流控制命令:能让脚本的流根据条件而改变的命令称为条件流控制命令 
•exit语句:退出程序的执行，并返回一个返回码，返回码为0正常退出，非0为非正常退出，例如: 
•exit 0

条件判断语法：
if [判断条件]---代码返回0表示真，非0为假
if语句语法如下: 
if [ list1 ];then   list1:你的测试条件，你要测试什么，对什么内容做判断
	list2
elif [ list3 ];then     ---------------> 接着在怎么做。（多条件判断）
	list4
else           ---------------> 如果前面的命令没有执行成功那就执行else下面的命令。
	list5
fi

例:
[root@linux-server ~]# cd /opt/test/script/
[root@linux-server script]# vim testif.sh
#!/bin/bash
read -p "请输入号码: " num 
if [ $num = 1 ];then
        echo "1"
elif [ $num = 2 ];then
				echo "2"
else 
				echo "输入有误!"
fi
[root@linux-server script]# chmod +x testif.sh

例:脚本if.sh，必须在脚本后加上适当的参数脚本才能正确执行
[root@linux-server script]# vim if.sh
#!/bin/bash
read -p "please input parameter:" num
if [ "$num" = "hello" ]; then
        echo "Hello! How are you ?"
elif [ "$num" = "" ]; then
      echo "You MUST input parameters"
else
      echo "The only accept parameter is hello"
fi
[root@linux-server script]# chmod +x if.sh
测试：
[root@linux-server script]# ./if.sh 
[root@linux-server script]# ./if.sh hello
[root@linux-server script]# ./if.sh 434


测试ip地址主机位从2到100的机器是否存活，并把存活的机器记录到文本文件alivehost.txt内。(使用ping命令)
案例
#!/usr/bin/bash
ip=192.168.198
for i in {2..100}
do
        ping -c1 $ip.$i &> /dev/null &    #调入后台执行
                if [ $? -eq 0 ];then
                        echo "$ip.$i is up" >> activehost.txt
                else
                        echo "$ip.$i is down"
                fi
done

多个条件联合
&&：逻辑与，前面执行成功，后面才执行。前面命令执行失败，后面命令也不执行
if [ $condition1 ] && [ $condition2 ];then 
if [[ $condition1 && $condition2 ]];then
||：逻辑或，前面执行失败，后面执行，前面命令执行成功，后面不执行。
if [ $condition1 ] || [ $condition2 ];then 
if [[ $condition1 || $condition2 ]];then
```

## case语句

```shell
[root@linux-server script]# vim system_tools.sh
#!/usr/bin/env bash
cat <<-EOF 
+-------------------------------------------------------------------------+ 
|                             System_tools V1.0                           | 
+-------------------------------------------------------------------------+
|                     a. Stop And Disabled Firewalld.                     |
|                     b. Disabled SELinux Secure System.                  |
|                     c. Install Apache Service.                          |
|                     d. Quit                                             | 
+-------------------------------------------------------------------------+ 
EOF
echo "Please input your select: " && read var
case "$var" in
  	"a")
        systemctl stop firewalld && systemctl disable firewalld
				;; 
		"b")
				setenforce 0
				;; 
		"c")
				yum -y install httpd httpd-tools
				;; 
		"d")
				exit
				;; 
			*)
				printf "请按照上方提供的选项输入!!!\n"
				;; 
esac
[root@linux-server script]# chmod +x system_tools.sh
[root@linux-server script]# ./system_tools.sh
```

## for语句

```shell
for i in {取值范围}  #for是关键字 i是变量名 in是关键字
do                  #循环体的开始
				循环体
done                #循环体的结束
```

```shell
[root@linux-server script]# vim for.sh
#!/usr/bin/env bash
#
# Author:
# Date: 2019/**/**
for i in {1..100} 
do
	echo $i 
done
```

```shell
[root@linux-server script]# vim for1.sh
#!/bin/bash
for (( i=1;i <= 5;i++ ))
do
				echo "$i"
done
[root@linux-server script]# chmod +x for1.sh 
[root@linux-server script]# ./for1.sh
参数解释：
默认值 i=1 
条件  i<=多少？取决于定义，为用户输入的变量，先条件成立在执行命令
增幅  i++  执行一次加一

区别:
i++===先赋值在运算
++i===先运算在赋值
例子
[root@localhost script]# i=1
[root@localhost script]# h=1
[root@localhost script]# let x=i++
[root@localhost script]# echo $x
1
[root@localhost script]# echo $i
2
[root@localhost script]# let y=++h
[root@localhost script]# echo $y
2
[root@localhost script]# echo $h
2
```

```shell
测试成产环境的主机存活性，将up的ip保存在一个文件中，将down的ip保存在一个文件中
[root@linux-server script]# vim ip.sh
#!/usr/bin/env bash
# Author: 
src_ip="192.168.174"
for i in {2..254}
do
        {
        ping -c1 $src_ip.$i &>/dev/null
        if [ $? -eq 0 ];then
                echo "alive: $src_ip.$i" >> ip_up.txt
                echo "alive: $src_ip.$i"
        else
                echo "down: $src_ip.$i" >> ip_down.txt
                echo "down: $src_ip.$i"
        fi
        } &
done
wait
echo "finish..."
[root@linux-server script]# chmod +x ip.sh 
[root@linux-server script]# ./ip.sh
参数详解：
wait：等待上面命令后台执行结束后(即上一个的进程终止)，在执行下面的echo命令
```

```shell
for循环创建用户
[root@linux-server script]# vim user.sh
#!/usr/bin/bash
read -p "请设置用户名/数量/密码: " prefix num pass
cat <<-EOF
用户前缀:$prefix
用户数量:$num
用户密码:$pass
EOF
for i in $(seq 1 $num)
do
user=$prefix$i
id $user &> /dev/null
if [ $? -eq 0 ];then
        echo "$user is already exist!"
        exit 0
else
        useradd $user &> /dev/null
        echo $pass | passwd --stdin $user &>/dev/null
fi
done
echo "starting create users..."
[root@linux-server script]# chmod +x user.sh 
[root@linux-server script]# ./user.sh

参数详解：
seq 打印序列号，只跟数字
seq 命令用于产生从某个数到另外一个数之间的所有整数。

seq命令的原理就不说了，这里说说为什么不能在{ }中使用变量。其实原因写在bash的man手册中：
大意是说，Bash中会最先展开{ }中的内容，这个时候$NUM还不会被具体的值替代，所以是i在循环中读取的是‘{1..$NUM}’的一个完整的字符串，输出时$NUM会被10替代，就有了'{1..10}'这样的结果。
```

## while语句

```shell
while 条件   #while关键字，条件和if的条件一样，#while循环当条件为真的时候循环同时会一直循环，也就所说的死循环，为假时不循环
do
		循环体
done
#注意：while循环处理文件里面的行比较擅长，不管有没有空格都是一行。

案例：
# vim c.sh
#!/usr/bin/bash
i=1
while [ $i -lt 50 ]
do
        echo $i
        let i++
done

注意观察，#请问如何能够自动终止
#在shell中，let命令用于指定算术运算,即 let expr
```

```shell
完善系统工具的输出及操作性
#通过一个文件批量创建用户：
#背景：写一个脚本，满足以下需求及应用，如一个文件的内容如下，根据文件内容实现批量创建用户，第一列为用户名，第二列为密码
[root@localhost ~]# vim a.txt #创建用户和密码文件
user1 123
user2 456
user3 567
user4 789
user5 012
[root@localhost ~]# vim b.sh #编写脚本
#!/usr/bin/bash

[ $UID -ne 0 ] && exit 1
while read line
do
        user=`echo $line | awk '{print $1}'`
        pass=`echo $line | awk '{print $2}'`
        id $user &> /dev/null || useradd $user && echo $pass | passwd $user --stdin
done < /root/a.txt
[root@localhost ~]# chmod +x create_user.sh 
[root@localhost ~]# bash create_user.sh

这段脚本的作用是从文件/root/a.txt中读取每行内容，并将每行的第一个单词作为用户名，第二个单词作为密码，然后使用useradd命令添加用户，并使用echo和管道命令将密码作为标准输入传递给passwd命令来设置用户的密码。

而第一行是一个条件语句，意思是如果当前用户不是root用户，则退出脚本。而第二行则是一个循环语句，指导读取完整个文件为止。
```

```shell
[root@linux-server script]# vim while.sh
#!/usr/bin/env bash
#
# Author:
while 1>0
do
cat <<-EOF 
+-------------------------------------------------------------------------+ 
|                            System_tools V1.0                            | 
+-------------------------------------------------------------------------+
|                      a. Stop And Disabled Firewalld.                    |
|                      b. Disabled SELinux Secure System.                 |
|                      c. Install Apache Service.                         |
|                      d. Quit                                            | 
+-------------------------------------------------------------------------+
EOF
echo " Please input your select: " && read var
case "$var" in
				"a")
        			systemctl stop firewalld && systemctl disable firewalld
							;; 
				"b")
				sed -ri s/SELINUX=enforcing/SELINUX=disabled/g /etc/selinux/config
							;; 
				"c")
				yum -y install httpd httpd-tools
							;; 
				"d")
				exit
				;; 
				  *)
				echo "请按照上方提供的选项输入!!!"
				;; 
esac
if [ $? -eq 0 ];then 
	clear
else
        echo "Warning: Your program exist ERROR!!!"
        break
fi 
done
[root@linux-server script]# chmod +x while.sh 
[root@linux-server script]# ./while.sh
```

```shell
嵌套循环
[root@localhost script]# vim test4.sh
#!/usr/bin/bash
for i in {1..100}
do
while [ $i -lt 50 ]
do
        echo $i
        let i++
done
done
[root@localhost script]# chmod +x test4.sh
[root@localhost script]# bash test4.sh
```

## until语句

```shell
until 条件   #当后面的条件表达式为假的时候的才循环，为真的时候就停止了
do
循环体
done
```

```shell
[root@linux-server script]# cat until.sh 
#!/bin/bash
x=1
until [ $x -ge 10 ]
do
		echo $x
		x=`expr $x + 1` 
done

x=1
while [ ! $x -ge 10 ]
do
		echo $x
		x=`expr $x + 1`
done
[root@linux-server script]# chmod +x until.sh 
[root@linux-server script]# ./until.sh
```

```shell
循环控制shift、continue、break、exit
shift命令
#位置参数可以用shift命令左移。比如shift 3表示原来的$4现在变成$1，原来的$5现在变成$2等等，原来的$1、$2、$3丢弃，$0不移动。不带参数的shift命令相当于shift 1。

#测试 shift 命令(x_shift3.sh)
[root@linux-server script]# cat x_shift3.sh 
#!/bin/bash
shift
echo "第一个位置参数: $1"
[root@linux-server script]# bash x_shift3.sh 2 3 
第一个位置参数: 3

若用户要求 Shell 在不知道位置变量个数的情况下，还能逐个的把参数一一处理，也就是在 $1 后为 $2,在 $2 后面为 $3 等,则需要用shift把所有参数变成$1
#测试 shift 命令(x_shift.sh) 
[root@linux-server script]# vim x_shift.sh
#!/bin/bash
until [ $# -eq 0 ]
do
echo "第一个参数为: $1 参数个数为: $#" 
shift
done
执行以上程序: 
[root@linux-server script]# bash x_shift3.sh 1 2 3 4
结果显示如下:
第一个参数为: 1 参数个数为: 4 
第一个参数为: 2 参数个数为: 3 
第一个参数为: 3 参数个数为: 2 
第一个参数为: 4 参数个数为: 1

从上可知 shift 命令每执行一次，变量的个数($#)减一，而变量值提前一位

#continue、break、exit命令
Linux脚本中的break continue exit

1.break
结束并退出本次循环

2.continue 
在循环中不执行continue下面的代码，转而进入下一轮循环

3.exit
退出脚本
常带一个整数给系统，如 exit 0

检测:
[root@localhost script]# vim break.sh
#!/usr/bin/bash
for i in {1..10}
do
if [ $i -eq 7 ];then
        continue
        #break
        #exit 34
else
        echo $i
fi
echo "本次输出结束"
done
echo "脚本结束循环"

#可理解为:break是立马跳出循环并退出脚本;continue是跳出当前条件循环，继续下一轮条件循环;exit是直接退出整个脚本 

在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，Shell使用两个命令来实现该功能:break和continue。 

break命令
break命令允许跳出所有循环(终止执行后面的所有循环)。
下面的例子中，脚本进入死循环直至用户输入数字大于5。要跳出这个循环，返回到shell提示符下，需要使用break命令。 
代码如下:
[root@linux-server script]# vim break.sh
#!/bin/bash
while :
do
echo -n "Input a number between 1 to 5: " 
read aNum
case $aNum in
        1|2|3|4|5)
        echo "Your number is $aNum!"
        ;;
        *)
        echo "You do not select a number between 1 to 5, game is over!"
        break 
        ;;
esac
done

#将break注释掉观察结果

案例2：continue
continue命令
continue命令与break命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。
[root@linux-server script]# bash break.sh
 
对上面的例子进行修改:
代码如下:
[root@linux-server script]# vim break1.sh
#!/bin/bash
while :  #默认为真
do
echo -n "Input a number between 1 to 5: " 
read aNum
case $aNum in
        1|2|3|4|5)
        echo "Your number is $aNum!"
        ;;
        *)
        echo "You do not select a number between 1 to 5, game is over!"
        continue 
        ;;
esac
done
[root@linux-server script]# bash break1.sh
#运行代码发现，当输入大于5的数字时，该例中的循环不会结束.
```

# 函数

```shell
function (功能) 功能函数

完成特定功能的代码片段
函数必须先定义才能使用
优点:避免重复的代码

定义函数---怎么写函数脚本，如何定义
调用函数--- 怎么使用函数，本地调用，2。通过其他脚本调用
取消函数---- unset func_name
函数传参----和脚本传参类似

命名空间：在shell语言中函数的命名空间是函数内和函数外定义变量是一样的，函数内外不能赋值同样名字的变量

#变量：如果在同一个命名空间可以用，如果不再同一个命名空间就不能用
#函数变量使用的范围：默认，函数里的变量会在函数外面生效
#函数变量如果不让在其他空间用使用：local 分开
local  变量名称  #变量只在函数内生效。属于局部变量

# vim var.sh
#!/usr/bin/bash
a=10
var() {
        echo $a
        #local a
        a=20
        echo $a
}
var       #调用函数
echo $a

#return: 返回结果并退出函数
返回值：return value：#value不能超过0-255，是函数里面函数最后一条执行命令的返回值，默认返回值是由这条命令执行结果确定的.
[root@localhost ~]# vim return.sh
#!/usr/bin/bash
func(){
        echo "hello"
        return 250 #函数返回值
}
func
#echo $?  #返回的是函数的返回值
if [ $? -eq 250 ];then
        echo "成功"
else
        echo "失败"
fi

exit:返回结果并退出程序
```

## function

```shell
function_name () {
   list of commands
}
```

```shell
函数名 function_name，这就是你将使用它从其他地方在你的脚本调用。 
```

```shell
unset myfunc #取消函数
```

```shell
[root@linux-server script]# vim func.sh
#!/bin/bash
myfunc(){   #定义函数
echo “This is my first shell function” 
}
myfunc   #函数调用
```

```shell
产生以下结果执行
[root@linux-server script]# bash func.sh 
“This is my first shell function”

函数必须提前定义测试
[root@linux-server script]# vim fun01.sh
#!/bin/bash
fun () {
echo "hello"
}
fun
unset fun   #取消函数
fun
[root@linux-server script]# bash fun01.sh 
hello
fun01.sh: line 7: fun: command not found
```

```shell
定义函数脚本
[root@localhost script]# cat a.sh 
#!/usr/bin/bash
check_net() {
        echo "正在检查网络通信"
        ping -c1 www.baidu.com 2&> /dev/null
                if [ $? -eq 0 ];then
                        echo "你的网络是没有问题的"
                else
                        echo "你的网络有问题，请先检查网络"
                        exit 2
                fi
}
check_net   #本地调用
echo "+++++++++++++++++++++++++++++++++++++++++++++"

check_yum() {
        echo "正在检查yum源是否可用"
        yum repolist
                if [ $? -eq 0 ];then
                        echo "你的yum源可以正常使用"
                else
                        echo "yum源不能用，请手动配置网络yum源"
                        exit 3
                fi
}
check_yum
echo "+++++++++++++++++++++++++++++++++++++++++++++++++++"

install_nginx() {
check_net
echo "正在配置nginx的yum源"
cat > /etc/yum.repos.d/nginx.repo <<EOF
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/7/x86_64/
gpgcheck=0
enabled=1
EOF
yum clean all && sleep 2    #让系统休息2秒
yum install -y nginx &> /dev/null
if [ $? -eq 0 ];then
        echo "nginx install successfull and start nginx"
        sleep 2
        systemctl start nginx
        if [ $? -eq 0 ];then
                echo "nginx started is success,plaess check port!"
                port=`netstat -lntp | grep nginx | awk '{print $4}' | awk -F ":" '{print $NF}'`
                echo "nginx is port $port"
        else
                echo "nginx 启动失败，请手动检查!"
                exit
        fi
else
        echo "nginx install failed!将会自动退出"
        exit
fi
}
install_nginx

函数调用
root@localhost script]# cat b.sh #通过其他脚本调用函数脚本 
#!/usr/bin/bash
while :
do
echo "这是服务器基本检测功能脚本"
        cat <<EOF
++++++++++++++++++++++++++++++++++
+       1. 检查yum源             +
+       2. 检查网络              +
+       3. 安装nginx             +
+       4. 退出                  +
++++++++++++++++++++++++++++++++++
EOF
source ./a.sh  #写你函数脚本的绝对路径，这里指的是执行函数脚本，让它为下面调用函数生效
read -p "请输入你的选项: " num
case $num in
        1)
        check_yum
        ;;
        2)
        check_net
        ;;
        3)
        install_nginx
        ;;
        4)
        exit;;
        *)
        echo "你输入的选项失败请重新输入"
esac
done
```

## 函数传递

```shell
在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 位置参数来进行传参的，$1表示第一个参数，$2表示第二个参数...
```

```shell
[root@linux-server script]# vim fun04.sh
fun() {
        echo $[$1*$2*$3]
}
fun $1 $2 $3
[root@linux-server script]# bash fun04.sh 1 3 5
15

这段 Shell 脚本定义了一个函数 fun，该函数接受三个参数，并在函数体内将这三个参数相乘并输出结果。然后，调用 fun 函数，并传入三个参数 $1、$2、$3，这三个参数的值是在调用脚本时传入的。
```

# 数组

```shell
什么是数组?
数组也是一种变量，常规变量只能保存一个值，数组可以保存多个值

#普通数组:只能用整数作为数组的索引--0  下标
#关联数组:可以使用字符串作为数组的索引
```

## 数组定义

```shell
普通数组定义:
[root@linux-server script]# books=( linux shell awk sed ) ---在python中叫列表

引用: echo ${array_name[index]} #引用 

[root@linux-server script]# echo ${books[0]}
linux
[root@linux-server script]# echo ${books[1]}
shell
[root@linux-server script]# echo ${books[2]}
awk

#关联数组需要提前声明
Declare命令：
[test @test test]# declare [-选项]
参数说明：
-a ：#定义为数组--array
-A : #定义关联数组

例1
declare -A myarry1
[root@linux-server script]# declare -A myarry1
[root@linux-server script]# myarry1=([name]=soso666 [sex]=man [age]=18)
[root@linux-server script]# echo ${myarry1[name]}
soso666
[root@linux-server script]# echo ${myarry1[age]}
18
```

```shell
数组定义方法:
定义方法1:
    # array=( one two three four five six )
    # array1=(`cat /etc/passwd`) #希望是将文件中的每一行作为一个值赋给数组array3
    # array2=(1 2 3 4 5 6 7 "linux shell" [20]=saltstack)

定义方法2:指定索引赋值 
#语法：数组名[index]=变量值
示例
[root@linux-server script]# vim shuzu.sh
#!/bin/bash
NAME[0]="BJ"
NAME[1]="SH"
NAME[2]="SZ"
NAME[3]="GZ"
NAME[4]="HZ"
NAME[5]="ZZ"
echo "First Index: ${NAME[0]}"
echo "Second Index: ${NAME[1]}"
echo "sixth Index: ${NAME[5]}"

输出结果
[root@linux-server script]# bash shuzu.sh 
First Index: BJ
Second Index: SH
sixth Index: ZZ
```

## 访问数组

```shell
当设置任何数组变量时，可以访问它
[root@linux-server script]# aa=(haha heihei baibai)
[root@linux-server script]# echo ${aa[0]}   #访问数组中的第一个元素
[root@linux-server script]# echo ${aa[@]}   #访问数组中所有的元素 等同与echo ${aa[*]} 
[root@linux-server script]# echo ${#aa[@]}  #统计元素的个数 
[root@linux-server script]# echo ${!aa[@]}  #打印数组的所有索引
```

```shell
您可以访问数组中的所有项目通过以下方式之一
${array_name[*]}
${array_name[@]}
```

```shell
shell数组中"*" 和 "@" 区别 
关于在shell脚本中数组变量中 “*”跟 “@” 区别
1.*当变量加上"" 会当成一串字符串处理. 
2.@当变量加上"" 依然当做数组处理. 
3.在没有加上"" 的情况下效果是一样的数组
```

```shell
[root@linux-server script]# vim test1.sh
#!/usr/bin/env bash
array=(gz cloud 19)
echo "case 1"
for line in "${array[@]}"
do
		echo $line
done

echo "case 2"
for line in "${array[*]}"
do
		echo $line
done

echo "case 3"
for line in ${array[*]}
do
		echo $line
done

echo "case 4"
for line in ${array[@]}
do
		echo $line
done
```

```shell
执行结果
[root@linux-server script]# bash test1.sh 
case 1
gz
cloud
19
case 2
gz cloud 19
case 3
gz
cloud
19
case 4
gz
cloud
19
```

# 正则表达式RE

```shell
1.正则表达式（regular expression, RE）是一种字符模式，用于在查找过程中匹配指定的字符。
2.在大多数程序里，正则表达式都被置于两个正斜杠之间；例如/l[oO]ve/就是由正斜杠界定的正则表达式，它将匹配被查找的行中任何位置出现的相同模式。在正则表达式中，#元字符是最重要的概念。
 
#正则表达式分为:
正则表达式基本元字符 
正则表达式拓展元字符

元字符使正则表达式具有处理能力。所谓元字符就是指那些在正则表达式中具有特殊意义的专用字符，可以用来规定其前导字符(即位于元字符前面的字符)在目标对象中的出现模式。
```

## 正则表达式基本元字符

```shell
基本正则表达式元字符 
元字符
示例                      功能                       示例
^                       行首定位符                  ^love 
$                       行尾定位符                  love$ 
.                       匹配单个字符                 l..e  
*                       匹配前导符0到多次            ab*love 
.*                      匹配任意多个字符（贪婪匹配）
[]                      匹配方括号中任意一个字符      [lL]ove
[ - ]                   匹配指定范围内的一个字符      [a-z0-9]ove             
[^]                     匹配不在指定组里的字符        [^a-z0-9]ove
\                       用来转义元字符               love\.
\<                      词首定位符                  \<love
\>                      词尾定位符                  love\>
\(\)                    匹配后的标签
```

## 正则表达式扩展元字符

```shell
扩展正则表达式元字符             功能                  示例
+                       匹配一次或多次前导字符        [a-z]+ove
?                       匹配零次或一次前导字符        lo?ve
a|b                     匹配a或b                   love|hate
x{m}                    字符x重复m次               o{5}
x{m,}                   字符x重复至少m次            o{5,}
x{m,n}                  字符x重复m到n次             o{5,10}
()						字符组						ov+ (ov)+
```

```ini
扩展内容：
自带的特殊字符正则表达式
1） [[:alpha:]]   匹配任何字母，无论大小写
2） [[:alnum:]]  匹配任何字母，无论大小写,还匹配数字0~9
3)   [[:blank:]]    匹配空格和制表符
4)   [[:digit:]]      匹配数字0~9
5)   [[:upper:]]    匹配大写字母
6)   [[:lower:]]     匹配小写字母
7） [[:punct:]]    匹配标点符号
8） [[:print:]]      匹配可打印字符

\w 匹配字母或数字或下划线或汉字 等价于 '[^A-Za-z0-9_]'。（能不能匹配汉字要视你的操作系统和你的应用环境而定）

\s 匹配任意的空白符  （包含水平制表符）

\d 匹配数字

\b 匹配单词的开始或结束
```

## 正则判断

```shell
[root@linux-server ~]# num1=1 
#运用正则，判断需要[[ ]]
[root@linux-server ~]# [[ $num1 =~ ^[0-9]+$ ]] && echo "yes" || echo "no"
yes


这段 Shell 脚本的含义是：定义一个变量 `num1` 并赋值为 1，然后判断 `num1` 的值是否为一个非负整数。具体地，使用正则表达式 `^[0-9]+$` 匹配 `num1` 变量，如果匹配成功则输出字符串 "yes"，否则输出字符串 "no"。

这里使用了双方括号的条件判断语法，其中 `=~` 表示匹配操作符，`^[0-9]+$` 表示一个由数字 0-9 组成的字符串。如果 `num1` 的值为空，或者不是一个整数字符串，则条件判断为假，输出 "no"；否则判断为真，输出 "yes"。



#注意：^在[]内表示取反，^在[]外表示以什么开头
[root@linux-server ~]# num3=1b1 
[root@linux-server ~]# [[ $num3 =~ ^[0-9]+$ ]] && echo "yes" || echo "no"
no

[root@linux-server ~]# num=1.6
[root@linux-server ~]# [[ $num =~ ^[0-9]\.[0-9]+$ || $num =~ ^[0-9]+$ ]] && echo "yes" || echo "no"       //输入的只能是数字(包括小数)
yes
```

# grep

```shell
grep命令是Globally search a Regular Expression and Print的缩写，表示进行全局的正则匹配并进行打印。grep的相关扩展命令egrep其中egrep支持更多的正则匹配。
```

## grep使用

```shell
[root@localhost ~]# grep '^#' /etc/ssh/ssh_config     #过滤以#号开头的行
[root@localhost ~]# grep -v '^#' /etc/ssh/ssh_config  #-v：取反，表示反向查找
[root@localhost ~]# grep 'sendenv' /etc/ssh/ssh_config 
[root@localhost ~]# grep -i 'sendenv' /etc/ssh/ssh_config  #-i忽略大小写
[root@localhost ~]# grep  'bash' /opt/test/ #过滤某个目录下面带有bash的行
[root@localhost ~]# grep  -r 'bash' /opt/test/  #-[r|R]表示递归查询
```

## grep正则过滤

```shell
#grep基本正则匹配

\< 词首定位符号 \>词尾定位符号 
[root@linux-server ~]# cat jack.txt 
Jack JACK JAck jackly jack
:% s/\<[Jj]ack\>/123/g

^以什么开头
[root@linux-server ~]# grep '^root' /etc/passwd
root:x:0:0:root:/root:/bin/bash

$以什么结尾
[root@linux-server ~]# grep 'bash$' /etc/passwd
root:x:0:0:root:/root:/bin/bash
confluence:x:1000:1000:Atlassian Confluence:/home/confluence:/bin/bash 
to:x:1003:1003::/home/to:/bin/bash

. 匹配单个字符
[root@linux-server ~]# grep 'r..t' /etc/passwd 
root:x:0:0:root:/root:/bin/bash 
operator:x:11:0:operator:/root:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin 
dockerroot:x:998:995:Docker User:/var/lib/docker:/sbin/nologin 
[root@linux-server ~]# 
grep 'r.t' /etc/passwd 
operator:x:11:0:operator:/root:/sbin/nologin 
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin

.* 任意多个字符
[root@linux-server ~]# grep 'r.*t' /etc/passwd 
root:x:0:0:root:/root:/bin/bash 
operator:x:11:0:operator:/root:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin 
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin polkitd:x:999:997:User for polkitd:/:/sbin/nologin postfix:x:89:89::/var/spool/postfix:/sbin/nologin 
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin dockerroot:x:998:995:Docker User:/var/lib/docker:/sbin/nologin 
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
abrt:x:1041:1041::/home/abrt:/bin/bash

[] 匹配方括号中的任意一个字符
[root@linux-server ~]# useradd Root
[root@linux-server ~]# grep 'Root' /etc/passwd
[root@linux-server ~]# grep '[Rr]oot' /etc/passwd 
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
Root:x:1000:1000::/home/Root:/bin/bash


[ - ] 匹配指定范围内的一个字符
[root@linux-server ~]# grep [a-z]oot /etc/passwd  #a-z
root:x:0:0:root:/root:/bin/bash 
operator:x:11:0:operator:/root:/sbin/nologin 
dockerroot:x:998:995:Docker User:/var/lib/docker:/sbin/nologin

[^] 匹配不在指定组内的字符,非得意思
[root@linux-server ~]# grep '[^0-9]' /etc/passwd 

[root@linux-server ~]# grep '[^0-9A-Z]oot' /etc/passwd 
root:x:0:0:root:/root:/bin/bash 
operator:x:11:0:operator:/root:/sbin/nologin 
dockerroot:x:998:995:Docker User:/var/lib/docker:/sbin/nologin
#注意：^在[]内表示取反，^在[]外表示以什么开头

\(\)匹配后的标签
[root@linux-server ~]# cat file1.txt 
IPADDR=192.168.1.123 
GATEWAY=192.168.1.1 
NETMASK=255.255.255.0 
DNS=114.114.114.114
:%s/\(192.168.1.\)123/\12/
:%s/\(192.\)\(168.\)\(1.\)12/\1\2\35/

变化后的结果：
IPADDR=192.168.1.2 
GATEWAY=192.168.1.1 
NETMASK=255.255.255.0 
DNS=114.114.114.114


IPADDR=192.168.1.53 
GATEWAY=192.168.1.1 
NETMASK=255.255.255.0 
DNS=114.114.114.114
```

```shell
#扩展正则匹配---egrep
扩展正则表达式元字符             功能                  示例
+                       匹配一次或多次前导字符        [a-z]+ove
?                       匹配零次或一次前导字符        lo?ve
a|b                     匹配a或b                   love|hate
x{m}                    字符x重复m次               o{5}
x{m,}                   字符x重复至少m次            o{5,}
x{m,n}                  字符x重复m到n次             o{5,10}
()						字符组						ov+ (ov)+

egrep 支持正则表达式的拓展元字符
[root@linux-server ~]# egrep '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' /etc/resolv.conf 
nameserver 192.168.246.2

+ 匹配一个或多个前导字符
[root@linux-server ~]# egrep 'ro+t' /etc/passwd 
root:x:0:0:root:/root:/bin/bash 
operator:x:11:0:operator:/root:/sbin/nologin 
dockerroot:x:998:995:Docker User:/var/lib/docker:/sbin/nologin

a|b    匹配a或b
[root@linux-server ~]# netstat -anlp|egrep ':80|:22'
[root@linux-server ~]# egrep 'root|jack' /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
jack1:x:1001:1001::/home/jack1:/bin/bash
jack2:x:1002:1002::/home/jack2:/bin/bash

x{m} 字符x重复m次 
[root@linux-server ~]# cat a.txt
love
love.
loove
looooove 
[root@linux-server ~]# egrep 'o{2}' a.txt
loove
looooove 
[root@linux-server ~]# egrep 'o{2,}' a.txt
loove
looooove 
[root@linux-server ~]# egrep 'o{6,7}' a.txt
```

# sed

```shell
sed:stream editor(流编辑器)的缩写是一种在线非交互式编辑器，它一次处理一行内容。这样不断重复，直到文件末尾。

Sed主要用

来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等

非交互式编辑器，一次处理一行内容。
```

```shell
支持正则表达式
	与grep一样，sed在文件中查找模式时也可以使用正则表达式(RE)和各种元字符,用于查找和替换，以下是sed支持的元字符:
使用基本元字符集	^, $, ., *, [], [^], \< \>,\(\)
使用扩展元字符集	?, +, { }, |, ( )
```

## sed基本用法

```shell
打印
sed  默认会输出文件的每一行，无论这行内容是否能匹配上匹配模式。
语法:
# sed -r '匹配内容'  file_name
-r:支持扩展正则，在实际使用的时候，都会加上 -r 参数，即使没有用的扩展正则也不会有任何影响。
```

```shell
搜索替换
sed会自动打印文件的每一行，同时查找模式匹配的行，找到后执行后面的命令，默认是 `p` 打印（不加 `-n` 的情况下）

示例文件
[root@localhost ~]# vim test.txt
MA Daggett, 341 King Road, Plymouth MA
Alice Ford, 22 East Broadway, Richmond VA 
MA Thomas, 11345 Oak Bridge Road, Tulsa OK 
Terry Kalkas, 402 Ma Road, mA Falls PA 
Eric Adams, 20 Post Road, Sudbury mA
Hubert Sims, 328A Brook Road, Roanoke VA
Amy Wilde, 334 Ma Pkwy, Mountain View CA 
Sal Carpenter, 73 MA Street, Boston MA

1.搜索每一行匹配到MA的将第一个替换为Massachusetts:
[root@localhost ~]# sed -r 's/MA/Massachusetts/' test.txt
s:----查找

2.搜索每一行，找到所有的MA字符，进行全局替换为Massachusetts
[root@localhost ~]# sed -r 's/MA/Massachusetts/g' test.txt

3.搜索每一行，找到所有的MA字符，进行全局替换为Massachusetts同时忽略大小写
[root@localhost ~]# sed -r 's/MA/Massachusetts/gi' test.txt
-i:忽略大小写


4.-n   #静默输出(不打印默认输出)
[root@localhost ~]# sed  -r -n 's/MA/Massachusetts/' test.txt


关闭防火墙selinux
[root@localhost ~]# sed -r 's/SELINUX=disabled/SELINUX=enabled/' /etc/sysconfig/selinux
```

```shell
多重编译选项
1.使用多重指令:-e  给予sed多个命令的时候需要-e选项
[root@localhost ~]# sed -r -e 's/MA/Massachusetts/' -e 's/PA/Pennsylvania/' test.txt

2.使用脚本文件:当有多个要编辑的项目时，可以将编辑命令放进一个脚本里，再使用sed搭配-f选项 
-f <script文件> 以选项中指定的script文件来处理输入的文本文件。

[root@localhost ~]# vim s.sed
s/MA/Massachusetts/ 
s/PA/Pennsylvania/ 
s/CA/California/ 
s/VA/Virginia/ 
s/OK/Oklahoma/
 
[root@localhost ~]# sed -f s.sed test.txt
保存输出:
[root@localhost ~]# sed -f s.sed test.txt > newfile.txt
```

```shell
地址(定址)
地址用于决定对哪些 `行` 进行编辑。地址形式可以是数字、正则表达式或二者的结合。如果没有指定地址，sed将处理输入文件中的所有行。

[root@localhost ~]# head /etc/passwd > passwd  #生成测试文件

[root@localhost ~]# sed -r '1d' passwd   #d:表示删除-- 删除文件的第1行 
bin:x:1:1:bin:/bin:/sbin/nologin 
daemon:x:2:2:daemon:/sbin:/sbin/nologin

[root@localhost ~]# sed -r '1,2d' passwd #删除文件的第1-2行 daemon:x:2:2:daemon:/sbin:/sbin/nologin

[root@localhost ~]# cat passwd
root:x:0:0:root:/root:/bin/bash 
bin:x:1:1:bin:/bin:/sbin/nologin 
daemon:x:2:2:daemon:/sbin:/sbin/nologin 
...
[root@localhost ~]# sed -r '2,$d' passwd     #删除第2行到最后一行
root:x:0:0:root:/root:/bin/bash 

[root@localhost ~]# sed -r '/^root/d' passwd    #匹配到root开头的行，删除此行
bin:x:1:1:bin:/bin:/sbin/nologin 
daemon:x:2:2:daemon:/sbin:/sbin/nologin

[root@localhost ~]# sed -r '/root/d' passwd  #含有root的行都删除
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

[root@localhost ~]# sed -r '/bash/,3d' passwd  #匹配到bash行，从此行到第3行删除
adm:x:3:4:adm:/var/adm:/sbin/nologin

[root@localhost ~]# cat -n passwd
    1 root:x:0:0:root:/root:/bin/bash
    2 bin:x:1:1:bin:/bin:/sbin/nologin
    3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
    4 adm:x:3:4:adm:/var/adm:/sbin/nologin
    5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
    6 sync:x:5:0:sync:/sbin:/bin/sync
    7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
    8 halt:x:7:0:halt:/sbin:/sbin/halt
    9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
    10 operator:x:11:0:operator:/root:/sbin/nologin
[root@localhost ~]# sed -r '1~2d' passwd   #删除奇数行,间隔两行删除
bin:x:1:1:bin:/bin:/sbin/nologin 
adm:x:3:4:adm:/var/adm:/sbin/nologin 
sync:x:5:0:sync:/sbin:/bin/sync 
halt:x:7:0:halt:/sbin:/sbin/halt 
operator:x:11:0:operator:/root:/sbin/nologin

[root@localhost ~]# sed '0~2d' passwd    #删除偶数行，从0开始间隔2行删除
passwd root:x:0:0:root:/root:/bin/bash  
daemon:x:2:2:daemon:/sbin:/sbin/nologin 
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
```

```shell
sed流编辑器命令用法及解析
1.插入命令--i
[root@localhost ~]# sed -r '2i\222222' passwd  #在第2行插入

2.修改命令---c
[root@localhost ~]# sed -r '4c\asfasdf' passwd
[root@localhost ~]# sed -r '4c\11111111\
> aaaaaaaaaa\
> bbbbbbbbb' passwd
案例：
[root@localhost ~]# sed -r '7c\SELINUX=enabled' /etc/sysconfig/selinux


3.选项 -i 会使得sed用修改后的数据替换原文件
[root@localhost ~]# sed -r -i '7c\SELINUX=enabled' /etc/sysconfig/selinux #修改
[root@localhost ~]# sed -r -i 's/SELINUX=enabled/SELINUX=disabled/' /etc/sysconfig/selinux #替换


4.由于在使用 -i 参数时比较危险, 所以我们在使用i参数时在后面加上.bak就会产生一个备份的文件,以防后悔
[root@localhost ~]# sed -r -i.bak 's/root/ROOT/' passwd
```

```shell
sed常见操作
[root@localhost ~]# cp /etc/ssh/ssh_config .

1.删除配置文件中 # 号注释的行
[root@localhost ~]# sed -ri '/^#/d' ssh_config

2.给文件行添加注释：
[root@localhost ~]# sed -r '2,5s/^/#/' passwd
给所有行添加注释：
[root@localhost ~]# sed -r 's/^/#/' passwd

3.给文件行添加和取消注释
[root@localhost ~]# sed -ri s/^#baseurl/baseurl/g /etc/yum.repos.d/CentOS-Base.repo
[root@localhost ~]# sed -r s/^mirrorlist/#mirrorlist/g /etc/yum.repos.d/CentOS-Base.repo
```

# awk

## 简介

```shell
awk 是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自一个或多个文件，或其它命令的输出。可以在命令行中使用，但更多是作为脚本来使用。

awk的处理文本和数据的方式是这样的，它逐行扫描文件，从第一行到最后一行，寻找匹配的特定模式的行，并在这些行上进行操作。如果没有指定模式，则所有被操作所指定的行都被处理。

awk分别代表其作者姓氏的第一个字母。因为它的作者是三个人，分别是Alfred Aho、Brian Kernighan、Peter Weinberger。
```

```shell
awk处理过程: 依次对每一行进行处理，然后输出,默认分隔符是空格或者tab键 
```

## awk形式语法格式

```shell
`awk [options] 'commands' filenames`

`options：`

`-F`  对于每次处理的内容，可以指定一个自定义的输入字段分隔符，默认的分隔符是空白字符（空格或 tab 键 ）
```

## awk工作原理

```shell
`awk -F":"  '{print $1,$3}' /etc/passwd`    7

(1)awk使用一行作为输入，并将这一行赋给变量$0,每一行可称作为一个记录，以换行符结束 
(2)然后，行被空格分解成字段，每个字段存储在已编号的变量中，从$1开始
```

```shell
command
BEGIN{}                   {}               END{}         filename

行处理前的动作          行内容处理的动作     行处理之后的动作    文件名

BEGIN{}和END{} 是可选项。
函数-BEGIN{}：读入文本之前要操作的命令。
{}:主输入循环：用的最多。读入文件之后擦操作的命令。如果不读入文件都可以不用写。
END{}:文本全部读入完成之后执行的命令。
```

```shell
示例
[root@awk ~]# awk 'BEGIN{ print 1/2} {print "ok"} END{print "----"}' /etc/hosts
或者：
[root@awk ~]# cat /etc/hosts | awk 'BEGIN{print 1/2} {print "ok"} END{print "----"}'
0.5
ok
ok
----


这个命令中使用了 awk 工具来处理文件 `/etc/hosts` 的内容。

其中 `'BEGIN{ print 1/2} {print "ok"} END{print "----"}'` 是 awk 的处理脚本，它包含了 BEGIN、END 和主体三个部分。

BEGIN 部分在 awk 开始处理文件前执行，这里打印了表达式 `1/2` 的结果 `0.5`。

主体部分 `{print "ok"}` 对于文件中的每一行都会执行，其中 `"ok"` 是要打印的内容。

END 部分在文件处理结束后执行，这里打印了一行 `"----"`。

因此，整个命令的输出结果应该是在文件 `/etc/hosts` 的每一行后面都加上了一个 `"ok"`，最后两行分别是 `"ok"` 和 `"----"`。
```

## 记录与字段相关内部变量

```shell
1.记录和字段
awk 按记录处理：一行是一条记录，因为awk默认以换行符分开的字符串是一条记录。（默认\n换行符：记录分隔符）
字段：以字段分割符分割的字符串   默认是单个或多个“ ” tab键。

2.awk中的变量
$0:表示整行；
NF : 统计字段的个数
$NF:是number finally,表示最后一列的信息
RS：输入记录分隔符； awk 默认输入记录分隔符为：\n
ORS：输出记录分隔符。 awk 默认输出记录分隔符为：\n
NR:打印记录号，（行号）
FNR：可以分开,按不同的文件打印行号。
FS : 输入字段分隔符,默认为一个空格。  
OFS 输出的字段分隔符，默认为一个空格。 
FILENAME 文件名  被处理的文件名称
$1  第一个字段，$2第二个字段，依次类推...
```

## 实战

```shell
FS(输入字段分隔符)---一般简写为-F(属于行处理前)
[root@awk ~]# cat /etc/passwd | awk 'BEGIN{FS=":"} {print $1,$2}'
root x
bin x
daemon x
adm x
lp x
sync x
shutdown x
halt x
mail x
[root@awk ~]# cat /etc/passwd | awk -F":" '{print $1,$2}'
root x
bin x
daemon x
adm x
lp x
sync x
shutdown x
halt x
mail x

#注：如果-F不加默认为空格区分！
===============================================================
OFS（输出字段分隔符）
[root@awk ~]# cat /etc/passwd | awk 'BEGIN{FS=":";OFS=".."} {print $1,$2}'
root..x
bin..x
daemon..x
adm..x
lp..x
sync..x
shutdown..x
halt..x
mail..x
======================================================================
1.创建两个文件
[root@awk ~]# vim a.txt
love
love.
loove
looooove


[root@awk ~]# vim file1.txt
isuo
IPADDR=192.168.246.211
hjahj123
GATEWAY=192.168.246.1
NETMASK=255.255.255.0
DNS=114.114.114.114

NR   表示记录编号, 在awk将行做为记录, 该变量相当于当前行号，也就是记录号
[root@awk ~]# awk '{print NR,$0}' a.txt file1.txt
1 love
2 love.
3 loove
4 looooove
5  
6 isuo
7 IPADDR=192.168.246.211
8 hjahj123
9 GATEWAY=192.168.246.1
10 NETMASK=255.255.255.0
11 DNS=114.114.114.114

FNR：表示记录编号, 在awk将行做为记录, 该变量相当于当前行号，也就是记录号(#会将不同文件分开)
[root@awk ~]# awk '{print FNR,$0}' a.txt file1.txt
1 love
2 love.
3 loove
4 looooove
5  
1 isuo
2 IPADDR=192.168.246.211
3 hjahj123
4 GATEWAY=192.168.246.1
5 NETMASK=255.255.255.0
6 DNS=114.114.114.114
===========================================================
RS(输入记录分隔符)
1.创建一个文件
[root@awk ~]# vim passwd
root:x:0:0:root:/root:/bin/bashbin:x:1:1:bin:/bin:/sbin/nologin
[root@awk ~]# cat passwd | awk 'BEGIN{RS="bash"} {print $0}' 
root:x:0:0:root:/root:/bin/
bin:x:1:1:bin:/bin:/sbin/nologin

ORS(输出记录分隔符)
2.对刚才的文件进行修改
[root@awk ~]# vim passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
[root@awk ~]# cat passwd | awk 'BEGIN{ORS=" "} {print $0}'
root:x:0:0:root:/root:/bin/bash bin:x:1:1:bin:/bin:/sbin/nologin
===========================================================
NF：统计字段的个数
[root@awk ~]# cat /etc/passwd | awk -F":" '{print NF}'
7
7
7
7
$NF:打印最后一列
[root@awk ~]# cat /etc/passwd | awk -F":" '{print $NF}'
/bin/bash
/sbin/nologin
/sbin/nologin
/sbin/nologin
/sbin/nologin

将文件合并为一行
[root@awk ~]# cat /etc/passwd | awk 'BEGIN{ORS="" } {print $0}'


把一行内容分成多行
1.首先创建一个文件
[root@awk ~]# vim d.txt
root:x:0:0:root:/root:/bin/bash
[root@awk ~]# cat d.txt | awk 'BEGIN{RS=":"} {print $0}'
root
x
0
0
root
/root
```

## 关系运算符号

```shell
实现 字符串的完全相等需要使用 ==

字符串需要使用双引号
!= 表示不等于
```

```shell
[root@awk ~]# awk -F":" '$NF == "/bin/bash"' /etc/passwd
[root@awk ~]# awk -F":" '$1 != "root"' /etc/passwd

这个命令中使用了 awk 工具来处理文件 /etc/passwd 的内容。

其中 -F":" 设置了字段分隔符为冒号 :，这样 awk 就能够正确地解析出每个字段的内容。

'$1 != "root"' 是一个用来过滤数据的模式，它的意思是只输出那些第一列不是 "root" 的行。

最后的 /etc/passwd 是要处理的文件的路径名。

因此，整个命令的输出结果是 /etc/passwd 中除了第一列是 "root" 的所有行，输出的是这些行的全部信息。
```

```shell
比较表达式
比较表达式采用对文本进行比较，只有当条件为真，才执行指定的动作。
比较表达式使用关系运算符，用于比较数字与字符串。

关系运算符有
`<` 小于  例如  `x<y`
`>`  大于 `x>y`
`<=` 小于或等于 `x<=y`
`==` 等于 `x==y`
`!=` 不等于 `x!=y`
`>=` 大于等于 `x>=y`
```

```shell
示例
[root@awk ~]# awk -F":" '$3 == 0' /etc/passwd
[root@awk ~]# awk -F":" '$3 < 10' /etc/passwd
```

```shell
算术运算：`+`, `-`, `*`, `/`, `%(模:   取余)`,   `^(幂：2^3)`

可以在模式中执行计算，awk都将按浮点数方式执行算术运算
```

## 常见使用

```shell
打印一个文件中的第2列和第5列
# cat /etc/passwd | awk -F : '{print $2,$5}'
```

```shell
打印指定行指定列的某个字符
# free -m | awk 'NR==2 {print $2}'
```

```shell
统计一个文件的行数
# cat /etc/passwd | awk '{print NR}'
```

```shell
在awk中使用if条件判断
i++===先赋值在运算
++i===先运算在赋值
if语句：
{if(表达式)｛语句;语句;...｝}

实战案例:
显示管理员用户姓名
[root@qfedu ~]# cat /etc/passwd | awk -F":" '{if($3==0) {print $1 " is administrator"}}'


这个命令的作用是查找 `/etc/passwd` 文件中用户的UID是否为0，如果为0，就输出该用户的用户名和 `is administrator`。

具体来说，这个命令使用了两个常用的命令：`cat` 和 `awk`。`cat` 用来显示文件内容，`awk` 则是一个用于处理文本文件的强大命令行工具。

`cat /etc/passwd` 是将 `/etc/passwd` 文件作为标准输入，然后将其输出到 `awk` 命令中。`awk -F":" '{if($3==0) {print $1 " is administrator"}}'` 设置了 ":" 为分隔符，并且使用条件语句打印出用户的用户名和" is administrator"。

具体的参数意义可以分解为以下几点：

- `-F":"` 是指定分隔符为冒号。
- `'{if($3==0) {print $1 " is administrator"}}'` 是一个 `awk` 脚本，当第三个字段等于 0 时，打印第一个字段和 " is administrator"。


统计系统用户数量
[root@qfedu ~]# cat /etc/passwd | awk -F":" '{if($3>=0 && $3<=1000){i++}} END{print i}'


这条命令的作用是统计系统中 UID（用户 ID）的值介于 0 和 1000 之间的用户数量。

具体步骤如下：

1. `cat /etc/passwd`: 读取系统中所有用户的信息，包括用户名、UID、默认 Shell 等。

2. `|`: 将上一条命令的输出作为下一条命令的输入。

3. `awk -F":" '{if($3>=0 && $3<=1000){i++}} END{print i}'`：根据冒号作为分隔符，将上一步读取的内容进行处理，并输出用户数量。具体解释如下：

- `awk`: 是一个用于文本处理的工具，可以按行读取文本，并逐行进行处理。

- `-F":"`: 指定冒号作为分隔符。

- `if($3>=0 && $3<=1000){i++}`: 判断该行用户的 UID 是否介于 0 和 1000 之间，若满足条件，则 `i` 变量加 1。

- `END{print i}`: 处理完所有行后，输出 `i` 变量的值，即 UID 介于 0 和 1000 之间的用户数量。

综上所述，这条命令的作用是统计系统中 UID 介于 0 和 1000 之间的用户数量。
```

```shell
在awk使用for循环
每行打印两遍
[root@qfedu ~]# awk '{for(i=1;i<=2;i++) {print $0}}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin


这个命令的作用是将 `/etc/passwd` 文件中的每一行都重复打印两次。下面是具体的解释：

- `awk`：一个文本处理工具，可以按照特定的模式进行文本分析和处理。
- `{for(i=1;i<=2;i++) {print $0}}`：一个 awk 脚本，其中：

  - `for(i=1;i<=2;i++)`：一个 for 循环，用来将接下来的指令重复执行两次。
  - `{print $0}`：一个 print 命令，用来将当前行（`$0`）输出到屏幕上。

- `/etc/passwd`：一个文件路径，表示要处理的文件是 `/etc/passwd`。

因此，当你执行这个命令时，它会读取 `/etc/passwd` 文件中的每一行，并将每一行重复输出两次。
```

```shell
数组遍历——用来统计网站日志的访问量
++i：从1开始加，运算在赋值
i++: 从0开始加，赋值在运算
#按索引遍历：
1.先创建一个test文件，统计用户的数量
# vim test.txt  #将文件内容的第一个字段作为数组的值，通过索引获取到值
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
# cat test.txt | awk -F":" '{username[x++]=$1} END{for(i in username) {print i,username[i]}}'
0 root
1 bin
#注意：变量i是索引

这是一个使用 awk 命令的脚本，主要功能是从以冒号分隔的文件中提取出所有用户名。下面是每个部分的含义：

- `-F":"`：设置 awk 的域分隔符为冒号。
- `'{username[x++]=$1}`：使用一个数组 `username` 存储每行的第一个字段，也就是用户名。`x++` 表示每次使用数组时下标 `x` 都会自动加一。
- `END{for(i in username) {print i,username[i]}}'`：在文件处理完毕后，遍历数组 `username` 并打印每个元素的下标和值，即所有的用户名。

该脚本可以用于像 Linux 的 `/etc/passwd` 中提取所有的用户名，并在屏幕上输出它们的列表。
```

```shell
案例
#把要统计的对象作为索引，最后对他们的值进行累加，累加出来的这个值就是你的统计数量

1. 统计/etc/passwd中各种类型shell的数量
# cat /etc/passwd | awk -F ":" '{shells[$NF]++} END{ for(i in shells){print i,shells[i]} }'

以上是一个 Shell 脚本命令，作用是用 awk 命令统计 /etc/passwd 文件中每个用户所使用的 shell 类型的数量，并输出每个 shell 类型出现的次数。具体解释如下：

1. `cat /etc/passwd`：将 /etc/passwd 文件的内容输出到命令行中；
2. `|`：管道符号，将前面一个命令的输出结果作为后面一个命令的输入；
3. `awk -F ":" '{shells[$NF]++} END{ for(i in shells){print i,shells[i]} }'`：awk 是一个流处理器，用于处理文本。该命令的含义为：

- `-F ":"`：指定每行记录的字段分隔符为 ":"；
- `'{shells[$NF]++}`：统计 /etc/passwd 文件中每个用户所使用的 shell 类型的数量，其执行过程如下：

  - `$NF`：系统默认的内置变量，代表该行记录中的最后一个字段，即 shell 类型；
  - `shells[$NF]++`：将每个 shell 类型作为 awk 数组 shells 的索引，每出现一次就将其对应的值加1。

- `END{ for(i in shells){print i,shells[i]} }'`：在 awk 处理完 /etc/passwd 文件后执行，在这里主要是输出每个 shell 类型出现的次数。该语句包含以下两部分：

  - `for(i in shells){print i,shells[i]}`：遍历 awk 数组 shells，将 shell 类型和其出现的次数作为输出，格式为 "shell类型 出现次数"；
  - `END`：表示语句块将在 awk 处理完最后一行记录后执行。

综上所述，该命令的作用是统计每种 shell 类型出现的次数，以便分析和了解系统用户 shell 类型的分布情况。




2.统计nginx日志出现的状态码
# cat access.log | awk '{stat[$9]++} END{for(i in stat){print i,stat[i]}}'

3.统计当前nginx日志中每个ip访问的数量
# cat access.log | awk '{ips[$1]++} END{for(i in ips){print i,ips[i]}}'

4.统计某一天的nginx日志中的不同ip的访问量
# cat access.log |grep '28/Sep/2019' | awk '{ips[$1]++} END{for(i in ips){print i,ips[i]}}' 

5.统计nginx日志中某一天访问最多的前10个ip
# cat access.log |grep '28/Sep/2019' | awk '{ips[$1]++} END{for(i in ips){print i,ips[i]}}' |sort -k2 -rn | head -n 10
sort：排序，默认升序
-k：指定列数
-r：降序
-n：以数值来排序

6.统计tcp连接的状态---下去自己查各个状态，包括什么原因造成的！
# netstat -n | awk '/^tcp/ {tcps[$NF]++} END {for(i in tcps) {print i, tcps[i]}}'
LAST_ACK 5 （正在等待处理的请求数）
SYN_RECV 30
ESTABLISHED 1597 （正常数据传输状态）
FIN_WAIT1 51
FIN_WAIT2 504
TIME_WAIT 1057 （处理完毕，等待超时结束的请求数）
```

```shell
经典案例
UV与PV统计
PV：即访问量，也就是访问您商铺的次数；
例如：今天显示有300 PV，则证明今天你的商铺被访问了300次。
================================================================
UV：即访问人数，也就是有多少人来过您的商铺；  #需要去重
例如：今天显示有50 UV，则证明今天有50个人来过你的商铺。
=================================================================
1.根据访问IP统计UV
# cat access.log | awk '{print $1}' |sort |uniq | wc -l

这是一个Linux命令，它的含义是读取名为 "access.log" 的文件，然后使用管道符将输出传递给下一个命令。
在接下来的命令中，使用了一些常见的文本处理命令，以对文件进行操作：

1. awk '{print $1}' 命令用于提取文件中的第一列数据，并将其打印到标准输出流中。

2. sort 命令用于按照字母顺序排序数据。

3. uniq 命令用于删除重复行，只保留唯一的数据行。

4. wc -l 命令用于统计文件的行数。在此命令的末尾加上-L选项表示只统计非空白行的数量。

因此，这个命令的最终目的是数出 "access.log" 文件的独特IP地址数量。

uniq：去重
-c：统计每行连续出现的次数
2.更具访问ip统计PV
# cat access.log | awk '{print $1}' |wc -l  #统计非空行的数量
或者是url
# cat access.log | awk '{print $7}' |wc -l
3.查询访问最频繁的URL
# cat access.log | awk '{print $7}'|sort | uniq -c |sort -n -k 1 -r | more
4.查询访问最频繁的IP
# cat access.log | awk '{print $1}'|sort | uniq -c |sort -n -k 1 -r | more
```

# expect

```shell
在实际工作中我们运行命令、脚本或程序时, 都需要从终端输入某些继续运行的指令,而这些输 入都需要人为的手工进行. 而利用 expect 则可以根据程序的提示, 模拟标准输入提供给程序, 从而实现自动化交互执 行. 这就是 expect .

它是一个免费的编程工具, 用来实现自动的交互式任务, 而无需人为干预. 说白了 expect 就是一套用来实现自动交互功能的软件 

既:通过expect可以实现将交互式的命令变为非交互式执行，不需要人为干预（手动输入）
```

## 安装

```shell
[root@qfedu ~] yum -y install expect
```

## 语法

```shell
用法: 
1)定义expect脚本执行的shell
		#!/usr/bin/expect     -----类似于#!/bin/bash 
2)spawn
		spawn是执行expect之后后执行的内部命令开启一个会话 #功能:用来执行shell的交互命令

3)expect ---相当于捕捉
		功能:判断输出结果是否包含某项字符串(相当于捕捉命令的返回的提示)。没有捕捉到则会断开，否则等待一段时间后返回，等待通过timeout设置 
4)send
		执行交互动作，将交互要执行的命令进行发送给交互指令，命令字符串结尾要加上“\r”，#---相当于回车
5)interact 
		执行完后保持交互状态，需要等待手动退出交互状态，如果不加这一项，交互完成会自动退出
6)exp_continue 
		继续执行接下来的操作
7）timeout
		返回设置超时时间(秒)
```

## 实战非交互式ssh连接

```shell
普通操作
[root@qfedu script]# vim expect01.sh
#!/usr/bin/expect
spawn ssh root@192.168.246.115

expect {
        "yes/no" {  send "yes\r"; exp_continue }
        "password:" { send "1\r" };
}
interact
[root@qfedu script]# chmod +x expect01.sh
[root@qfedu script]# ./expect01.sh
spawn ssh root@192.168.246.115
root@192.168.246.115's password: 
Last login: Fri Aug 28 16:57:09 2019

#如果添加interact参数将会等待我们手动交互进行退出。如果不加interact参数在登录成功之后会立刻退出。
```

