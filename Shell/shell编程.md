# shell编程

### 初始shell

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

### shell 定义

```shell
Shell 也是一种程序设计语言，它有变量，关键字，各种控制语句，有自己的语法结构，利用shell程序设计语 言可以编写功能很强、代码简短的程序。
shell是外壳的意思，就是系统的外壳，我们可以通过shell的命令来控制和操作操作系统，比如linux中的shell命令就包括ls、cd、pwd等等，总结来说shell就是一个命令解释器，他通过接收用户输入的shell命令来启动、停止程序的运行或者对计算机进行控制。
```

### shell的分类和切换

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

### 使用场景

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

### shell 特性回顾

- shell常见元素 

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

### bash 初始化

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

- bash shell 特性

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

- 历史命令

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

- Bash 部分快捷键

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

- 通配符置换

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

### shell 脚本规范

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

### 变量的类型

```shell
变量：bash作为程序设计语言和其它高级语言一样也提供使用和定义变量的功能，简单说就是让一个特定的字符串代表不固定的内容 a=123,echo $a
#分为：预定义变量、环境变量、自定义变量、位置变量
```

```ini
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

##### 练习1

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
time=`date +%F-%T`
ip=`ip a | grep ens33 | awk 'NR==2 {print $2}'`
echo "现在的时间是:" $time
echo "当前的用户是:" $USER
echo "当前的用户标识:" $UID
      head -1 /etc/passwd | awk -F ":" `{print $3}
echo "当前的主机名称是:" $HOSTNAME
echo "当前可用网卡IP是:" $ip


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

##### 练习二

```shell
编写一个脚本实现收集主机的基本信息，最后脚本还会将这些信息写入一个日志文件.
[root@linux-server script]# vim xinxi.sh
#!/bin/bash
#获取主机基本信息
centime=`date '+%Y-%m-%d %H:%M:%S'`
nowtime=`uptime |awk '{print $1}'`
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

```ini
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

```ini
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

### 脚本运行

##### 创建bash脚本（shell脚本）

```shell
1.创建脚本文件 
		指定命令解释器
		注释
		编写bash指令集合 
2.修改权限
```

##### bash脚本执行

```shell
# chmod +x script
# ./scripts
# /shelldoc/scripts
# source ./scripts 使用当前shell执行 比如cd /tmp会改变当前shell环境，但是其他的方式不会 
# bash scripts
```

### 变量置换

##### 命令替换

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

##### 变量替换

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

总结如下图：

![](assets\shell变量替换.jpg)



##### 变量替换-匹配截取

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

##### basename & dirname

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

### shell编程-流程控制

##### shell编程-条件结构

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

### shell脚本之流程控制

##### shell分支if语句

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
if [ "$1" = "hello" ]; then
        echo "Hello! How are you ?"
elif [ "$1" = "" ]; then
				echo "You MUST input parameters"
else
				echo "The only accept parameter is hello"
fi
[root@linux-server script]# chmod +x if.sh
测试：
[root@linux-server script]# ./if.sh 
[root@linux-server script]# ./if.sh hello
[root@linux-server script]# ./if.sh 434

练习:
1.测试ip地址主机位从2到100的机器是否存活，并把存活的机器记录到文本文件alivehost.txt内。(使用ping命令)
案例
#!/usr/bin/bash
ip=192.168.198
for i in {2..100}
do
        ping -c1 $ip.$i &> /dev/null
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

```shell
作业
1. ping主机测试
2. 判断一个用户是否存在
3. 判断当前内核主版本是否为3，且次版本是否大于10
4. 判断vsftpd软件包是否安装，如果没有则自动安装 (yum是否能用，不能用自动修复，安装完成测试以下，是否能用。)
5. 判断指定的主机是否能ping通，必须使用$1变量
6. 判断用户输入的是否是数字 
read -p "请输入:" get
case $get in 
[0-9][0-9]*)     #判断输入是否是数字
		echo -e "你输入是数字。\n"
;; 
*)
		echo -e "你输入的不是数字。\n"
;; 
esac
```

##### shell 分支case语句

```shell
case 语句是 shell 中流控制的第二种方式,语法如下: 
case $变量 in
     pattern1)
          list1
          ;;                     ---------------------结尾。
     pattern2)
          list2
          ;;
     ... ...
     patternN)
          listN
         ;;
    *)                       --------------------> 如果前面命令没有执行成功那么执行下面这个
         list*
         ;;
