# Regular Expression

linux 系统中有两类正则表达式引擎：

* The POSIX Basic Regular Expression (BRE) engine （sed中使用）
* The POSIX Extended Regular Expression (ERE) engine （gawk中使用）

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

## extended regular expression

### question mark ?

?  	匹配只有0次或1次

### plus mark +

\+ 	匹配至少一次

### curly braces {}

interval 指定前面的字符出现的次数限制：

> {m}	只出现m次
>
> {m,n}	至少出现m次，不超过n次

### pipe symbol |

expr1|expr2

> 表示或OR语义，只要其中任何一个表达式满足，就表示匹配成功
>
> 表达式与|之间不要有空格，否则会认为表达式包含了空格

### grouping expression ()

(expr)

>  ?+{}*等特殊字符只对前一个字符起作用，使用分组，可以让分组当成一个字符，从而是这些特殊字符对整个分组生效
>
> (a|b)==[ab]
>
> (expr1|expr2)