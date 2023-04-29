# 简介

## 什么是shell

```shell
Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。
Shell 既是一种命令语言，又是一种程序设计语言。
Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问 Linux 内核的服务。
Ken Thompson 的 sh 是第一种 Unix Shell，Windows Explorer 是一个典型的图形界面 Shell。
```

## 什么是shell脚本

```shell
Shell 脚本（shell script），是一种为 shell 编写的脚本程序，一般文件后缀为 .sh。

业界所说的 shell 通常都是指 shell 脚本，但 shell 和 shell script 是两个不同的概念。
```

## shell环境

```shell
Shell 编程跟 java、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

Shell 的解释器种类众多，常见的有：

sh - 即 Bourne Shell。sh 是 Unix 标准默认的 shell。
bash - 即 Bourne Again Shell。bash 是 Linux 标准默认的 shell。
fish - 智能和用户友好的命令行 shell。
xiki - 使 shell 控制台更友好，更强大。
zsh - 功能强大的 shell 与脚本语言。
```

## 制定shell脚本解释器

```shell
在 shell 脚本，#! 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 解释器。#! 被称作shebang（也称为 Hashbang ）。

所以，你应该会在 shell 中，见到诸如以下的注释：

制定sh解释器
#!/bin/sh

制定bash解释器
#!/bin/bash

注意
上面的指定解释器的方式是比较常见的，但有时候，你可能也会看到下面的方式：
#!/usr/bin/env bash

这样做的好处是，系统会自动在 PATH 环境变量中查找你指定的程序（本例中的bash）。相比第一种写法，你应该尽量用这种写法，因为程序的路径是不确定的。这样写还有一个好处，操作系统的PATH变量有可能被配置为指向程序的另一个版本。比如，安装完新版本的bash，我们可能将其路径添加到PATH中，来“隐藏”老版本。如果直接用#!/bin/bash，那么系统会选择老版本的bash来执行脚本，如果用#!/usr/bin/env bash，则会使用新版本。
```

## 模式

```shell
shell 有交互和非交互两种模式。
```

```shell
1、交互模式
简单来说，你可以将 shell 的交互模式理解为执行命令行。

看到形如下面的东西，说明 shell 处于交互模式下：

user@host:~$
接着，便可以输入一系列 Linux 命令，比如 ls，grep，cd，mkdir，rm 等等。

2、非交互模式
简单来说，你可以将 shell 的非交互模式理解为执行 shell 脚本。

在非交互模式下，shell 从文件或者管道中读取命令并执行。

当 shell 解释器执行完文件中的最后一个命令，shell 进程终止，并回到父进程。

可以使用下面的命令让 shell 以非交互模式运行：
sh /path/to/script.sh
bash /path/to/script.sh
source /path/to/script.sh
./path/to/script.sh

上面的例子中，script.sh是一个包含 shell 解释器可以识别并执行的命令的普通文本文件，sh和bash是 shell 解释器程序。你可以使用任何喜欢的编辑器创建script.sh（vim，nano，Sublime Text, Atom 等等）。

其中，source /path/to/script.sh 和 ./path/to/script.sh 是等价的。

除此之外，你还可以通过chmod命令给文件添加可执行的权限，来直接执行脚本文件：
chmod +x /path/to/script.sh #使脚本具有执行权限
/path/to/test.sh

这种方式要求脚本文件的第一行必须指明运行该脚本的程序，比如：
#!/usr/bin/env bash
echo "Hello, world!"
上面的例子中，我们使用了一个很有用的命令echo来输出字符串到屏幕上。
```

## 基本语法

```shell
1、解释器
前面虽然两次提到了#! ，但是本着重要的事情说三遍的精神，这里再强调一遍：

在 shell 脚本，#! 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 解释器。#! 被称作shebang（也称为 Hashbang ）。

#! 决定了脚本可以像一个独立的可执行文件一样执行，而不用在终端之前输入sh, bash, python, php等。

# 以下两种方式都可以指定 shell 解释器为 bash，第二种方式更好
#!/bin/bash
#!/usr/bin/env bash
```

```shell
2、注释
注释可以说明你的代码是什么作用，以及为什么这样写。

shell 语法中，注释是特殊的语句，会被 shell 解释器忽略。

单行注释 - 以 # 开头，到行尾结束。
多行注释 - 以 :<<EOF 开头，到 EOF 结束。

#--------------------------------------------
# shell 注释示例
# author：zp
#--------------------------------------------

# echo '这是单行注释'

########## 这是分割线 ##########

:<<EOF
echo '这是多行注释'
echo '这是多行注释'
echo '这是多行注释'
EOF
```