esac

命令;;表明流应该跳转到case语句的最后，类似C语言中的break指令。
第一行: 声明case关键字调用case语法, 紧跟的“变量”一般为用户的输入值, in代表从下方的各个模式进行匹配 
第2-4行: 匹配到“pattern1”后进行命令的输出或执行, pattern1: 一般为字符或数值
第11-12行: 当用户输入的字符不存在匹配模式时, 直接执行或打印*)下的命令或语句


实例1：
[root@linux-server script]# vim foo.sh
#!/usr/bin/env bash
case $1 in
        foo)
        echo "bar"
        ;;
        bar)
        echo "foo"
        ;;
        *)
        echo "Usage:$0 '{foo|bar}'"
        ;;
esac
[root@linux-server script]# chmod +x foo.sh
[root@linux-server script]# ./foo.sh bar

练习:建立脚本case.sh，当执行时，要求我们在键盘输入适当的值(one|two|three)，当输入正确时并打印，当输入错误 时会提示你，应该输入正确的值。
```

示例2：

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

练习:
1.建立脚本service.sh,当执行的时候要求输入(1、2、3、4、5)时安装对应的httpd、vim、wget、更换aliyum等功能，当输入错误 时会提示你，应该输入正确的值。
```

### shell编程-循环结构

##### shell循环-for语句

```shell
for i in {取值范围}  #for是关键字 i是变量名 in是关键字
do                  #循环体的开始
				循环体
done                #循环体的结束
```

```shell
实战1：
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

测试成产环境的主机存活性，将up的ip保存在一个文件中，将down的ip保存在一个文件中

```shell
[root@linux-server script]# vim ip.sh
#!/usr/bin/env bash
# Author: 
src_ip="192.168.246"
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

for循环批量创建用户 

```shell
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

##### shell 循环while语句

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
done

注意观察，#请问如何能够自动终止
#在shell中，let命令用于指定算术运算,即 let expr
```

完善系统工具的输出及操作性

```shell
#通过一个文件批量创建用户：
#背景：写一个脚本，满足以下需求及应用，如一个文件的内容如下，根据文件内容实现批量创建用户，第一列为用户名，第二列为密码
[root@localhost script]# vim user_pass.txt #创建用户和密码文件
user1 qfedu123
user2 qfedu456
user3 qfedu567
user4 qfedu789
user5 qfedu012
[root@localhost script]# vim create_user.sh #编写脚本
#!/usr/bin/bash

[ $UID -ne 0 ] && exit 1
while read line
do
        user=`echo $line | awk '{print $1}'`
        pass=`echo $line | awk '{print $2}'`
        id $user &> /dev/null || useradd $user && echo $pass | passwd $user --stdin
done < /opt/test/script/user_pass.txt
[root@localhost script]# chmod +x create_user.sh 
[root@localhost script]# bash create_user.sh

案例二：
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

案例三嵌套循环
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
练习题:
1.输入用户输入的参数，直到用户输入 "end" 结束循环
2.给脚本service.sh进行修改,当执行的时候要求输入(1、2、3、4、5)时安装对应的httpd、vim、wget、更换aliyum等功能，当输入错误 时提示应该输入正确的值但是不会退出。
3.建立批量删除用户脚本
# vim deluser.sh
#!/usr/bin/bash
read -p "请输入用户名: " na
read -p "请输入要删除的个数? " num
echo $num
read -p "确认要删除$na[Y|y]: " x
if [ $x = Y ] || [ $x = y ];then
for i in $(seq 1 $num )
do
echo "$i"
user=$na"$i"
id $user
if [ $? -eq 0 ];then
        userdel -r $user
else
        exit 9
fi
done
fi

#chmod +x deluser.sh
#./deluser.sh
```

##### shell循环until语句

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

##### shell 循环控制shift、continue、break、exit

```ini
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

### shell 编程-函数

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

##### shell 函数function

函数声明

```shell
function_name () {
   list of commands
}
```

函数名 function_name，这就是你将使用它从其他地方在你的脚本调用。 

取消函数 

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

产生以下执行结果

```shell
[root@linux-server script]# bash func.sh 
“This is my first shell function”
```

```shell
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

