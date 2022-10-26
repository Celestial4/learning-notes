# searching

> \* 搜索光标所在word（正向）
>
> \# 搜索光标所在word（反向）
>
> g* 搜索光标所在word（正向、广义）
>
> g# 搜索光标所在word（反向、广义）
>
> /后接 ctrl+r ctrl+w 可以读取光标所在位置word并粘贴到/后面

# navigation

## norm模式下光标前进后退

>  ctrl+o 跳转到上次光标所在位置
>
>  ctrl+i 跳转到下次光标所在位置

## 滚动屏幕

> ctrl+d 向下半屏
>
> ctrl+u 向上半屏
>
> ctrl+f 向下整屏
>
> ctrl+b 向上整屏

## 文件内跳转

> % 跳转到匹配的(){}[]
>
> 50% 跳转到整个文档50%
>
> :NUM  跳转到NUM行(NUM+gg)

## 窗口内跳转

> H L M 顶部，底部，中间

## insert模式内跳转

> ctrl+o  退出insert模式一次，执行一个operate后返回insert模式
>
> shift+right arrow 右移一个word

# undo&redo

>  norm模式下ctrl+r  redo
>
> u  :u   undo
>
> U undo all changes

# substitution & global command

## [range] s/pattern/string/[flag]

Substitution flags can be:

• c - to **c**onfirm each substitution

• g - to replace all occurrences in the line

• i - **i**gnore case for the pattern

• I - don’t ignore case for the pattern

> 默认只会执行当前光标所在行，需要添加range参数来替换多行

range:

% 整个文件所有行

$ 最后一行

. 当前行

> range 的格式  [a,b] ，a、b表示文件中的行

## [range]\[i]g/pattern/cmd

> global 命令默认执行整个文件，需要添加range参数来缩小范围
>
> 对匹配到pattern的行执行cmd
>
> i 表示匹配取反

## to

[a,b]t[c]

> a到b行复制到c行之后

## move

[a,b]m[c]

> a到b行移动到c行之后