# echo

```shell
echo用于字符串的输出
```

```shell
输出普通字符串
echo "hello, world"
# Output: hello, world
```

```shell
输出含变量的字符
echo "hello, \"zp\""
# Output: hello, "zp"
```

```shell
输出含变量的字符串
name=zp
echo "hello, \"${name}\""
# Output: hello, "zp"
```

```shell
输出含换行的字符串
echo "YES\nNO"
#  Output: YES\nNO

echo -e "YES\nNO" # -e 开启转义
#  Output:
#  YES
#  NO
```

```shell
输出含不换行的字符串
echo "YES"
echo "NO"
#  Output:
#  YES
#  NO

echo -e "YES\c" # -e 开启转义 \c 不换行
echo "NO"
#  Output:
#  YESNO
```

```shell
输出重定向至文件
echo "test" > test.txt
```

```shell
输出执行结果
echo `pwd`
#  Output:(当前目录路径)
```

## **💻 『示例源码』**

```shell
#!/usr/bin/env bash

# 输出普通字符串
echo "hello, world"
#  Output: hello, world

# 输出含变量的字符串
echo "hello, \"zp\""
#  Output: hello, "zp"

# 输出含变量的字符串
name=zp
echo "hello, \"${name}\""
#  Output: hello, "zp"

# 输出含换行符的字符串
echo "YES\nNO"
#  Output: YES\nNO
echo -e "YES\nNO" # -e 开启转义
#  Output:
#  YES
#  NO

# 输出含不换行符的字符串
echo "YES"
echo "NO"
#  Output:
#  YES
#  NO

echo -e "YES\c" # -e 开启转义 \c 不换行
echo "NO"
#  Output:
#  YESNO

# 输出内容定向至文件
echo "test" > test.txt

# 输出执行结果
echo `pwd`
#  Output:(当前目录路径)
```

# printf

```shell
printf 用于格式化输出字符串。
默认，printf 不会像 echo 一样自动添加换行符，如果需要换行可以手动添加 \n。
```

## **💻 『示例源码』**

```shell
# 单引号
printf '%d %s\n' 1 "abc"
#  Output:1 abc

# 双引号
printf "%d %s\n" 1 "abc"
#  Output:1 abc

# 无引号
printf %s abcdef
#  Output: abcdef(并不会换行)

# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出
printf "%s\n" abc def
#  Output:
#  abc
#  def

printf "%s %s %s\n" a b c d e f g h i j
#  Output:
#  a b c
#  d e f
#  g h i
#  j

# 如果没有参数，那么 %s 用 NULL 代替，%d 用 0 代替
printf "%s and %d \n"
#  Output:
#   and 0

# 格式化输出
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876
#  Output:
#  姓名     性别   体重kg
#  郭靖     男      66.12
#  杨过     男      48.65
#  郭芙     女      47.99
```

## printf转义符

```shell
序列	说明
\a	警告字符，通常为 ASCII 的 BEL 字符
\b	后退
\c	抑制（不显示）输出结果中任何结尾的换行字符（只在%b 格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略
\f	换页（formfeed）
\n	换行
\r	回车（Carriage return）
\t	水平制表符
\v	垂直制表符
\\	一个字面上的反斜杠字符
\ddd	表示 1 到 3 位数八进制值的字符。仅在格式字符串中有效
\0ddd	表示 1 到 3 位的八进制值字符
```

# 变量

```shell
跟许多程序设计语言一样，你可以在 bash 中创建变量。

Bash 中没有数据类型，bash 中的变量可以保存一个数字、一个字符、一个字符串等等。同时无需提前声明变量，给变量赋值会直接创建变量。
```

## 变量命名原则

```shell
命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
中间不能有空格，可以使用下划线（_）。
不能使用标点符号。
不能使用 bash 里的关键字（可用 help 命令查看保留关键字）。
```

## 声明变量

```shell
访问变量的语法形式为：${var} 和 $var 。

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，所以推荐加花括号。
```

```shell
word="hello"
echo ${word}
# Output: hello
```

## 只读变量

```shell
使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。
```

```shell
rword="hello"
echo ${rword}
readonly rword
# rword="bye"  # 如果放开注释，执行时会报错

[root@cainana ~]# bash a.sh
hello
```