##### 函数调用之二

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
#check_yum
echo "+++++++++++++++++++++++++++++++++++++++++++++++++++"

install_nginx() {
#检查网络是否可以上网
check_net
echo "正在配置nginx的yum源"
cat > /etc/yum.repos.d/nginx.repo <<EOF
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/7/x86_64/
gpgcheck=0
enabled=1
EOF
yum clean all && sleep 2
yum install -y nginx 2&> /dev/null
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
                exit 4
        fi
else
        echo "nginx install failed!将会自动退出"
        exit 5
fi
}
#install_nginx

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

函数传参

 ```ini
在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 位置参数来进行传参的，$1表示第一个参数，$2表示第二个参数...
 ```

示例 

```shell
[root@linux-server script]# vim fun04.sh
fun() {
        echo $[$1*$2*$3]
}
fun $1 $2 $3
[root@linux-server script]# bash fun04.sh 1 3 5
15
```

### shell 编程-数组

```shell
什么是数组?
数组也是一种变量，常规变量只能保存一个值，数组可以保存多个值

#普通数组:只能用整数作为数组的索引--0  下标
#关联数组:可以使用字符串作为数组的索引
```

##### 数组定义

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

##### 访问数组

当设置任何数组变量时，可以访问它

```shell
[root@linux-server script]# aa=(haha heihei baibai)
[root@linux-server script]# echo ${aa[0]}   #访问数组中的第一个元素
[root@linux-server script]# echo ${aa[@]}   #访问数组中所有的元素 等同与echo ${aa[*]} 
[root@linux-server script]# echo ${#aa[@]}  #统计元素的个数 
[root@linux-server script]# echo ${!aa[@]}  #打印数组的所有索引
```

您可以访问数组中的所有项目通过以下方式之一: 

```shell
${array_name[*]}
${array_name[@]}
```

疑难点
 shell数组中"*" 和 "@" 区别 

```ini 
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

执行结果 

```shell
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

### 正则表达式RE 

-----

---

```shell
1.正则表达式（regular expression, RE）是一种字符模式，用于在查找过程中匹配指定的字符。
2.在大多数程序里，正则表达式都被置于两个正斜杠之间；例如/l[oO]ve/就是由正斜杠界定的正则表达式，它将匹配被查找的行中任何位置出现的相同模式。在正则表达式中，#元字符是最重要的概念。
 
#正则表达式分为:
正则表达式基本元字符 
正则表达式拓展元字符

元字符使正则表达式具有处理能力。所谓元字符就是指那些在正则表达式中具有特殊意义的专用字符，可以用来规定其前导字符(即位于元字符前面的字符)在目标对象中的出现模式。
```

##### No.1 正则表达式基本元字符

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

##### No.2正则表达式拓展元字符

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

```apl
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



##### 正则判断

```shell
[root@linux-server ~]# num1=1 
#运用正则，判断需要[[ ]]
[root@linux-server ~]# [[ $num1 =~ ^[0-9]+$ ]] && echo "yes" || echo "no"
yes
#注意：^在[]内表示取反，^在[]外表示以什么开头
[root@linux-server ~]# num3=1b1 
[root@linux-server ~]# [[ $num3 =~ ^[0-9]+$ ]] && echo "yes" || echo "no"
no

[root@linux-server ~]# num=1.6
[root@linux-server ~]# [[ $num =~ ^[0-9]\.[0-9]+$ || $num =~ ^[0-9]+$ ]] && echo "yes" || echo "no"       //输入的只能是数字(包括小数)
yes
```

### shell 编程-grep

grep命令是Globally search a Regular Expression and Print的缩写，表示进行全局的正则匹配并进行打印。grep的相关扩展命令egrep其中egrep支持更多的正则匹配。

##### grep使用

```shell
[root@localhost ~]# grep '^#' /etc/ssh/ssh_config     #过滤以#号开头的行
[root@localhost ~]# grep -v '^#' /etc/ssh/ssh_config  #-v：取反，表示反向查找
[root@localhost ~]# grep 'sendenv' /etc/ssh/ssh_config 
[root@localhost ~]# grep -i 'sendenv' /etc/ssh/ssh_config  #-i忽略大小写
[root@localhost ~]# grep  'bash' /opt/test/ #过滤某个目录下面带有bash的行
[root@localhost ~]# grep  -r 'bash' /opt/test/  #-[r|R]表示递归查询
```

##### grep正则过滤

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

### shell 编程-SED

---

sed:stream editor(流编辑器)的缩写是一种在线非交互式编辑器，它一次处理一行内容。这样不断重复，直到文件末尾。

Sed主要用

来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等

非交互式编辑器，一次处理一行内容。

```shell
支持正则表达式
	与grep一样，sed在文件中查找模式时也可以使用正则表达式(RE)和各种元字符,用于查找和替换，以下是sed支持的元字符:
