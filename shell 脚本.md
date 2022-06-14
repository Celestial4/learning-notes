# shell basic

## ${variable}

{}只是用于帮助区分变量名

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

### $[]  使用方括号

```shell
把要计算的表达式放在$[]方括号中
va1=$[5+1]
echo $[5-1]
```



## exit status

>退出状态范围为0~255，模256运算

> 访问最近一次执行的命令返回状态：
>
> > echo $?

# shell structured command

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
if [ condition ] then #空格是必须的
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