## 删除变量

```shell
使用 unset 命令可以删除变量。变量被删除后不能再次使用。unset 命令不能删除只读变量。
```

```shell
dword="hello"  # 声明变量
echo ${dword}  # 输出变量值
# Output: hello

unset dword    # 删除变量
echo ${dword}
# Output: （空）
```

## 变量类型

```shell
局部变量 - 局部变量是仅在某个脚本内部有效的变量。它们不能被其他的程序和脚本访问。
环境变量 - 环境变量是对当前 shell 会话内所有的程序或脚本都可见的变量。创建它们跟创建局部变量类似，但使用的是 export 关键字，shell 脚本也可以定义环境变量。
```

```shell
常见的环境变量
变量	描述
$HOME	当前用户的用户目录
$PATH	用分号分隔的目录列表，shell 会到这些目录中查找命令
$PWD	当前工作目录
$RANDOM	0 到 32767 之间的整数
$UID	数值类型，当前用户的用户 ID
$PS1	主要系统输入提示符
$PS2	次要系统输入提示符
```

## **💻 『示例源码』**

```shell
#!/usr/bin/env bash

################### 声明变量 ###################
name="world"
echo "hello ${name}"
# Output: hello world

################### 输出变量 ###################
folder=$(pwd)
echo "current path: ${folder}"

[root@cainana ~]# bash a.sh
current path: /root

################### 只读变量 ###################
rword="hello"
echo ${rword}
# Output: hello
readonly rword
# rword="bye"  # 如果放开注释，执行时会报错

################### 删除变量 ###################
dword="hello" # 声明变量
echo ${dword} # 输出变量值
# Output: hello

unset dword # 删除变量
echo ${dword}
# Output: （空）

################### 系统变量 ###################
echo "UID:$UID"
echo LOGNAME:$LOGNAME
echo User:$USER
echo HOME:$HOME
echo PATH:$PATH
echo HOSTNAME:$HOSTNAME
echo SHELL:$SHELL
echo LANG:$LANG

################### 自定义变量 ###################
days=10
user="admin"
echo "$user logged in $days days age"
days=5
user="root"
echo "$user logged in $days days age"
# Output:
# admin logged in 10 days age
# root logged in 5 days age

################### 从变量读取列表 ###################
colors="Red Yellow Blue"
colors=$colors" White Black"

for color in $colors
do
	echo " $color"
done

[root@cainana ~]# bash a.sh
 Red
 Yellow
 Blue
 White
 Black
```

# 字符串

## 单引号和双引号

```shell
shell 字符串可以用单引号 ''，也可以用双引号 “”，也可以不用引号。

单引号的特点
单引号里不识别变量
单引号里不能出现单独的单引号（使用转义符也不行），但可成对出现，作为字符串拼接使用。
双引号的特点
双引号里识别变量
双引号里可以出现转义字符
综上，推荐使用双引号。
```

## 拼接字符串

```shell
# 使用单引号拼接
name1='white'
str1='hello, '${name1}''
str2='hello, ${name1}'
echo ${str1}_${str2}
# Output:
# hello, white_hello, ${name1}

# 使用双引号拼接
name2="black"
str3="hello, "${name2}""
str4="hello, ${name2}"
echo ${str3}_${str4}
# Output:
# hello, black_hello, black
```

## 获取字符串长度

```shell
text="12345"
echo ${#text}
# Output:
# 5
```

## 截取子字符串

```shell
text="12345"
echo ${text:2:2}
# Output:
# 34

从第 3 个字符开始，截取 2 个字符
```

## 查找子字符串

```shell
#!/usr/bin/env bash

text="hello"
echo `expr index "${text}" ll`

# Execute: ./str-demo5.sh
# Output:
# 3

查找 ll 子字符在 hello 字符串中的起始位置。
```

## **💻 『示例源码』**

