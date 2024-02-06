#linux #awk #tool

# gawk on windows

gawk 在 windows 和 linux 上， 对命令行的细节处理存在差异。

```shell
#linux
gawk -F ':' '{printf("%s\n", $1)}' 1.txt
```

```cmd
:windows 
gawk -F ":" "{printf(\"%s\n\", $1)}" 1.txt
```

在 windows 上， 需要用双引号包裹命令部分， 内部双引号需要转义。

# 术语

## 记录(record)

awk 读入的数据， 每一行(`\n`)为一条记录。NR 表示已经处理的记录数， FNR 表示当前文件中已经被处理的记录数。

## 段（field)

awk 把记录分成若干行， 默认使用 `[[:blank:]]` 作为分割符， 也可使用 `-F` 指定， 分割符可以是某个字符，  可以是正则（ / / 不是必须的)。

```shell
gawk -F "[[:digit:]{2,}]" 
```

awk 定义了一些变量可以获取段的值

- $0 所有段
- $1 第一个段
 .....
- $n 第 n 个段

NF 表示当前被处理的段。

## 模式(pattern)

用来决定动作是否可以被执行， 起过滤记录的作用。

## 动作(action)

由花括号包裹起来的代码块，  是脚本的主体， 用来处理记录。

## 规则(rules)

awk 脚本由一个个规则组成， 用来处理输入的记录, 每个规则由 模式 和 动作组成。

# 命令行参数

- f 指定 awk 脚本
- F 指定列分隔符
- v 设置变量
- D 开启调试
- i 导入库文件
- l 加载扩展

命令行中可以定义一些变量， 供脚本使用， 有两个方式

```shell
# -v 
gawk -v n=10 -f test.awk 1.txt

# before filename

gawk -f test.awk n=10 1.txt n=20 1.txts
```

# 脚本结构

awk 脚本结构如下所示

```awk
pattern{action}.....parrern{action}
....
function func() {}
....
```

每个 pattern{action} 叫做规则(rules), action 是逻辑执行块。

有些 pattern 应用于文件每一行， 用来过滤感兴趣的 record, 也可以在 action 块使用 if 语句来过滤， 此时 pattern 可是用逻辑运算符来组合。有些 pattern 只在特定位置生效， 为系统内置。多个规则按照定义顺序来执行， 但是 BEGIN/END, BEGINFILE/ENDFILE 例外， 他们的执行时机已被系统固定下来了。最佳实践中， 最好按执行时机的先后顺序来书写规则。

## pattern

- 正则(7.1.1)                                        应用每行
- 表达式(7.1.2)                                    应用每行
- range(7.1.3)                                      应用每行
- BEGIN,END(7.1.4)                           脚本开始/结束生效
- BEGINFILE,ENDFILE(7.1.5)          文件开始/结束生效
- empty(7.1.6)                                     应用每行

### 正则pattern

```awk
/regxstr/ {}
str ~ /regxstr/{}
$1 ~ /regxstr/{}
need_check ~ /regxstr/ {need_check 是一个变量}
```

### 表达式pattern

一般是逻辑表达式， 可判端真假, 下面的例子也是合法的具有真值表达式

```awk
#非 0
if (3.1415927) 
	print " A strange truth value"

#非空
if (" Four Score And Seven Years Ago" )
	print " A strange truth value"

#非0?????
if (j = 57)
	print " A strange truth value"
```

字段($i)和 action中的变量都可以是逻辑表达式的组成部分， 不过二者还是有区别

- $i  可以把当前行的某个字段
- 变量 只能是由历史行计算的中间结果

```awk
#每行都生效
$1 > 10 {print $2}

# 有效
{
   sum=$2+$3+$4
}
sum>111{
   print $1
}

# 无效果,系统无法得知 sum 的值

sum>111 {
   sum=$2+$3+$4
   print $1
}
```

### range 

形如 `bgpat, edpat`, 其中 bgpat 和 edpat 不能是 range pattern, 同时 range pattern 也不能和其他的 pattern 组合, 但是 bgpat 和 enpat 可以是组合的。

接受规则如下：

