# Shell basic

## 加载过程中执行的文件

* login shell:  /etc/profile  -->  ~/.bash_profile ---> ~/.bash_login ---> ~/.profile
* interactive shell(subshell):  /etc/bash.bashrc ---> ~/.bashrc

## $相关

> * $$     当前进程PID
> * $?      最后一个执行命令的返回值
> * $#     positional parameter的个数
> * $@    所有参数当成一个string中的多个分隔的word
> * $*     把所有参数当成一个word，也就是说，当成只有一个参数
> * ${<u>num</u>}     取参数，区分token，如引用数组下标时 \${arr[0]}
> * $(<u>command</u>)      取command的返回值
> * $((<u>expression</u>)) 、\$[]  (尽量不用)  取算数表达式的返回值

## key combination

> CTRL+Z	暂停任务
>
> CTRL+C	hang-up任务，终止任务
>
> CTRL+D	EOF字符，表示读结束

## terminology

### metacharacter

> 在没有被引号括起来的时候，元字符用于分隔word(token)，有以下符号
>
> | 	 &	 ;	 (	 )	 <	 > 	space	 tab	 newline

### control operator

> 起控制功能的token，有以下符号
>
> ||	 &	 &&	 ;	 ;;	 ;& 	;;& 	( 	) 	| 	|& 	<newline>

### single command

> 第一个token为命令名，后面接着的是可选的以blank分隔的用户参数，或者重定向，最终被control operator终止

### pipelines

> pipeline是一个由一个或多个命令组成的序列，命令之间由control operator | 或者 |& 分隔
>
> 格式为：

```sh
[time [-p]] [ ! ] command [ [|⎪|&] command2 ... ]
```

> 注意事项：
>
> * command的标准输出通过pipe连接到command2的标准输入，这个动作在任何重定向之前发生
> * |& 会额外将command的标准err输出连接到command2的标准输入
> * pipeline的退出状态exit status由最后一个命令决定
> * ！ 将pipeline的exit status逻辑取反
> * time 用于记录运行时间

### lists

> 一个list是由一个或者多个pipeline组成，pipeline之间用control operator中的  ;，&，&&，|| 分隔，终止符用 ;，&，<newline>.
>
> * && 和|| 优先级最高，其次是； &
> * 在shell脚本中，可能用newline代替；来分隔pipeline
> * 一个命令用&结束的话，会在子shell中后台运行该命令
> * 用；分隔的命令依次执行，exit status由最后一个命令决定



##### AND list

```sh
command1 && command2
```

> command2只有在command1执行返回0情况下才执行

##### OR list

```sh
command1 || command2
```

> command2 只有在commond1返回非0 情况下才执行（command1执行异常）

以上两种list返回状态都是由最后一个执行的命令决定

### compound command

#### (list)

> list执行在子shell中

#### { list; }  （group command）

> * 执行在当前shell中
> * 因为{ 和 } 都是保留字符而不是元字符或者控制字符，他们不能用于分隔token，因此间隔一个空格或者其他元字符
> * exit status由list决定，也就是其中最后一个执行命令决定

#### ((expression))

> 数字运算
>
> 等价于执行  let "expression"

#### [[ expression ]]

> expression 为条件表达式，整体返回值由这个exp的计算结果表示
>
> * 在[[ ]]里面，word splitting 和pathname expansion 不会作用于word上，其他的expansion有效
> * 在这里面对比字符串，==、!=右边的字符串会被当做模式，模式中如果需要强制当做字符串匹配，把那部分引用起来
> * =与==等价
> * 匹配返回0，不匹配返回1

## ${}

{}只是用于帮助区分变量名，通常用于shell脚本中使用大于10个参数的引用

区分token，如引用数组下标时 \${arr[0]}

```sh
${10}
${arr[0]}
```



## $()	``

Command substitution—将命令返回的信息保存到变量中

两种赋值方式：

