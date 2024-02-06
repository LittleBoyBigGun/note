#bat

bat 全称 batch, 也即批处理， 起源 ms-dos 时代， windows 后续版本都带有 cmd.exe, 可以执行 bat 命令和程序。其在 windows平台的兼容性很强， 常常作为一种胶水语言， 整合其他命令来完成特定任务。

bat 缺点十分明显

- 流程控制欠佳， 仅仅支持 if else for 等
- "函数"没有返回值， 可以使用其他方式模拟， 可实在难用
- 变量定义和使用也不及现代语言
- 字符串拼接， 截取等操作不够直观， 对新手不够友好
- 调用"函数"无法获取返回值， 不过可以使用 for 来模拟， 实属麻烦
- 格式要求严格， 相反没有错误提示定位， 也没有好用的调试手段

# 编码环境

推荐使用 vscode 来编写代码， 智能提示和自动补全可以很好帮助我们编写 bat 脚本。推荐使用 `bat snippets` 这款插件。

# 注释

bat 支持多种注释方式

- rem 
- ::

```cmd
rem this ls a comment
:: this is anthor comment
```

# echo

echo 可用于向屏幕打印信息， 可重定向到文件甚至 `nul`。默认情况下，   命令的执行， 都会在终端回显命令本身， 可以在命令前冠以 `@` 来取消该条命令的回显， 也可以使用 `@echo off` 来关闭所有命令的回显， 必要的时候可以通过 `@echo on`来关闭。

# 变量

`set` 可用于设置和移除变量， 这里的变量本质是系统环境变量。bat 变量是字符串， 也支持有限的运算。和一般的编程语言不同， bat 变量的扩展(extension)

其语法示例如下

## 设置变量

- `set name=value` 设置一个普通变量
- `set /a name="10+2"` 会把 10+2 评估的结果赋给 name
- `set /p name=提示文字` 从终端读取变量值， 显示 `提示文字`的提示

## 展示变量

set 可以列出当前系统环境中存在的变量， 仅当 `set x` 找不到 x 时， 置 `errorlevel` 为 1， 其他情况均不改变 errorlevel。

```cmd
::show all variable in current environment
set

::show all variable starting with x
set x

::show variable x in current environment
set x
```

## 数值计算

使用 `/a` 参数后， bat 支持简单的数值计算

```cmd
set /a b="10+2"
echo %b% :: 12
```

支持的计算有

| operator                         | meaning              |
| -------------------------------- | -------------------- |
| ()                               | grouping             |
| * / %                            | arithmetic operators |
| + -                              | arithmetic operators |
| << >>                            | logical shift        |
| &                                | bitwise and          |
| ^                                | bitwise exclusive or |
| \|                               | bitwise or           |
| = \*= /= %= -= &= ^= \|= <<= =>> | assignment           |
| ,                                | expression separator |
|                                  |                      |
          
## 变量展开(扩展) 

bat 变量的引用， 是一种值替换， 叫做`变量扩展`, 英文 `expansion`, 类似 c 语言的宏展开。有三种变量扩展符：

- `%var%`  非延迟扩展变量
- `!var%`   延迟扩展变量
- `%%var`   for 命令的取出的值

变量替换和截取叫做 `不知道如何翻译` (`extension`)。`函数`参数 %n 也支持 extension。for 命令中`%%i`同样支持 extension。

### 替换

格式为 `%var:str1=str2`, str1 是 var 某个子串， 替换为 str2。

### 截取

格式：`%var:~start, count`, 表示从 start（从 0 开始计数) 开始， 取 count 个字符。count 为负数表示倒数个数， start 可以省略

```cmd
%var:~0,1% :: 从第一个字符开始，取一个字符
%var:~2,-5% :: 从第三个字符开始，到倒数第五个字符
%var:-3% :: 最后三个字符
```

### 其他形式扩展

**for & argvs %n**