1. 一旦某行满足 bgpat ， 则开始接受行
2. 直到某行满足 enpat, 则停止接受行
3. 可以多次打开关闭，直至文件最后一行

### BEGIN/END

脚本开始/结束时生效， 若接受多个文件， 也只生效一次， 一般用于脚本的准备和清理。begin 规则执行时， awk 还没读入任何输入。end 规则会在读入输入后执行。一个文件中可以有多个 begin 规则， 他们的执行顺序取决于定义顺序, end 也一样。 

我们知道 begin 规则不能读取记录， 但这也不是绝对的， awk 允许我们使用 getline 来提前读取记录。

```awk
# 读取两行
BEGIN{
	getline tmp;
	print tmp;

	getline tmp;
	print tmp;
}
```

由于历史原因， awk end 规则中不能读取 NF 和 $0(段), 同样可以用 getline 来获取。

begin 规则中也无法使用 next 和 nextfile, 因为主循环（读记录-匹配-处理）还没开始。end 规则亦然， 因为主循环已经结束。

### BEGINFILE/ENDFILE

beginfile/endfile 规则是 gawk 独有特性。beginfile 在 gawk 读入第一行时执行， 比一般规则先执行；endfile 在 gawk 读入文件最后一行后执行。一个文件中也可以有多个 beginfile 规则， 他们的执行顺序取决于定义顺序, endfile 也一样。 beginfile 在 begin 后执行， endfile 在 end 之前执行。输入多个文件时， 每个文件都会触发 beginfile / endfile 规则。

** BEGINFILE 持有预定义变量 **