使用基本元字符集	^, $, ., *, [], [^], \< \>,\(\)
使用扩展元字符集	?, +, { }, |, ( )
```

### sed基本用法

#### 打印

> sed  默认会输出文件的每一行，无论这行内容是否能匹配上匹配模式。

```shell
语法:
# sed -r '匹配内容'  file_name
-r:支持扩展正则，在实际使用的时候，都会加上 -r 参数，即使没有用的扩展正则也不会有任何影响。
```

##### 搜索替换

sed会自动打印文件的每一行，同时查找模式匹配的行，找到后执行后面的命令，默认是 `p` 打印（不加 `-n` 的情况下）

```shell
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


案例：
[root@localhost ~]# sed -r 's/SELINUX=disabled/SELINUX=enabled/' /etc/sysconfig/selinux
```

##### 多重编辑选项

```shell
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

##### 地址（定址）

地址用于决定对哪些 `行` 进行编辑。地址形式可以是数字、正则表达式或二者的结合。如果没有指定地址，sed将处理输入文件中的所有行。

```shell
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

##### sed流编辑器命令用法及解析

```shell
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

##### sed常见操作

```shell
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

# shell编程之awk

##### 一、awk简介

awk 是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自一个或多个文件，或其它命令的输出。可以在命令行中使用，但更多是作为脚本来使用。

awk的处理文本和数据的方式是这样的，它逐行扫描文件，从第一行到最后一行，寻找匹配的特定模式的行，并在这些行上进行操作。如果没有指定模式，则所有被操作所指定的行都被处理。

awk分别代表其作者姓氏的第一个字母。因为它的作者是三个人，分别是Alfred Aho、Brian Kernighan、Peter Weinberger。

```makefile
awk处理过程: 依次对每一行进行处理，然后输出,默认分隔符是空格或者tab键 
```

##### 二、awk的形式语法格式

`awk [options] 'commands' filenames`

`options：`

`-F`  对于每次处理的内容，可以指定一个自定义的输入字段分隔符，默认的分隔符是空白字符（空格或 tab 键 ）

##### 三、awk工作原理

`awk -F":"  '{print $1,$3}' /etc/passwd`    7

```basic
(1)awk使用一行作为输入，并将这一行赋给变量$0,每一行可称作为一个记录，以换行符结束 
(2)然后，行被空格分解成字段，每个字段存储在已编号的变量中，从$1开始
```

`command：`

```basic
BEGIN{}                   {}               END{}         filename

行处理前的动作          行内容处理的动作     行处理之后的动作    文件名

BEGIN{}和END{} 是可选项。
函数-BEGIN{}：读入文本之前要操作的命令。
{}:主输入循环：用的最多。读入文件之后擦操作的命令。如果不读入文件都可以不用写。
END{}:文本全部读入完成之后执行的命令。
```

###### 示例

```bash
[root@awk ~]# awk 'BEGIN{ print 1/2} {print "ok"} END{print "----"}' /etc/hosts
或者：
[root@awk ~]# cat /etc/hosts | awk 'BEGIN{print 1/2} {print "ok"} END{print "----"}'
0.5
ok
ok
----
```

##### 四、记录与字段相关内部变量：

```bash
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

###### 实战

```bash
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
```