| extension | meaning                                                                                                                                   |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| %~I       | 删除任何引号(")，扩展 %I                                                                                                                  |
| %~fI      | 将 %I 扩展到一个完全合格的路径名                                                                                                          |
| %~dI      | 仅将 %I 扩展到一个驱动器号                                                                                                                |
| %~pI      | 仅将 %I 扩展到一个路径                                                                                                                    |
| %~nI      | 仅将 %I 扩展到一个文件名                                                                                                                  |
| %~xI      | 仅将 %I 扩展到一个文件扩展名                                                                                                              |
| %~sI      | 扩展的路径只含有短名                                                                                                                      |
| %~aI      | 将 %I 扩展到文件的文件属性                                                                                                                |
| %~tI      | 将 %I 扩展到文件的日期/时间                                                                                                               |
| %~zI      | 将 %I 扩展到文件的大小                                                                                                                    |
| %~$PATH:I | 查找列在路径环境变量的目录，并将 %I 扩展到找到的第一个完全合格的名称。如果环境变量名未被定义，或者没有找到文件，此组合键会扩展到 空字符串 |

可以组合修饰符来得到多重结果:

| extension   | meaning                                                                |
| ----------- | ---------------------------------------------------------------------- |
| %~dpI       | 仅将 %I 扩展到一个驱动器号和路径                                       |
| %~nxI       | 仅将 %I 扩展到一个文件名和扩展名                                       |
| %~fsI       | 仅将 %I 扩展到一个带有短名的完整路径名                                 |
| %~dp$PATH:I | 搜索列在路径环境变量的目录，并将 %I 扩展到找到的第一个驱动器号和路径。 |
| %~ftzaI     | 将 %I 扩展到类似输出线路的 DIR                                         | 

在以上例子中，%I 和 PATH 可用其他有效数值代替。%~ 语法
用一个有效的 FOR 变量名终止。选取类似 %I 的大写变量名
比较易读，而且避免与不分大小写的组合键混淆。

### 关闭extension

bat 支持关闭extension， `setlocal disableextensions xxxxx endlocal`。关闭后， extension 不再生效， 试图打印 extension 后的变量， 则提示 `echo 已经关闭`


### 延迟扩展

命令执行期间， 变量的展开不会发生变化，   在此期间对变量的修改， 只会在命令执行完毕后才生效。命令执行期间， 如果需要变量修改立即生效， 则必须使用延迟扩展。例如：

```cmd
:: disable delayed expansion
set b=yuxia
for %%i in (1 2 3 4) do (
		set b=%b%%%i 
		echo %b% ::echo yuxia four times
)
echo %b% ::echo yuxia4

:: enable delayed expansion
setlocal enabledelayedexpansion
for %%i in (1 2 3 4) do (
		set b=%b%%%i 
		echo !b! ::echo yuxia1 yuxia2 yuxia3 yuxia4
)
echo %b% ::echo yuxia4
endlocal
```

如上所示， for 未执行完毕， 开启变量延迟后， 对变量 b 的修改会立即生效， 否则在 for 执行完毕后才生效。使用 `setlocal enabledelayedexpansion` 来开启延迟扩展， 如果没有 `endlocal` 则到文件末尾一直生效。

```cmd
@echo off

set b=1x
setlocal enableextensions 
set a=%b:x=y%
echo %a%
echo %b:~0,1%
endlocal
```

## 系统内置变量

### argvs

bat 提供 `%0 --- %9` 共 10 个内置变量， 类似传统编程语言的函数入参和 `argv`。如果入参个数超过 10 个， 则使用 `shift` 移动区域外的值进入到 %n 变量， 其特性类似移位寄存器， shift 就是时钟脉冲。

- %0 指代被执行的 bat 文件本身， 可以 extension 出路径盘符,
- %1---9 传入的其他变量

例如 

```cmd
:: test.bat
echo %~dp0
echo %1
echo %~1

:: commandline
test.bat 111 :: echo path/to/test.bat  111
test.bat "111 222" :: echo path/to/test.bat  111 222
```


### 其他内置变量

| variable                | meaning                                                                                                                       |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| %=C:%                   | expands to the current directory string on the C: drive                                                                       |
| %=_D_:%                 | expands to the current directory string on the _D_: drive _if_ drive _D_: has been accessed in the current CMD session        |
| %=ExitCode%             | expands to the _hexadecimal_ value of the last return code set by `EXIT /B`                                                   |
| %=ExitCodeAscii%        | expands to the _ASCII_ value of the last return code set by `EXIT /B` _if greater than 32 (decimal)_                          |
| %__APPDIR__%            | expands to the requesting executable's (CMD.EXE for batch files) parent folder, _always_ terminated with a trailing backslash |
| %\_\_CD\_\_%            | expands to the current directory string, _always_ terminated with a trailing backslash                                        |
| %CD%                    | expands to the current directory string (no trailing backslash, unless the current directory is a root directory)             |
| %CMDCMDLINE%            | expands to the original command line that invoked the Command Processor                                                       |
| %CMDEXTVERSION%         | expands to the current Command Processor Extensions version number                                                            |
| %DATE%                  | expands to current date using same format as DATE command                                                                     |
| %ERRORLEVEL%            | expands to the current ERRORLEVEL value                                                                                       |
| %HIGHESTNUMANODENUMBER% | expands to the highest [NUMA node](https://en.wikipedia.org/wiki/Non-uniform_memory_access) number on this machine            |
| %RANDOM%                | expands to a random decimal number between 0 and 32767                                                                        |
| %TIME%                  | expands to current time using same format as TIME command                                                                     |


## 变量的拼接

前文提到变量本质是字符串， 其拼接十分便捷， 变量的引用直接就可以和其他变量引用或字符拼接。

# 函数

bat 原生不支持函数， 不过可以通过 `call goto label` 一定程度上模拟出函数。不过无法模拟出函数返回值。给出一个例子

```cmd
::test.bat
call:func aaa
goto eof

:func
	echo %1
	
	goto eof

:eof

```

## call

call 可以调用程序片段或者其他文件， 执行完毕后返回到调用处。如果调用的是文件， 则可以通过 `exit /b errorcode` 来返回错误码， 调用者通过 `%errorlevel%` 来获取执行状态。call 可以带参数, 数量不做限制。

## goto label eof

goto 可以重定向程序指针到指定便签。模拟函数的时候为了防止执行到其他程序片段， 必须在文件末尾设置 `eof` 标签， 函数末尾使用 `goto eol` 来跳过其他程序片段。

## 函数中使用参数

类似 c 语言的形参， %1 ~ %9 是 bat 的形参，  参数多于 9 个时使用 shift 以循环移位的方式修改 %n 的值。 

```cmd
call:func 1 2 3 4 5 6 7 8 9 10 11
goto eof 

:func 
echo %1
shift
goto eof

:eof
```

## 返回值

bat 不支持返回值 ， 所以不存在类似 `set a=call:func xx` 的语法。bat 是单线程的， 可以把函数执行结果赋给全局变量， 其他函数使用该全局变量即可。如果调用的是 bat 文件， 可通过 `errorlevel exit /b` 来传递状态码。

调用其他 exe ， 假如其输出结果打印到终端， 则 `for /f` 可以获取筛选出想要的返回值。

```cmd
call:sizeoffileall path name
goto eof

:sizeoffileall
for /f "tokens=3, delims= " %%size in ('dir "%1" ^| findstr %2 ') (
	echo %%size
)
goto eof

:eof
```

# errorlevel

表示程序退出码， 反应程序执行状态， 一般用来判断程序是否成功执行。

# 流程控制

## for

从一个集合中循环取出一项进行处理, 主要用于文件内容处理， 遍历文件夹和文件。ford, forr 处理文件和文件夹， forl 生成数列， forf 处理文件内容。

集合可以是

- 多个字符串
- exe 程序返回字符串集合
- 一个文件或多个文件， 可以使用通配符 `* ?`
- 多个目录

```cmd
::ford, forr 集合
(*.doc) (*.doc *.txt *.me) (jan*.doc jan*.rpt feb*.doc feb*.rpt) (ar??1991.* ap??1991.*)

::forl 集合
(1,2,10) (100, -2, 10)

::forf 集合
(1 2 35 4) (filename)

::ford , forr ,forl , forf 都可以处理 exe 程序的输出
```


### ford

`FOR /D %%variable IN (set) DO command [command-parameters]`

从 set 指定的路径中取出目录， 但是也有例外。若直接把 set 指定为一组路径(不管是文件还是文件夹)甚至单纯是字符(空格隔开), 也会被赋给 `%%i`。如果使用了通配符， 则确实只输出目录。

```cmd
FOR /D %%i IN (c:\*) DO (
  echo %%i
)
```

### forr

`for /r [[<drive>:]<path>] {%%|%}<variable> in (<set>) do <command> [<commandlinepptions>]`

用于目录和文件的递归遍历, 结果赋给 `%%i`。如果 `<set>` 设置成 `.`, 则遍历文件夹。forr 可以指定遍历的根目录， 未指定则默认使用当前目录。

```cmd
FOR /r C:\Users\YUXIA\Desktop\autojspro %%i IN (*) DO (
  echo %%i
)

:: set := * all files and directorys
:: set := *.ts all ts file
```

### forl

`for /l {%%|%}<variable> in (<start#>,<step#>,<end#>) do <command> [<commandlinepptions>]`

生成一个数字序列, 赋给 `%%i`， 由 start step end 控制， 如果生成的数值仍在start end 范围内， 循环继续。例如：（1， 2 ，5） => 1, 3, 5,  (5, -1, 2) => 5, 4, 3, 2

### forf

读取一行文本， 从中取出需要的文本段赋给 `%%i`， 由delims， tokens, eol, skip 四个参数控制。文本可以从文件中获取， 此时 set 就是文件名；也可以从字符串中获取， 此时 set 就是一行文本；还可以是其他程序的输出， 此时 set 是程序执行的命令且必须用 `' '` 括起来。

```cmd
for /f [usebackq <parsingkeywords>] {%%|%}<variable> in (<Set>) do <command> [<commandlinepptions>]
for /f [usebackq <parsingkeywords>] {%%|%}<variable> in ('<LiteralString>') do <command> [<commandlinepptions>]
for /f [usebackq <parsingkeywords>] {%%|%}<variable> in (`<command>`) do <command> [<commandlinepptions>]```
```

| keyword            | Despcription                                                                                                                                                                                                                                                                                                                                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| eol=`<c>`          | Specifies an end of line character (just one character).                                                                                                                                                                                                                                                                                                                                                                 |
| skip=`<n>`         | Specifies the number of lines to skip at the beginning of the file.                                                                                                                                                                                                                                                                                                                                                      |
| delims=`<xxx>`     | Specifies a delimiter set. This replaces the default delimiter set of space and tab.                                                                                                                                                                                                                                                                                                                                     |
| tokens=`<x,y,m–n>` | Specifies which tokens from each line are to be passed to the **for** loop for each iteration. As a result, additional variable names are allocated. \_m-n\_ specifies a range, from the \_m\_th through the \_n\_th tokens. If the last character in the **tokens=** string is an asterisk (), an additional variable is allocated, and it receives the remaining text on the line after the last token that is parsed. |
| usebackq           | Specifies to run a back-quoted string as a command, use a single-quoted string as a literal string, or, for long file names that contain spaces, allow file names in `<set>`, to each be enclosed in double-quotation marks.                                                                                                                                                                                                                                                                                                                                                                                                                         |

**特别强调:**

如果读取的文件每一行有多列， 他们直接用空格分开则

```bat
for /f "tokens=1,2 delims= " %%c in (%path%) do (
  echo %%c %%d
)
```

这里有两行， 但是不能用两个变量分别来接受， 只能定义一个 `%%c`。默认第二行用 `%%d`来表示。如果使用 `%%a` 则第二行为 `%%b`，依次类推。

## if

执行条件处理

```cmd
if [not] ERRORLEVEL <number> <command> [else <expression>]
if [not] <string1>==<string2> <command> [else <expression>]
if [not] exist <filename> <command> [else <expression>]
```

如果开启 extensions (setlocal enableextensions) 

```cmd
if [/i] <string1> <compareop> <string2> <command> [else <expression>]
if cmdextversion <number> <command> [else <expression>]
if defined <variable> <command> [else <expression>]
```

可以用于三个功能

- 判断 errorlevel ， 决定执行不同的代码
- 判断两个文本的相等性， 决定执行不同的代码
- 判断文件存在性， 决定指定不同的代码

if 可以嵌套。


当开启 extensions 后支持比较运算符 `equ, neq, lss, leq, gtr,geq`, 以及判断变量的存在性。

| parameter                | description                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| not                      | Specifies that the command should be carried out only if the condition is false.                                                                                                                                                                                                                                                                                                                                                           |
| errorlevel `<number>`    | Specifies a true condition only if the previous program run by Cmd.exe returned an exit code equal to or greater than `number`.                                                                                                                                                                                                                                                                                                            |
| `<command>`              | Specifies the command that should be carried out if the preceding condition is met.                                                                                                                                                                                                                                                                                                                                                        |
| `<string1>==<string2>`   | Specifies a true condition only if `string1` and `string2` are the same. These values can be literal strings or batch variables (for example, `%1`). You do not need to enclose literal strings in quotation marks.                                                                                                                                                                                                                        |
| exist `<filename>`       | Specifies a true condition if the specified file name exists.                                                                                                                                                                                                                                                                                                                                                                              |
| `<compareop>`            | Specifies a three-letter comparison operator                                                                                                                                                                                                                                                                                                                                                                                               |
| /i                       | Forces string comparisons to ignore case. You can use **/i** on the `string1==string2` form of **if**. These comparisons are generic, in that if both _string1_ and _string2_ are comprised of numeric digits only, the strings are converted to numbers and a numeric comparison is performed.                                                                                                                                            |
| cmdextversion `<number>` | Specifies a true condition only if the internal version number associated with the command extensions feature of Cmd.exe is equal to or greater than the number specified. The first version is 1. It increases by increments of one when significant enhancements are added to the command extensions. The **cmdextversion** conditional is never true when command extensions are disabled (by default, command extensions are enabled). |
| defined `<variable>`     | Specifies a true condition if _variable_ is defined.                                                                                                                                                                                                                                                                                                                                                                                       |

### compareop

-   **EQU** - Equal to
-   **NEQ** - Not equal to
-   **LSS** - Less than
-   **LEQ** - Less than or equal to
-   **GTR** - Greater than
-   **GEQ** - Greater than or equal to

## goto

跳转到指定标号处， 相等于重定向程序指针。从 if for 命令体中跳出， 则if for 命令不再执行。

## call

```cmd
call [drive:][path]<filename> [<batchparameters>]] 
call [:<label> [<arguments>]]
```

调用程序片段或文件， 可以指定参数， 执行完毕后会返回。调用程序片段， 跳转至标号处开始执行， 至文件末尾， 如果有多个程序片段， 建议在片段结尾处跳转到文件末尾标号， 用来模拟函数。
## start

```cmd
start ["title"] [/d <path>] [/i] [{/min | /max}] [{/separate | /shared}] [{/low | /normal | /high | /realtime | /abovenormal | /belownormal}] [/node <NUMA node>] [/affinity <hexaffinity>] [/wait] [/b] [/machine <x86|amd64|arm|arm64>] [<command> [<parameter>... ] | <program> [<parameter>... ]]
```

开启一个新的 cmd 窗口， 运行指定的脚本和程序。同时可以指定新 cmd 窗口的外观信息

- titile
- size min max
- 新窗口还是共享窗口
- 高度
- ....

# 转义

bat 使用 `^` 作为转义符

# 重定向和管道

## 管道

可以把一个命令的输出作为另外一个命令的输入， 本质上是一种输入输出的重定向。`|` 是重定向符号

## 重定向

输出分三种， 使用 `>` 可以进行重定向

- 标准输入 0
- 标准输出 1
- 标准错误 2

打开 cmd 里边展示的为标准输出。可以重定向到

- 文件
- nul (黑洞)

```cmd
ffmpeg -i 1.mp4 -c:v copy 1.avi >nul 2>nul
::重定向, 用来屏蔽输出和错误，不会打印到终端
```

# 一些经验

## 路径

bat 程序中路径使用 `\`作为分割符， 这个很重要

## 条件为空格或者空的 if

可以使用 `""` 来表示空， 此时变量也要用 `""` 括起来， 空格也是如此

```cmd
set x=a

if "%x%"=="" (xxxxx)
if "%x%"==" " (xxxxx)
```

# 命令&&环境变量

## path

## pathext

设置 `pathexe`后无需执行文件扩展名即可用来运行打开。`echo %pathext%` 可以查看输出

```cmd
.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.PY;.PYW
```

使用 `set pathext=%pathext%;.XXX` 来添加一个后缀。或者通过 `set pathext=%pathext:;.xxx;=;%` 来删除某个扩展名。

## sc

开机启动

## mklink

链接

## chcp

chcp 65001 可以解决中文乱码

# 参考

