# Shell basic

## 加载过程中执行的文件

* login shell:  /etc/profile  -->  ~/.bash_profile ---> ~/.bash_login ---> ~/.profile
* interactive shell(subshell):  /etc/bash.bashrc ---> ~/.bashrc

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

```sh
${10}
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

### special parameter $#,$@,$*

#### $#

```sh
echo $#
```

> $#只能表示脚本参数的个数，不能用于${$#}

```sh
echo ${$#}  #不是传入的最后一个参数
echo ${!#}  #表达最后一个参数的正确方式，没有参数传入时表示脚本名

###
s.sh 
# 输出为 s.sh
s.sh 1 1 1 2 2 2
# 输出为 2
```

#### $@和$*

这两个para把所有参数当成一个string，用于for迭代

> 不同点：
>
> $@把所有参数当成一个string中的多个分隔的word
>
> $*把所有参数当成一个word，也就是说，当成只有一个参数

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

set -- $(getopt -q ab:cd "$@")

> 使用set -- xxx，可以将脚本positional parameters设置为xxx

## input directly from the keyboard

使用read命令

```sh
read name ### 读取stdio保存到name变量中
read -p "Enter your name: " first last ###带提示的读取，分别到first，last变量中，多余的数据读入last中

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