```shell
练习1：将文件合并为一行
[root@awk ~]# cat /etc/passwd | awk 'BEGIN{ORS="" } {print $0}'
练习2：把一行内容分成多行
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

##### 关系运算符号

实现 字符串的完全相等需要使用 `==`

> 字符串需要使用双引号
> `!=` 表示不等于

```bash
[root@awk ~]# awk -F":" '$NF == "/bin/bash"' /etc/passwd
[root@awk ~]# awk -F":" '$1 != "root"' /etc/passwd
```


- 比较表达式：

> 比较表达式采用对文本进行比较，只有当条件为真，才执行指定的动作。
> 比较表达式使用***关系运算符***，用于比较数字与字符串。
>
> 关系运算符有
> `<` 小于  例如  `x<y`
> `>`  大于 `x>y`
> `<=` 小于或等于 `x<=y`
> `==` 等于 `x==y`
> `!=` 不等于 `x!=y`
> `>=` 大于等于 `x>=y`

###### 示例

```bash
[root@awk ~]# awk -F":" '$3 == 0' /etc/passwd
[root@awk ~]# awk -F":" '$3 < 10' /etc/passwd
```

- 算术运算：`+`, `-`, `*`, `/`, `%(模:   取余)`,   `^(幂：2^3)`

> 可以在模式中执行计算，awk都将按浮点数方式执行算术运算

```bash
awk -F: '$3 * 10 > 500' /etc/passwd
```

##### 常见使用

1.打印一个文件中的第2列和第5列

```shell
# cat /etc/passwd | awk -F : '{print $2,$5}'
```

2.打印指定行指定列的某个字符

```shell
# free -m | awk 'NR==2 {print $2}'
```

3.统计一个文件的行数

```shell
# cat /etc/passwd | awk '{print NR}'
```

获取根分区的使用量

4.在awk中使用if条件判断

```bash
i++===先赋值在运算
++i===先运算在赋值
if语句：
{if(表达式)｛语句;语句;...｝}

实战案例:
显示管理员用户姓名
[root@qfedu ~]# cat /etc/passwd | awk -F":" '{if($3==0) {print $1 " is administrator"}}'

统计系统用户数量
[root@qfedu ~]# cat /etc/passwd | awk -F":" '{if($3>=0 && $3<=1000){i++}} END{print i}'
```

5.在awk中使用for循环

```bash
每行打印两遍
[root@qfedu ~]# awk '{for(i=1;i<=2;i++) {print $0}}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```

**数组遍历**--用来统计网站日志的访问量。

```bash
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
```

##### 真实案例

```bash
#把要统计的对象作为索引，最后对他们的值进行累加，累加出来的这个值就是你的统计数量

1. 统计/etc/passwd中各种类型shell的数量
# cat /etc/passwd | awk -F: '{shells[$NF]++} END{ for(i in shells){print i,shells[i]} }'

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

##### 经典案例

```bash
UV与PV统计
PV：即访问量，也就是访问您商铺的次数；
例如：今天显示有300 PV，则证明今天你的商铺被访问了300次。
================================================================
UV：即访问人数，也就是有多少人来过您的商铺；  #需要去重
例如：今天显示有50 UV，则证明今天有50个人来过你的商铺。
=================================================================
1.根据访问IP统计UV
# cat access.log | awk '{print $1}' |sort |uniq | wc -l
uniq：去重
-c：统计每行连续出现的次数
2.更具访问ip统计PV
# cat access.log | awk '{print $1}' |wc -l
或者是url
# cat access.log | awk '{print $7}' |wc -l
3.查询访问最频繁的URL
# cat access.log | awk '{print $7}'|sort | uniq -c |sort -n -k 1 -r | more
4.查询访问最频繁的IP
# cat access.log | awk '{print $1}'|sort | uniq -c |sort -n -k 1 -r | more
```

作业:切割nginx的日志，统计PV\UV，出现次数最多的url等各种切割统计

### shell 编程-Expect

在实际工作中我们运行命令、脚本或程序时, 都需要从终端输入某些继续运行的指令,而这些输 入都需要人为的手工进行. 而利用 expect 则可以根据程序的提示, 模拟标准输入提供给程序, 从而实现自动化交互执 行. 这就是 expect .

它是一个免费的编程工具, 用来实现自动的交互式任务, 而无需人为干预. 说白了 expect 就是一套用来实现自动交互功能的软件 

既:通过expect可以实现将交互式的命令变为非交互式执行，不需要人为干预（手动输入）

##### No.1 expect的安装

`[root@qfedu ~] yum -y install expect `

##### No.2 expect的语法:

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

##### 实战非交互式ssh连接：

```shell
案例1：普通操作
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
============================================================================
```

