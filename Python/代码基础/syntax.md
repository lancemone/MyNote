# 语法基础

## 字面值
字面值是以变量或常量给出的原始数据。字面值(文本)是内置类型常量值的表示法。  

- 字面值类型  
    - [数字字面值](#数字字面值 )
    - [字符串字面值](#字符串与字节串字面值)
    - [布尔字面值]()
    - [特殊字面值]()
    - [集字面值]()

### 数字字面值  
数值字面值有三种类型：整数、浮点数、虚数。没有复数字面值（复数由实数加虚数构成）。  
> 注意，数值字面值不含正负号；实际上，-1 等负数是由一元运算符 '-' 和字面值 1 合成的。

1. 整数字面值
整数字面值词法定义如下：
```
integer      ::=  decinteger | bininteger | octinteger | hexinteger
decinteger   ::=  nonzerodigit (["_"] digit)* | "0"+ (["_"] "0")*
bininteger   ::=  "0" ("b" | "B") (["_"] bindigit)+
octinteger   ::=  "0" ("o" | "O") (["_"] octdigit)+
hexinteger   ::=  "0" ("x" | "X") (["_"] hexdigit)+
nonzerodigit ::=  "1"..."9"
digit        ::=  "0"..."9"
bindigit     ::=  "0" | "1"
octdigit     ::=  "0"..."7"
hexdigit     ::=  digit | "a"..."f" | "A"..."F"
```

- 整数字面量可以是 10进制数，2进制数，8进制数，16进制数,整数字面值的长度没有限制.  
- 确定数值时，会忽略字面值中的下划线。下划线只是为了分组数字，让数字更易读。下划线可在数字之间，也可在 0x 等基数说明符后。
- Python使用"L"来显示长整型。在整数之后加"L"或者"l(小写字母L)"
> 注意，除了 0 以外，十进制数字的开头不允许有零。

2. 浮点数字面值
浮点数字面值词法定义如下：
```
floatnumber   ::=  pointfloat | exponentfloat
pointfloat    ::=  [digitpart] fraction | digitpart "."
exponentfloat ::=  (digitpart | pointfloat) exponent
digitpart     ::=  digit (["_"] digit)*
fraction      ::=  "." digitpart
exponent      ::=  ("e" | "E") ["+" | "-"] digitpart
```
> 解析时，整数和指数部分总以 10 为基数。例如，077e010 是合法的，表示的数值与 77e10 相同. 浮点数字面值的支持范围取决于具体实现。整数字面值支持用下划线分组数字。

浮点数字面值示例如下：  
`3.14    10.    .001    1e100    3.14e-10    0e0    3.14_15_93`

3. 虚数字面值
虚数字面值词法定义如下：
`imagnumber ::=  (floatnumber | digitpart) ("j" | "J")`


### 字符串与字节串字面值
1. 字符串与字节串字面值

字符串字面值的词法定义如下：
```
stringliteral   ::=  [stringprefix](shortstring | longstring)
stringprefix    ::=  "r" | "u" | "R" | "U" | "f" | "F"
                     | "fr" | "Fr" | "fR" | "FR" | "rf" | "rF" | "Rf" | "RF"
shortstring     ::=  "'" shortstringitem* "'" | '"' shortstringitem* '"'
longstring      ::=  "'''" longstringitem* "'''" | '"""' longstringitem* '"""'
shortstringitem ::=  shortstringchar | stringescapeseq
longstringitem  ::=  longstringchar | stringescapeseq
shortstringchar ::=  <any source character except "\" or newline or the quote>
longstringchar  ::=  <any source character except "\">
stringescapeseq ::=  "\" <any source character>
```
- 前缀 u|U表示支持 unicode 字面值
- 前缀 f|F 的字符串称为 [格式字符串](#格式字符串字面值)。f' 可与 'r' 连用，但不能与 'b' 或 'u' 连用

字节串字面值的词法定义如下：
```
bytesliteral   ::=  bytesprefix(shortbytes | longbytes)
bytesprefix    ::=  "b" | "B" | "br" | "Br" | "bR" | "BR" | "rb" | "rB" | "Rb" | "RB"
shortbytes     ::=  "'" shortbytesitem* "'" | '"' shortbytesitem* '"'
longbytes      ::=  "'''" longbytesitem* "'''" | '"""' longbytesitem* '"""'
shortbytesitem ::=  shortbyteschar | bytesescapeseq
longbytesitem  ::=  longbyteschar | bytesescapeseq
shortbyteschar ::=  <any ASCII character except "\" or newline or the quote>
longbyteschar  ::=  <any ASCII character except "\">
bytesescapeseq ::=  "\" <any ASCII character>
```

+ 两种字面值都可以用单引号（'）或双引号（"） 标注。也可以用三个单引号或双引号标注（俗称 三引号字符串）。反斜杠（\）用于转义特殊字符，例如，换行符、反斜杠本身、引号等。
+ 三引号字面值可以包含未转义的换行和引号（原样保留），除了连在一起的，用于终止字面值的，未经转义的三个引号。
+ 字节串字面值要加前缀 'b' 或 'B'；生成的是类型 bytes 的实例，不是类型 str 的实例；bytes是一系列字节组成的序列，是不可改变的。bytes每个元素的值在[0, 255]范围内（无符号单字节）
+ 字节串只能包含 ASCII 字符；字节串数值大于等于 128 时，必须用转义表示。
+ 字符串和字节串都可以加前缀 'r' 或 'R'，称为 原始字符串，原始字符串把反斜杠当作原义字符，不执行转义操作。但 r"\" 却是一个非法字符串（即原始的字符串也不能以奇数个反斜杠结尾）。

2. 转义  
字符串和字节串字面值中可用的转义序列:

|  转义序列   | 含意  |  备注  |
|  :----  | :----  |  :----  |
| \newline | 忽略反斜杠与换行符 | |
| \\\       | 反斜杠（\）    | |
| \\'   | 单引号（'）| |
| \\"   | 双引号（"） | |
| \a    | ASCII 响铃（BEL） | |
| \b    | ASCII 退格符（BS） | |
| \f    | ASCII 换页符（FF） | |
| \n    | ASCII 换行符（LF） | |
| \r    | ASCII 回车符（CR） | |
| \t    | ASCII 水平制表符（TAB）| |
| \v    | ASCII 垂直制表符（VT） | |
| \ooo  | 八进制数 ooo 字符 | (1,3) |
| \xhh  | 十六进制数 hh 字符  | (2,3) |

字符串字面值专用的转义序列：
|  转义序列   | 含意  |  备注  |
|  :----  | :----  |  :----  |
| \N{name}  | Unicode 数据库中名为 name 的字符 | (4) |
| \uxxxx    | 16 位十六进制数 xxxx 码位的字符 | |
| \Uxxxxxxxx | 32 位 16 进制数 xxxxxxxx 码位的字符 | |

#### 格式字符串字面值
格式字符串字面值 或称 f-string 是标注了 'f' 或 'F' 前缀的字符串字面值。这种字符串可包含替换字段，即以 {} 标注的表达式。
语法如下: 
```
f_string          ::=  (literal_char | "{{" | "}}" | replacement_field)*
replacement_field ::=  "{" f_expression ["="] ["!" conversion] [":" format_spec] "}"
f_expression      ::=  (conditional_expression | "*" or_expr)
                         ("," conditional_expression | "," "*" or_expr)* [","]
                       | yield_expression
conversion        ::=  "s" | "r" | "a"
format_spec       ::=  (literal_char | NULL | replacement_field)*
literal_char      ::=  <any code point except "{", "}" or NULL>
```
双花括号 '{{' 或 '}}' 被替换为单花括号，花括号外的字符串仍按字面值处理。单左花括号 '{' 标记以 Python 表达式开头的替换字段。在表达式后加等于号 '='，可在求值后，同时显示表达式文本及其结果（用于调试）。 随后是用叹号 '!' 标记的转换字段。还可以在冒号 ':' 后附加格式说明符。替换字段以右花括号 '}' 为结尾。  

- 3.8 新版功能: 等号 '=':  
表达式里含等号 '=' 时，输出内容包括表达式文本、'=' 、求值结果。输出内容可以保留表达式中左花括号 '{' 后，及 '=' 后的空格。没有指定格式时，'=' 默认调用表达式的 repr()。指定了格式时，默认调用表达式的 str()，除非声明了转换字段 '!r'。   
指定了转换符时，表达式求值的结果会先转换，再格式化。转换符 '!s' 调用 str() 转换求值结果，'!r' 调用 repr()，'!a' 调用 ascii()。 
格式字符串字面值示例如下：  
```
>>> name = "Fred"
>>> f"He said his name is {name!r}."
"He said his name is 'Fred'."
>>> f"He said his name is {repr(name)}."  # repr() is equivalent to !r
"He said his name is 'Fred'."
>>> width = 10
>>> precision = 4
>>> value = decimal.Decimal("12.34567")
>>> f"result: {value:{width}.{precision}}"  # nested fields
'result:      12.35'
>>> today = datetime(year=2017, month=1, day=27)
>>> f"{today:%B %d, %Y}"  # using date format specifier
'January 27, 2017'
>>> f"{today=:%B %d, %Y}" # using date format specifier and debugging
'today=January 27, 2017'
>>> number = 1024
>>> f"{number:#0x}"  # using integer format specifier
'0x400'
>>> foo = "bar"
>>> f"{ foo = }" # preserves whitespace
" foo = 'bar'"
>>> line = "The mill's closed"
>>> f"{line = }"
'line = "The mill\'s closed"'
>>> f"{line = :20}"
"line = The mill's closed   "
>>> f"{line = !r:20}"
'line = "The mill\'s closed" '
```