* The backtick character (`) 

  ``` shell
  test=`date`
  ```

  

* The $() format

  ```shell
  test=$(date)
  ```

> 命令替换会创建一个新的子shell来执行里面的命令，这个子shell是执行脚本时shell的子shell，因此，脚本中创建的变量在这个command substitution中没法使用
>
> 在命令提示行中执行命令会创建子shell，而shell内置命令不会创建

## redirection

### output redirection

* \> 覆盖写
* \>> 追加写

#### 重定向error流

{fd}>{file} [{fd2}>{file2}]

```sh
ls filename 2> errorlog.log
```

0,1,2分别代表的STDIN,STDOUT,STDERR文件描述符

&>，用于将1,2流一起重定向

### input redirection

从文件中读

```shell
command < file
```

inline input redirection

> << 从命令行中读，必须要指定文本标记用于表明输入结束

```shell
command << EOF
>hello
>world
>EOF
```

## pipes

> command1 | command2 

Linux系统把两个命令链接起来，也就是说同时执行的，第一个命令执行完立即将结果输入给第二个命令，并执行第二个命令

## 数学计算

> bash只支持整数算数

### expr

```shell
expr arg1 op arg2
```

op常常需要转义\

### $[]  使用方括号	\$(())

```shell
把要计算的表达式放在$[]方括号中
va1=$[5+1]
echo $[5-1]
```

> $[] 快要过时，建议使用   \$(())

## exit status

>退出状态范围为0~255，模256运算

> 访问最近一次执行的命令返回状态：
>
> > echo $?

# Shell structured command

shell脚本中的控制流由command控制，与通常的程序语言不同的是，shell利用command返回值来判断控制条件，如果command返回0（即执行成功）则表示true，其他返回值表示false

## if-then-else

```shell
if command 
then commands 
else commands 
fi
```

> then可以写在command后，前面添加分号;

```sh
if command; then
	comands
else
	commands
if
```

### if-then-elif-then

```sh
if command; then
	commands
elif command; then
	commands
#else ##跟elif组合
#	commands
if
```

> 这种结构中，任何elif后的else都是跟这个elif组合在一起的，而不是前面的if

## test command

之前的if command是根据普通命令返回状态值来决定控制流，除此之外，shell提供了一些工具utility来帮助测试除命令返回值以外的条件，test command就是干这个事情的

### if test condition

调用格式

```sh
if test condition; then
	commands
fi
```

> condition 是一系列test命令测试的参数和值（参考 man test）
>
> 如果condition为空，则test命令返回值为非零值，即false

### if []

```sh
if [ condition ]; then #空格是必须的
	commands 
fi
```

> [ ]与condition之间要有空格

### test condition 测试的内容

参考

```sh
>man test
```

> 在表达式中，数字比较通常用-eq 等指令，而不直接用> <等编程语言常用的比较运算符，因为它们需要被转义，也就是说如果不使用 \\>的话，会被shell解释成重定向
>
> 使用>，<比较字符串，是按照ascii值大小比较

### test command中的注意事项

[ condition ]，condition中的逻辑判断或者其他运算符都是命令，注意要跟两边的操作数中间间隔空格

```sh
if [ string1 = string2 ]
```



## compound test

```shell
[ condition1 ] && [ condition2 ]
[ condition1 ] || [ condition2 ]
```

> 注意空格

## advanced feature

除了以上的常用用法，shell还有两种高级特性可以用在if-then语句中

### 双括号(())

提供更复杂的数字运算

```sh
#用于普通命令，在((中可以赋值
(( expression ))
#用于 if 命令
if (( expression ))
#根据expression的结果判断，0为假，非0为真
```

> 除了标准的[ ]中能使用的数学操作符，双括号可以提供数字赋值或者比较的表达式
>
> 不同的格式：
>
> * 赋值可以=两边有空格
> * 使用变量时不用前面加$

| Symbol | Description    |
| ------ | -------------- |
| val++  |                |
| val—   |                |
| ++val  |                |
| —val   |                |
| !      |                |
| ∼      |                |
| **     | exponentiation |
| <<     |                |
| >>     |                |
| &      |                |
| \|     |                |
| &&     |                |
| \|\|   |                |

### 双方括号[[]]

提供高级字符串比较（模式匹配），注意不是所有的shell都支持[[]]

## loop

### for command

```shl
for var in list; do
	commands 
done
```

list有几种情况：

#### 自身定义的list

```sh
for v in a b c d; do 
	echo $v
done
```

> 每次迭代向$v赋list中的值，\$v在整个脚本中都有效，并且出了for后\$v保留最后一次迭代的值

#### 保存在变量中

```sh
var1="hello world"
for v in $var1; do
	echo $v
done
```

#### 利用command返回值

> utilize command substitution

```sh
for v in $(xxx); do
	commands
done
```

#### IFS环境变量

> 默认IFS（internal field separator，word分隔符）为（space tab newline。。。）
>
> 有时候需要将返回值中一行当做一个整体数据，需要改变IFS默认值

```sh
IFS.OLD=$IFS
IFS=$'\n'
xxxx#具体操作
IFS=$IFS.OLD
```

> 另外可以设置IFS为其他字符用于处理其他格式的字符串，例如处理/etc/passwd文件时，因为用的: 分隔，可以设置IFS为冒号来方便后续处理
>
> 可以设置任意多个IFS字符，方法就是直接在后面接着写

```sh
IFS=$'\n':;"
```

#### 利用file glob

file glob用于产生一个list，内容为满足通配表达式的文件路径

```sh
for v in /home/*; do
	commands
done
```

### c style for

```sh
for (( expr1 ; expr2 ; expr3 )) ; do 
	list 
done
```

### while command

```sh
while test command; do
	other commands 
done
```

> 根据test command返回结果判断是否执行，exit status为0则执行，反之则反
>
> test command 可以用 [ ]

### util command

类似while，不过测试条件判断相反

### braek command

跳出外层循环：

```sh
xxxx
break n
xxxx
```

> n默认为1，表示本层循环，n设置为其他就可以跳出那一层的循环

### continue command

类似break，可以continue n

### 处理循环过程中输出的信息

#### 重定向保存

> 在循环中可能涉及到输出信息，可以将这些信息保存下来而不是输出在父shell中

```sh
xxx
done > data.txt
```

#### pipe到其他命令

```sh
xxx
done | less
```

## case

```sh
case world in
pattern | pattern...)
	list;;
xxx)
	list;;
*)
	list;;
esac
```



# Handing user input

## command line parameters

### positional parameters

$0表示脚本名，$1表示第一个参数...

#### ${}

参数多于9个后，引用参数必须使用如下方式

```sh
${10}
```

>  {}里面不能用常规的替换，如$xxx，也就是说里面只有“编译时”合规
>
> 调用脚本最后一个参数用${!#}



#### basename 命令

该命令返回命令名，忽略所有路径，这对于写基于脚本名提供不同功能的脚本尤为重要

```sh
name=$(basename $0)
```

### special parameter $#,\$@,\$*

#### $#

```sh
echo $#
```

> \$#只能表示脚本参数的个数，不能用于${$#}

```sh
echo ${$#}  #不是传入的最后一个参数
echo ${!#}  #表达最后一个参数的正确方式，没有参数传入时表示脚本名

###
s.sh 
# 输出为 s.sh
s.sh 1 1 1 2 2 2
# 输出为 2
```

#### \$@和$*

这两个para把所有参数当成一个string，用于for迭代

> 不同点：
>
> $@把所有参数当成一个string中的多个分隔的word
>
> $*把所有参数当成一个word，也就是说，当成只有一个参数

#### $$

shell自动设置这个环境变量为当前的PID

### 迭代脚本参数

除了上面的$@,$*外，还有一种方法用于迭代参数，shift

shift命令每次丢弃$1，然后把后面的参数往前移动，以此达到栈的作用（出栈）

因此可以每次只是用$1来迭代所有参数

```sh
shift [n] ##shift几次
```



## command line options

single-letter values that modify the behavior of the command read 

和普通处理参数一样，不过通常结合case和shift来处理option

例子：

```sh
while [ -n "$1" ]
do
 case "$1" in
 -a) echo "Found the -a option" ;;
 -b) echo "Found the -b option";;
 -c) echo "Found the -c option" ;;
 --) shift ##预处理处理非option的parameter（以--为分隔）
 	break ;;
 *) echo "$1 is not an option";;
 esac
 shift
done
##处理param
count=1
for param in $@
do
 echo "Parameter #$count: $param"
 count=$[ $count + 1 ]
done
```

#### getopt command

getopt <u>optstring</u> <u>parameters</u>

optstring，列出所有可能的option字符，对于有parameter的option，后面跟接一个：，如下所示

eg:

```sh
getopt ab:cd -a -b test1 -cde test2 test3
###  -a -b test1 -c -d -- test2 test3
```

#### using getopt

set -- \$(getopt -q ab:cd "$@")

> 使用set -- xxx，可以将脚本positional parameters设置为xxx

## input directly from the keyboard

使用read命令

```sh
read name ### 读取stdio保存到name变量中
read -p "Enter your name: " first last ###带提示的读取，分别到first，last变量中，多余的数据读入last中，没有变量用于保存读的数据，默认保存在REPLY中，$REPLY

```

# Advance redirection

一个进程最多有9个文件描述符0-8，文件描述符用于指向输入输出的位置，默认输入是STDIN，输出是STDOUT

重定向语法：{fd}>{obj}

fd:文件描述符，0，1, 2分别默认表示STDIN,STDOUT,STDERR

obj: 重定向目标位置（如果这个位置是文件描述符，前面必须加上&）

## 在脚本内重定向输入

单条：

```sh
echo "xxxx" >&{fd} ##从定向xxxx到fd文件描述符（即输出到第几个通道）
```

后续整个脚本（设置文件描述符）：

```sh
exec {fd}>{obj}
```

<<<<<<< Updated upstream
## /dev/null 文件

这个文件是空文件，用于丢弃输出或者清空文件

```sh
exec 2>/dev/null  ##redirect STDERR to /dev/null，discard all message
cat /dev/null>text.txt ## clear the file making that empty but still exist
```



# Script control

![image-20220718202842490](C:\Users\pc\AppData\Roaming\Typora\typora-user-images\image-20220718202842490.png)

## generate signals

interrupt process  CTRL+C

> SIGINT信号
>
> shell被打断，操作系统停止给shell调用时间，当shell收到这个信号时，会给每一个在这个shell中启动的进程发送SIGINT信号

stop process CTRL+Z

> SIGTSTP 信号
>
> 停止进程和终止进程不一样的，停止进程会保留进程数据在内存中，以便继续执行

## trap signals

> **trap** *commands signals* 
>
> 常用在脚本中，当shell收到signal时，用于执行command，而不是默认的shell处理
>
> trap可以改变，只影响之后的脚本
>
> 删除trap：
>
> trap -- signals

## running scripts in background mode

```sh
command &
```

后台运行的脚本在shell退出时也会退出

阻止这个脚本退出的命令：

```sh
nohup command &
```

## restart stopped job

fg、bg：不加job号会对default job生效，即使用jobs命令显示的带有+号的job

```sh
fg [jobnum]
bg [jobnum]
```

## task priority

从shell中启动的进程优先级都是一样的，linux优先级数范围是 -20到19，数值越小优先级越高。shell中启动的进程默认优先级为0

启动时改变进程优先级：nice命令

> nice -n [prio] command

启动后改变进程优先级：renice命令

> renice -n [prio] -p [pid]

## schedule job

使用at命令和cron 表

```sh
at [-f filename] time
```

> at默认从STDIN读取command送入队列，-f 从文件中读
>
> 若time 已经过了，则会从明天同一时间开始执行
>
> 使用at时，command送入队列，队列有26个，可以通过a-z（大写）引用这些队列，字母越靠后，优先级越低
>
> at任务会使用邮件服务来输入执行信息，一般建议重定向输入输出

```sh
atq
```

> 显示当前所有任务

```sh
atrm [jobnum]
```

> 删除任务

## cron 

cron表格式为：

> *min hour dayofmonth month dayofweek command*

每个entry可以是具体的值，也可以是范围值或者通配符得到的值	

> 15 16 * * 1 *command*  表示每周一下午4点15分执行
>
> dayofweek可以表示成 (mon, tue, wed, thu, fri, sat, sun) 或者数字(0表示星期天)
>
> 命令command必须是绝对路径

### cron directory

linux系统设置了几个文件夹，/etc/cron.*ly，里面放的脚本会根据文件夹名字自动执行

### anacron 程序

如果机器宕机而错过了一些任务，anacron会立即执行错过的任务，在机器重新开启时。

anacron只会执行cron 目录，也就是说/etc/cron.* 目录下的脚本

# Function

## basic

name () compound-command

function name [()] compound-command

常用语法有二：

```sh
name () {
	commands
}
function name [()] {
	commands
}
```

> * 函数要在使用之前定义，否则会报错
> * 函数名必须唯一，否则后面定义的相同的函数名会覆盖以前定义的（不会报错）
> * 如果function保留字有，但是没有（），那么commands必须用{}包括起来

## returning value

* 默认返回值：函数中最后一个执行的命令的返回值，用$?查看返回值，不推荐，中间执行的命令返回值不可捕捉

* 使用return command：

  > 手动设置函数exit status，仍然保存在$?中，存在超过范围的问题
  >
  > 应尽快使用返回值，因为可以被覆盖
  >
  > 值的范围只能在0-255之间

* 捕捉返回值

  ```sh
  f () compound-command
  captured=$(f)
  ```

  > return的值不会被捕捉，建议用echo，所有echo的内容都会被捕捉

## using variable in function

函数就是一个迷你脚本，可以使用一般脚本传递参数一样的规则

> * $0是通用的，也就是说函数内使用的和所属的脚本一样
>
> * 其他的positional parameter不能通用，要想使用外层脚本的参数，需要调用函数时传入进来
>
>   func $1 \$2
>
> * 在函数中使用的shift只作用于函数

函数内可以使用两类变量：global、local

### global variable

在脚本内定义的变量默认都是global的，即函数内定义的全局变量能够在函数外使用，函数外定义的全局变量也可以在函数内使用，不建议使用，造成很多混淆

### local variable

使用local关键词放在变量名前

```sh
local varname
```

> local 只用于函数内！

### pasing arrays to function

数组：

```sh
array=(x x x x x)
```

> （）中用空格分隔开
>
> 一个字符串也是一个数组，所有字符都保存在0号位置
>
> ​	var="hello world"; echo ${var[0]}

传递数组给函数：

```sh
arr=(1 2 3 4 5)
func $(echo ${a[*]})
####
arr2=$@
```

> 先解引用数组（拆散），然后传递给函数
>
> 在函数内通过$@取出数组（重装）

### returning array to script

类似，现在函数内拆散，然后在外面重装

```sh
f () {
	arr=(1 2 3 4 5)
	echo ${arr[*]}
}
result=($(f))
```

## creating library script

因为会创建一个新的shell执行shell脚本，当那个脚本执行完成后，所产生的影响不会在执行脚本的shell中

一个办法就是使用source命令，这个命令用于在当前shell中执行脚本，也就达到了library的作用

```sh
. ./lib.sh
xxx
xxx
```

这样lib.sh执行后产生的影响后续脚本也能使用

### advanced library script

在.bashrc中source 你需要的库脚本，之后所有的shell都能使用了

# Sed

## basic

sed stream editor，流式处理文本文件，处理流程：从STDIN读取一行，匹配预先写好的sed 脚本，同时处理，最后输出到STDOUT。处理完后读取下一行进行同样的流程直到所有行都处理完成。

> 基本命令格式：**sed** *options  script  file* 
>
> script 只能指定一个command，如果有多个命令要执行，需要加上 -e / -f filename（分别从命令行中读，和从文件中读取执行命令）
>
> sed不会对源文件进行修改，只会把处理后的数据输出到STDOUT
### 执行多条命令

```sh
sed -e 's/xxx/xxx/;s/xxx/xxx/;' data.txt
```

> 替代字符串后必须跟/



# Gawk

## basic

gawk从STDIO读取数据，然后对整个数据流进行编程式处理

>  基本命令格式： **gawk** *options program file*
>
> program必须由 {} 包围，整个program应该由‘ ’ 包围，以示单字符串

## data field

gawk处理每行数据时，会根据field separation character划分字段，并将每个字段赋值给相应的变量，对应规则如下：

> $0 代表一整行数据
>
> $1 代表一行数据中划分出来的第一个字段
>
> $2 xxx第二个字段
>
> ...
>
> $n 第n个字段

gawk默认fs为空白字符，例如tab space

# Regular Expression

## basic regular expression (BRE)

> plain text
>
> special characters ：	.*[]^${}\+?|()  这些字符要匹配的话必须转义 "\\."
>
> anchor characters ：	^ $
>
> dot character ： 占一个字符，可以匹配除newline符以外任何字符
>
> asterisk ：	*前的字符可以重复0-n次

### character classes

[] 方括号中放任意想匹配的字符，只要其中一个匹配上就算成功匹配，占一个字符

> * [abc] 手动输入
> * [a-z1-9]  范围
> * [^abc1-9]  范围否定

special classes:

> [[:alpha:]]    Matches any alphabetical character, either upper or lower case
>
> [[:alnum:]]	Matches any alphanumeric character 0–9, A–Z, or a–z
>
> [[:blank:]]		Matches a space or Tab character
>
> [[:digit:]]		Matches a numerical digit from 0 through 9
>
> [[:lower:]]		Matches any lowercase alphabetical character a–z
>
> [[:print:]]		Matches any printable character
>
> [[:punct:]]		Matches a punctuation character
>
> [[:space:]]		Matches any whitespace character: space, Tab, NL, FF, VT, CR
>
> [[:upper:]]		Matches any uppercase alphabetical character A–Z