```shell
#!/usr/bin/env bash

################### 使用单引号拼接字符串 ###################
name1='white'
str1='hello, '${name1}''
str2='hello, ${name1}'
echo ${str1}_${str2}
# Output:
# hello, white_hello, ${name1}

################### 使用双引号拼接字符串 ###################
name2="black"
str3="hello, "${name2}""
str4="hello, ${name2}"
echo ${str3}_${str4}
# Output:
# hello, black_hello, black

################### 获取字符串长度 ###################
text="12345"
echo "${text} length is: ${#text}"
# Output:
# 12345 length is: 5

# 获取子字符串
text="12345"
echo ${text:2:2}
# Output:
# 34

################### 查找子字符串 ###################
text="hello"
echo `expr index "${text}" ll`
# Output:
# 3

################### 判断字符串中是否包含子字符串 ###################
result=$(echo "${str}" | grep "feature/")
if [[ "$result" != "" ]]; then
	echo "feature/ 是 ${str} 的子字符串"
else
	echo "feature/ 不是 ${str} 的子字符串"
fi


[root@cainana ~]# bash a.sh
feature/ 不是  的子字符串


################### 截取关键字左边内容 ###################
full_branch="feature/1.0.0"
branch=`echo ${full_branch#feature/}`   #将feature/截取掉、不要了
echo "branch is ${branch}"

[root@cainana ~]# bash a.sh    
branch is 1.0.0


################### 截取关键字右边内容 ###################
full_version="0.0.1-SNAPSHOT"
version=`echo ${full_version%-SNAPSHOT}`
echo "version is ${version}"

[root@cainana ~]# bash a.sh
version is 0.0.1


################### 字符串分割成数组 ###################
str="0.0.0.1"
OLD_IFS="$IFS"     #当前IFS变量的值备份到OLD_IFS变量中
IFS="."			   #将IFS变量的值修改为"."，即使用"."作为数组分隔符。
array=( ${str} )   #将字符串str使用IFS变量的分隔符分割，将分割后的结果依次存储到名为array的数组中。
IFS="$OLD_IFS"     #将IFS变量的值恢复为原先的值，即为默认的空格、制表符和换行符
size=${#array[*]}  #使用变量size记录数组array的长度
lastIndex=`expr ${size} - 1`   #使用变量lastIndex记录数组的最后一个元素的下标。
echo "数组长度：${size}"
echo "最后一个数组元素：${array[${lastIndex}]}"
for item in ${array[@]}  #使用for循环遍历数组array，并将每个元素的值分别赋值给变量item，然后使用echo命令输出item的值。其中@表示遍历数组中的所有元素。
do
	echo "$item"
done


[root@cainana ~]# bash a.sh
数组长度：4
最后一个数组元素：1
0
0
0
1


################### 判断字符串是否为空 ###################
#-n 判断长度是否非零
#-z 判断长度是否为零

str=testing
str2=''
if [[ -n "$str" ]]
then
	echo "The string $str is not empty"
else
	echo "The string $str is empty"
fi

if [[ -n "$str2" ]]
then
	echo "The string $str2 is not empty"
else
	echo "The string $str2 is empty"
fi

#	Output:
#	The string testing is not empty
#	The string  is empty


这段shell脚本的含义如下：
1.定义了一个变量str，并将其值设置为testing。
2.定义了一个空变量str2。
3.使用if语句判断变量$str是否为空，如果不为空则输出"The string $str is not empty"，否则输出"The string $str is empty"。
4.使用if语句判断变量$str2是否为空，如果不为空则输出"The string $str2 is not empty"，否则输出"The string $str2 is empty"。

在执行该脚本时，会输出"The string testing is not empty"和"The string  is empty"，因为$str变量有内容，而$str2变量是空的。其中，双方括号用于条件测试，并提供了更多的测试选项，比如字符串判断、数字判断等。 "-n"则是用于判断字符串是否为空。


################### 字符串比较 ###################
str=hello
str2=world
if [[ $str = "hello" ]]; then
	echo "str equals hello"
else
	echo "str not equals hello"
fi

if [[ $str2 = "hello" ]]; then
	echo "str2 equals hello"
else
	echo "str2 not equals hello"
fi
```

# 数组

```shell
bash 只支持一维数组。

数组下标从 0 开始，下标可以是整数或算术表达式，其值应大于或等于 0。
```

## 创建数组

```shell
# 创建数组的不同方式
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")
```

## 访问数组元素

```shell
访问数组单个元素
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")
echo ${nums[1]}
```

```shell
访问数组的所有元素
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")
echo ${colors[*]}
# Output: red yellow dark blue

echo ${colors[@]}
# Output: red yellow dark blue
```

```shell
上面两行有很重要（也很微妙）的区别：
```

```shell
为了将数组中每个元素单独一行输出，我们用 printf 命令：
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")
printf "+ %s\n" ${colors[*]}
# Output:
# + red
# + yellow
# + dark
# + blue

这段shell脚本的含义如下:
1.定义了一个名为nums的数组，这个数组有三个元素分别是2、0、1，其中每个元素都有一个关联的索引值。
2.定义了一个名为colors的数组，这个数组有三个元素分别是red、yellow和"dark blue"。
3.使用printf函数打印输出了colors数组的所有元素，其中"%s"是格式控制字符串，${colors[*]}表示将数组中的所有元素作为参数进行组合和展开，通过*号可以将数组膨胀为一系列的参数。
在执行该脚本时，会输出如下内容：

+ red
+ yellow
+ dark blue

即打印输出了colors数组中的所有元素。注意到printf命令会解释其中的转义字符，所以在输出内容中会有"+"号作为每一个元素的前缀。此外，对于数组中含有空格的元素，应该使用双引号进行括起来，否则数组元素会被错误地分割。
```

```shell
为什么dark和blue各占一行？尝试用引号包起来：
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")
printf "+ %s\n" "${colors[*]}"
# Output:
# + red yellow dark blue
```

```shell
现在所有的元素都在一行输出 —— 这不是我们想要的！让我们试试${colors[@]}
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")
printf "+ %s\n" "${colors[@]}"
# Output:
# + red
# + yellow
# + dark blue

在引号内，${colors[@]}将数组中的每个元素扩展为一个单独的参数；数组元素中的空格得以保留。
```

```shell
访问数组的部分元素
nums=([2]=2 [0]=0 [1]=1)
echo ${nums[@]:0:2}
# Output:
# 0 1

这段 Shell 脚本的含义是先定义了一个数组 `nums`，并在`nums`数组中分别指定了三个位置分别为 0、1、2 的元素的值。其中，`[2]=2` 表示 `nums` 数组中位置为 2 的元素值为 2，`[0]=0` 表示位置为 0 的元素值为 0，`[1]=1` 表示位置为 1 的元素值为 1。

接着，这段脚本使用了 Shell 中的字符串截取操作 `${nums[@]:0:2}`，其中 `nums[@]` 代表整个数组，`:0:2` 代表要取出数组从下标 0 开始的连续两个元素。最后使用 `echo` 命令输出取出的元素，即输出 `0 1`。

${array[@]} 扩展为整个数组，:0:2取出了数组中从 0 开始，长度为 2 的元素。
```

## 访问数组长度

```shell
nums=([2]=2 [0]=0 [1]=1)
echo ${#nums[*]}
# Output:
# 3
```

## 向数组中添加元素

```shell
colors=(red yellow "dark blue")
colors=(white "${colors[@]}" green black)
echo ${colors[@]}
# Output:
# white red yellow dark blue green black

上面的例子中，${colors[@]} 扩展为整个数组，并被置换到复合赋值语句中，接着，对数组colors的赋值覆盖了它原来的值。
```

## 从数组中删除元素

```shell
用unset命令来从数组中删除一个元素：
nums=([2]=2 [0]=0 [1]=1)
unset nums[0]
echo ${nums[@]}
# Output:
# 1 2
```

## **💻 『示例源码』**

```shell
#!/usr/bin/env bash

################### 创建数组 ###################
nums=( [ 2 ] = 2 [ 0 ] = 0 [ 1 ] = 1 )
colors=( red yellow "dark blue" )

################### 访问数组的单个元素 ###################
echo ${nums[1]}
# Output: 1

################### 访问数组的所有元素 ###################
echo ${colors[*]}
# Output: red yellow dark blue

echo ${colors[@]}
# Output: red yellow dark blue

printf "+ %s\n" ${colors[*]}
# Output:
# + red
# + yellow
# + dark
# + blue

printf "+ %s\n" "${colors[*]}"
# Output:
# + red yellow dark blue

printf "+ %s\n" "${colors[@]}"
# Output:
# + red
# + yellow
# + dark blue

################### 访问数组的部分元素 ###################
echo ${nums[@]:0:2}
# Output:
# 0 1

################### 获取数组长度 ###################
echo ${#nums[*]}
# Output:
# 3

################### 向数组中添加元素 ###################
colors=( white "${colors[@]}" green black )
echo ${colors[@]}
# Output:
# white red yellow dark blue green black

################### 从数组中删除元素 ###################
unset nums[ 0 ]
echo ${nums[@]}
# Output:
# 1 2
```

待续................