- FILENAME  当前文件名
- FNR 设为 0
- $i (field) 被清空 (As with the BEGIN and END rules#148)

beginefile 规则存在主要的目的

- 测试文件是否可读

>不可读时， 错误信息保存在 ERRNO ， 否则为空。测试 ERRNO 一旦不为空， 则 nextfile 跳过该文件， 使得脚本更加鲁棒。`if (ERRNO) nextfile;`

- 设置自定义的文件解析函数 

### 空规则

每条记录都会执行。

## action

由若干个 awk 语句组成， 每个语句以分号或者换行隔开。所有语句用花括号包裹起来。action 可以省略， 此时 pattern 不能省略

```awk
gawk -e "/[[:digit:]]/" 1.txt
gawk -e "$0 ~ /[[:digit:]]/ {print $0}" 1.txt
```

# 读文件

## 列的分割

- 使用 -F 参数指定分割符， 支持正则
- FS 分隔符分割， 支持正则 `FS=/[^a-z]/`

```awk
#一个字符一列
FS=""
#整行作为一列
FS="\n"
```

- FIELDWIDTHS 宽度分割， 实用于非结构化数据， 很难找到适合的分割符

```awk
#数据不足, 不发生错误，但是 NF 等于实际的列数
#取三列，没取到的字符被舍弃
FIELDWIDTHS = " 2 3 4"

# * 表示剩余部分
FIELDWIDTHS = "2 3 *"

# 第一列跳过 1 个字符，相当于从第二个字符开始取两个字符,设为第一列
FIELDWIDTHS="1:2 3 4"
```

- FPAT 内容分割， 正则匹配的文本作为列

## 行的分割

- RS

## getline 自由控制读入

getline 读取一条记录，  返回

- 1 成功读取
- 0 到文件末尾
- -1 遇到错误(文件打不开）， 错误信息放到 ERRNO
- -2 重试， `PROCINFO[" input" ," RETRY" ]` 会被设置

awk 中， 所有规则(除去 begin/end, beginfile/endfile) 执行一遍后， NR (包括NF,  FNR, RT)自动加 1， 此时 NR 相当于行指针, 换句话说 $0 的值完全取决于 NR。getline 执行后同样会更新 NR 等。如果未指定数据存放位置， getline 会把数据存放到 $0。如果 NR 指向最后一行， 执行 getline 并不会更新 NR 的值。

前面的讨论针对当前文件， getline 同样支持读取其他文件， 此时系统会维护多个 NR。如果通过管道获取数据， getline 的执行不会更新 NR 等。

```awk
{
	if ((getline var) < 0){
		print "ERROR"
	} else {
		print var
		print $0
	}
}

#原始数据
1
2
3
4
5

#输出
2
1
4
3
5 #本来NR应该设置为 6 但只有 5 条数据，NR 不更新
5 
```

**几种用法**

- getline 从当前文件读取， 读到的内容存放到 $0
- getline var 从当前文件读取， 读取的内容存放到 var
- getline var < "1.txt" 从 1.txt 读取， 读到的内容存放到 var  
- command | getline var 从管道中读取

```awk
tmp = substr($0, 10)
while ((tmp | getline) > 0)
	print
close(tmp)


#另外一个例子
sortcom = " sort -r names"
sortcom | getline foo
...
close(sortcom)
```

- getline 与 合作进程(coprocess)

awk 支持， 把数据交给其他程序处理， 然后通过 getline 读取处理结果

```awk
print " some query"|& " db_server"

" db_server"|& getline
```

## 读取超时

gawk 支持通过 tcp/ip 协议获取网络数据， 引入读取超时， 可以提高程序的健壮性。`PROCINFO[" input_name" , " READ_TIMEOUT" ] = timeout in milliseconds`

```awk
Service = " /inet/tcp/0/localhost/daytime"
PROCINFO[Service, " READ_TIMEOUT" ] = 100
if ((Service |& getline) > 0)
	print $0
else if (ERRNO != " " )
	print ERRNO
```

## 重试

`PROCINFO[" input_name" , " RETRY" ] = 1`

# 输出

awk 处理完毕后， 结果可以输出(print / printf)到标准输出也可以到文件。OFS 和 ORS 分别控制输出的行分割符和列分割符。awk 可以使用 OFMT 控制输出数字的精度(本质 sprintf())。

## OFS

print xx, bb, ccc,dd 输出多列时使用 OFS 作为分割符, 默认为空格

## ORS

print xx
print yy

输出多行时， 使用 ORS 作为行分割符, 默认为 `\n`

## OFMT

控制输出数字的精度(位数)， 默认为 `%.6f`， 即保留小数后 6 位。

## 格式化字符串

printf 支持格式化字符串， 可以实现更精细的输出控制

**格式控制字符**

不同类型的数据， 需要不同的输出格式和显示方式， 格式控制字符就是来达成此目的的设计

- %a 浮点数(格式 `[-]0xh.hhhhp+-dd`)
- %c  char
- %d 十进制数字
- %e 科学计数法
- %f  浮点数格式输出
- %g 科学计数法或者浮点数
- %o 八进制
- %s 字符串
- %u  无符号数
- %x  十六进制

**格式修饰符**

可以控制输出宽度， 对齐方式， 小数位数， 整数位数等

- N$ 位置修饰符

```awk
printf " %s %s\n" , " don't" , " panic"
printf " %2$s %1$s\n" , " panic" , " don't"
```

- - 左对齐， 默认右对齐

```awk
printf "%-10s", "foo"
```

- + 显示数字的正负号

```awk
print "%+d", -19, 10
```

- `#` 输出十六/八进制数字显示 `0x` `0`, %e %f 输出点， %g 输出后面的 0
- `0` 空格替换成 0, 对数字有效， 宽度大于数字宽度有效

```awk
printf "%010d\n", 10
```

- width 指定输出宽度
- .prec 指定小数位数

## 输出重定向

```awk
#重定向到文件
print items >> output-file #追加,文件不存在则创建
print items > output-file #先删除在写入

#管道
print items | command  

#协程
print items |& command
```

```awk
print $1 > " names.unsorted"
command = " sort -r > names.sorted"
print $1 | command

#另外一个
print $0 | "coreutils sort -k2 -t:"
```

```awk
report = " mail bug-system"
print(" Awk script failed:" , $0) | report
print(" at record number" , FNR, " of" , FILENAME) | report
close(report)
```

**特殊文件**

- /dev/stdin
- /dev/stdout
- /dev/stderr
- /dev/fd/N 使用其他打开的文件 N 为 文件描述符编号
- /net-type/protocol/local-port/remote-host/remote-port 网络文件

## 关闭

```awk
sortcom = " sort -r names"
sortcom | getline foo
...
close(sortcom)
```

**为何要关闭打开的文件或者管道**

- 防止 awk 耗尽自身进程空间的文件描述符
- 关闭文件促使 管道 flush 数据

# 变量

awk 支持自定义变量， 和 c 分享相同标识符定义规则。

## 定义

```awk
a="";
a="hello";
print a
```

## 赋值

1. 赋值符号赋值

```awk
variable=value
```
2. 参数 `-v` 赋值

```shell
#awk 程序中可以直接使用未赋值和未定义的变量，通过 awk -v var="value" 来赋值，类似 c 中 #ifdef xxx, gcc 可以 通过参数给 xxx 赋值
```

3. var=value file var=value file 方式

```shell
awk '{print n}' n=3 file1 n=2 file2
#输出 3 2
```

## 使用未定义的变量

awk 使用未定义的变量不会发生错误， 相反会自动创建该变量， 并且赋予初始值, 类型不同初始值也不同

-  string 赋值空串
- number 赋值 0

## 作用域

在规则中定义的变量是全局的， 可以在所有的规则和自定义函数中使用

```awk
FNR <= 3{

  if (FNR==1){
    a=1
  }else if (FNR==2) {
    b=2
  }else if (FNR==3) {
    c=3
  }
  print a b c
  
}

#output
1
12
123
```

自定义函数中定义的变量是局部的， 对外不可见。

# 常量

## 数字

```awk
#浮点数
3.4
#科学计数法
3.4e-2
#八进制
04
#十六进制
0xa
```

## 字符串

```awk
#字符串
"exp"
#支持续行
"hello\
world"
```

## 正则

```awk
#常规
/^regxp$/ 
#表示匹配
str ~ /regxp/
单独出现 /regxp/ 等同于 match(str, /regxp)
#表示没有匹配
str !~ /regxp/
```

鉴于读取每行脚本中间部分都会执行， 变量应该在 begin 部分声明。对于 gawk 还支持加强的正则， 正则常量前冠以 @, 用以显示表明为正则常量。

## 数字、字符串转换

### 串联

ab 或者 a b 这种叫做串联， 字符串、数字、变量都可以相互串联， 表示拼接。

```awk
a=1;b=4;a b #14

a=1;b=s;a b #1s

a$1
```

### 转换原则

字符和数字相互可以转换。

```awk
a=1;b=4;(a b)+4 #18

a=1;b=4;(a+b)+4 #9
```

### 强制转换

- a "" 强制转换成字符串
- a+0 强制转换成数字

## 内置变量

太多了， 不一一列举， 详情参看 （Effective AWK Programming#159）

### 控制 awk 行为相关

1. 输入
	- FIELDWIDTH  指定 field 的宽度， 按段宽度分割记录时要用到。
	- FPAT  指定正则， 按正则分割记录时要用到
	- FS  指定分割符， 按分割符分割记录时要用到, 支持正则
	- RS 输入记录(行)分割符， 默认是 `\n` 。若为空， 则用空行分割， 也可以是正则
 2. 输出
	- OFMT 控制 print , sprintf 打印数字的精度， 默认为 `%.6g` , 即小数点后6位。
	- CONVFMT posix 系统引入， 作用同 OFMT
	- OFS 输出段分割符，  print 输出默认使用空格作为分割符, 可以使用 ofs 来修改
	- ORS 输出行分隔符， 默认为换行符

 3. 数学
	- PREC 指定浮点数的精度
	- ROUNDMODE  指定数字近似规则 （Effective AWK Programming#392） 
 4. 数组
	- SUBSEP 指定多维数组下标的分割符， awk 中多维数组本质为一维数组， 假设 subsep=@, 则 `a[1,2]=10` 转换 `a[1@2]=10`
 5. 其他
	- IGNORECASE  为真(非空或者非0，) 则字符比较和正则匹配都会是大小写无关。会改变众多函数行为， 也会影响 行 和 列的分割。
	- TEXTDOMAIN  用于程序国际化 ，参看 (Effective AWK Programming#351)

### 传递信息给脚本

- ARGC  / ARGV : 同 c 的定义， 不过 option 不被计算在内。`gawk -F ":" -f showtable.awk -v a="b" n=10 1.txt m=20 2.txt`  输出 `gawk;n=10;1.txt;m=20;2.txt;off5rrr1;end1;`

- ARGIND: 表示当前正在处理的文件在 ARGV 中的 index。处理中途发生错误可以告诉调用者哪些文件已经成功处理， 那个文件发生错误。如果在 程序中改变 ARGIND ， 则程序立即转到下一个文件。

- ENVIRON: 系统环境变量， 为一个关联数组， 环境变量名为数组下标， 值为环境变量的值

- ERRNO: 储存错误信息。BEGINFILE 中， getline, close 函数调用后， getline 重定向后， 如果发生错误， 原因(string)会被填写在 ERRNO 中。其他情况 ERRNO 储存错误码， 就像 c 的 errno。如果非系统错误发生 `PROCINFO["errno"]=0`， 否则  `PROCINFO["errno"]=errno`
- FILENAME: 表示当前正在处理的文件名
- FNR: 处理中文件的当前行， 转到下一个文件 FNR 重置为 1
- NF: 处理中字段的编号， 转到下一行NF 重置为 1
- NR: 自开始执行，处理过的记录(行)数， 可以指定其值来直接跳到某行开始处理
- PROCINFO  太杂了看Effective AWK Programming
- RLENGTH 由 match() 函数设置， 返回匹配到的子串长度， 没有匹配到返回 -1
- RSTART 由 match() 函数设置， 返回匹配到的子串起始 index, 没有匹配到返回 0
- RT 代指分隔符，当RS是固定匹配此时RS=RT。如果RS是正则匹配，则RT是RS正则匹配的值
- SYMTAB  为一个数组， 下标为 awk 程序中定义的变量和数组名称， `a=10` 等同于 `SYMTAB["a"]=10`。awk 解析完脚本后， SYSTAB 就被初始化了。

---
# 数组

awk 支持数组， 数组下标可以是数字， 也可以是字符串。awk 支持多维数组， 本质是一维数组，  `a[1,3]=4` 被处理成 `a[1SUBSEP3]=4`, 其中 SUBSEP 为可修改的变量。

awk 数组相当灵活， 无需指定数组大小， 元素类型也无需相同, 因为 awk 数组本质为关联数组， 换句话说 key 和 value 作为一个关联组共同作为一个数组元素。

## 元素引用

awk 数组元素的引用同 c 语言一致， `a[index]`。对不存在的数组元素的引用， awk 会创建该元素， 设定其值为"", 即空字符串，并返回空字符串。

## 删除元素

```awk
delete arr[a]
```

## 元素存在测试

若想测试某个元素是否存在， 可以使用 `index in array` 

```awk
if (2 in arr) {
	print "exists"
}
```

## 元素遍历

### for

实用于 Index 为数字的数组

```awk
for(i=1;i<=length(arr);i++){
	print arr[i]
}
```

### for..in..

此种方式更为通用

```awk
for(index in arr) {
	print arr[index]
}
```

## 多维数组

多维数组的本质是一维数组， 只要数组的元素是数组， 就可以模拟出多维数组。另外 awk 还支持伪多维数组。

### 伪多维数组

多维数组 `arr[a,b]`， 是一个一维数组， a b 解析成一个唯一的 index, awk 负责该解析工作。`arr[a,b]` 等价于 `arr[aSUBSEPb]`, SUBSEP 为一个常量， 作为分割符。

### c 风格多维数组

形如`arr[a][b]`, 本质是一位数组， length(arr) 等于 a, `arr[i]` 是一个数组。遍历

```awk
for (nn in arr){
	for (mm in arr){
		print arr[nn][mm]
	}
}
```

## 数组排序

可是使用 `asort` 和 `asorti` 对数组排序。他们的函数原型

```awk
asort(src, [dest, how])
asorti(src,[dest, how])

#src 为待排序数组
#dest 为排序后结果,可选
#how 排序方式, 为函数名
```

asort 对数组元素排序， asorti 对数组下标排序。排序结果可以写回原数组， 也可以写到新数组。排序结果统一使用数字来索引， 也就是会丢失原来的索引。

```awk
PROCINFO["sorted_in"]="@ind_str_asc"
asort(arr)
for (nn in arr){
	asort(arr[nn])
	for (mm in arr){
	  print arr[nn][mm]
	}
}

# 对索引升序排列, 且对两个索引均排序, 实现多列排序效果

asort(arr, dest, "@ind_str_asc")
for(nn in dest) {
	print dest[nn]
}
```

### 指定排序方式

- 通过 `PROCINFO["sorted_in"]` 指定
- 通过 asort 和 asorti 参数 how 指定

###  排序函数

**内置**

awk 内置丰富的内置排序函数, 参看 （Effective AWK Programming#179）

**自定义**

形如 `function aa(i1, v1, i2, v2)` , i1, i2 为索引， v1, v2 为数组元素。两个例子

```awk
#按索引升序
function ind_asc(i1, v1, i2, v2) {
	if (i1 > i2){
		 return 1
	} else if (i1==i2) {
		 return 0
	} else if (i1 < i2) {
		 return -1
	}
}

#按元素升序, 假设是一维数组
function val_asc(i1, v1, i2, v2) {
	if (v1 > v2){
		 return 1
	} else if (v1==v2) {
		 return 0
	} else if (v1 < v2) {
		 return -1
	}
}
```

## awk 多列排序

构建一个多维数组， 把需要排序的列作为索引， 把行存为数组的值。就可以实现多列排序。

---

# 流程控制

## if -else

```awk
if (cond) doing some thing; else doing another

if (cond) {
	doing some sthing 
} else {
	doing another
}
```

## for 

```awk
for (init-cond; exit-cond; change-cond) {doing some thing }
for (init-cond; exit-cond; change-cond) doing some thing 

for (i in array)
    do something with array[i]
```

## while

```awk
while (condition)
  body
```

## do-while

```awk
do
	body
while (condition)
```

## switch

swich 只能在 gawk 中使用， 语法和 c 一样。

## break
## continue

和 c  语言一致

## next

停止处理当前记录， 转到下一个记录。next 不被允许使用， 在：

- **end / endfile**    所有的记录已经处理完毕。
- **beginfile**    还没有读入记录。
- **begin / end**     posix 标准不能使用， 强行使用会报错
- **用户自定义函数**

## nextfile

停止处理当前文件， 转到下一个文件。切换到下一个文件会导致：

- FILENAME 指向下一个文件
- FNR 重新重置为 1
- 重新执行初 begin 以外的规则
- endfile 被执行
- ARGIND 加 1

 如果在 beginfile 中使用 nextfile , 则不会触发 endfile。其他 awk 不支持在函数中使用 nextfile, 但 gawk 可以。

## exit

`exit exit_code` , 停止脚本， 并且返回退出码给调用者。所处位置不同行为也有差异：

- begin 中使用 exit， 记录不会被读入， 如果有 end,  则会被执行
- end 中使用 exit, 则立即停止， 没有其他规则的执行。
- 非 begin / end 规则中使用， 停止执行后续规则， 也不处理后续的记录， 如果有 end 则执行。gawk 不执行 endfile。

发生错误时， 调用 exit 并返回错误码， 供调用者分析， 为最佳实践。

# 正则

awk 的正则支持比较完整的正则特性， 但是不支持 环视

- 量词
- 字符组(class)
- 分支
- 捕获组(capture group)
- 元字符

另外还支持 Posix 标准的 "元字符"

- `[:alnum:]` 数字和字母
- `[:blank:]` 空格和 tab
- `[:cntrl:]` 控制字符
- `:digit:]` 数字
- `[:graph:]` 可打印也可见，空格非可见
- `[:lower:]` 小写
- `[:print:]` 可打印，即非控制符
- `[:punct:]` punctation(标点)， 非字母、数字、空格字符、控制字符
- `[:space:]` 空格字符，包括空格，\t, \v, \n, \r(回车), \f
- `[:upper:]` 大写
- `[:xdigit:]` 16进制

使用时， posix 元字符和其他的没区别, 例如：`[[:alnum:]]` 匹配一个数字或字母

gawk 支持 gnu 标准的元字符

- `\s` 空格
- `\S` 非空格
- `\w` 字（字母，数字，下划线）
- `\W` 非字

零长度断言, 用来断字

- `\<` 开头空串(empty string)
- `\>` 结尾空串
- `\y` 开头或结尾空串
-  `B` 是 `\y` 反面  
- <hbk>\`</hbb> buffer 开头的空串
- `\'` buffer 结尾的空串

gawk 默认支持 gnu 和 posix 元字符， 可使用 gawk 命令行选项来做选择

- --posix
- --tranditional

## 大小写

- IGNORECASE=1 敏感
- IGNORECASE=0 不敏感

## 匹配

-  (动态正则表达式Dynamic Regexps) 匹配

```awk
#匹配
str ~regx
#不匹配
str !~regx
#匹配
regx 等价于 $0 ~regx
```

- match()

# 函数

awk 脚本也支持库的引入(Effective AWK Programming#p237)

## 用户自定义函数

和 c 类似。这里着重介绍返回函数结果的两种方式

- return
- 参数列表返回(类似 c 的指针和引用)

```awk
{
	arr[NR]=$0
	
}
END{
	copy(arr, dest)
	for(nn in dest) 
		print dest[nn]
}

function copy(arr) {
	for (mm in arr) {
		 dest[mm] = arr[mm]
	}
}
```

## 内建函数

### 生成布尔值

- mkbool(exp)

### 数学运算

- atan2(y,x) 反正切
- cos(x)
- sin(x)
- exp(x)
- int(x) 取整
- log(x)
- rand() 0~1随机
- sqrt(x)
- srand(x)

### 字符串处理

#### length 取长度 

`length(str)`

如果 str 省略， 则默认取 $0 的长度, 还可取数字的长度， 先转字符串， 再取位数

####  asort/asorti  排序

`asort(src, [dest, how]) asorti(src, [dest, how]) ` 排序

其具体用法参看， 数组排序

####  gensub/gsub/sub 替换

`gensub(regexp, replacement, how [, target])` 替换

通用替换函数， 使用正则来匹配目标字符串， 比 sub(), gsub( )更为强大。gawk      支持捕获组， /(.+) (.+)/ 有两个捕获组， 可是使用 `\\1  \\2` 来引用捕获组的内容。

- regexp 正则
- replacement 替换文本
- how 为数字， 多个匹配时指定替换位置， 若为 g 或 G 则替换所有匹配
- target 可选， 指定匹配对象， 若省略则为 $0

```awk
  a = " abc def"
  b = gensub(/(.+) (.+)/, " \\2 \\1" , " g" , a)
  print b
```

- `gsub(regexp, replacement [, target])`  替换

gsub 默认替换所有匹配的位置。

- `sub(regexp, replacement [, target])`  替换

只替换第一个

####  index 搜索第一次出现位置

`index(in, find)`

在 in 中搜索 find, 返回首次发现的位置索引

####  match 匹配

`match(string, regexp [, array])`

正则匹配， 返回第一次匹配位置的索引， 无匹配则返回 0。通过 match 也可以很方便获取匹配的字符串， 有两种方式

1.  RSTART/ RLENGTH

match 会设置系统预定义变量 RSTART 和 RLENGTH。成功匹配，  RSTART 储存起始位置， RLENGTH 存储长度， 否则 RSTART 为 0, RLENGTH 为 -1

2.  array

match 第三个参数不为空， 这是一个传出参数， 第一个元素恒为匹配的字符串， 如果有捕获组， 则后续元素为捕获组的内容。

```awk
match(p, /\\([^\\]+)\.cmd$/, get_name_ret) 

substr(p, RSTART+1, RLENGTH-4)
get_name_ret[1]
```

#### patsplit/split 分割字符串

`patsplit(string, array [, fieldpat [, seps ] ]) `

用正则表达式fieldpat匹配字符串string，将所有匹配成功的部分保存到数组array中，数组索引从1开始存储。返回值是array的元素个数，即匹配成功了多少次

- array 分割的字符串储存于此
- fieldpat 正则或者字符串， 用来分割字符串， 类似 FPAT, 未指定则为 FPAT
- seps 存储分割符， 为一个数组, 未匹配的则为分割符, gawk 填写， 为输出参数

`split(string, array [, fieldsep [, seps ] ])`

将字符串分割后保存到数组array中，数组索引从1开始存储。并返回分割得到的元素个数

- fieldsep 分割符， 为字符串， 省略则为 FS
- seps 为数组， 储存每次分割的分隔符

#### sprintf 格式化字符串

`sprintf(format, expression1, ...)` 

格式化字符串， 非打印， 返回格式化的字符串

#### strtonum 字符串转数字

`strtonum(str)`

字符串转数字。str 

- 0 开头， 解析为 八进制
- 0x 开头， 解析为十六进制

#### substr 取子串

`substr(string, start [, length ])`

字符索引从 1 开始， 返回取得的子串长度。

#### tolower/toupper

`tolower(string)/ toupper(string)`

大小写转换

### 输入输出函数

#### close

`close(filename [, how])`

用来关闭文件， shell 命令， 管道。管道为半双工文件， 此时 how 可为 from 和 to， 指明管道通信方向。

#### fflush

`fflush([filename])`

刷新文件的存储 buffer, 对暂存输出的文件来说， 完成后刷新很有必要。

### 时间函数

#### mktime

`mktime(datespec [, utc-flag ])`

把时间日期格式化文本转成时间戳, 发生错误返回 -1

- dataspec   `YYYY MM DD HH MM SS [DST]`

#### strftime

`strftime([format [, timestamp [, utc-flag] ] ])`

把时间戳转成人可以阅读的格式字符串。如果没有参数， 则 `PROCINFO[" strftime" ]` 指定时间格式, 支持的格式

- %a  weekday name 缩写
- %A weekday name
- %b  month name 缩写
- %B month name
- %c  基于地域（本地）的时间显示格式
- %d 1~31 日期
- %D  等于 %m/%d/%y
- %e day of month, 但填充空格如果只有一个数字
- %F 等于 ‘%Y-%m-%d’
- %g 当前周数， 1~53
- %H  小时， 00~23
- %I 小时， 01~12
- %j day of year, 0~366
- %M 分钟， 00~59
- %n newline
- %p 12小时制的 am/pm
- %r The locale’s 12-hour clock time
- %R 等于 ‘%H:%M’
- %S 秒数， 00~59
- %t tab
- %T 等于 ‘%H:%M:%S’
- %u 星期， 1~7
- %w 星期， 0~6
- %k 24小时制， 填充空格
- %l 12小时制，填充空格

#### systime

系统时间， 返回时间戳

### 位操作函数

```awk
and(v1, v2 [, . . .])
compl(val)
lshift(val, count) 
or(v1, v2 [, . . .])
rshift(val, count)
xor(v1, v2 [, . . .])
```

### 获取类型

#### isarray

`isarray(x)`

#### typeof

`typeof(x)`

返回变量 x 的类型， 为字符串

- " array" 
-  "regexp"
-  "number"
- " number|bool"
- " string"
- " strnum"
- "unassigned"
- " untyped"

## 库函数

#### 合并array

```awk
function join(array, start, end, sep,
result, i)
{
if (sep == " " )
sep = "
"
else if (sep == SUBSEP) # magic value
sep = " "
result = array[start]
for (i = start + 1; i <= end; i++)
result = result sep array[i]
return result
}
```

# 库

引用库, 两种方式

- 命令行 `-i`
- @include 

```awk
# test1.awk
BEGIN {
print " This is script test1."
}

#test2.awk
@include " test1"
BEGIN {
print " This is script test2."
}
```

@include 支持相对路径

# 扩展

扩展是使用 awk 提供的接口， 其他语言写就， 编译生成的文件。

加载扩展

- 命令行使用 -l 
- @load

gawk 会从环境变量 `AWKLIBPATH ` 指定的路径中寻找扩展。

```awk
@load xxxx
{
	print $0
}
```